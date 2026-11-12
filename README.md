<h1 align="center">Opa! Seja bem vindo ao meu git!</h1>

<p align="center">
  Estudante de Estatística na <a href="https://www.unb.br">Universidade de Brasília (UnB)</a> · ciência de dados para a vigilância em saúde no <a href="https://cenarios.unb.br">Programa Cenários (NESP/UnB)</a>
</p>

<p align="center">
  <a href="https://www.r-project.org"><img src="https://img.shields.io/badge/R-276DC3?style=for-the-badge&logo=r&logoColor=white" alt="R"></a>
  <a href="https://www.python.org"><img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python"></a>
  <a href="https://duckdb.org/docs/sql/introduction"><img src="https://img.shields.io/badge/SQL-FFF000?style=for-the-badge&logo=duckdb&logoColor=black" alt="SQL"></a>
  <a href="https://developer.mozilla.org/docs/Web/JavaScript"><img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript"></a>
  <a href="https://developer.mozilla.org/docs/Web/CSS"><img src="https://img.shields.io/badge/CSS-663399?style=for-the-badge&logo=css&logoColor=white" alt="CSS"></a>
  <a href="https://developer.mozilla.org/docs/Web/HTML"><img src="https://img.shields.io/badge/HTML-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML"></a>
</p>

---

## Sobre mim

Trabalho na fronteira entre a estatística, a epidemiologia e o desenvolvimento de software. A maior parte do meu código é **R**: pipelines que limpam e cruzam bases do SUS e painéis Shiny que entregam o resultado a gestores e equipes de vigilância. Para **redes neurais** e para os projetos que rodam ao vivo, uso **Python**, com **SQL** (DuckDB e PostgreSQL) no meio dos dois.

Gosto de projetos que terminam em algo utilizável: uma ferramenta que roda no navegador, uma documentação que explica cada decisão e um teste com dados reais.

---

## 🦠 Epidemiologia e vigilância em saúde · [Dev-Cenarios](https://github.com/Dev-Cenarios)

No Programa Cenários (eixos de **tuberculose** e **hanseníase**), desenvolvo a cadeia que transforma notificações em informação territorial: **georreferenciar** os casos, **vincular** as bases e **mostrar** os indicadores em painéis.

### 📍 Georreferenciamento: [Sistema SINAN × CNEFE](https://github.com/Dev-Cenarios/Sistema_de_Georreferenciamento_Geocenarios)

