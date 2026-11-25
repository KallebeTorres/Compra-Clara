# 🛒 Compra Clara — Análise Inteligente de Preços Públicos

<p align="center">
  <strong>Dados públicos, análise inteligente e transparência nas compras governamentais.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-blue?logo=python" />
  <img src="https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas" />
  <img src="https://img.shields.io/badge/Big%20Data-PySpark-orange?logo=apachespark" />
  <img src="https://img.shields.io/badge/Data%20Lake-Bronze%20%7C%20Silver%20%7C%20Gold-blueviolet" />
  <img src="https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow" />
</p>

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>📌 Visão Geral</h2>
    </td>
  </tr>
</table>

O **Compra Clara** é um projeto de análise de dados desenvolvido para investigar padrões de preços em compras públicas, utilizando informações disponibilizadas pelo portal Compras.gov.br.

A solução implementa um pipeline de dados capaz de coletar, organizar, limpar e analisar registros de materiais e pesquisas de preços, com foco inicial em componentes de hardware e informática.

Por meio de técnicas de análise estatística e detecção de anomalias, o projeto busca identificar valores que se distanciam significativamente dos padrões observados em contratações comparáveis, contribuindo para a priorização de registros que merecem uma análise mais detalhada.

O Compra Clara funciona como uma ferramenta de **triagem analítica e apoio à decisão**, não como um sistema de comprovação automática de irregularidades.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🎯 Objetivos do Projeto</h2>
    </td>
  </tr>
</table>

- **Monitoramento de preços públicos:** reunir informações de preços praticados em compras governamentais.
- **Benchmarking de preços:** estabelecer referências de mercado a partir de compras comparáveis.
- **Detecção de anomalias:** identificar registros com valores significativamente diferentes dos padrões estatísticos.
- **Análise regional:** comparar preços por Unidade Federativa (UF), considerando referências nacionais quando necessário.
- **Estimativa de economia potencial:** calcular diferenças entre preços observados e benchmarks de referência.
- **Qualidade dos dados:** avaliar registros incompletos, inconsistentes ou duplicados.
- **Transparência:** facilitar a identificação de compras que possam ser priorizadas para revisão humana.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🔎 Problema que o Projeto Busca Resolver</h2>
    </td>
  </tr>
</table>

A análise de compras públicas pode ser dificultada pelo grande volume de registros, pela diversidade de materiais e pelas diferenças entre unidades, especificações e condições de contratação.

Uma comparação direta entre preços nem sempre é suficiente para identificar possíveis discrepâncias, pois produtos aparentemente semelhantes podem apresentar características distintas.

O Compra Clara busca reduzir esse problema por meio de um processo estruturado que:

1. Seleciona materiais de interesse dentro do catálogo oficial.
2. Coleta os registros de preços associados.
3. Padroniza e valida os dados.
4. Constrói referências de preços comparáveis.
5. Identifica valores atípicos.
6. Organiza os resultados para investigação e revisão.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🧠 Metodologia e Pipeline de Dados</h2>
    </td>
  </tr>
</table>

O projeto utiliza uma arquitetura inspirada em Data Lake, organizada em três camadas: **Bronze, Silver e Gold**.

### 🥉 Camada Bronze — Dados Brutos

Responsável por armazenar os dados originais obtidos da API do Compras.gov.br.

Nesta etapa são realizadas:

- Coleta do catálogo de PDMs (Padrões de Descrição de Materiais).
- Consulta dos itens do catálogo CATMAT.
- Coleta das pesquisas de preços.
- Paginação automática das consultas.
- Armazenamento das respostas originais em formato JSON.
- Utilização de cache e checkpoints para reduzir chamadas repetidas e permitir a retomada da coleta.

A Bronze preserva os dados recebidos, servindo como base para as etapas posteriores de processamento.

### 🥈 Camada Silver — Dados Tratados

Responsável pela preparação e padronização dos registros.

Nesta camada são aplicados:

- Normalização de textos e descrições.
- Conversão de tipos numéricos e datas.
- Validação de preços e quantidades.
- Remoção de registros duplicados.
- Seleção de itens ativos do catálogo.
- Criação de chaves de comparabilidade.
- Organização de informações regionais e temporais.
- Geração de relatórios de qualidade dos dados.

Os dados tratados são armazenados em formatos adequados para processamento e análise, como CSV e Parquet.

### 🥇 Camada Gold — Dados Analíticos

A Gold representa a camada final de análise do Compra Clara.

Nesta etapa são consolidados:

- Benchmarks de preços.
- Estatísticas de comparação.
- Indicadores de discrepância.
- Classificação de alertas.
- Estimativas de economia potencial.
- Níveis de confiança para revisão.

A Gold constitui a principal fonte de dados para os relatórios, gráficos e interface interativa do projeto.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>📊 Benchmarking e Comparação de Preços</h2>
    </td>
  </tr>
</table>

O Compra Clara utiliza uma estratégia de benchmarking hierárquico para comparar preços de materiais semelhantes.

A referência considera preferencialmente:

