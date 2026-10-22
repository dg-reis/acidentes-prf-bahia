# Dicionário de dados: acidentes PRF (agrupados por pessoa, 2017 em diante)

**Fonte:** Dicionário de variáveis oficial da PRF, "DICIONÁRIO DE VARIÁVEIS – ACIDENTES (DADOS AGREGADOS POR PESSOA. REGISTROS A PARTIR DE JANEIRO DE 2017)". Dados abertos da PRF.

**Granularidade:** uma linha por **pessoa envolvida**. Um acidente pode ocupar várias linhas (mesmo `id`).

## Nível acidente (se repete em todas as linhas do mesmo `id`)

| Coluna | Descrição | Tipo | Uso no projeto |
| --- | --- | --- | --- |
| `id` | Identificador do acidente | Inteiro | Contagem de acidentes (`COUNT(DISTINCT id)`) |
| `data_inversa` | Data da ocorrência (dd/mm/aaaa) | Data | Evolução anual, sazonalidade |
| `horario` | Horário da ocorrência (hh:mm:ss) | Hora | Análise por hora do dia |
| `fase_dia` | Fase do dia (amanhecer, pleno dia etc.) | Texto | Risco por período do dia |
| `uf` | Unidade da Federação | Texto | Filtro da Bahia (`BA`) |
| `br` | Número da BR | Inteiro | Ranking de rodovias |
| `km` | Quilômetro do acidente | Decimal | Trechos críticos |
| `municipio` | Município da ocorrência | Texto | Ranking de municípios |
| `latitude`, `longitude` | Coordenadas do local | Decimal | Mapa no Power BI |
| `causa_acidente` | Causa principal | Texto | Análise de causas |
| `tipo_acidente` | Tipo do acidente (colisão frontal, saída de pista etc.) | Texto | Análise por tipo |
| `classificacao_acidente` | Gravidade: sem vítimas, feridas, fatais, ignorado | Texto | Métrica de gravidade |

## Nível pessoa (varia entre as linhas do mesmo `id`)

| Coluna | Descrição | Tipo | Uso no projeto |
| --- | --- | --- | --- |
| `pesid` | Identificador da pessoa | Inteiro | Contagem de envolvidos |
| `tipo_envolvido` | Condutor, passageiro, pedestre etc. | Texto | Perfil dos envolvidos (opcional) |
| `estado_fisico` | Condição conforme a lesão | Texto | Conferência de óbitos |
| `idade` | Idade do envolvido (`-1` = sem informação) | Inteiro | Perfil (opcional) |
| `sexo` | Sexo (`inválido` = sem informação) | Texto | Perfil (opcional) |
| `mortos` | 1 se a pessoa morreu, 0 caso contrário | Inteiro | **Somar por `id`** = óbitos do acidente |
| `feridos_graves` | 1 se ferido grave | Inteiro | Somar por `id` |
| `feridos_leves` | 1 se ferido leve | Inteiro | Somar por `id` |

**Atenção:** `mortos`, `feridos_graves` e `feridos_leves` são **binários por pessoa**, não totais do acidente.

## Colunas omitidas (fora do escopo)

 `dia_semana` (derivável da data), `sentido_via`, `condicao_metereologica`, `tipo_pista`, `tracado_via`, `uso_solo`, `id_veiculo`, `tipo_veiculo`, `marca`, `ano_fabricacao_veiculo`, `ilesos`, `regional`, `delegacia`, `uop`.