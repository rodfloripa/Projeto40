<h1>Relatório de Análise e Modelagem de Dados: Epicurious</h1>
<h2>Predição Nutricional e Séries Temporais de Receitas</h2>

<p align="justify"><h3>1. Visão Geral e Origem dos Dados</h3></p>

<p align="justify">Este projeto utiliza o <b>Epicurious Recipes Dataset</b>, um conjunto de dados amplamente reconhecido na comunidade de Ciência de Dados e hospedado no <b>Kaggle</b>. Os dados brutos foram extraídos via <i>web scraping</i> do portal gastronômico Epicurious.com. O objetivo central desta análise é compreender a composição nutricional de mais de 20 mil receitas e aplicar algoritmos de <b>Machine Learning</b> para prever métricas de avaliação e tendências temporais.</p>

<p align="justify"><h3>2. Características do Dataset</h3></p>

<p align="justify">O conjunto de dados apresenta uma estrutura robusta, composta por 20.130 registros e 11 colunas. Durante a exploração inicial, identificamos que aproximadamente 20% das receitas possuem valores ausentes em colunas críticas como <b>calorias, gorduras e sódio</b>. Essa característica exigiu uma etapa rigorosa de limpeza e tratamento de dados para evitar vieses nas predições dos modelos.</p>

<p align="justify"><h3>3. Processamento de Texto</h3></p>

<p align="justify">O processamento de texto no projeto utiliza uma combinação de <b>limpeza por RegEx</b>, <b>tokenização estatística</b> e <b>análise de co-ocorrência</b> para converter strings complexas de ingredientes e direções em variáveis utilizáveis por modelos de Machine Learning.</p>

<p align="justify"><h3>3.1 Limpeza e Normalização com RegEx</h3></p>

<p align="justify">Este bloco é responsável por remover o "ruído" do texto, como observações entre parênteses e caracteres especiais, garantindo que o modelo foque apenas no nome do ingrediente:</p>

```Python

def clean_ingredient(ingredient_name):
    ingredient_name = ingredient_name.lower() # Converte para minúsculas
    ingredient_name = re.sub(r'\s*\([^)]*\)', '', ingredient_name) # Remove parênteses
    ingredient_name = re.sub(r'[^a-z\s]', '', ingredient_name) # Mantém apenas letras
    ingredient_name = re.sub(r'\s+', ' ', ingredient_name).strip() # Remove espaços extras
    return ingredient_name
``` 

<p align="justify"><h3>3.2 Tokenização Contextual (BERT)</h3></p>

<p align="justify">O código utiliza o <b>BERT</b> para transformar o texto em tokens, permitindo medir a complexidade textual de uma forma mais profunda do que uma simples contagem de palavras:</p>

```Python

tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

def count_bert_tokens_for_ingredients(ingredients_data):
    # ... (lógica de tratamento de strings)
    tokens = tokenizer.tokenize(text) # Quebra o texto em subtokens do BERT
    return len(tokens)
``` 

<p align="justify"><h3>3.3 Identificação de Ingredientes Comuns</h3></p>

<p align="justify">Para reduzir o volume de dados irrelevantes (ingredientes raros), o script filtra apenas o que é estatisticamente significativo:</p>

```Python

min_occurrence = 100 
common_ingredients = {ing for ing, count in ingredient_counts.items() if count >= min_occurrence}
``` 

<p align="justify"><h3>3.4 Análise de Co-ocorrência</h3></p>

<p align="justify">Este bloco utiliza <code>itertools.combinations</code> para descobrir quais ingredientes aparecem juntos, o que ajuda a entender a estrutura semântica das receitas:</p>

```Python

for ing1, ing2 in combinations(sorted(list(recipe_cleaned_ingredients)), 2):
    co_occurrence[(ing1, ing2)] += 1
```

<p align="justify"><h3>3.5 Resumo da Lógica Aplicada</h3></p>

<p align="justify">O fluxo segue uma lógica de <b>Funil de Dados</b>: o texto bruto é limpo (Etapa 1), quantificado em sua complexidade (Etapa 2), filtrado por relevância estatística (Etapa 3) e, finalmente, relacionado entre si para gerar insights de padrões culinários (Etapa 4).</p>