- Código do item CATMAT.
- Unidade de fornecimento comparável.
- Capacidade ou características relevantes, quando disponíveis.
- Unidade Federativa.
- Período de observação.

O benchmark regional é priorizado quando existe uma quantidade suficiente de observações. Caso a amostra regional seja pequena, o sistema utiliza uma referência nacional para o mesmo item e unidade comparável.

A **mediana** é utilizada como referência principal, pois tende a ser menos influenciada por valores extremos do que a média aritmética.

O cálculo do benchmark também utiliza a estratégia *leave-one-out*, removendo a própria compra da amostra de referência. Dessa forma, o registro analisado não influencia diretamente o benchmark utilizado para avaliá-lo.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🚨 Detecção de Anomalias</h2>
    </td>
  </tr>
</table>

A identificação de possíveis discrepâncias utiliza métodos estatísticos robustos, combinando:

- **IQR (Intervalo Interquartil):** identifica valores que se afastam significativamente da distribuição central dos preços.
- **MAD (Desvio Absoluto Mediano):** mede a dispersão dos dados utilizando a mediana como referência.
- **Robust Z-Score:** permite avaliar o afastamento de um preço em relação ao comportamento típico da amostra.

A combinação dessas técnicas busca reduzir a dependência de métodos sensíveis a distribuições assimétricas e valores extremos.

Os registros identificados são classificados para facilitar a análise e a priorização de casos.

> Um preço atípico não significa necessariamente fraude ou superfaturamento. O alerta indica apenas que o registro apresenta características que justificam uma investigação mais aprofundada.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>💰 Estimativa de Economia Potencial</h2>
    </td>
  </tr>
</table>

O projeto calcula uma estimativa de economia potencial a partir da diferença entre o preço observado e o benchmark comparável.

Essa estimativa permite priorizar registros que apresentam diferenças financeiras mais relevantes em relação à referência estatística.

O indicador deve ser interpretado como uma estimativa analítica, e não como um valor de economia efetivamente realizado ou garantido.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>📈 Dashboard e Visualizações</h2>
    </td>
  </tr>
</table>

O Compra Clara possui uma interface interativa desenvolvida com `ipywidgets`, integrada ao ambiente Jupyter Notebook.

A interface permite explorar os resultados da camada Gold por meio de:

- Pesquisa de materiais por descrição ou código.
- Filtro por classificação de alerta.
- Filtro por Unidade Federativa.
- Exibição exclusiva de registros com alertas.
- Ordenação por economia potencial estimada.
- Indicadores consolidados.
- Visualização dos registros prioritários para revisão.

O projeto também gera gráficos para auxiliar a interpretação dos resultados, incluindo:

- Ranking dos registros com maiores economias potenciais estimadas.
- Distribuição dos registros por classificação de alerta.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🛠️ Tecnologias Utilizadas</h2>
    </td>
  </tr>
</table>

| Tecnologia | Aplicação |
|---|---|
| Python 3 | Linguagem principal |
| Requests | Comunicação com a API |
| Pandas | Tratamento e análise de dados |
| NumPy | Operações numéricas |
| Matplotlib | Gráficos e visualizações |
| PyArrow | Armazenamento em Parquet |
| OpenPyXL | Geração de relatórios Excel |
| PySpark | Benchmark distribuído complementar |
| IPyWidgets | Interface interativa |
| Google Colab / Jupyter | Ambiente de execução |

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🌐 Fontes de Dados</h2>
    </td>
  </tr>
</table>

Os dados são obtidos por meio da API oficial de dados abertos do Compras.gov.br.

<p align="center">
  <a href="https://dadosabertos.compras.gov.br/swagger-ui/index.html">
    <img src="https://img.shields.io/badge/Compras.gov.br-API%20de%20Dados%20Abertos-blue?style=for-the-badge" />
  </a>
</p>

O projeto utiliza os módulos relacionados ao catálogo de materiais e à pesquisa de preços:

- Consulta de PDMs (Padrões de Descrição de Materiais).
- Consulta de itens CATMAT.
- Consulta de pesquisas de preços de materiais.

Os dados analisados são provenientes de registros públicos disponibilizados pela plataforma.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>📂 Estrutura do Projeto</h2>
    </td>
  </tr>
</table>

A estrutura de armazenamento utilizada pelo pipeline é organizada da seguinte forma:

```text
Compra_Clara/
│
├── dados/
│   ├── bronze/
│   │   ├── pdms_*.json
│   │   ├── itens_catmat_*.json
│   │   └── precos_*.json
│   │
│   ├── silver/
│   │   ├── itens_silver_latest.csv
│   │   ├── itens_silver_latest.parquet
│   │   ├── precos_silver_latest.csv
│   │   └── precos_silver_latest.parquet
│   │
│   └── gold/
│       ├── analise_gold_latest.csv
│       └── analise_gold_latest.parquet
│
├── relatorios/
│   ├── Compra_Clara_Relatorio_*.xlsx
│   ├── top_economia_potencial.png
│   └── distribuicao_alertas.png
│
├── logs/
│   ├── erros_itens_*.csv
│   └── erros_precos_*.csv
│
└── Compra_Clara.ipynb
```

