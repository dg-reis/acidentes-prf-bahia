# Decisões do Projeto 

## Decisão 1: dados agrupados por pessoa

A decisão é manter apenas os arquivos que possuem dados agrupados **por pessoa**. O motivo é explicado logo abaixo.

Os arquivos `.csv` contendo dados de acidentes, disponíveis na área de dados públicos do site da PRF, possuem dois tipos: **dados agrupados por ocorrência** e **dados agrupados por pessoa**.

| AGRUPAMENTO POR PESSOA | AGRUPAMENTO POR OCORRÊNCIA |
| :---: | :---: |
| As linhas de dados de acidentes possuem localização completa, características dos acidentes, identificador do acidente, identificador das pessoas, características das pessoas (sexo e idade) e características dos veículos envolvidos. | As linhas de dados de acidentes possuem localização completa, características dos acidentes e identificador do acidente. |

Portanto, a escolha segue sendo os arquivos `.csv` agrupados por pessoa, já que possibilitam uma análise posterior direcionada exclusivamente ao perfil dos envolvidos nos acidentes, caso necessário.

Uma comparação da quantidade de acidentes entre os dois arquivos já foi realizada, utilizando a fórmula `=CONT.VALORES(ÚNICO(A2:A194630))` no próprio Excel. Essa comparação também foi reproduzida no notebook (célula 10), confirmando **72.529 acidentes** nos dados agrupados por ocorrência e por pessoa em 2025.

Nos dados agrupados por ocorrência (onde cada linha representa um acidente) do ano de 2025, havia **72529** linhas, que representam essa mesma quantidade em número (nacional) de acidentes no ano. E nos dados agrupados por pessoa do ano de 2025, também há essa mesma quantidade de acidentes.

Porém, como uma linha nesse arquivo não representa necessariamente um acidente único, a contagem dos acidentes, nesse caso, será feita pelo próprio identificador (`id`).

**Exemplo:** um identificador pode estar presente, sequencialmente, em 4 linhas do arquivo, indicando 4 registros associados ao mesmo acidente. Essas linhas podem corresponder a pessoas envolvidas no acidente.

## Decisão 2: leitura dos CSVs
- Separador `;`, decimal `,`, encoding `latin-1`
- Vale para todos os anos.
- Evidência 1: erro de UTF-8 em 2025 ("byte `0xe1`") ao tentar o padrão
- Evidência 2: os 7 arquivos (2019 a 2025) foram lidos com esses parâmetros, sem erros.

## Decisão 3: como medir acidentes e gravidade
- Acidentes: `COUNT(DISTINCT id)`
- **Quantidade de mortos:** soma de `mortos` por `id`, considerando que cada acidente pode possuir várias linhas no arquivo agrupado por pessoa.

**Evidências:**
- Em 2025, foram identificados **72.529 IDs únicos** em **194.629 linhas**.
- A classificação `classificacao_acidente` apresentou apenas 1 valor distinto em **473.805 IDs**; os outros **8 IDs** não possuem classificação registrada.
- Não foram identificados IDs repetidos entre anos: os **473.813 acidentes únicos** do conjunto correspondem à soma das quantidades de acidentes únicos de cada ano.
- No conjunto nacional de 2019 a 2025, foram identificados **473.813 acidentes** e **39.303 mortes registradas**.
- Foram identificados **33.745 acidentes classificados como fatais**, com **39.302 mortes registradas**.
- Um acidente sem classificação apresentou a única morte restante.
- A comparação entre `classificacao_acidente` e `mortos > 0` demonstrou consistência entre a classificação e a existência de mortes registradas nos acidentes classificados.

**Limitação:** a comparação confirma a consistência quanto à existência de mortes, mas não permite validar se a quantidade exata de mortos está completa.

## Pendências para a limpeza (passo 4)

| Problema | Evidência | Decisão |
|---|---|---|
| Nulos em `tipo_envolvido`, `estado_fisico` e `sexo` | 109.623 linhas nos 7 anos (17.150 em 2025); os nulos estão exatamente nas mesmas linhas. Nessas linhas, `ilesos`, `feridos_leves`, `feridos_graves` e `mortos` somam 0. Além disso, 14.181 linhas estão associadas a acidentes classificados como fatais. | A definir no passo 4 |
| Nulos em `tipo_veiculo` | 6.341 registros em 2025. | A definir no passo 4 |
| Nulos em `tipo_acidente` e `classificacao_acidente` | 33 e 36 registros, respectivamente, considerando os 7 anos. | A definir no passo 4 |
| `idade = -1` e `sexo = inválido` | a quantificar | a definir |
| Coordenadas e `km` plausíveis | a conferir | a definir |
| `regional`, `delegacia`, `uop` com nulos | fora do escopo | ignorar |
| `classificacao_acidente` ausente | 8 acidentes sem classificação; 1 possui `mortos > 0` | Investigar o registro com morte contabilizada antes de definir o tratamento |