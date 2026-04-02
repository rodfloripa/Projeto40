<h1>Relatório de Análise e Modelagem de Dados: Epicurious</h1>
<h2>Predição Nutricional e Séries Temporais de Receitas</h2>

<p align="justify"><h3>1. Visão Geral e Origem dos Dados</h3></p>

<p align="justify">Este projeto utiliza o <b>Epicurious Recipes Dataset</b>, um conjunto de dados amplamente reconhecido na comunidade de Ciência de Dados e hospedado no <b>Kaggle</b>. Os dados brutos foram extraídos via <i>web scraping</i> do portal gastronômico Epicurious.com. O objetivo central desta análise é compreender a composição nutricional de mais de 20 mil receitas e aplicar algoritmos de <b>Machine Learning</b> para prever métricas de avaliação e tendências temporais.</p>

<p align="justify"><h3>2. Características do Dataset</h3></p>

<p align="justify">O conjunto de dados apresenta uma estrutura robusta, composta por 20.130 registros e 11 colunas. Durante a exploração inicial, identificamos que aproximadamente 20% das receitas possuem valores ausentes em colunas críticas como <b>calorias, gorduras e sódio</b>. Essa característica exigiu uma etapa rigorosa de limpeza e tratamento de dados para evitar vieses nas predições dos modelos.</p>


<p align="justify"><h3>3. Perfil Nutricional e Complexidade</h3></p>

<p align="justify">A análise exploratória através de histogramas revelou que a maior parte do <i>dataset</i> concentra-se em um perfil nutricional moderado. Contudo, a presença marcante de <b>outliers</b> (valores extremos) evidencia a existência de receitas com densidade calórica e teores de sódio atípicos, que se distanciam da média do conjunto. Para quantificar a estrutura dessas receitas, utilizamos a engenharia de atributos para extrair métricas de complexidade: o número de ingredientes (<b>n_ingredients</b>) e o número de passos no preparo (<b>n_steps</b>).</p>

<p align="justify">Diferente do esperado, a análise bivariada demonstrou que a relação entre essa complexidade e os valores nutricionais apresenta uma <b>correlação linear muito fraca</b>. Isso indica que um preparo longo ou com muitos itens não implica necessariamente em um prato mais calórico ou salgado. Essa dispersão acentuada revela que a <b>natureza qualitativa</b> dos ingredientes (ex: usar manteiga vs. vegetais) é muito mais determinante para o valor energético final do que a quantidade bruta de passos ou componentes, validando a necessidade de modelos não-lineares para capturar tais nuances.</p>

</div>

<p align="justify"><h3>4. Importância das Variáveis (Feature Importance)</h3></p>

<p align="justify">Utilizando modelos de florestas aleatórias (Random Forest), quantificamos quais fatores "movem o ponteiro" para <b>prever o teor de sódio das receitas</b> com base em suas outras características nutricionais e de preparo. O objetivo do modelo é entender se a complexidade da receita e outros nutrientes conseguem estimar com precisão a quantidade de sal presente no prato. O resultado revelou uma concentração extrema de influência:</p>

<ul>
<li><p align="justify"><b>Domínio de X7 e X2:</b> Estas duas variáveis somam quase <b>97%</b> da importância do modelo (52,24% e 44,72%, respectivamente). Isso significa que para prever o sódio, o modelo foca quase inteiramente no perfil de calorias e no número de ingredientes, ignorando quase todo o resto.</p></li>
<li><p align="justify"><b>Eficiência do Modelo:</b> As demais variáveis (X1 a X6) apresentaram impacto irrelevante, sugerindo que o modelo pode ser simplificado para focar apenas nos pilares fundamentais, reduzindo ruído e custo computacional.</p></li>
</ul>

<p align="justify"><h3>5. Modelagem de Série Temporal</h3></p>

<p align="justify">A análise temporal foi aplicada com o objetivo de <b>prever o volume de postagens de receitas ao longo do tempo</b>. O intuito é identificar o comportamento histórico de publicação no portal Epicurious para antecipar a quantidade de novos conteúdos em períodos futuros. O modelo alcançou um <b>MAPE de 4,60%</b>, o que indica uma precisão altíssima no ajuste aos dados históricos de postagem. No entanto, a previsão para os próximos 4 períodos resultou em um valor constante de <b>52.054</b>. Este "platô" sugere que o modelo capturou a média estável da série de publicações, mas requer a inclusão de componentes de sazonalidade mais complexos para prever oscilações dinâmicas de novos cadastros de receitas.</p>

<p align="justify"><h3>6. Conclusão</h3></p>

<p align="justify">O projeto conclui que a estrutura técnica de uma receita é um preditor mais forte do que atributos superficiais. A alta performance do erro percentual (MAPE) aliada à clara distinção de importância das variáveis fornece uma base sólida para a criação de sistemas de recomendação nutricional e ferramentas de previsão de demanda gastronômica.</p>

</div>