*Observação: os nomes dos arquivos podem variar conforme a execução e o identificador de cada processamento.*

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>⚙️ Como Executar</h2>
    </td>
  </tr>
</table>

### 1. Ambiente

O projeto pode ser executado no Google Colab ou em um ambiente Jupyter Notebook compatível com Python.

### 2. Instalação das dependências

Execute a célula inicial do notebook:

```python
!pip -q install requests pandas numpy matplotlib openpyxl pyarrow pyspark
```

### 3. Configuração

Na seção de configuração central, é possível ajustar parâmetros como:

```python
TAMANHO_PAGINA = 500
TIMEOUT = 60
PAUSA_ENTRE_REQUISICOES = 0.15
REUTILIZAR_CACHE = True
JANELA_DIAS = 365
MIN_AMOSTRA_BENCHMARK = 5
```

Para testes rápidos, também é possível limitar a quantidade de itens processados:

```python
MAX_ITENS_PRECOS = 20
```

Para processar todos os itens selecionados:

```python
MAX_ITENS_PRECOS = None
```

### 4. Execução do Pipeline

Execute as células do notebook na ordem apresentada.

O pipeline realizará a coleta dos dados, o tratamento, a construção dos benchmarks, a detecção de anomalias e a geração dos relatórios.

### 5. Visualização

Após a execução das etapas de processamento, a interface interativa poderá ser utilizada para pesquisar materiais e analisar os registros classificados.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>📑 Resultados Esperados</h2>
    </td>
  </tr>
</table>

A camada Gold permite responder a perguntas como:

- Qual foi o preço registrado para determinado material?
- Qual é o benchmark nacional ou regional utilizado?
- Quantas observações sustentam a referência?
- O preço apresenta uma discrepância estatística?
- Qual é a diferença percentual em relação ao benchmark?
- Qual é a economia potencial estimada?
- Qual é o nível de confiança do alerta?
- Quais registros devem ser priorizados para revisão?

O resultado é uma base analítica estruturada que facilita a exploração dos preços públicos e a identificação de padrões relevantes.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>⚡ Big Data com PySpark</h2>
    </td>
  </tr>
</table>

O projeto inclui um módulo complementar desenvolvido com PySpark para demonstrar o processamento distribuído de dados.

Esse módulo lê a camada Silver em formato Parquet e reproduz o cálculo de benchmarks por grupos comparáveis, utilizando agregações distribuídas e medianas aproximadas.

A implementação permite explorar conceitos de Big Data, como:

- Processamento distribuído.
- Agrupamento de grandes volumes de registros.
- Agregações estatísticas.
- Leitura de dados em formato colunar.
- Comparação entre processamento Pandas e Spark.

O pipeline principal utiliza Pandas, enquanto o bloco PySpark funciona como uma demonstração complementar de processamento distribuído.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>⚠️ Limitações e Considerações</h2>
    </td>
  </tr>
</table>

O Compra Clara é um projeto de análise exploratória e apoio à decisão.

Algumas limitações importantes devem ser consideradas:

- A qualidade dos resultados depende da qualidade e disponibilidade dos dados públicos.
- A comparação exige atenção às especificações e unidades dos materiais.
- Diferenças de marca, capacidade, quantidade, localização e condições de contratação podem explicar variações de preço.
- A quantidade de observações disponíveis pode afetar a confiabilidade dos benchmarks.
- A economia potencial é uma estimativa estatística.
- A identificação de outliers não comprova irregularidade, fraude ou superfaturamento.

Os alertas devem ser interpretados como **indícios para revisão humana**, e não como conclusões definitivas.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>🚀 Próximas Evoluções</h2>
    </td>
  </tr>
</table>

Possíveis melhorias para versões futuras:

- Desenvolvimento de uma aplicação web independente do notebook.
- Integração com mapas e visualizações geográficas.
- Expansão do catálogo para outras categorias de materiais.
- Automatização periódica da atualização dos dados.
- Comparação entre diferentes períodos de contratação.
- Implementação de modelos adicionais de detecção de anomalias.
- Criação de mecanismos de acompanhamento histórico dos alertas.
- Evolução da arquitetura para processamento distribuído em maior escala.

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>👥 Sobre o Projeto</h2>
    </td>
  </tr>
</table>

O **Compra Clara** foi desenvolvido como um projeto de análise de dados, engenharia de dados e aplicação de métodos estatísticos ao contexto das compras públicas.

A iniciativa busca demonstrar como dados governamentais abertos podem ser transformados em informações estruturadas e indicadores úteis para a transparência, a análise de preços e o apoio à tomada de decisão.

<p align="center">
  <strong>Compra Clara — Dados públicos, análise inteligente e transparência nas compras governamentais.</strong>
</p>

---

<table>
  <tr>
    <td bgcolor="#161B22">
      <h2>👥 Integrantes do Grupo</h2>
    </td>
  </tr>
</table>

- Felipe
- Gabriel Paz
- Gabriel Luiz
- Kallebe Torres

---
