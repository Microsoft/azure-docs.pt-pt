---
title: Compreender a sua conta de DB do Azure Cosmos
description: Este artigo explica como entender a sua conta de DB Azure Cosmos com alguns exemplos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: 3ac90b79053e59047dbe64598688e77b9df059d1
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358716"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Compreender a fatura do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Como um serviço de base de dados totalmente gerido na nuvem, a Azure Cosmos DB simplifica a faturação cobrando apenas para as suas operações de base de dados e armazenamento consumido. Não existem taxas adicionais de licença, hardware, custos de utilidade ou custos de instalação em comparação com as alternativas no local ou alternativas hospedadas pelo IaaS. Quando se considera as capacidades multi-regiões da Azure Cosmos DB, o serviço de base de dados proporciona uma redução substancial dos custos em comparação com as soluções existentes no local ou iaaS.

- **Operações de base de dados** : A forma como é cobrado pelas suas operações de base de dados depende do tipo de conta Azure Cosmos que está a utilizar.

  - **Produção prevista** : É cobrado de hora em hora para o rendimento máximo previsto para uma determinada hora, em incrementos de 100 RU/s.
  - **Serverless** : É cobrado por hora a quantidade total de Unidades de Pedido consumidas pelas suas operações de base de dados.

- **Armazenamento** : É faturada uma taxa fixa para a quantidade total de armazenamento (em BB) consumida pelos seus dados e índices durante uma hora.

