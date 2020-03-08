---
title: Compreendendo sua fatura de Azure Cosmos DB
description: Este artigo explica como entender sua lista de Azure Cosmos DB com alguns exemplos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 74a4279d347be92b1047a9cf361e233ecc7fcff8
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674309"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Entenda sua fatura de Azure Cosmos DB

Como um serviço de banco de dados nativo de nuvem totalmente gerenciado, Azure Cosmos DB simplifica a cobrança cobrando apenas pela taxa de transferência provisionada e pelo armazenamento consumido. Não há taxas de licença adicionais, hardware, custos do utilitário ou custos de instalações em comparação com alternativas locais ou de IaaS. Quando você considera os recursos de várias regiões de Azure Cosmos DB, o serviço de banco de dados fornece uma redução significativa nos custos em comparação com as soluções locais ou IaaS existentes.

Com o Azure Cosmos DB, você será cobrado por hora com base na taxa de transferência provisionada e no armazenamento consumido. Para a entrada prevista, a unidade de faturação é de 100 RU/seg por hora, cobrada a $0,008 por hora, assumindo preços públicos padrão, ver a [página de Preços.](https://azure.microsoft.com/pricing/details/cosmos-db/) Para o armazenamento consumido, é cobrado $0,25 por 1 GB de armazenamento por mês, consulte a [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Este artigo utiliza alguns exemplos para ajudar a compreender os detalhes apresentados na fatura mensal. Os números mostrados nos exemplos poderão ser diferentes se os seus contentores do Azure Cosmos tiverem uma quantidade diferente de débito aprovisionado, se estiverem em várias regiões ou se forem executados durante um período diferente ao longo de um mês.

> [!NOTE]
> A cobrança é para qualquer parte de uma hora do relógio de parede, não uma duração de 60 minutos.

## <a name="billing-examples"></a>Exemplos de faturação

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exemplo de cobrança-taxa de transferência em um contêiner (mês inteiro)

* Vamos supor que você configure uma taxa de transferência de 1.000 RU/s em um contêiner e ela existe por 24 horas * 30 dias para o total do mês = 720 horas.  

* 1\.000 RU/s é 10 unidades de 100 RU/seg por hora para cada hora em que os contêineres existem (ou seja, 1000/100 = 10). 

* Multiplicar 10 unidades por hora pelo custo de $0.08 (por 100 RU/s por hora) = $0.08 por hora. 

* Multiplicar o $0.08 por hora pelo número de horas no mês igual a $0.08 * 24 horas * 30 dias = $57.60 para o mês.  

* A fatura mensal total mostrará 7.200 unidades (of 100 RUs), que custarão $57.60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exemplo de cobrança-taxa de transferência em um contêiner (mês parcial)

* Vamos supor que criamos um contêiner com taxa de transferência provisionada de 2.500 RU/s. O contêiner reside por 24 horas ao longo do mês (por exemplo, nós o excluímos 24 horas depois de criá-lo).  

* Em seguida, veremos 600 unidades na fatura (2.500 RU/s/100 RU/s/unidade * 24 horas). O custo será de $4.80 (600 unidades * $0.008/unidade).

* A fatura total do mês será $4.80.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de cobrança se o tamanho do armazenamento for alterado

A capacidade de armazenamento é faturada por unidades da quantidade máxima horária de dados armazenados, em GB, durante o período de um mês. Por exemplo, se utilizou 100 GB de armazenamento durante metade do mês e 50 GB na segunda metade, ser-lhe-á cobrado o equivalente a 75 GB de armazenamento durante esse mês.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Taxa de cobrança quando o contêiner ou um conjunto de contêineres está ativo por menos de uma hora

Você é cobrado pela taxa fixa por cada hora em que o contêiner ou banco de dados existe, não importa o uso ou se o contêiner ou banco de dados está ativo por menos de uma hora. Por exemplo, se você criar um contêiner ou banco de dados e excluí-lo 5 minutos depois, sua fatura incluirá uma hora.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Taxa de cobrança quando a produtividade em um contêiner ou banco de dados é dimensionada para cima/para baixo

Se você aumentar a taxa de transferência provisionada às 9:30 de 400 RU/seg para 1.000 RU/seg e, em seguida, reduzir a taxa de transferência provisionada às 10:45 de volta para 400 RU/seg, você será cobrado por duas horas de 1.000 RU/s. 

Se você aumentar a taxa de transferência provisionada para um contêiner ou um conjunto de contêineres às 9:30 de 100-K RU/SEC para 200-K RU/seg e, em seguida, reduzir a taxa de transferência provisionada na 10:45 de volta para 100-K RU/seg, você será cobrado por duas horas de 200 K RU/seg.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de cobrança: vários contêineres, cada um com modo de taxa de transferência provisionado dedicado

* Se você criar uma conta do Azure Cosmos no leste dos EUA 2 com dois contêineres com taxa de transferência provisionada de 500 RU/seg e 700 RU/seg, respectivamente, você teria uma taxa de transferência total provisionada de 1.200 RU/seg.  

* Você será cobrado em 1200/100 * $0.08 = $0.096/hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a capacidade de cada contêiner em 500 RU/s ao criar também um novo contêiner ilimitado com 20.000 RU/seg, sua capacidade provisionada geral será de 22.200 RU/seg (1.000 RU/seg + 1.200 RU/SEC + 20, forem/s).  

* Em seguida, sua fatura mudaria para: $0.08 x 222 = $1.776/hora. 

* Em um mês de 720 horas (24 horas * 30 dias), se for 500 horas, a taxa de transferência provisionada foi 1.200 RU/seg e, para a taxa de transferência provisionada restante de 220 horas, foi de 22.200 RU/seg, sua fatura mensal mostrará: 500 x $0.096/hora + 220 x $1.776/hora = $438.72/mês.

![Exemplo de fatura de taxa de transferência dedicada](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Exemplo de cobrança: contêineres com modo de taxa de transferência compartilhada

* Se você criar uma conta do Azure Cosmos no leste dos EUA 2 com dois bancos de dados do Azure Cosmos (com um conjunto de contêineres que compartilham a taxa de transferência no nível do banco de dados) com a taxa de transferência provisionada de 50-K RU/seg e 70-K RU/seg, respectivamente, você teria uma taxa de transferência total provisionada de 120 K RU  

* Você será cobrado 1200 x $0.08 = US $9,60/hora. 

* Se suas necessidades de taxa de transferência forem alteradas e você aumentar a taxa de transferência provisionada de cada banco de dados em 10K RU/s para cada banco de dados, e você adiciona um novo contêiner ao primeiro banco de dados com o modo de taxa de transferência dedicado de 15 a K RU/seg ao seu banco de dados de produtividade compartilhado, sua capacidade provisionada geral seria 155-K RU/s (60 K RU/seg + 80 K RU/seg + 15 K RU/s).  

* Em seguida, sua fatura mudaria para: 1.550 * $0.08 = $12.40/hora.  

* Em um mês de 720 horas, se for 300 horas, a taxa de transferência provisionada foi 120-K RU/seg e, para a taxa de transferência provisionada restante de 420 horas, foi 155-K RU/seg, sua fatura mensal mostrará: 300 x $9,60/hora + 420 x $12.40/hora = $2880 + $5208 = $8088/mês. 

![Exemplo de fatura de taxa de transferência compartilhada](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Exemplos de cobrança com replicação geográfica e vários mestres  

Você pode adicionar/remover regiões do Azure em qualquer lugar do mundo para sua conta de banco de dados Cosmos do Azure a qualquer momento. A taxa de transferência que você configurou para vários contêineres e bancos de dados Cosmos do Azure será reservada em cada uma das regiões do Azure associadas à sua conta de banco de dados Cosmos do Azure. Se a soma da taxa de transferência provisionada (RU/s) configurada em todos os bancos de dados e contêineres na sua conta do Azure Cosmos Database (provisionado por hora) for T e o número de regiões do Azure associado à sua conta de banco de dados for N, em seguida, a taxa de transferência total provisionada para uma determinada hora, para sua conta de banco de dados Cosmos do Azure, (a) configurada com uma única região de gravação é igual a T x N RU/seg e (b) configurado com todas as regiões capazes de processar gravações é igual a T x (N + 1) RU/s, respectivamente. O produto provisionado (região de escrita única) custa $0.008/hora por 100 RU/sec e a provisão com múltiplas regiões writáveis (config multi-master) custa $0,016/hora por 100 RU/sec (ver [página de preços).](https://azure.microsoft.com/pricing/details/cosmos-db/) Seja sua única região de gravação ou várias regiões de gravação, Azure Cosmos DB permite que você leia dados de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de cobrança: conta de várias regiões do Azure Cosmos, gravações de região única

Vamos supor que você tenha um contêiner Cosmos do Azure no oeste dos EUA. O contêiner é criado com a taxa de transferência de 10K RU/seg e você armazena 1 TB de dados neste mês. Vamos supor que você adicione três regiões (leste dos EUA, Europa Setentrional e Ásia Oriental) à sua conta do Azure Cosmos, cada uma com o mesmo armazenamento e taxa de transferência. Sua fatura mensal total será (supondo 30 dias em um mês). Sua fatura seria a seguinte: 

|**Item** |**Utilização (mês)** |**Taxa** |**Custo Mensal** |
|---------|---------|---------|-------|
|Conta de débito para o contentor em E.U.A. Oeste      | 10K RU/s * 24 * 30    |$0.08 por 100 RU/s por hora   |$576|
|Conta de débito para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental       | 3 * 10K RU/s * 24 * 30    |$0.08 por 100 RU/s por hora  |$1728|
|Conta de armazenamento para o contentor em E.U.A. Oeste      | 250 GB    |US $0,25/GB  |$62.50|
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental      | 3 * 250 GB    |US $0,25/GB  |$187.50|
|**Total**     |     |  |**$2.554**|

*Vamos também assumir que você retira 100 GB de dados todos os meses do contentor nos EUA Ocidentais para replicar dados para os EUA Orientais, Norte da Europa e Ásia Oriental. É cobrado por uma saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de cobrança: conta de várias regiões do Azure Cosmos, gravações de várias regiões

Vamos supor que você crie um contêiner Cosmos do Azure no oeste dos EUA. O contêiner é criado com a taxa de transferência de 10K RU/seg e você armazena 1 TB de dados neste mês. Vamos supor que você adicione três regiões (leste dos EUA, Europa Setentrional e Ásia Oriental), cada uma com o mesmo armazenamento e taxa de transferência e queira a capacidade de gravar nos contêineres em todas as regiões associadas à sua conta do Azure Cosmos. A sua fatura mensal total será (supondo 30 dias em um mês) da seguinte maneira:

|**Item** |**Utilização (mês)**|**Taxa** |**Custo Mensal** |
|---------|---------|---------|-------|
|Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)       | 10K RU/s * 24 * 30    |$0.16 por 100 RU/s por hora    |$1152 |
|Fatura de taxa de transferência para três regiões adicionais – leste dos EUA, Europa Setentrional e Ásia Oriental (todas as regiões são graváveis)        | (3 + 1) * 10K RU/seg * 24 * 30    |$0.16 por 100 RU/s por hora   |$4608 |
|Conta de armazenamento para o contentor em E.U.A. Oeste      | 250 GB    |US $0,25/GB  |$62.50|
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental      | 3 * 250 GB    |US $0,25/GB  |$187.50|
|**Total**     |     |  |**$6.010**|

*Vamos também assumir que você retira 100 GB de dados todos os meses do contentor nos EUA Ocidentais para replicar dados para os EUA Orientais, Norte da Europa e Ásia Oriental. É cobrado por uma saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de cobrança: conta do Azure cosmos com a taxa de transferência de nível de banco de dados de vários mestres, incluindo modo de taxa de transferência dedicada para alguns contêineres

Vamos considerar o exemplo a seguir, em que temos uma conta do Azure Cosmos de várias regiões, em que todas elas são graváveis (multimestre config). Para simplificar, vamos pressupor que o tamanho do armazenamento permaneça constante e não mude e omita-o aqui para manter o exemplo mais simples. A taxa de transferência provisionada durante o mês variada da seguinte maneira (supondo 30 dias ou 720 horas): 

[0-100 horas]\:  

* Criamos uma conta do Azure Cosmos de três regiões (oeste dos EUA, leste dos EUA, Europa Setentrional), onde todas as regiões são graváveis 

* Criamos um banco de dados (D1) com taxa de transferência compartilhada de 10K RU/seg 

* Criamos um banco de dados (D2) com taxa de transferência compartilhada 30-K RU/seg e  

* Criamos um contêiner (C1) com taxa de transferência dedicada 20 K RU/seg 

[101-200 horas]\:  

* O banco de dados foi expandido (D1) para 50 K RU/seg 

* Aumentamos o banco de dados (D2) para 70 K RU/seg  

* Nós excluímos o contêiner (C1)  

[201-300 horas]\:  

* Criamos o contêiner (C1) novamente com a taxa de transferência dedicada 20 K RU/seg 

[301-400 horas]\:  

* Removemos uma das regiões da conta do Azure Cosmos (n º de regiões graváveis agora é 2) 

* Reduzimos o banco de dados (D1) para 10K RU/seg 

* Aumentamos o banco de dados (D2) para 80 K RU/seg  

* Excluímos o contêiner (C1) novamente 

[401-500 horas]\:  

* Reduzimos o banco de dados (D2) para 10K RU/seg  

* Criamos o contêiner (C1) novamente com a taxa de transferência dedicada 20 K RU/seg 

[501-700 horas]\:  

* Aumentamos o banco de dados (D1) para 20 K RU/seg  

* Aumentamos o banco de dados (D2) para 100 K RU/seg  

* Excluímos o contêiner (C1) novamente  

[701-720 horas]\:  

* Aumentamos o banco de dados (D2) para 50 K RU/seg  

Visualmente, as alterações na taxa de transferência total provisionada durante 720 horas para o mês são mostradas na figura abaixo: 

![Exemplo de vida real](./media/understand-your-bill/bill-example3.png)

A conta mensal total será (supondo 30 dias/720 horas em um mês) será calculada da seguinte maneira:

|**Horas**  |**RU/S** |**Item** |**Utilização (hora a hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30 MIL <br/>C1:20 MIL |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2880  |
|[101-200] |D1:50 MIL <br/>D2:70K <br/>C1: -- |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5760  |
|[201-300]  |D1:50 MIL <br/>D2:70K <br/>C1:20 MIL |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2240  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1440   |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20 MIL |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1280  |
|[501-700] |D1:20 MIL <br>D2:100 MIL <br>C1: -- |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3840  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7680  |
|[701-720] |D1:20 MIL <br/>D2:50 MIL <br/>C1: -- |Fatura de taxa de transferência para o contêiner no oeste dos EUA (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Fatura de taxa de transferência para 2 regiões adicionais: leste dos EUA, Europa Setentrional (todas as regiões são graváveis)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Custo Mensal Total**  | |**$38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Exemplos de faturação com contas de nível livre
Com o nível gratuito Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento na sua conta gratuitamente, aplicado ao nível da conta. Qualquer RU/s e armazenamento para além de 400 RU/s e 5 GB serão cobrados às taxas de preços regulares por página de preços. Na conta, não verá um item de linha ou carga gratuito para os 400 Ru/s gratuitos e 5 GB, apenas o RU/s e armazenamento para além do que é coberto por nível livre. O 400 RU/s aplica-se a qualquer tipo de RU/s - entrada aprovisionada, piloto automático (pré-visualização) e multi-master.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Exemplo de faturação - contentor ou base de dados com entrada aprovisionada
- Vamos supor que criamos uma base de dados ou um contentor numa conta de nível livre com 400 RU/s e 5 GB de armazenamento.
- Sua conta não mostrará qualquer custo por este recurso. O seu custo horário e mensal será $0.
- Agora, suponhamos na mesma conta, adicionamos outra base de dados ou contentor com 1000 RU/s e 10 GB de armazenamento.
- A sua conta irá agora mostrar uma taxa para os 1000 RU/s e 10 GB de armazenamento. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Exemplo de faturação - recipiente ou base de dados com entrada de piloto automático (pré-visualização)
- Vamos supor, numa conta de nível livre, que criamos uma base de dados ou um contentor com piloto automático habilitado, com um máximo ru/s de 4000 RU/s. Este recurso escalará automaticamente entre 400 RU/s - 4000 RU/s. 
- Suponha que na hora 1 a hora 10, o recurso está no mínimo de 400 RU/s. Durante a hora 11, o recurso escala até 1000 RU/s e depois volta para baixo para 400 RU/s dentro de uma hora.
- Nas horas 1 a 10, será cobrado $0 por entrada, já que os 400 RU/s foram cobertos por nível livre. 
- Na hora 11, você será cobrado para um eficaz 1000 RU/s - 400 RU/s = 600 RU/s, já que este é o RU/s mais alto da hora. Esta será de 6 unidades de 100 RU/s por hora, por isso o custo total de produção da hora será de 6 unidades * $0,012 = $0,072. 
- Qualquer armazenamento para além dos primeiros 5 GB será faturado a taxas normais de armazenamento. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exemplo de faturação - conta de região de escrita única e multi-região
- Vamos supor, numa conta de nível livre, que criamos uma base de dados ou um contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta para 3 regiões, e temos uma conta de um único mestre (região de escrita única).
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um eficaz 3200 RU/s (32 unidades) de entrada provisionada à taxa única da região de escrita e 25 GB de armazenamento.
- O custo mensal para ru/s seria: 32 unidades * $0,008 * 24 horas * 31 dias = $190,46. O custo mensal para o armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $190.46 + $6,25 = $196,71.
- Nota: se o preço unitário para RU/s ou armazenamento diferir nas regiões, o nível livre 400 RU/s e 5 GB refletirão as taxas da conta em que a região foi criada.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Exemplo de faturação - conta multi-região, multi-master (região de escrita múltipla)

Este exemplo reflete [preços multi-master](https://azure.microsoft.com/pricing/details/cosmos-db/) para contas criadas após 1 de dezembro de 2019. 
- Vamos supor, numa conta de nível livre, que criamos uma base de dados ou um contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta para 3 regiões, e temos uma conta multi-master (região de escrita múltipla). 
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um eficaz 3200 RU/s (32 unidades) de entrada provisionada à taxa de região de escrita múltipla e 25 GB de armazenamento.
- O custo mensal para ru/s seria: 32 unidades * $0,016 * 24 horas * 31 dias = $380,93. O custo mensal para o armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $380.93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Estimando proativamente sua fatura mensal  

Vamos considerar outro exemplo, no qual você deseja estimar proativamente sua fatura antes do fim do mês. Você pode estimar sua fatura da seguinte maneira:

|**Custo de Armazenamento** | |
|----|----|
|Tamanho médio de registro (KB) |1 |
|Número de registros  |100 milhões  |
|Armazenamento total (GB)  |100 |
|Custo mensal por GB  |$0.25  |
|Custo mensal esperado para armazenamento   |$25  |

<br>

|**Custo de entrada** | | | |
|----|----|----|----|
|Tipo de Operação| Solicitações/s| Média de RU/solicitação| RUs necessário|
|Escrita| 100 | 5 | 500|
|Leitura| 400| 1| 400|

Total de RU/s: 500 + 400 = 900 custo por hora: 900/100 * $0.08 = $0.72 custo mensal esperado para taxa de transferência (supondo 31 dias): $0.72 * 24 * 31 = $53.57

**Custo Mensal Total**

Custo mensal total = custo mensal para armazenamento + custo mensal para taxa de transferência-custo mensal total = $25 + $53.57 = $78.57

*Os preços podem variar por região. Para obter preços atualizados, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Cobrança com Azure Cosmos DB capacidade reservada

Azure Cosmos DB capacidade reservada permite que você compre a taxa de transferência provisionada antecipadamente (uma capacidade reservada ou uma reserva) que pode ser aplicada a todos os bancos de dados e contêineres do Azure Cosmos (para qualquer API ou modelo de dado) em todas as regiões do Azure. Como o preço de taxa de transferência provisionado varia por região, ele ajuda a considerar a capacidade reservada como um crédito monetário que você comprou com desconto, que pode ser obtido de uma taxa de transferência provisionada com o respectivo preço em cada região. Por exemplo, digamos que você tenha uma conta do Azure cosmos com um único contêiner provisionado com 50-K RU/s e replicado globalmente duas regiões-leste dos EUA e leste do Japão. Se você escolher a opção pré-paga, pagará:  

* no leste dos EUA: para 50-K RU/seg na taxa de $0.08 por 100 RU/s nessa região 

* no leste do Japão: para 50-K RU/seg na taxa de $0.09 por 100 RU/s nessa região

A sua fatura total (sem a capacidade reservada) seria (supondo 30 dias ou 720 horas): 

|**Região**| **Preço hortário por 100 RU/s**|**Unidades (RU/s)**|**Valor faturado (hora a hora)**| **Valor Faturado (mensalmente)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.08 |50 K|$4|$2880 |
|Leste do Japão|$0.09 |50 K| $4.50 |$3240 |
|Total|||$8.50|$6120 |

Em vez disso, vamos considerar que você comprou a capacidade reservada. Você pode comprar capacidade reservada para 100-K RU/seg com o preço de $56064 por um ano (com 20% de desconto) ou $6.40 por hora. Consulte os preços de capacidade reservados na [página de Preços).](https://azure.microsoft.com/pricing/details/cosmos-db/)  

* Custo de taxa de transferência (pago conforme o uso): 100.000 RU/s/100 * $0.008/hora * 8760 horas em um ano = $70080 

* Custo de taxa de transferência (com capacidade reservada) $70080 com desconto em 20% = $56064 

O que você adquiriu efetivamente é um crédito de $8 por hora, para 100 K RU/seg usando o preço da lista no leste dos EUA, com o preço de $6.40 por hora. Você pode então desenhar dessa reserva de produtividade pré-paga por hora para a capacidade de taxa de transferência provisionada em qualquer região global do Azure no respectivo preço de lista regional definido para sua assinatura. Neste exemplo, em que você provisiona 50 K RU/seg no leste dos EUA e no leste do Japão, você poderá desenhar $8 de taxa de transferência provisionada por hora e será cobrado o excedente de $0.50 por hora (ou $360/mês). 

|**Região**| **Preço hortário por 100 RU/s**|**Unidades (RU/s)**| **Valor faturado (hora a hora)**| **Valor Faturado (mensalmente)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.08 |50 K|$4|$2880 |
|Leste do Japão|$0.09 |50 K| $4.50 |$3240 |
|||Pay as you go|$8.50|$6120|
|Capacidade de Reserva Comprada|$0.64 (20% de desconto) |100 RU/seg ou $8 de capacidade adquirida previamente |-$8|-$5760 |
|Fatura Líquida|||$0.50 |$360 |

## <a name="next-steps"></a>Passos Seguintes

Em seguida, você pode continuar a aprender sobre a otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como o modelo de [preços da Cosmos DB é rentável para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)
