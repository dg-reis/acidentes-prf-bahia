# Decisões do Projeto 

## Decisão 1: dados agrupados por pessoa

A decisão inicial é manter apenas os arquivos que possuem dados agrupados **por pessoa**. O motivo é explicado logo abaixo.

Os arquivos `.csv` contendo dados de acidentes, disponíveis na área de dados públicos do site da PRF, possuem dois tipos: **dados agrupados por ocorrência** e **dados agrupados por pessoa**.

| AGRUPAMENTO POR PESSOA | AGRUPAMENTO POR OCORRÊNCIA |
| :---: | :---: |
| As linhas de dados de acidentes possuem localização completa, características dos acidentes, identificador do acidente, identificador das pessoas, características das pessoas (sexo e idade) e características dos veículos envolvidos. | As linhas de dados de acidentes possuem localização completa, características dos acidentes e identificador do acidente. |

Portanto, a escolha segue sendo os arquivos `.csv` agrupados por pessoa, já que possibilitam uma análise posterior direcionada exclusivamente ao perfil dos envolvidos nos acidentes, caso necessário.

Uma comparação da quantidade de acidentes entre os dois arquivos já foi realizada, utilizando a fórmula `=CONT.VALORES(ÚNICO(A2:A194630))` no próprio Excel.

Nos dados agrupados por ocorrência (onde cada linha representa um acidente) do ano de 2025, havia **72529** linhas, que representam essa mesma quantidade em número (nacional) de acidentes no ano. E nos dados agrupados por pessoa do ano de 2025, também há essa mesma quantidade de acidentes.

Porém, como uma linha nesse arquivo não representa necessariamente um acidente único, a contagem dos acidentes, nesse caso, será feita pelo próprio identificador (`id`).

***Exemplo:*** um identificador pode estar presente, sequencialmente, em 4 linhas do arquivo, o que significa que 4 pessoas estavam presentes em um mesmo acidente, logo possuem o mesmo `id`. 

## Decisão 2: leitura dos CSVs
- Separador `;`, decimal `,`, encoding `latin-1`
- Vale para todos os anos.
- Evidência: erro de UTF-8 em 2025 ("byte `0xe1`") ao tentar o padrão

## Decisão 3: como medir acidentes e gravidade
- Acidentes: `COUNT(DISTINCT id)`
- Óbitos: soma de `mortos` por `id` (ver pendência sobre nulos)
- Evidência: 2025 tem **72529** ids únicos para **194.629** linhas

## Pendências para a limpeza (passo 4)

| Problema | Evidência | Decisão |
|---|---|---|
| Nulos em `tipo_envolvido`, `estado_fisico`, `sexo` | ___ linhas em 2025; mesmas linhas? | a definir |
| Nulos em `tipo_veiculo` | ___ em 2025 | a definir |
| Nulos em `tipo_acidente` e `classificacao_acidente` | poucos casos por ano | a definir |
| `idade = -1` e `sexo = inválido` | a quantificar | a definir |
| Coordenadas e `km` plausíveis | a conferir | a definir |
| `regional`, `delegacia`, `uop` com nulos | fora do escopo | ignorar |