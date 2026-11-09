# Decisões do Projeto

## Decisão 1: dados agrupados por pessoa

A decisão é manter os arquivos `.csv` com dados agrupados **por pessoa**, em vez dos arquivos agrupados por ocorrência.

A PRF disponibiliza os dois formatos:

| AGRUPAMENTO POR PESSOA | AGRUPAMENTO POR OCORRÊNCIA |
| :---: | :---: |
| Contêm informações sobre a localização, características dos acidentes, identificadores dos acidentes e das pessoas, características individuais (sexo e idade) e dos veículos envolvidos. | Contêm informações sobre a localização, características dos acidentes e identificadores dos acidentes. |

A escolha é manter os arquivos agrupados por pessoa, pois permitem uma possível análise posterior do perfil dos envolvidos nos acidentes, além das análises gerais de ocorrências.

**Comparação entre os formatos:** a quantidade de acidentes de 2025 foi comparada no Excel, utilizando a fórmula `=CONT.VALORES(ÚNICO(A2:A194630))`. Ambos os formatos apresentaram **72.529 acidentes**. No notebook, a seção *"Quantas Linhas e quantos Acidentes por ano?"* confirmou os mesmos **72.529 IDs únicos** no arquivo agrupado por pessoa.

Como uma linha desse arquivo não representa necessariamente um acidente único, a contagem de acidentes será realizada por meio dos IDs distintos (`id`).

**Exemplo:** um mesmo `id` pode aparecer em quatro linhas, representando quatro registros associados ao mesmo acidente, geralmente correspondentes às pessoas envolvidas.

**Custo da decisão:** foram identificadas **109.623 linhas (aproximadamente 8,8% da base)** sem informações individuais nas colunas `tipo_envolvido`, `estado_fisico` e `sexo`. Portanto, uma eventual análise do perfil dos envolvidos terá cobertura parcial.

## Decisão 2: leitura dos arquivos CSV

**Parâmetros de leitura:**

- Separador: `sep=';'`
- Separador decimal: `decimal=','`
- Codificação: `encoding='latin-1'`

**Evidências:**

- A tentativa de leitura com a codificação padrão UTF-8 apresentou erro em 2025 (`byte 0xe1`).
- Os sete arquivos, de 2019 a 2025, foram lidos corretamente com os parâmetros definidos.
- Os nomes das colunas foram comparados utilizando `set()`, confirmando que os sete arquivos possuem os mesmos nomes de colunas.
- As colunas `km`, `latitude` e `longitude` foram interpretadas como `float64`, indicando que os valores decimais foram reconhecidos corretamente.

## Decisão 3: contagem de acidentes e mortes

- **Acidentes:** contar IDs distintos (`COUNT(DISTINCT id)`).
- **Mortes:** somar os valores da coluna `mortos` por `id`, considerando que um acidente pode possuir várias linhas no arquivo agrupado por pessoa.

**Evidências:**

- Em 2025, foram identificados **72.529 IDs únicos** em **194.629 linhas**.
- No conjunto nacional de 2019 a 2025, foram identificados **473.813 acidentes** e **39.303 mortes registradas**.
- Não foram identificados IDs repetidos entre anos: os 473.813 acidentes únicos do conjunto correspondem à soma das quantidades de acidentes únicos de cada ano.
- A coluna `classificacao_acidente` apresentou exatamente uma classificação distinta em **473.805 IDs**. Os outros **8 IDs** não possuem classificação registrada.
- Foram identificados **33.745 acidentes classificados como fatais**, com **39.302 mortes registradas**.
- Os 39.303 mortos registrados se dividem em **39.302 nos acidentes classificados como fatais e 1 no acidente sem classificação**.
- A comparação entre `classificacao_acidente` e `mortos > 0` demonstrou consistência entre a classificação e a existência de mortes registradas.

**Limitação:** a comparação confirma a consistência quanto à existência de mortes, mas não permite validar se a quantidade exata de mortos está completa.

## Decisão 4: tipo da coluna `id`

A coluna `id` foi convertida para o tipo inteiro (`int64`).

**Justificativa:** alguns arquivos apresentavam essa coluna como `float64`, embora os valores fossem inteiros, sem casas decimais reais. Como não foram identificados valores nulos, a conversão para inteiro foi realizada para padronizar o identificador dos acidentes.

## Decisão 5: criação do DataFrame `acidentes`

Foi criado um DataFrame chamado `acidentes`, agrupado por `id`, com uma linha por acidente e duas informações:

- `gravidade`: classificação do acidente, obtida com `first`.
- `mortos`: soma das mortes registradas nas linhas correspondentes ao acidente, obtida com `sum`.

**Justificativa:**

- A classificação do acidente apresentou apenas um valor distinto por ID entre os registros não nulos.
- A comparação entre a classificação e a existência de mortes registradas demonstrou consistência nos acidentes classificados.
- O agrupamento permite trabalhar com a granularidade de uma linha por acidente, facilitando a contagem e as análises posteriores.

## Pendências para a limpeza (Passo 4)

| Problema | Evidência | Decisão |
|---|---|---|
| Nulos em `tipo_envolvido`, `estado_fisico` e `sexo` | 109.623 linhas nos sete anos (17.150 em 2025). Os nulos estão exatamente nas mesmas linhas. Nessas linhas, `ilesos`, `feridos_leves`, `feridos_graves` e `mortos` somam 0. Além disso, 14.181 linhas estão associadas a acidentes classificados como fatais. | Definir tratamento. |
| Nulos em `tipo_veiculo` | 45.838 linhas nos sete anos. | Verificar a relação com os registros de pedestres e definir tratamento. |
| Nulos em `tipo_acidente` | 33 linhas nos sete anos. | Definir tratamento. |
| Nulos em `classificacao_acidente` | 36 linhas nulas nos sete anos; 8 acidentes não possuem classificação em nenhuma linha, e 1 deles possui `mortos > 0`. Não foi verificado se as demais linhas nulas pertencem a acidentes classificados em outras linhas. | Investigar o acidente com morte registrada antes de definir o tratamento. |
| `idade = -1` e `sexo = inválido` | O dicionário de dados indica que representam ausência de informação. Quantidades ainda não verificadas. | Quantificar e definir tratamento. |
| Coordenadas e `km` | Necessário verificar a plausibilidade dos valores de latitude, longitude e quilometragem. | Validar. |
| Nulos em `regional`, `delegacia` e `uop` | Campos fora do escopo principal do projeto. | Ignorar. |