# ibge-cidades
Dados dos municípios no Portal Cidades@ recuperados da API do IBGE

## Fonte
Os dados em questão são disponibilizados pelo IBGE no Portal Cidades@ (https://cidades.ibge.gov.br/brasil/panorama). Os dados foram coletados por chamadas à API em 21-22 de maio de 2025.

## Dados no dataset
O dataset contém dados de 5.565 localidades no Brasil (Cidades, Povoados, Vilarejos, etc.) coletados pelo IBGE, organizados por ano.
No total, são 40 indicadores que têm seus valores disponibilizados, cada um representado por um código específico.
| Código da API | Rótulo atribuído |
| -----: | :----- |
| 78192 | anos_finais_ens_fund |
| 78187 | anos_iniciais_ens_fund |
| 60029 | arborizacao_via_publica |
| 29167 | area_unidade_territorial |
| 95335 | area_urbanizada_km2 |
| 77861 | bioma |
| 29169 | cod_municipio |
| 96386 | densidade_demografica |
| 29749 | despesas_brutas_empenhadas |
| 5929 | docentes_ens_fund |
| 5934 | docentes_ens_med |
| 60030 | esgotamento_sanitario_adequado |
| 28242 | estabelecimento_saude_sus |
| 5950 | estabelecimentos_ens_fund |
| 5955 | estabelecimentos_ens_med |
| 87529 | hierarquia_urbana |
| 30255 | idh |
| 60032 | internacoes_diarreia_sus_por_cem_mil |
| 5908 | matriculas_ens_fund |
| 5913 | matriculas_ens_med |
| 91249 | mesorregiao |
| 91251 | microrregiao |
| 30279 | mortalidade_infantil |
| 60037 | percent_populacao_renda_per_capita_mais_meio_salario |
| 143514 | pessoal_ocupado |
| 47001 | pib_per_capita |
| 29171 | populacao_estimada |
| 93371 | populacao_exposta_risco |
| 60036 | populacao_ocupada |
| 96385 | populacao_ultimo_censo |
| 29170 | prefeito |
| 28141 | receitas_brutas_realizadas |
| 91247 | regiao_imediata |
| 87530 | regiao_influencia |
| 91245 | regiao_intermediaria |
| 143558 | rendimento_medio_sal_min |
| 82270 | sistema_costeiro_marinho |
| 60045 | taxa_escolarizacao_6-14 |
| 60048 | transferencias_correntes |
| 60031 | urbanizacao_vias_publicas |

Foram adicionados os dados de georreferenciamento de cada município à lista de atributos.

## Formatos dos dados e Arquivos
Neste repositório há algumas representações diferentes dos dados obtidos, as quais descrevemos a seguir.

### Dados Brutos
A arquivo compactado (para otimizar armazenamento) `dados.json.zip` contém um objeto em que

 - cada chave corresponde ao código atribuído pelo IBGE a uma localidade,
 - cada valor corresponde aos dados brutos obtidos de uma chamada à API do IBGE com a seguinte url `https://servicodados.ibge.gov.br/api/v1/pesquisas/indicadores/29169|29170|96385|29171|96386|143558|143514|60036|60037|60045|78187|78192|5908|5913|5929|5934|5950|5955|47001|30255|28141|60048|29749|30279|60032|28242|95335|60030|60029|60031|93371|77861|82270|29167|87529|87530|91245|91247|91249|91251/resultados/<codigo_da_localidade>` (em que `<codigo_da_localidade>` é substituído pelo código da localidade em questão.

Os dados brutos têm como chave os códigos dos indicadores (ver tabela acima), em vez de uma descrição textual. Os valores de cada indicador estão em formato de string.

Para otimização de espaço, o arquivo foi zipado (`dados.json.zip`)

### Dados Formatados
O arquivo `dados_formatados.json` contém os dados brutos após um tratamento (convertendo os valores dos indicadores para seus respectivos tipos e representando dados faltantes como `null`). A estrutura dos valores de cada indicador também foi simplificada, de forma que são representados como um objeto em que a chave é o ano e o valor é o valor daquele indicador no ano indicado. Adicionalmente, foram acrescentados os dados de cada localidade (nome, categoria, coordenadas de georreferenciamento, etc.) disponibilizadas pelo IBGE em https://geoftp.ibge.gov.br/organizacao_do_territorio/estrutura_territorial/localidades/, descritos no arquivo `municipios.json`.

### Dados "Achatados"
O arquivo `dados_features.json` contêm uma representação dos dados em que os valores de cada indicador, em vez de serem representados com aninhamento de objetos, por ano, for extraído para um atributo específico no objeto.
Como cada indicador vindo da API é uma coleção de valor-por-ano, para facilitar a utilização dos diversos valores em um `DataFrame`, por exemplo, representamos cada combinação valor-ano em uma coluna separada.
Por exemplo, o valor abaixo:
```json
"docentes_ens_medio": {
  "2020": 50,
  "2021": 45,
  "2022": 30
}
```
foi convertido em:
```json
"docentes_ens_medio_2020": 50, 
"docentes_ens_medio_2021": 45, 
"docentes_ens_medio_2022": 30,
```

Essa mesma representação se encontra nos arquivos `dados_lista.csv` e `dados_lista.json` (sendo este último uma lista simples, em que cada linha corresponde aos dados de uma localidade).

### Dados Recentes
O arquivo `dados_recentes.json` contém um recorte dos dados em `dados_formatados.json`, de forma que cada indicador contém somente o dados mais recente dentre os recuperados da API. Por exemplo, o indicador abaixo:

```json
"docentes_ens_medio": {
  "2020": 50,
  "2021": 45,
  "2022": 30
}
```
foi convertido em:

```json
"docentes_ens_medio": 30
```

