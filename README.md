# Análise de Estatísticas para as Cartinhas da Rinha de Chorões

[Rinha de Chorões](https://www.twitch.tv/rinhadechoroes) é um campeonato amador de Dota 2 e para sua terceira edição foi desenvolvida 
uma página web contendo "cartas de Jogadores" como esta:

<p align="center">
<img src=https://i.imgur.com/SClKzTj.png/>
 </p>


# Funcionamento em Alto Nível

## Fonte dos dados

Os números mostrados nas cartinhas são obtidos a partir de manipulações nas estatísticas dos jogadores. Essas estatísticas
são as métricas de desempenho disponibilizadas no final de cada jogo, como ouro por minuto ou abates. Para todos os jogadores
a fonte dessas estatísticas são os próprios jogos da Rinha. Ou seja, apenas o desepenho obtido pelos jogadores durante o
campeonato é analisado. Consequentemente, é necessário obter as estatísitcas das partidas do campeonato para cada jogador.
   
No inicio do campeonato a organização conseguiu ,em conjunto com a Valve, oficializar o campeonato. Isso permitiu que todas 
as partidas realizadas até a data do término do ticket sejam publicas. Por conta disso, todas as partidas realizadas
nesse período estão disponíveis em sites como Opendota, Stratz e Dotabuff. Todas as estísticas são obtidas a partir da [API do Stratz](https://stratz.com/api).

Depois que esses dados são tratados, são armazenados em uma base de dados SQL. Isso é importante porque o numero de requisções para a API do stratz é limitada na versão gratuita. Dessa forma, podemos ter uma versão local dos dados, e fazer o que quiser com eles. 

Tendo a base de dados local, uma outra parte do programa fornece esses dados em forma de "estatísticas de cartinhas" em conjunto com informações sobre os jogadores. Na prática, o que a API (a nossa API) retorna, são as informações necessárias para compor todas as cartinhas para uma dada liga (edição do campeonato).
    
## Significado dos dados presentes nas cartinhas


# Implementação

## Instalação e Execução

1. Clone o repositório
2. Instale o [poetry](https://python-poetry.org/docs/#installing-with-the-official-installer).
3. Execute:

```
poetry install
poetry run flask
```
4. Tem muito tempo que eu não mexo no repositório. Se isso não for o suficiente é skill issue :)

## Arquivos

### rinhaapi/app.py

Aqui é a API em si. Atualmente só contém apenas uma rota relevante (/get_league_card_stats). Para uma dada liga, retorna as informações de todos os jogadores, já formatado em forma de "cartinha".

Essa API também atualiza um cache das informações de cartinhas periodicamente. Ou seja, na primeira vez em que alguem faz a requisição a API le a base de dados. Na segunda vez em diante, uma variável contem as informações. Caso mais de um dia tenha passado, o cache é atualizado.

(Alguém que etende mais que eu de backend deve saber um jeito melhor de fazer essa otimização)

### rinhaapi/match_parser.py

Nesse arquivo, há funções que permitem, para uma dada liga do stratz (qualquer uma serve em teoria, mas estamos interessados nas edições passadas da Rinha de Chorões), obter as estatísticas que estamos interessados. 

A função de atualizar uma dada liga faz duas coisas: verifica quais partidas da liga já estão presentes na base de dados local e, em seguida, adiciona as partidas faltantes. Vale notar que a requisição para a API do stratz já é feita apenas com as partidas faltantes, então partidas antigas não são re-requisitadas. Dados "mal inseridos" devem ser removidos/modificados "manualmente".

### rinhaapi/performances.py

Aqui que a mágica acontece. Tendo em mãos as tabelas geradas no arquivo anterior, trata as estatísticas e retorna em formato de "cartinhas".

### /tests/*

Contém alguns testes unitários. Não se preocupe muito com eles.