Associa cada caso notificado no SINAN a um endereço da base [CNEFE/IBGE](https://www.ibge.gov.br/estatisticas/sociais/populacao/38734-cadastro-nacional-de-enderecos-para-fins-estatisticos.html) e ao seu **setor censitário**.

- Para o **Distrito Federal**, um pipeline determinístico de **18 fases** em SQL, ajustado ao volume e ao formato dos endereços do DF.
- Para outras **21 UFs**, *record linkage* probabilístico de endereços (5 fases, 21 subfases, similaridade Jaro-Winkler) ou geocodificação com [`geocodebr`](https://github.com/ipeaGIT/geocodebr), do Ipea.
- Cada caso sai com a **precisão posicional** (número exato, aproximado, início do logradouro, centroide), além de mapas de pontos e de densidade (KDE) e de uma tela de triagem manual para os matches de baixa similaridade.
- Usado na página do programa: [cenarios.unb.br/sistema-de-geocodificacao-de-dados-de-saude](https://cenarios.unb.br/sistema-de-geocodificacao-de-dados-de-saude/).

### 🔗 Linkage: [Sistema SINAN × SIM](https://github.com/Dev-Cenarios/Sistema_de_Linkage_SINANxSIM)

Vincula notificações (SINAN) a óbitos (SIM) para corrigir o desfecho dos casos e recuperar os **óbitos por TB sem notificação**.

- Pré-processamento de nomes, datas e sexo, e deduplicação da base SINAN por Jaro-Winkler.
- **15 passagens sucessivas** num [DuckDB](https://duckdb.org) em memória, das chaves mais restritivas (nome + nome da mãe + nascimento) às mais flexíveis.
- Os pares da **"zona cinza"** vão para revisão manual, em que o usuário marca MATCH ou NÃO MATCH.

### 🗺️ Painéis de vigilância: SINAN Territórios

Painéis R/Shiny empacotados com [`golem`](https://thinkr-open.github.io/golem/), um por território: [tuberculose em Recife-PE](https://github.com/Dev-Cenarios/SINAN_Territorios_Recife_PE_TB), [hanseníase em Pernambuco](https://github.com/Dev-Cenarios/SINAN_Territorios_Pernambuco_Hansen) e versões para outras capitais.

- **Indicadores por território** (bairro, distrito administrativo, região de saúde): incidência e mortalidade por 100 mil habitantes, letalidade e proporções de cura e de abandono.
- **Mapas** em [Leaflet](https://rstudio.github.io/leaflet/) com [`sf`](https://r-spatial.github.io/sf/): balões por território no zoom da cidade e pontos de cada notificação ao aproximar, com camadas de UBS e áreas de abrangência.
- Usam a saída do georreferenciamento e do linkage acima (desfecho corrigido pelo SIM, casos sem endereço de residência tratados à parte) e leem os dados de **DuckDB/PostgreSQL**.

### 🤖 Classificação sindrômica de prontuários com LLM *(repositório privado, em desenvolvimento)*

Triagem de **casos suspeitos de síndrome febril** a partir do texto livre de prontuários (queixa e anamnese), dentro do projeto SESH · Cenários+. Parte de [camposvieira/anamnese_classifier](https://github.com/camposvieira/anamnese_classifier).

- Um **LLM local** via [Ollama](https://ollama.com) (`qwen2.5:7b`, comparado com o `llama3.1:8b`) faz duas tarefas: extrai os sintomas de um dicionário, com qualificadores e **negação**, e classifica o atendimento em 5 síndromes febris (exantemática, respiratória, gastrointestinal, hemorrágica ou inconclusiva).
- Os **agravos compatíveis** (dengue, sarampo, leptospirose…) vêm das definições oficiais de caso do *Guia de Vigilância em Saúde* (MS, 2024), mantidas num CSV que alimenta o prompt.
- A resposta segue um **JSON schema** com limites de tamanho, para evitar que modelos pequenos entrem em loop. Os trechos citados são conferidos no texto original.
- Os resultados ficam gravados em DuckDB. O painel mostra os padrões semânticos (rede de coocorrência de sintomas, fluxo síndrome → agravo) e a concordância entre o LLM e um extrator por regex.

---

## 🏎️ Redes neurais e Fórmula 1

### [Neural Stints](https://github.com/schneiderweisse/Neural_Stints) · [demo](https://schneiderweisse.github.io/Neural_Stints/)

Previsão do **ritmo de corrida e da degradação dos pneus** na F1 com redes recorrentes. É o projeto final da disciplina Redes Neurais 2 (Estatística/UnB).

- **Arquitetura:** GRU *encoder–decoder* em [PyTorch](https://pytorch.org). O encoder lê as voltas já disputadas; o decoder recebe o que já se sabe das voltas futuras (idade do pneu, composto, voltas restantes). Variáveis categóricas entram por *embeddings*, e a rede é condicionada pela era do regulamento (2022–2026), pela especificação do pneu e pelo composto (C1 a C6).
- **Saída probabilística:** quantis p10, p50 e p90, com a ordem garantida por construção (*softplus*), para comparar cenários como "parar agora" ou "ficar mais *k* voltas".
- **Avaliação:** validação por corrida em ordem cronológica, contra baselines (ingênuo, modelo linear misto, LightGBM); bateria de hiperparâmetros e *ensemble* de 5 sementes.
- **Teste ao vivo:** um serviço em Python lê o live timing da F1 (via [f1-dash](https://github.com/slowlydev/f1-dash)) e gera previsões a cada volta. A primeira corrida foi o [GP de São Paulo de 2026](https://github.com/schneiderweisse/Neural_Stints/blob/main/docs/race_reports/2026_sao_paulo.md).
- **Painel** em [Streamlit](https://streamlit.io), com uma demo que roda no navegador via [stlite](https://github.com/whitphx/stlite) no GitHub Pages.

### Outros projetos com dados esportivos

- **[F1 Data Pit Wall](https://github.com/schneiderweisse/Shiny-F1)** · [demo](https://schneiderweisse.github.io/Shiny-F1/): painel Shiny com sete fontes abertas de dados da F1 (F1DB, Jolpica, OpenF1, TracingInsights…), com o visual das transmissões dos anos 2000. Roda inteiro no navegador com [Shinylive](https://posit-dev.github.io/r-shinylive/) (R em WebAssembly) e é reconstruído toda semana por GitHub Actions.
- **[UFC 8-BIT](https://github.com/schneiderweisse/UFC-data)** · [demo](https://schneiderweisse.github.io/UFC-data/): painel dos lutadores ranqueados no estilo de fliperama 8-bit, com dados coletados da API pública da ESPN e uma versão estática para o GitHub Pages.

---

## 📦 Pacotes que mais uso

### R

<p>
  <a href="https://shiny.posit.co"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/shiny.png" height="90" alt="shiny" title="shiny"></a>
  <a href="https://thinkr-open.github.io/golem/"><img src="https://raw.githubusercontent.com/ThinkR-open/golem/master/inst/rstudio/templates/project/golem.png" height="90" alt="golem" title="golem"></a>
  <a href="https://rstudio.github.io/bslib/"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/bslib.png" height="90" alt="bslib" title="bslib"></a>
  <a href="https://rinterface.github.io/bs4Dash/"><img src="https://raw.githubusercontent.com/RinteRface/bs4Dash/master/man/figures/logo.png" height="90" alt="bs4Dash" title="bs4Dash"></a>
  <a href="https://deanattali.com/shinyjs/"><img src="https://raw.githubusercontent.com/daattali/shinyjs/master/inst/img/hex.png" height="90" alt="shinyjs" title="shinyjs"></a>
  <a href="https://rstudio.github.io/htmltools/"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/htmltools.png" height="90" alt="htmltools" title="htmltools"></a>
  <a href="https://dplyr.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/dplyr.png" height="90" alt="dplyr" title="dplyr"></a>
  <a href="https://tidyr.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/tidyr.png" height="90" alt="tidyr" title="tidyr"></a>
  <a href="https://purrr.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/purrr.png" height="90" alt="purrr" title="purrr"></a>
  <a href="https://stringr.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/stringr.png" height="90" alt="stringr" title="stringr"></a>
  <a href="https://lubridate.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/lubridate.png" height="90" alt="lubridate" title="lubridate"></a>
  <a href="https://readr.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/readr.png" height="90" alt="readr" title="readr"></a>
  <a href="https://tibble.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/tibble.png" height="90" alt="tibble" title="tibble"></a>
  <a href="https://glue.tidyverse.org"><img src="https://raw.githubusercontent.com/tidyverse/glue/main/man/figures/logo.png" height="90" alt="glue" title="glue"></a>
  <a href="https://ggplot2.tidyverse.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/ggplot2.png" height="90" alt="ggplot2" title="ggplot2"></a>
  <a href="https://scales.r-lib.org"><img src="https://raw.githubusercontent.com/r-lib/scales/main/man/figures/logo.png" height="90" alt="scales" title="scales"></a>
  <a href="https://plotly.com/r/"><img src="https://raw.githubusercontent.com/plotly/plotly.R/master/man/figures/plotly.png" height="90" alt="plotly" title="plotly"></a>
  <a href="https://r-spatial.github.io/sf/"><img src="https://raw.githubusercontent.com/r-spatial/sf/main/man/figures/logo.png" height="90" alt="sf" title="sf"></a>
  <a href="https://ipeagit.github.io/geocodebr/"><img src="https://raw.githubusercontent.com/ipeaGIT/geocodebr/main/r-package/man/figures/logo.png" height="90" alt="geocodebr" title="geocodebr"></a>
  <a href="https://r.duckdb.org"><img src="https://raw.githubusercontent.com/duckdb/duckdb-r/main/man/figures/logo.svg" height="90" alt="duckdb" title="duckdb"></a>
  <a href="https://rdatatable.gitlab.io/data.table/"><img src="https://raw.githubusercontent.com/Rdatatable/data.table/master/.graphics/logo.png" height="90" alt="data.table" title="data.table"></a>
  <a href="https://httr2.r-lib.org"><img src="https://raw.githubusercontent.com/rstudio/hex-stickers/main/PNG/httr2.png" height="90" alt="httr2" title="httr2"></a>
</p>

<p>
  <a href="https://rstudio.github.io/leaflet/"><img src="https://img.shields.io/badge/leaflet-199900?style=flat-square&logo=leaflet&logoColor=white" alt="leaflet"></a>
  <a href="https://glin.github.io/reactable/"><img src="https://img.shields.io/badge/reactable-555?style=flat-square" alt="reactable"></a>
  <a href="https://rstudio.github.io/DT/"><img src="https://img.shields.io/badge/DT-555?style=flat-square" alt="DT"></a>
  <a href="https://dreamrs.github.io/shinyWidgets/"><img src="https://img.shields.io/badge/shinyWidgets-555?style=flat-square" alt="shinyWidgets"></a>
  <a href="https://datastorm-open.github.io/visNetwork/"><img src="https://img.shields.io/badge/visNetwork-555?style=flat-square" alt="visNetwork"></a>
  <a href="https://dbi.r-dbi.org"><img src="https://img.shields.io/badge/DBI-555?style=flat-square" alt="DBI"></a>
  <a href="https://rpostgres.r-dbi.org"><img src="https://img.shields.io/badge/RPostgres-4169E1?style=flat-square&logo=postgresql&logoColor=white" alt="RPostgres"></a>
  <a href="https://github.com/ipeaGIT/enderecobr"><img src="https://img.shields.io/badge/enderecobr-555?style=flat-square" alt="enderecobr"></a>
  <a href="https://posit-dev.github.io/r-shinylive/"><img src="https://img.shields.io/badge/shinylive-447099?style=flat-square&logo=posit&logoColor=white" alt="shinylive"></a>
</p>

### Python

<p>
  <a href="https://pytorch.org"><img src="https://img.shields.io/badge/PyTorch-EE4C2C?style=for-the-badge&logo=pytorch&logoColor=white" alt="PyTorch"></a>
  <a href="https://pandas.pydata.org"><img src="https://img.shields.io/badge/pandas-150458?style=for-the-badge&logo=pandas&logoColor=white" alt="pandas"></a>
  <a href="https://numpy.org"><img src="https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white" alt="NumPy"></a>
  <a href="https://arrow.apache.org/docs/python/"><img src="https://img.shields.io/badge/PyArrow%20%2F%20Parquet-50ABF1?style=for-the-badge&logo=apacheparquet&logoColor=white" alt="PyArrow"></a>
  <a href="https://plotly.com/python/"><img src="https://img.shields.io/badge/Plotly-3F4F75?style=for-the-badge&logo=plotly&logoColor=white" alt="Plotly"></a>
  <a href="https://streamlit.io"><img src="https://img.shields.io/badge/Streamlit-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white" alt="Streamlit"></a>
  <a href="https://dash.plotly.com"><img src="https://img.shields.io/badge/Dash-008DE4?style=for-the-badge&logo=plotly&logoColor=white" alt="Dash"></a>
  <a href="https://www.crummy.com/software/BeautifulSoup/"><img src="https://img.shields.io/badge/BeautifulSoup-555?style=for-the-badge" alt="BeautifulSoup"></a>
</p>

### Dados, IA e infraestrutura

<p>
  <a href="https://duckdb.org"><img src="https://img.shields.io/badge/DuckDB-FFF000?style=for-the-badge&logo=duckdb&logoColor=black" alt="DuckDB"></a>
  <a href="https://www.postgresql.org"><img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" alt="PostgreSQL"></a>
  <a href="https://ollama.com"><img src="https://img.shields.io/badge/Ollama-000000?style=for-the-badge&logo=ollama&logoColor=white" alt="Ollama"></a>
  <a href="https://posit.co/products/open-source/shiny-server/"><img src="https://img.shields.io/badge/Shiny%20Server-447099?style=for-the-badge&logo=posit&logoColor=white" alt="Shiny Server"></a>
  <a href="https://github.com/features/actions"><img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white" alt="GitHub Actions"></a>
  <a href="https://pages.github.com"><img src="https://img.shields.io/badge/GitHub%20Pages-222222?style=for-the-badge&logo=githubpages&logoColor=white" alt="GitHub Pages"></a>
  <a href="https://git-scm.com"><img src="https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white" alt="Git"></a>
</p>

---

<p align="center">
  <a href="https://cenarios.unb.br"><img src="https://img.shields.io/badge/Programa%20Cenários-NESP%2FUnB-0B5394?style=flat-square" alt="Programa Cenários"></a>
  <a href="https://github.com/Dev-Cenarios"><img src="https://img.shields.io/badge/org-Dev--Cenarios-181717?style=flat-square&logo=github" alt="Dev-Cenarios"></a>
</p>