<p align="justify"><h3>4. Engenharia de Atributos e Co-ocorrência</h3></p>

<p align="justify">O processamento termina convertendo texto em números (features). O código identifica <b>ingredientes comuns</b> (aqueles que aparecem em mais de 100 receitas) e calcula a <b>matriz de co-ocorrência</b>. Isso revela quais termos "andam juntos" (como sal e pimenta), permitindo que a importância de cada variável seja calculada nos modelos de regressão e classificação presentes no final do script.</p>

<p align="justify"><h3>5. Perfil Nutricional e Complexidade</h3></p>

<p align="justify">A análise exploratória através de histogramas revelou que a maior parte do <i>dataset</i> concentra-se em um perfil nutricional moderado. Contudo, a presença marcante de <b>outliers</b> (valores extremos) evidencia a existência de receitas com densidade calórica e teores de sódio atípicos, que se distanciam da média do conjunto. Para quantificar a estrutura dessas receitas, utilizamos a engenharia de atributos para extrair métricas de complexidade: o número de ingredientes (<b>n_ingredients</b>) e o número de passos no preparo (<b>n_steps</b>).</p>

<p align="justify">Diferente do esperado, a análise bivariada demonstrou que a relação entre essa complexidade e os valores nutricionais apresenta uma <b>correlação linear muito fraca</b>. Isso indica que um preparo longo ou com muitos itens não implica necessariamente em um prato mais calórico ou salgado. Essa dispersão acentuada revela que a <b>natureza qualitativa</b> dos ingredientes (ex: usar manteiga vs. vegetais) é muito mais determinante para o valor energético final do que a quantidade bruta de passos ou componentes, validando a necessidade de modelos não-lineares para capturar tais nuances.</p>

</div>

<p align="justify"><h3>6. Importância das Variáveis (Feature Importance)</h3></p>

<p align="justify">Utilizando modelos de florestas aleatórias (Random Forest), quantificamos quais fatores "movem o ponteiro" para <b>prever o teor de sódio das receitas</b> com base em suas outras características nutricionais e de preparo. O objetivo do modelo é entender se a complexidade da receita e outros nutrientes conseguem estimar com precisão a quantidade de sal presente no prato. O resultado revelou uma concentração extrema de influência:</p>

<ul>
<li><p align="justify"><b>Domínio de X7 e X2:</b> Estas duas variáveis somam quase <b>97%</b> da importância do modelo (52,24% e 44,72%, respectivamente). Isso significa que para prever o sódio, o modelo foca quase inteiramente no perfil de calorias e no número de ingredientes, ignorando quase todo o resto.</p></li>
<li><p align="justify"><b>Eficiência do Modelo:</b> As demais variáveis (X1 a X6) apresentaram impacto irrelevante, sugerindo que o modelo pode ser simplificado para focar apenas nos pilares fundamentais, reduzindo ruído e custo computacional.</p></li>
</ul>

<p align="justify"><h3>7. Modelagem de Série Temporal</h3></p>

<p align="justify">A análise temporal foi aplicada com o objetivo de <b>prever o volume de postagens de receitas ao longo do tempo</b>. O intuito é identificar o comportamento histórico de publicação no portal Epicurious para antecipar a quantidade de novos conteúdos em períodos futuros. O modelo alcançou um <b>MAPE de 4,60%</b>, o que indica uma precisão altíssima no ajuste aos dados históricos de postagem. No entanto, a previsão para os próximos 4 períodos resultou em um valor constante de <b>52.054</b>. Este "platô" sugere que o modelo capturou a média estável da série de publicações, mas requer a inclusão de componentes de sazonalidade mais complexos para prever oscilações dinâmicas de novos cadastros de receitas.</p>

<p align="justify"><h3>8. Conclusão</h3></p>

<p align="justify">O projeto conclui que a estrutura técnica de uma receita é um preditor mais forte do que atributos superficiais. A alta performance do erro percentual (MAPE) aliada à clara distinção de importância das variáveis fornece uma base sólida para a criação de sistemas de recomendação nutricional e ferramentas de previsão de demanda gastronômica.</p>

</div>