Consulte [a página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as últimas informações sobre preços.

Este artigo utiliza alguns exemplos para ajudar a compreender os detalhes apresentados na fatura mensal. Os números mostrados nos exemplos poderão ser diferentes se os seus contentores do Azure Cosmos tiverem uma quantidade diferente de débito aprovisionado, se estiverem em várias regiões ou se forem executados durante um período diferente ao longo de um mês. Todos os exemplos deste artigo calculam a fatura com base nas informações de preços mostradas na [página de preços.](https://azure.microsoft.com/pricing/details/cosmos-db/)

> [!NOTE]
> Faturação é para qualquer parte de uma hora de relógio de parede, não uma duração de 60 minutos. Todos os exemplos mostrados neste doc baseiam-se no preço de uma conta Azure Cosmos implantada numa região não governamental nos EUA. Os preços e cálculos variam consoante a região que está a usar, consulte a [página de preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter informações mais recentes sobre preços.

## <a name="billing-examples"></a>Exemplos de faturação

### <a name="billing-example---provisioned-throughput-on-a-container-full-month"></a>Exemplo de faturação - produção a provisionada num contentor (mês inteiro)

* Vamos supor que você configura uma produção de 1.000 RU/seg em um recipiente, e existe por 24 horas * 30 dias para o mês = 720 horas no total.  

* 1.000 RU/seg é 10 unidades de 100 RU/seg por hora por cada hora que os contentores existem (ou seja, 1.000/100 = 10). 

* Multiplicando 10 unidades por hora pelo custo de $0.008 (por 100 RU/seg por hora) = $0,08 por hora. 

* Multiplicar os $0,08 por hora pelo número de horas no mês equivale a $0,08 * 24 horas * 30 dias = $57,60 para o mês.  

* A fatura mensal total mostrará 7.200 unidades (de 100 RUs), que custarão $57,60.

### <a name="billing-example---provisioned-throughput-on-a-container-partial-month"></a>Exemplo de faturação - produção a provisionada num contentor (mês parcial)

* Vamos assumir que criamos um contentor com produção de 2.500 RU/seg. O contentor vive durante 24 horas ao longo do mês (por exemplo, apagamo-lo 24 horas depois de o criarmos).  

* Em seguida, veremos 600 unidades na fatura (2.500 RU/sec / 100 RU/sec/unidade * 24 horas). O custo será de $4,80 (600 unidades * $0,008/unidade).

* A conta total do mês será de $4,80.

### <a name="billing-example---serverless-container"></a>Exemplo de faturação - recipiente sem servidor

* Vamos supor que criamos um recipiente sem servidor. 

* Ao longo de um mês, emitimos pedidos de base de dados consumindo um total de 500.000 Unidades de Pedido. O custo será de $0.125 (500.000 * $0,25/milhão).

* A conta total do mês será de $0.125.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de faturação se o tamanho do armazenamento mudar

A capacidade de armazenamento é faturada em unidades da quantidade máxima horária de dados armazenados, em GB, durante um período mensal. Por exemplo, se utilizar 100 GB de armazenamento durante metade do mês e 50 GB para a segunda metade do mês, será cobrado um equivalente a 75 GB de armazenamento durante esse mês.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Taxa de faturação quando o contentor ou um conjunto de contentores estão ativos por menos de uma hora

É cobrado a taxa fixa por cada hora que o contentor ou base de dados existe, independentemente da utilização ou se o contentor ou base de dados estiver ativo por menos de uma hora. Por exemplo, se criar um contentor ou base de dados e o eliminar 5 minutos depois, a sua conta incluirá uma hora.

### <a name="billing-rate-when-provisioned-throughput-on-a-container-or-database-scales-updown"></a>Taxa de faturação quando provisida em um contentor ou escalas de base de dados para cima/para baixo

Se aumentar a produção proviscizada às 9:30 da manhã de 400 RU/seg para 1.000 RU/seg e, em seguida, menor produção provisida às 10:45 am de volta para 400 RU/seg, você será cobrado por duas horas de 1.000 RU/sec. 

Se aumentar a produção proviscizada para um contentor ou um conjunto de contentores às 9:30 da manhã de 100-K RU/sec para 200-K RU/seg e, em seguida, baixar a produção prevista às 10:45 am de volta para 100-K RU/seg, você será cobrado por duas horas de 200 K RU/sec.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de faturação: vários contentores, cada um com modo de produção dedicado a provisionado

* Se criar uma conta Azure Cosmos no Leste dos EUA 2 com dois contentores com produção de 500 RU/seg e 700 RU/seg, respectivamente, teria um rendimento total provisitivo de 1.200 RU/seg.  

* Seria cobrado 1.200/100 * $0.008 = $0.096/hora. 

* Se as suas necessidades de produção mudarem, e tiver aumentado a capacidade de cada contentor em 500 RU/seg, ao mesmo tempo que cria um novo recipiente ilimitado com 20.000 RU/seg, a sua capacidade global a provisionada seria de 22.200 RU/sec (1.000 RU/sec + 1.200 RU/+ 20.000 RU/seg).  

* A sua conta mudaria então para: $0.008 x 222 = $1.776/hora. 

* Num mês de 720 horas (24 horas * 30 dias), se por 500 horas provisidas a produção foi de 1.200 RU/seg e para as restantes 220 horas provisidas a produção foi de 22.200 RU/seg, a sua conta mensal mostra: 500 x $0,096/hora + 220 x $1.776/hora = $438,72/mês.

:::image type="content" source="./media/understand-your-bill/bill-example1.png" alt-text="Exemplo de conta de produção dedicada":::

### <a name="billing-example-containers-with-shared-provisioned-throughput-mode"></a>Exemplo de faturação: contentores com modo de produção partilhado (a provisionado)

* Se criar uma conta Azure Cosmos no Leste dos EUA 2 com duas bases de dados Azure Cosmos (com um conjunto de contentores que partilham a produção ao nível da base de dados) com o rendimento previsto de 50-K RU/sec e 70-K RU/sec, respectivamente, teria um rendimento total provisto de 120 K RU/sec.  

* Ser-lhe-ia cobrado 1200 x $0,008 = $9,60/hora. 

* Se as suas necessidades de produção forem alteradas e aumentar a produção provisitada de cada base de dados em 10K RU/seg para cada base de dados, e adicionar um novo recipiente à primeira base de dados com o modo de produção dedicado de 15-K RU/sec à sua base de dados de produção partilhada, a sua capacidade global a provisionada seria de 155-K RU/sec (60 K RU/sec + 80 K RU/sec+ 15 K RU/SEC).  

* A sua conta mudaria então para: 1.550 * $0,008 = $12,40/hora.  

* Num mês de 720 horas, se durante 300 horas a produção prevista for forada for forada de 120-K RU/seg e para as restantes 420 horas provisidas foi 155-K RU/seg, sua conta mensal mostrará: 300 x $9,60/hora + 420 x $12,40/hora = $2.880 + $5.208 = $8.088/mês. 

:::image type="content" source="./media/understand-your-bill/bill-example2.png" alt-text="Exemplo de conta de produção partilhada":::

## <a name="billing-examples-with-geo-replication"></a>Exemplos de faturação com geo-replicação  

Pode adicionar/remover regiões do Azure em qualquer parte do mundo à sua conta de base de dados Azure Cosmos a qualquer momento. A produção que configuraste para várias bases de dados e contentores da Azure Cosmos será reservada em cada uma das regiões do Azure associadas à sua conta de base de dados Azure Cosmos. Se a soma de produção (RU/sec) configurada em todas as bases de dados e contentores da sua conta de base de dados Azure Cosmos (a provisionada por hora) forda por hora, e o número de regiões Azure associadas à sua conta de base de dados for N, então o total provisurável por uma hora, para a sua conta de base de dados Azure Cosmos é igual a T x N RU/sec. O rendimento previsto (região de escrita única) custa $0.008/hora por 100 RU/seg e o rendimento previsto com várias regiões writable (multi-região escreve config) custa $0,016/hora por 100 RU/sec (ver [página de preços).](https://azure.microsoft.com/pricing/details/cosmos-db/) Seja a sua região de escrita única, ou várias regiões de escrita, a Azure Cosmos DB permite-lhe ler dados de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de faturação: conta Azure Cosmos multi-região, única região escreve

Vamos supor que tens um contentor Azure Cosmos no Oeste dos EUA. O contentor é criado com produção 10K RU/seg e armazena 1 TB de dados este mês. Vamos supor que adicione três regiões (Leste dos EUA, Norte da Europa e Ásia Oriental) à sua conta Azure Cosmos, cada uma com o mesmo armazenamento e produção. A sua fatura mensal total será (assumindo 30 dias num mês). A sua conta seria a seguinte: 

|**Item** |**Utilização (mês)** |**Rate** (Taxa) |**Custo Mensal** |
|---------|---------|---------|-------|
|Conta de produção de contentores no Oeste dos EUA      | 10K RU/seg * 24 * 30    |$0,008 por 100 RU/seg por hora   |$576|
|Conta de produção para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental       | 3 * 10K RU/seg * 24 * 30    |$0,008 por 100 RU/seg por hora  |$1.728|
|Conta de armazenamento para contentor no Oeste dos EUA      | 250 GB    |$0,25/GB  |$62,50|
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Total**     |     |  |**$2.554**|

*Vamos também assumir que você escava 100 GB de dados todos os meses do contentor no Oeste dos EUA para replicar dados em Leste dos EUA, Norte da Europa e Ásia Oriental. Está cobrado por saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de faturação: conta Azure Cosmos multi-região, multi-região escreve

Vamos supor que crias um contentor Azure Cosmos no Oeste dos EUA. O contentor é criado com produção 10K RU/seg e armazena 1 TB de dados este mês. Vamos supor que você adiciona três regiões (Eua Leste, Norte da Europa e Ásia Oriental), cada uma com o mesmo armazenamento e produção e você quer a capacidade de escrever para os contentores em todas as regiões associadas à sua conta Azure Cosmos. A sua fatura mensal total será (assumindo 30 dias num mês) da seguinte forma:

|**Item** |**Utilização (mês)**|**Rate** (Taxa) |**Custo Mensal** |
|---------|---------|---------|-------|
|Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)       | 10K RU/seg * 24 * 30    |$0,016 por 100 RU/seg por hora    |$1.152 |
|Conta de produção para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental (todas as regiões são writable)        | (3 + 1) * 10K RU/seg * 24 * 30    |$0,016 por 100 RU/seg por hora   |$4.608 |
|Conta de armazenamento para contentor no Oeste dos EUA      | 250 GB    |$0,25/GB  |$62,50|
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental      | 3 * 250 GB    |$0,25/GB  |$187,50|
|**Total**     |     |  |**$6.010**|

*Vamos também assumir que você escava 100 GB de dados todos os meses do contentor no Oeste dos EUA para replicar dados em Leste dos EUA, Norte da Europa e Ásia Oriental. Está cobrado por saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-azure-cosmos-account-with-multi-region-writes-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de faturação: Conta Azure Cosmos com gravações multi-regiões, produção de nível de base de dados, incluindo modo de produção dedicado para alguns contentores

Consideremos o exemplo seguinte, onde temos uma conta Azure Cosmos multi-região onde todas as regiões são writable (várias regiões de escrita config). Para simplificar, assumimos que o tamanho do armazenamento permanece constante e não muda e omiti-lo aqui para manter o exemplo mais simples. A produção prevista durante o mês variou da seguinte forma (assumindo 30 dias ou 720 horas): 

[0-100 horas]:  

* Criámos uma conta de três regiões Azure Cosmos (Eua Ocidental, Leste dos EUA, Norte da Europa), onde todas as regiões são writable 

* Criamos uma base de dados (D1) com produção partilhada 10K RU/seg 

* Criamos uma base de dados (D2) com produção partilhada 30-K RU/seg  

* Criamos um contentor (C1) com produção dedicada 20 K RU/seg 

[101-200 horas]:  

* Escalamos a base de dados (D1) a 50 K RU/seg 

* Escalamos a base de dados (D2) a 70 K RU/seg  

* Apagamos o contentor (C1)  

[201-300 horas]:  

* Criamos o contentor (C1) novamente com produção dedicada 20 K RU/seg 

[301-400 horas]:  

* Removemos uma das regiões da conta Azure Cosmos (# das regiões writable é agora 2) 

* Reduzimos a base de dados (D1) para 10K RU/seg 

* Escalamos a base de dados (D2) a 80 K RU/seg  

* Apagamos novamente o contentor (C1) 

[401-500 horas]:  

* Reduzimos a base de dados (D2) para 10K RU/seg  

* Criamos o contentor (C1) novamente com produção dedicada 20 K RU/seg 

[501-700 horas]:  

* Escalamos a base de dados (D1) a 20 K RU/seg  

* Escalamos a base de dados (D2) a 100 K RU/seg  

* Apagamos novamente o contentor (C1)  

[701-720 horas]:  

* Reduzimos a base de dados (D2) a 50 K RU/seg  

Visualmente, as alterações na produção total prevista durante 720 horas do mês são apresentadas na figura abaixo: 

:::image type="content" source="./media/understand-your-bill/bill-example3.png" alt-text="Exemplo da vida real":::

A fatura mensal total será (assumindo 30 dias/720 horas num mês) será calculada da seguinte forma:

|**Hours**  |**RU/s** |**Item** |**Utilização (à hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: - |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5.760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2.240  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6.720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: - |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1.440   |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1.280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: - |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3.840  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7.680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: - |Conta de produção de contentores nos EUA Ocidentais (todas as regiões são writable)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Conta de produção para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são writable)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Custo Total Mensal**  | |**$38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Exemplos de faturação com contas de nível livre
Com o nível livre de DB Azure Cosmos, você receberá os primeiros 400 RU/s e 5 GB de armazenamento na sua conta gratuitamente, aplicados ao nível da conta. Qualquer RU/s e armazenamento para além de 400 RU/s e 5 GB serão cobrados às taxas de preços regulares por página de preços. Na conta, não verá um item de custo ou linha para os 400 Ru/s e 5 GB gratuitos, apenas o RU/s e o armazenamento além do que é coberto por nível livre. O 400 RU/s aplica-se a qualquer tipo de RU/s - produção a provisionada, autoescala e várias regiões.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Exemplo de faturação - contentor ou base de dados com produção prevista
- Suponhamos que criamos uma base de dados ou um contentor numa conta de nível livre com 400 RU/s e 5 GB de armazenamento.
- A sua conta não mostrará qualquer encargo por este recurso. O seu custo horário e mensal será $0.
- Agora, vamos supor na mesma conta, adicionamos outra base de dados ou contentor com 1000 RU/s e 10 GB de armazenamento.
- A sua conta irá agora mostrar uma taxa para os 1000 RU/s e 10 GB de armazenamento. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Exemplo de faturação - recipiente com produção de autoescala
- Suponhamos que, numa conta de nível livre, criamos um recipiente com autoescala ativada, com um máximo de RU/s de 4000 RU/s. Este recurso irá escalar automaticamente entre 400 RU/s - 4000 RU/s. 
- Suponha que na hora 1 a hora 10, o recurso está no mínimo de 400 RU/s. Durante a hora 11, o recurso escala até 1000 RU/s e, em seguida, recua para 400 RU/s dentro de uma hora.
- Nas horas 1 a 10, será cobrado $0 por produção, já que os 400 RU/s foram cobertos por um nível livre. 
- Na hora 11, você será cobrado para um efetivo 1000 RU/s - 400 RU/s = 600 RU/s, já que este é o RU/s mais alto da hora. Serão 6 unidades de 100 RU/s por hora, pelo que o custo total de produção da hora será de 6 unidades * $0,012 = $0,072. 
- Qualquer armazenamento além dos primeiros 5 GB será cobrado a taxas normais de armazenamento. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exemplo de faturação - conta multi-região, região de escrita única
- Vamos supor que numa conta de nível livre, criamos uma base de dados ou contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta em 3 regiões, e temos uma única conta de região escrita.
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um efetivo 3200 RU/s (32 unidades) de produção provisitada à taxa única de escrita e 25 GB de armazenamento.
- O custo mensal para RU/s seria: 32 unidades * $0,008 * 24 horas * 31 dias = $190,46. O custo mensal para armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $190.46 + $6.25 = $196.71.
- Nota: se o preço unitário para RU/s ou armazenamento diferir nas regiões, o nível livre 400 RU/s e 5 GB refletirá as taxas da região em que a conta foi criada.

### <a name="billing-example---multi-region-account-with-multiple-write-regions"></a>Exemplo de faturação - multi-região, conta com múltiplas regiões de escrita

Este exemplo reflete [os preços das gravações multi-regiões](https://azure.microsoft.com/pricing/details/cosmos-db/) para as contas criadas após 1 de dezembro de 2019. 
- Vamos supor que numa conta de nível livre, criamos uma base de dados ou contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta em 3 regiões, e temos uma conta de múltiplas regiões de escrita. 
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um efetivo 3200 RU/s (32 unidades) de produção alocada à taxa de região de escrita múltipla e 25 GB de armazenamento.
- O custo mensal para RU/s seria: 32 unidades * $0,016 * 24 horas * 31 dias = $380,93. O custo mensal para armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $380.93 + $6.25 = $387.18.

## <a name="proactively-estimating-your-monthly-bill"></a>Estimando proativamente a sua conta mensal  

Vamos considerar outro exemplo, onde quer estimar proactivamente a sua conta antes do final do mês. Pode estimar a sua conta da seguinte forma:

**Custo do armazenamento**

* Tamanho recorde de Avg (KB) = 1 
* Número de Registos = 100.000.000 
* Armazenamento total (GB) = 100 
* Custo mensal por GB = $0,25 
* Custo Mensal Esperado para Armazenamento = $25,00 

**Custo de Produção**

|Tipo de operação| Pedidos/seg| Avg. RU/pedido| RUs necessários|
|----|----|----|----|
|Escrita| 100 | 5 | 500|
|Ler| 400| 1| 400|

Total RU/seg: 500 + 400 = 900 Custo horário: 900/100 * $0.008 = $0.072 Custo Mensal Esperado para Produção (assumindo 31 dias): $0,072 * 24 * 31 = $53,57

**Custo Total Mensal**

Custo Mensal Total = Custo Mensal de Armazenamento + Custo Mensal para Produção Total Mensal = $25,00 + $53,57 = $78,57

*Os preços podem variar por região. Para obter preços atualizados, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Faturação com capacidade reservada da Azure Cosmos

A capacidade reservada da Azure Cosmos permite-lhe adquirir o rendimento provisitivo com antecedência (uma capacidade reservada ou uma reserva) que pode ser aplicado a todas as bases de dados e contentores da Azure Cosmos (para qualquer API ou modelo de dados) em todas as regiões do Azure. Uma vez que o preço de produção previsto varia por região, ajuda a pensar na capacidade reservada como um crédito monetário que adquiriu com desconto, que pode ser extraído do rendimento a pedido ao respetivo preço em cada região. Por exemplo, digamos que você tem uma conta Azure Cosmos com um único contentor a provisionado com 50-K RU/seg e globalmente replicado duas regiões - Leste dos EUA e Japão Leste. Se escolher a opção pay-as-you-go, pagará:  

* no Leste dos EUA: para 50-K RU/seg à taxa de $0.008 por 100 RU/sec naquela região 

* no Japão Leste: para 50-K RU/seg à taxa de $0.009 por 100 RU/sec naquela região

A sua fatura total (sem capacidade reservada) seria (assumindo 30 dias ou 720 horas): 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**|**Valor cobrado (à hora)**| **Valor Faturado (mensal)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.008 |50 K|$4|$2.880 |
|Leste do Japão|$0,009 |50 K| $4,50 |$3.240 |
|Total|||$8,50|$6.120 |

Vamos considerar que comprou a capacidade reservada. Você pode comprar capacidade reservada para 100-K RU/seg ao preço de $56.064 por um ano (com 20% de desconto), ou $6,40 por hora. Consulte os preços de capacidade reservados na [página de preços).](https://azure.microsoft.com/pricing/details/cosmos-db/)  

* Custo de produção (pay-as-you-go): 100.000 RU/sec/100 * $0.008/hora * 8760 horas num ano = $70.080 

* Custo de produção (com capacidade reservada) $70.080 com desconto em 20% = $56.064 

O que você efetivamente comprou é um crédito de $8 por hora, por 100 K RU/seg usando o preço da lista no Leste dos EUA, pelo preço de $6,40 por hora. Em seguida, pode retirar-se desta reserva de produção pré-paga numa base horária para a capacidade de produção a provisionada em qualquer região global de Azure nos respetivos preços regionais definidos para a sua subscrição. Neste exemplo, onde você provisões 50 K RU/seg cada um no Leste dos EUA, e Japão Leste, você será capaz de desenhar $8,00 de produção provisida por hora, e será cobrado o excesso de $0,50 por hora (ou $360/mês). 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**| **Valor cobrado (à hora)**| **Valor Faturado (mensal)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.008 |50 K|$4|$2.880 |
|Leste do Japão|$0,009 |50 K| $4,50 |$3.240 |
|||Pay-as-you-go|$8,50|$6120|
|Capacidade Reservada Adquirida|$0.0064 (20% de desconto) |100 RU/seg ou $8 capacidade pré-comprada |-$8|-$5.760 |
|Conta Líquida|||$0.50 |$360 |

## <a name="next-steps"></a>Passos Seguintes

Em seguida, pode proceder a conhecer a otimização de custos na Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [como o modelo de preços da Cosmos DB é rentável para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [otimizar o custo de produção](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](./optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das contas da Azure Cosmos em várias regiões](optimize-cost-regions.md)