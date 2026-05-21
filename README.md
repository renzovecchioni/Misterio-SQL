## Projeto - Banco de Dados
Realizado por: Arthur de Assis, Davi Rosa, Mateus Bosquine, Matheus Mendes e Renzo Vecchioni

## O Roubo da Pizza
Na calada da noite, um crime assolou a Pizzaria da cidade: o roubo misterioso de uma pizza.

Tudo começou com uma reclamação trivial de suporte. Um cliente faminto ligou para o estabelecimento enfurecido: seu pedido estava atrasado há horas. Ao checar o sistema, o atendente tomou um susto — o rastreamento indicava que a pizza já havia saído para a entrega fazia muito tempo. O entregador simplesmente sumiu do mapa, e a pizza nunca chegou ao destino.

O que parecia ser apenas uma falha de logística ou uma tremenda falta de profissionalismo logo se revelou algo muito mais sério. O entregador não sumiu por querer; ele foi emboscado e teve sua moto (e, ironicamente, a janta do cliente) levada por criminosos.

Uma queixa formal sobre o roubo do veículo foi registrada no sistema da polícia. Agora, munido apenas de um terminal SQL e do acesso aos bancos de dados da pizzaria, do sistema criminal e dos radares de trânsito da cidade, sua missão é cruzar os dados, rastrear as pistas e solucionar o mistério.


## Ponto de Partida
No momento, a única informação concreta que a gerência da pizzaria conseguiu resgatar do sistema de logística foi o identificador do pedido que gerou a reclamação do cliente. 

- ID do Pedido Relacionado: 19

Sua primeira missão como detetive é descobrir quem era o entregador responsável por este pedido e coletar suas informações para checar se há algum desdobramento no sistema policial.

## Solução 
Siga os passos abaixo para cruzar os dados e descobrir a identidade do criminoso que roubou a moto e a pizza do entregador.

**Passo 1**: Identificando o Pedido Atrasado
O mistério começa com a reclamação do cliente sobre o pedido que nunca chegou. Primeiro, listamos todos os pedidos para localizar o caso ou filtramos direto pelo ID do pedido reportado.

```SQL
-- Listar todos os pedidos para entender o cenário
SELECT * FROM PEDIDOS;
```

```SQL
-- Filtrar o pedido específico do cliente (ID 19)
SELECT * FROM PEDIDOS WHERE ID_PEDIDO = 19;
Pista obtida: Descobrimos o horário do pedido (19h18) e o CPF_ENTREGADOR responsável: '11111111101'.
```

**Passo 2**: Investigando o Entregador e o Boletim de Ocorrência
Agora que temos o CPF do entregador, precisamos verificar seus dados e buscar se ele registrou alguma queixa na polícia sobre o ocorrido.

```SQL
-- Verificar os dados do entregador
SELECT * FROM ENTREGADORES WHERE CPF_ENTREGADOR = '11111111101';

-- Buscar boletins de ocorrência vinculados ao CPF dele
SELECT * FROM BOLETIM_OCORRENCIA WHERE CPF_PESSOA_BOLETIM = '11111111101';
```
**Pista obtida**: O boletim de ocorrência confirma o roubo da moto e fornece uma característica crucial do veículo usado na fuga ou da própria moto (ex: parte da placa interceptada: %GNV4%).

**Passo 3**: Rastreando o Suspeito pelo Sistema de Radar
Com o padrão da placa em mãos, recorremos às câmeras de trânsito para rastrear por onde o veículo passou.

```SQL
-- Buscar registros no radar com o padrão da placa encontrado
SELECT * FROM SISTEMA_RADAR WHERE PLACA_RADAR LIKE '%GNV4%';
```
**Nota de Investigação**: Esta consulta inicial retorna 3 casos, o que ainda deixa margem para dúvidas. Precisamos refinar a busca pelo local do crime.

```SQL
-- Filtrando pelo local exato do crime (Rua das Flores)
SELECT * FROM SISTEMA_RADAR WHERE PLACA_RADAR LIKE '%GNV4%' AND LOCAL_RADAR = 'Rua das Flores';
```

**Nota de Investigação**: O filtro de local reduz o resultado para 2 casos. Cruzando o horário desses registros com o horário do pedido original (19h18), eliminamos o caso falso e isolamos o registro exato do suspeito! A descrição do radar aponta um detalhe visual marcante: o piloto usava um capacete muito específico.

**Passo 4**: Cruzando a Pista do Capacete na Loja
A descrição do radar informou que o suspeito usava um "Capacete Azul Bebe de Lacinho". Vamos verificar quem comprou esse produto recentemente para chegar ao nome do comprador.

```SQL
-- Identificar o comprador do capacete utilizado no crime
SELECT * FROM LOJA_CAPACETE WHERE NOME_PRODUTO = 'Capacete Azul Bebe de Lacinho';
```
**Pista obtida**: A consulta nos retorna o CPF do comprador do capacete: '77889900112'.

**Passo 5**: Revelando a Identidade do Criminoso.
Por fim, jogamos o CPF do comprador no Sistema Criminal para descobrir a ficha completa do indivíduo.

```SQL
-- Consultar a ficha criminal do suspeito
SELECT * FROM SISTEMA_CRIMINAL WHERE CPF_PESSOA = '77889900112';
```

Portanto, mistério solucionado! O suspeito principal é o **Renato Barros**, um velho conhecido da polícia por furtos recorrentes, traído pelo seu gosto exótico para capacetes!