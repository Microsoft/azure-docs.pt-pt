---
title: Compreender a sua conta de DB Azure Cosmos
description: Este artigo explica como entender a sua conta de DB Azure Cosmos com alguns exemplos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 9384b974463c963cc130e7ca0d4a9ee815a92e53
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647718"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Compreender a fatura do Azure Cosmos DB

Como um serviço de base de dados totalmente gerido em nuvem, o Azure Cosmos DB simplifica a faturação cobrando apenas por entrada e armazenamento consumido. Não existem taxas de licença adicionais, hardware, custos de utilidade ou custos de facilidade em comparação com alternativas hospedadas no local ou iaaS. Ao considerar as capacidades multi-regiões do Azure Cosmos DB, o serviço de base de dados proporciona uma redução substancial dos custos em comparação com as soluções existentes no local ou iaaS.

Com o Azure Cosmos DB, você é faturado de hora em hora com base na entrada prevista e no armazenamento consumido. Para a entrada prevista, a unidade de faturação é de 100 RU/seg por hora, cobrada a $0,008 por hora, assumindo preços públicos padrão, ver a [página de Preços.](https://azure.microsoft.com/pricing/details/cosmos-db/) Para o armazenamento consumido, é cobrado $0,25 por 1 GB de armazenamento por mês, consulte a [página de Preços](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Este artigo utiliza alguns exemplos para ajudar a compreender os detalhes apresentados na fatura mensal. Os números mostrados nos exemplos poderão ser diferentes se os seus contentores do Azure Cosmos tiverem uma quantidade diferente de débito aprovisionado, se estiverem em várias regiões ou se forem executados durante um período diferente ao longo de um mês.

> [!NOTE]
> A faturação é para qualquer porção de hora de parede, não uma duração de 60 minutos.

## <a name="billing-examples"></a>Exemplos de faturação

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exemplo de faturação - entrada num recipiente (mês inteiro)

* Vamos supor que configura uma entrada de 1.000 RU/seg num recipiente, e existe por 24 horas * 30 dias para o mês = 720 horas no total.  

* 1.000 RU/seg é 10 unidades de 100 RU/seg por hora por cada hora que os contentores existem (isto é, 1.000/100 = 10). 

* Multiplicar 10 unidades por hora pelo custo de $0.008 (por 100 RU/sec por hora) = $0,08 por hora. 

* Multiplicar os $0,08 por hora pelo número de horas do mês equivale a $0,08 * 24 horas * 30 dias = $57,60 para o mês.  

* A fatura total mensal mostrará 7.200 unidades (de 100 RUs), que custarão $57,60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exemplo de faturação - entrada num recipiente (mês parcial)

* Vamos supor que criamos um recipiente com entrada aprovisionada de 2.500 RU/seg. O contentor vive 24 horas ao longo do mês (por exemplo, apagamo-lo 24 horas depois de o criarmos).  

* Em seguida, veremos 600 unidades na conta (2.500 RU/seg / 100 RU/sec/unidade * 24 horas). O custo será de $4,80 (600 unidades * $0,008/unidade).

* A conta total do mês será de $4,80.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de faturação se o tamanho do armazenamento mudar

A capacidade de armazenamento é faturada em unidades da quantidade máxima horária de dados armazenados, em GB, durante um período mensal. Por exemplo, se utilizasse 100 GB de armazenamento durante metade do mês e 50 GB para a segunda metade do mês, seria cobrado por um equivalente a 75 GB de armazenamento durante esse mês.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Taxa de faturação quando contentor ou um conjunto de contentores estão ativos por menos de uma hora

É cobrado a taxa fixa por cada hora que o recipiente ou base de dados existe, não importa o uso ou se o recipiente ou base de dados estiver ativo por menos de uma hora. Por exemplo, se criar um recipiente ou base de dados e o apagar 5 minutos depois, a sua conta incluirá uma hora.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Taxa de faturação quando a entrada num recipiente ou base de dados escala para cima/para baixo

Se aumentar a produção prevista às 9:30 da manhã de 400 RU/seg para 1.000 RU/sec e, em seguida, baixar a produção prevista às 10:45 da manhã para 400 RU/seg, será cobrado por duas horas de 1.000 RU/seg. 

Se aumentar a produção prevista para um recipiente ou um conjunto de contentores às 9:30 AM de 100-K RU/sec para 200-K RU/sec e, em seguida, baixar a produção prevista às 10:45 da manhã para 100-K RU/sec, você será cobrado por duas horas de 200 K RU/seg.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de faturação: vários contentores, cada um com modo de entrada previsto

* Se criar uma conta Azure Cosmos no Leste dos EUA 2 com dois contentores com produção de 500 RU/seg e 700 RU/seg, respectivamente, teria um total de produção de 1.200 RU/seg.  

* Seria cobrado 1.200/100 * $0,008 = $0,096/hora. 

* Se a sua entrada precisar de ser alterada, e tiver aumentado a capacidade de cada contentor em 500 RU/sec, ao mesmo tempo que cria um novo recipiente ilimitado com 20.000 RU/seg, a sua capacidade total seria de 22.200 RU/seg (1.000 RU/seg + 1.200 RU/seg + 20.000RU/seg).  

* A sua conta mudaria então para: $0.008 x 222 = $1.776/hora. 

* Num mês de 720 horas (24 horas * 30 dias), se durante 500 horas previstas a entrada fosse de 1.200 RU/sec e para as restantes 220 horas previstas era de 22.200 RU/sec, a sua fatura mensal mostra: 500 x $0,096/hora + 220 x $1.776/hora = $438,72/mês.

![Exemplo dedicado de fatura de entrada](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Exemplo de faturação: recipientes com modo de entrada partilhada

* Se criar uma conta Azure Cosmos no East US 2 com duas bases de dados Azure Cosmos (com um conjunto de contentores que partilham a produção ao nível da base de dados) com a produção prevista de 50-K RU/sec e 70-K RU/sec, respectivamente, teria um total de produção de 120 K RU/seg.  

* Seria cobrado 1200 x $0,008 = $9,60/hora. 

* Se a sua entrada precisar alterada e aumentar a entrada de cada base de dados em 10K RU/sec para cada base de dados, e adicionar um novo recipiente à primeira base de dados com o modo de entrada dedicado de 15-K RU/sec à sua base de dados de entrada partilhada, a sua capacidade total seria 155-K RU/seg (60 K RU/sec + 80 K RU/sec + 15 K RU/sec).  

* A sua conta mudaria então para: 1.550 * $0,008 = $12,40/hora.  

* Num mês de 720 horas, se durante 300 horas de entrada prevista fosse 120-K RU/sec e para as restantes 420 horas previstas de entrada foi de 155-K RU/sec, a sua fatura mensal mostrará: 300 x $9,60/hora + 420 x $12,40/hora = $2.880 + $5.208 = $8,008 / 

![Exemplo de fatura de entrada partilhada](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Exemplos de faturação com geo-replicação e multi-master  

Pode adicionar/remover regiões Azure em qualquer parte do mundo à sua conta de base de dados Azure Cosmos a qualquer momento. O contributo que configurapara várias bases de dados e contentores do Azure Cosmos será reservado em cada uma das regiões azure associadas à sua conta de base de dados Azure Cosmos. Se a soma de entrada aprovisionada (RU/sec) configurada em todas as bases de dados e contentores dentro da sua conta de base de dados Azure Cosmos (provisionada por hora) for T e o número de regiões Azure associadas à sua conta de base de dados for N, em seguida, o total de produção previsto durante uma determinada hora, para a sua conta de base de dados Azure Cosmos, (a) configurado com uma única região de escrita é igual a T x N RU/sec e (b) configurado com todas as regiões capazes de processar escritos é igual a T x (N+1) RU/seg, respectivamente. O produto provisionado (região de escrita única) custa $0.008/hora por 100 RU/sec e a provisão com múltiplas regiões writáveis (config multi-master) custa $0,016/hora por 100 RU/sec (ver [página de preços).](https://azure.microsoft.com/pricing/details/cosmos-db/) Seja a sua região de escrita única, ou várias regiões de escrita, o Azure Cosmos DB permite-lhe ler dados de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de faturação: conta multi-região Azure Cosmos, uma região única escreve

Vamos supor que tem um contentor Azure Cosmos no Oeste dos EUA. O recipiente é criado com a entrada 10K RU/seg e armazena 1 TB de dados este mês. Vamos supor que você adiciona três regiões (Leste dos EUA, Norte da Europa e Ásia Oriental) à sua conta Azure Cosmos, cada uma com o mesmo armazenamento e entrada. A sua conta mensal total será (assumindo 30 dias num mês). A sua conta seria a seguinte: 

|**Item** |**Utilização (mês)** |**Tarifa** |**Custo Mensal** |
|---------|---------|---------|-------|
|Fatura de entrada para contentor nos EUA Ocidentais      | 10K RU/seg * 24 * 30    |$0.008 por 100 RU/seg por hora   |$576|
|Fatura de entrada para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental       | 3 * 10K RU/seg * 24 * 30    |$0.008 por 100 RU/seg por hora  |$1.728|
|Conta de armazenamento de contentores no Oeste dos EUA      | 250 GB    |$0.25/GB  |$62,50|
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental      | 3 * 250 GB    |$0.25/GB  |$187,50|
|**Total**     |     |  |**$2.554**|

*Vamos também assumir que você retira 100 GB de dados todos os meses do contentor nos EUA Ocidentais para replicar dados para os EUA Orientais, Norte da Europa e Ásia Oriental. É cobrado por uma saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de faturação: conta multi-região Azure Cosmos, escreve várias regiões

Vamos supor que cria um contentor Azure Cosmos no Oeste dos EUA. O recipiente é criado com a entrada 10K RU/seg e armazena 1 TB de dados este mês. Vamos supor que você adiciona três regiões (Leste dos EUA, Norte da Europa e Ásia Oriental), cada uma com o mesmo armazenamento e entrada e você quer a capacidade de escrever para os recipientes em todas as regiões associadas à sua conta Azure Cosmos. A sua conta mensal total será (assumindo 30 dias num mês) da seguinte forma:

|**Item** |**Utilização (mês)**|**Tarifa** |**Custo Mensal** |
|---------|---------|---------|-------|
|Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)       | 10K RU/seg * 24 * 30    |$0.016 por 100 RU/seg por hora    |$1.152 |
|Fatura de entrada para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental (todas as regiões são repreensíveis)        | (3 + 1) * 10K RU/seg * 24 * 30    |$0.016 por 100 RU/seg por hora   |$4.608 |
|Conta de armazenamento de contentores no Oeste dos EUA      | 250 GB    |$0.25/GB  |$62,50|
|Conta de armazenamento para 3 regiões adicionais - Leste dos EUA, Norte da Europa e Ásia Oriental      | 3 * 250 GB    |$0.25/GB  |$187,50|
|**Total**     |     |  |**$6.010**|

*Vamos também assumir que você retira 100 GB de dados todos os meses do contentor nos EUA Ocidentais para replicar dados para os EUA Orientais, Norte da Europa e Ásia Oriental. É cobrado por uma saída de acordo com as taxas de transferência de dados.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de faturação: Conta Azure Cosmos com multi-master, entrada de nível de base de dados incluindo modo de entrada dedicado para alguns recipientes

Vamos considerar o seguinte exemplo, onde temos uma conta multi-região Azure Cosmos onde todas as regiões são writáveis (config multi-mestre). Para a simplicidade, assumimos que o tamanho do armazenamento permanece constante e não muda e omite-o aqui para manter o exemplo mais simples. A entrada prevista durante o mês variou da seguinte forma (assumindo 30 dias ou 720 horas): 

[0-100 horas]:  

* Criámos uma conta de três regiões Azure Cosmos (Oeste dos EUA, Leste dos EUA, Norte da Europa), onde todas as regiões são repreensíveis 

* Criámos uma base de dados (D1) com a entrada partilhada 10K RU/sec 

* Criámos uma base de dados (D2) com um passe partilhado 30-K RU/sec e  

* Criámos um recipiente (C1) com entrada dedicada 20 K RU/seg 

[101-200 horas]:  

* Dimensionamos a base de dados (D1) para 50 K RU/seg 

* Dimensionamos a base de dados (D2) para 70 K RU/seg  

* Apagamos o recipiente (C1)  

[201-300 horas]:  

* Criamos um recipiente (C1) novamente com o efeito dedicado 20 K RU/seg 

[301-400 horas]:  

* Removemos uma das regiões da conta Azure Cosmos (# das regiões de mandato é agora 2) 

* Reduzimos a base de dados (D1) para 10K RU/seg 

* Dimensionamos a base de dados (D2) para 80 K RU/seg  

* Apagamos o recipiente (C1) novamente 

[401-500 horas]:  

* Reduzimos a base de dados (D2) para 10K RU/seg  

* Criamos um recipiente (C1) novamente com o efeito dedicado 20 K RU/seg 

[501-700 horas]:  

* Dimensionamos a base de dados (D1) para 20 K RU/seg  

* Dimensionamos a base de dados (D2) para 100 K RU/seg  

* Apagamos o recipiente (C1) novamente  

[701-720 horas]:  

* Reduzimos a base de dados (D2) para 50 K RU/seg  

Visualmente, as alterações na produção total prevista durante 720 horas para o mês são mostradas no valor abaixo: 

![Exemplo da vida real](./media/understand-your-bill/bill-example3.png)

A fatura total mensal será calculada (assumindo 30 dias/720 horas num mês) será calculada da seguinte forma:

|**Horas**  |**RU/S** |**Item** |**Utilização (hora a hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: - |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5.760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2.240  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6.720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: - |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1.440   |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1.280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: - |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3.840  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7.680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: - |Fatura de entrada para contentor estoque nos EUA Ocidentais (todas as regiões são repreensíveis)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Fatura de entrada para 2 regiões adicionais: Leste dos EUA, Norte da Europa (todas as regiões são repreensíveis)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Custo Mensal Total**  | |**$38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Exemplos de faturação com contas de nível livre
Com o nível gratuito Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento na sua conta gratuitamente, aplicado ao nível da conta. Qualquer RU/s e armazenamento para além de 400 RU/s e 5 GB serão cobrados às taxas de preços regulares por página de preços. Na conta, não verá um item de linha ou carga gratuito para os 400 Ru/s gratuitos e 5 GB, apenas o RU/s e armazenamento para além do que é coberto por nível livre. O 400 RU/s aplica-se a qualquer tipo de RU/s - produção, escala automática e multi-master.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Exemplo de faturação - contentor ou base de dados com entrada aprovisionada
- Vamos supor que criamos uma base de dados ou um contentor numa conta de nível livre com 400 RU/s e 5 GB de armazenamento.
- Sua conta não mostrará qualquer custo por este recurso. O seu custo horário e mensal será $0.
- Agora, suponhamos na mesma conta, adicionamos outra base de dados ou contentor com 1000 RU/s e 10 GB de armazenamento.
- A sua conta irá agora mostrar uma taxa para os 1000 RU/s e 10 GB de armazenamento. 

### <a name="billing-example---container-with-autoscale-throughput"></a>Exemplo de faturação - recipiente com entrada em escala automática
- Vamos supor, numa conta de nível livre, que criamos um recipiente com escala automática ativada, com um máximo de RU/s de 4000 RU/s. Este recurso escalará automaticamente entre 400 RU/s - 4000 RU/s. 
- Suponha que na hora 1 a hora 10, o recurso está no mínimo de 400 RU/s. Durante a hora 11, o recurso escala até 1000 RU/s e depois volta para baixo para 400 RU/s dentro de uma hora.
- Nas horas 1 a 10, será cobrado $0 por entrada, já que os 400 RU/s foram cobertos por nível livre. 
- Na hora 11, você será cobrado para um eficaz 1000 RU/s - 400 RU/s = 600 RU/s, já que este é o RU/s mais alto da hora. Esta será de 6 unidades de 100 RU/s por hora, por isso o custo total de produção da hora será de 6 unidades * $0,012 = $0,072. 
- Qualquer armazenamento para além dos primeiros 5 GB será faturado a taxas normais de armazenamento. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Exemplo de faturação - conta de região de escrita única e multi-região
- Vamos supor, numa conta de nível livre, que criamos uma base de dados ou um contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta para 3 regiões, e temos uma conta de um único mestre (região de escrita única).
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um eficaz 3200 RU/s (32 unidades) de entrada provisionada à taxa única da região de escrita e 25 GB de armazenamento.
- O custo mensal para ru/s seria: 32 unidades * $0,008 * 24 horas * 31 dias = $190,46. O custo mensal para o armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $190.46 + $6,25 = $196,71.
- Nota: se o preço unitário para RU/s ou armazenamento diferir nas regiões, o nível livre 400 RU/s e 5 GB refletirão as taxas da região em que a conta foi criada.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Exemplo de faturação - conta multi-região, multi-master (região de escrita múltipla)

Este exemplo reflete [preços multi-master](https://azure.microsoft.com/pricing/details/cosmos-db/) para contas criadas após 1 de dezembro de 2019. 
- Vamos supor, numa conta de nível livre, que criamos uma base de dados ou um contentor com 1200 RU/s e 10 GB de armazenamento. Replicamos a conta para 3 regiões, e temos uma conta multi-master (região de escrita múltipla). 
- No total, sem nível livre, seríamos cobrados por 3 * 1200 RU/s = 3600 RU/s e 3 * 10 GB = 30 GB de armazenamento.
- Com o desconto de nível livre, após a remoção de 400 RU/s e 5 GB de armazenamento, seremos cobrados para um eficaz 3200 RU/s (32 unidades) de entrada provisionada à taxa de região de escrita múltipla e 25 GB de armazenamento.
- O custo mensal para ru/s seria: 32 unidades * $0,016 * 24 horas * 31 dias = $380,93. O custo mensal para o armazenamento seria: 25 GB * 0,25 / GB = $6,25. O custo total seria $380.93 + $6,25 = $387,18.

## <a name="proactively-estimating-your-monthly-bill"></a>Estimando proativamente a sua conta mensal  

Vamos considerar outro exemplo, onde quer estimar proactivamente a sua conta antes do final do mês. Pode estimar a sua conta da seguinte forma:

|**Custo de Armazenamento** | |
|----|----|
|Tamanho do recorde avg (KB) |1 |
|Número de Registos  |100,000,000  |
|Armazenamento Total (GB)  |100 |
|Custo mensal por GB  |$0,25  |
|Custo mensal esperado para armazenamento   |$25,00  |

<br>

|**Custo de entrada** | | | |
|----|----|----|----|
|Tipo de Operação| Pedidos/seg| Avg. RU/pedido| RUs necessários|
|Escrita| 100 | 5 | 500|
|Leitura| 400| 1| 400|

Total RU/seg: 500 + 400 = 900 Custo horária: 900/100 * $0,008 = $0,072 Custo mensal esperado para a produção (assumindo 31 dias): $0,072 * 24 * 31 = $53,57

**Custo Mensal Total**

Custo Mensal Total = Custo Mensal para Armazenamento + Custo Mensal do Custo Mensal de Produção = $25,00 + $53,57 = $78,57

*Os preços podem variar por região. Para obter preços atualizados, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Faturação com capacidade reservada à Azure Cosmos DB

A capacidade reservada da Azure Cosmos DB permite-lhe adquirir o fornecimento de entrada com antecedência (uma capacidade reservada ou uma reserva) que pode ser aplicada a todas as bases de dados e contentores da Azure Cosmos (para qualquer API ou modelo de dados) em todas as regiões do Azure. Uma vez que o preço de entrada previsto varia por região, ajuda a pensar na capacidade reservada como um crédito monetário que adquiriu com desconto, que pode ser extraído do que é previsto para o fornecimento ao respetivo preço em cada região. Por exemplo, digamos que tem uma conta Azure Cosmos com um único contentor aprovisionado com 50-K RU/sec e globalmente replicado duas regiões - Leste dos EUA e Japão Leste. Se escolher a opção pay-as-you-go, pagará:  

* no Leste dos EUA: para 50-K RU/sec à taxa de $0.008 por 100 RU/sec naquela região 

* no Japão Leste: para 50-K RU/sec à taxa de $0.009 por 100 RU/sec naquela região

A sua conta total (sem capacidade reservada) seria (assumindo 30 dias ou 720 horas): 

|**Região**| **Preço hortário por 100 RU/s**|**Unidades (RU/s)**|**Valor faturado (hora a hora)**| **Valor Faturado (mensalmente)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.008 |50 K|$4|$2.880 |
|Leste do Japão|$0.009 |50 K| $4,50 |$3.240 |
|Total|||$8,50|$6.120 |

Vamos considerar que comprou a capacidade reservada. Pode comprar uma capacidade reservada por 100-K RU/seg ao preço de $56.064 por um ano (com desconto de 20%), ou $6,40 por hora. Consulte os preços de capacidade reservados na [página de Preços).](https://azure.microsoft.com/pricing/details/cosmos-db/)  

* Custo do mput (pay-as-you-go): 100.000 RU/sec/100 * $0.008/hora * 8760 horas num ano = $70.080 

* Custo da entrada (com capacidade reservada) $70.080 descontados a 20% = $56.064 

O que comprou efetivamente é um crédito de 8 dólares por hora, por 100 K RU/sec usando o preço da lista no Leste dos EUA, pelo preço de $6,40 por hora. Em seguida, pode retirar desta reserva de produção pré-paga numa base horária para a capacidade de produção prevista em qualquer região global do Azure, a preços de lista regional respetivos definidos para a sua subscrição. Neste exemplo, onde você disponibiliza 50 K RU/sec cada um nos EUA Leste, e Japão Leste, você será capaz de obter $8,00 de produção prevista por hora, e será cobrado o excesso de $0,50 por hora (ou $360/mês). 

|**Região**| **Preço hortário por 100 RU/s**|**Unidades (RU/s)**| **Valor faturado (hora a hora)**| **Valor Faturado (mensalmente)**|
|----|----|----|----|----|
|E.U.A. Leste|$0.008 |50 K|$4|$2.880 |
|Leste do Japão|$0.009 |50 K| $4,50 |$3.240 |
|||Pay-as-you-go|$8,50|$6120|
|Capacidade Reservada Adquirida|$0.0064 (desconto de 20%) |100 RU/seg ou $8 capacidade pré-comprada |-$8|-$5.760 |
|Fatura Líquida|||$0.50 |$360 |

## <a name="next-steps"></a>Passos Seguintes

Em seguida, poderá proceder a conhecer a otimização de custos em Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre como o modelo de [preços da Cosmos DB é rentável para os clientes](total-cost-ownership.md)
* Saiba mais sobre [Otimização para desenvolvimento e testes](optimize-dev-test.md)
* Saiba mais sobre [otimização do custo de entrada](optimize-cost-throughput.md)
* Saiba mais sobre [otimização do custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo das leituras e dos escritos](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo das consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo das contas multi-regiões da Azure Cosmos](optimize-cost-regions.md)
