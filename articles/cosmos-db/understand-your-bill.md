---
title: Entender a sua fatura do Azure Cosmos DB
description: Este artigo explica como compreender a sua fatura do Azure Cosmos DB com alguns exemplos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 9411cc42f2fbc12348b4d841174edbe75c584247
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890564"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Compreender a sua fatura do Azure Cosmos DB

Como um serviço de base de dados nativas da cloud totalmente gerido, o Azure Cosmos DB simplifica a faturação por cobrar apenas para o débito aprovisionado e consumidos de armazenamento. Não são as taxas de licença adicionais, hardware, os custos de utilitário, ou os custos do recurso em comparação comparados a no local ou alojado no IaaS alternativas. Ao considerar as várias capacidades de região do Azure Cosmos DB, o serviço de base de dados fornece uma redução substancial nos custos em comparação comparada o existente no local ou soluções de IaaS.

Com o Azure Cosmos DB, a faturação por hora com base no débito aprovisionado e o armazenamento consumido. Para o débito aprovisionado, a unidade de faturação é 100 RU/seg por hora, cobrada US $0.008 por hora, partindo do princípio de preços pública padrão, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). Para o armazenamento consumido, é faturado US $0,25 por 1 GB de armazenamento por mês, consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/). 

Este artigo utiliza alguns exemplos para ajudar a compreender os detalhes que consulte na fatura mensal. Os números apresentados nos exemplos podem ser diferentes se os seus contentores do Cosmos do Azure com uma quantidade diferente de débito aprovisionado, se eles abrangem várias regiões ou forem executadas para outro durante um período mais de um mês.

## <a name="billing-examples"></a>Exemplos de faturação

### <a name="billing-example---throughput-on-a-container-full-month"></a>Exemplo de faturação - débito num contentor (mês completo)

* Vamos supor que configurar um débito de 1.000 RU/seg num contêiner, e existe durante 24 horas * 30 dias do mês = 720 horas total.  

* 1.000 RU/seg é de 10 unidades de 100 RU/seg por hora por cada hora, os contentores existe (ou seja, 1000/100 = 10). 

* Multiplicar 10 unidades por hora pelo custo de US $0.008 (por 100 RU/seg por hora) = 0,08 us $ por hora. 

* Multiplicar 0,08 us $ por hora pelo número de horas no mês de é igual a horas de US $0,08 * 24 * 30 dias = us $57.60 durante o mês.  

* A fatura mensal total mostrará as unidades de 7.200 (de 100 RUs), que custará us $57.60.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Exemplo de faturação - débito num contentor (mês parcial)

* Vamos supor que vamos criar um contentor com débito aprovisionado de 2.500 RU/seg. O contentor reside durante 24 horas durante o mês (por exemplo, podemos eliminá-lo 24 horas depois de criarmos-lo).  

* Em seguida, vamos ver 600 unidades na fatura (2.500 RU/seg / 100 RU/seg/unidade * 24 horas). O custo será de US $4.80 (600 unidades * US $0.008/unidade).

* Cobrança total para o mês será de US $4.80.

### <a name="billing-rate-if-storage-size-changes"></a>Taxa de faturação, se alterar o tamanho de armazenamento

A capacidade de armazenamento é faturada por unidades da quantidade máxima horária de dados armazenados, em GB, durante o período de um mês. Por exemplo, se utilizou 100 GB de armazenamento durante metade do mês e 50 GB na segunda metade, ser-lhe-á cobrado o equivalente a 75 GB de armazenamento durante esse mês.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Quando o contentor ou um conjunto de contentores terem estado ativas menos de uma hora a taxa de faturação

É-lhe cobrada a tarifa fixa para cada hora, o contentor ou a base de dados existe, independentemente da utilização ou o contentor ou a base de dados esteve ativa menos de uma hora. Por exemplo, se cria um contentor ou a base de dados e a eliminar passados cinco minutos, a sua fatura incluirá uma hora.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Taxa de faturação, quando o débito num contentor ou da base de dados pode ser dimensionada para cima/para baixo

Se aumentar o débito aprovisionado às 9H30 a partir de 400 RU/seg para 1.000 RU/seg e, em seguida, reduzir o débito aprovisionado às 10:45 novamente para 400 RU/seg, será cobrado de duas horas de 1.000 RU/seg. 

Se aumentar o débito aprovisionado para um contentor ou um conjunto de contentores em 9H30 de 100 mil RU/seg para 200 mil RU/seg e, em seguida, inferior débito aprovisionado às 10:45 novamente para 100 mil RU/seg, ser-lhe-á cobrado de duas horas de 200 mil RU/seg.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Exemplo de faturação: vários contentores, cada um com o modo de débito dedicado

* Se criar uma conta do Cosmos do Azure na região E.U.A. Leste 2 com dois contentores com débito aprovisionado de 500 RU/seg e 700 RU/seg, respectivamente, teria um débito aprovisionado total de 1200 RU/seg.  

* Será cobrado 1200/100 * US $0.008 = US $0.096 / hora. 

* Se mudou de suas necessidades de débito e que aumentou a capacidade de cada contentor 500 RU/seg ao criar também um contentor ilimitado novo com 20.000 RU/seg, a sua capacidade aprovisionada geral seria 22.200 RU/seg (1.000 RU/seg + 1200 RU/seg + 20 000 RU/seg).  

* A fatura passa a ser: $0.008 x 222 = US $1.776 / hora. 

* Num mês de horas de 720 (24 horas * 30 dias), se por 500 horas de débito aprovisionado foi 1200 RU/seg e para as restantes horas 220 débito aprovisionado tiver sido 22.200 RU/seg, a fatura mensal mostra: 500 x 0.096 us $/ hora + 220 x 1.776 us $/ hora = us $438.72 / mês.

![Exemplo de fatura de débito de dedicado](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Exemplo de faturação: contentores com o modo de taxa de transferência partilhada

* Se criar uma conta do Cosmos do Azure na região E.U.A. Leste 2 com duas bases de dados do Cosmos do Azure (com um conjunto de contentores a taxa de transferência ao nível da base de dados de partilha) com o débito aprovisionado de 50 mil RU/seg e 70 mil RU/seg, respectivamente, teria um total aprovisionado débito de 120 mil RU/seg.  

* Será cobrado 1200 x US $0.008 = us $9.60 / hora. 

* Se as suas necessidades de débito alterado e aumentou a débito aprovisionado de cada base de dados por 10 mil RU/seg para cada base de dados e adicionar um novo contentor para a primeira base de dados com o modo de débito dedicado de 15 mil RU/seg para a base de dados de taxa de transferência partilhada, sua capacidade aprovisionada geral seria 155 mil RU/seg (60 mil RU/seg + 80 mil RU/seg + 15 mil RU/seg).  

* A fatura passa a ser: 1,550 * US $0.008 = us $12.40 / hora.  

* Num mês de horas de 720, se por 300 horas débito aprovisionado tiver sido 120 mil RU/seg e para as restantes horas 420 débito aprovisionado tiver sido 155 mil RU/seg, a fatura mensal irá mostrar: 300 x 9.60 us $/ hora + 420 x 12.40 us $/ hora = us $2,880 + US $5,208 = us $8,088 / mês. 

![Exemplo de fatura de débito de partilhado](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Exemplos de faturação com georreplicação e com vários mestres  

Pode adicionar/remover regiões do Azure em qualquer parte do mundo para a sua conta de base de dados do Azure Cosmos DB em qualquer altura. O débito que configurou para várias bases de dados do Azure Cosmos DB e contentores irá ser reservado em cada uma das regiões do Azure associado à conta de base de dados do Cosmos do Azure. Se a soma de débito aprovisionado (RU/seg) configurado em todas as bases de dados e contentores no seu banco de dados do Cosmos do Azure (aprovisionada por hora) de conta é T e o número de regiões do Azure associado à conta de base de dados é N, em seguida, o total débito aprovisionado para uma determinada hora, para o seu Cosmos Azure, conta de base de dados, (a) configurada com uma região de escrita única é igual a T x N RU/seg e (b) configurado com todas as regiões capazes de processar escritas é igual a T x (N + 1) RU/seg , respectivamente. Débito aprovisionado (região de escrita única) custa US $0.008/ hora, por 100 RU/seg e o débito aprovisionado com várias regiões graváveis (configuração de vários mestre) custa US $0.016 / por hora, por 100 RU/seg (consulte a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)). Se o seu único escrever região ou em várias regiões de escrita, o Azure Cosmos DB permite-lhe ler dados a partir de qualquer região.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Exemplo de faturação: escritas de região única conta do Cosmos do Azure de várias regiões,

Vamos supor que tem um contentor do Cosmos do Azure em E.U.A. oeste. O contentor é criado com débito de 10 mil RU/seg e armazena 1 TB de dados deste mês. Vamos supor que adiciona três regiões (E.U.A. leste, Europa do Norte e Ásia Oriental) à sua conta do Cosmos do Azure, cada um com o mesmo armazenamento e débito. A fatura mensal total será (assumindo 30 dias num mês). A fatura seria da seguinte forma: 

|**Item** |**Utilização (mensal)** |**Taxa de** |**Custo mensal** |
|---------|---------|---------|-------|
|Conta de débito para o contentor em E.U.A. Oeste      | 10 mil RU/seg * 24 * 30    |US $0.008 por 100 RU/seg por hora   |$576|
|Conta de débito para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental       | 3 * 10 mil RU/seg * 24 * 30    |US $0.008 por 100 RU/seg por hora  |$1,728|
|Conta de armazenamento para o contentor em E.U.A. Oeste      | 250 GB    |US $ 0,25/GB  |$62.50|
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental      | 3 * 250 GB    |US $ 0,25/GB  |$187.50|
|**Total**     |     |  |**$2,554**|

*Vamos também assumir que retira 100 GB de dados todos os meses de contentor em E.U.A. oeste para replicar dados para E.U. a leste, Europa do Norte e Ásia Oriental. É-lhe cobrada de acordo com as taxas de transferência de dados de saída.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Exemplo de faturação: escritas de região de transmissões de conta do Cosmos do Azure de várias regiões,

Vamos supor que criar um contentor do Cosmos do Azure em E.U.A. oeste. O contentor é criado com débito de 10 mil RU/seg e armazena 1 TB de dados deste mês. Suponhamos que adiciona três regiões (E.U.A. leste, Europa do Norte e Ásia Oriental), cada um com o mesmo armazenamento e débito e quiser ter a capacidade para escrever os contentores em todas as regiões à sua conta do Cosmos do Azure. A fatura mensal total será (supondo 30 dias num mês) da seguinte forma:

|**Item** |**Utilização (mensal)**|**Taxa de** |**Custo mensal** |
|---------|---------|---------|-------|
|Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)       | 10 mil RU/seg * 24 * 30    |US $0.016 por 100 RU/seg por hora    |$1,152 |
|Conta de débito para 3 regiões adicionais – E.U. a leste, Europa do Norte e Ásia Oriental (todas as regiões são graváveis)        | (3 + 1) * 10 mil RU/seg * 24 * 30    |US $0.016 por 100 RU/seg por hora   |$4,608 |
|Conta de armazenamento para o contentor em E.U.A. Oeste      | 250 GB    |US $ 0,25/GB  |$62.50|
|Conta de armazenamento para três regiões adicionais – E.U.A Leste, Europa do Norte e Ásia Oriental      | 3 * 250 GB    |US $ 0,25/GB  |$187.50|
|**Total**     |     |  |**$6,010**|

*Vamos também assumir que retira 100 GB de dados todos os meses de contentor em E.U.A. oeste para replicar dados para E.U. a leste, Europa do Norte e Ásia Oriental. É-lhe cobrada de acordo com as taxas de transferência de dados de saída.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Exemplo de faturação: Conta do Cosmos do Azure com débito vários mestre, ao nível da base de dados, incluindo o modo de débito dedicado para alguns contentores

Consideremos o exemplo seguinte, em que temos uma conta do Cosmos do Azure de várias regiões em que todas as regiões são gravável (configuração de vários mestre). Para simplificar, vamos supor que o tamanho de armazenamento permanecem constante e não alterar e omita-o aqui para manter o exemplo mais simples. O débito aprovisionado durante o mês diversificados da seguinte forma (supondo que 30 dias ou horas de 720): 

[0 e 100 horas]:  

* Criamos uma conta de Cosmos do Azure de três região (E.U.A. oeste, E.U.A. leste, Europa do Norte), onde todas as regiões sejam graváveis 

* Criámos uma base de dados (D1) com taxa de transferência partilhada 10 mil RU/seg 

* Criámos uma base de dados (D2) com taxa de transferência partilhada 30-mil RU/seg e  

* Criámos um contentor (C1) com débito dedicado 20 mil RU/seg 

[101 200 horas]:  

* Aumentámos verticalmente a base de dados (D1) para 50 mil RU/seg 

* Aumentámos verticalmente a base de dados (D2) para 70 mil RU/seg  

* Vamos eliminar contentor (C1)  

[201-300 horas]:  

* Criámos o contentor (C1) novamente com débito dedicado 20 mil RU/seg 

[301 400 horas]:  

* Removemos uma das regiões da conta do Cosmos do Azure (n. º de regiões graváveis agora é 2) 

* Podemos reduzidos verticalmente a base de dados (D1) para 10 mil RU/seg 

* Aumentámos verticalmente a base de dados (D2) para 80 mil RU/seg  

* Estamos a eliminar contentor (C1) 

[401-500 horas]:  

* Podemos reduzidos verticalmente a base de dados (D2) para 10 mil RU/seg  

* Criámos o contentor (C1) novamente com débito dedicado 20 mil RU/seg 

[501-700 horas]:  

* Aumentámos verticalmente a base de dados (D1) para 20 mil RU/seg  

* Aumentámos verticalmente a base de dados (D2) para 100 mil RU/seg  

* Estamos a eliminar contentor (C1)  

[701 720 horas]:  

* Podemos reduzidos verticalmente a base de dados (D2) para 50 mil RU/seg  

Visualmente as alterações no débito aprovisionado total durante as horas de 720 durante o mês são mostradas na figura abaixo: 

![Exemplo da vida real](./media/understand-your-bill/bill-example3.png)

A fatura mensal total será (supondo 30 dias/720 horas num mês) vai ser calculada da seguinte forma:

|**Horas**  |**RU/s** |**Item** |**Utilização (por hora)** |**Custo** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$1920  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$5,760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2,240  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6,720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$1,440   |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2,880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$1,280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3,840  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |$7,680  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Conta de débito para o contentor em E.U.A. oeste (todas as regiões são graváveis)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$224  |
| | |Conta de débito para 2 regiões adicionais: E.U.A. leste, Europa do Norte (todas as regiões são graváveis)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$224  |
|| |**Custo mensal total**  | |**$38,688**   |

## <a name="proactively-estimating-your-monthly-bill"></a>Estimar proativamente a fatura mensal  

Vejamos outro exemplo, o qual pretende calcular proativamente a sua fatura antes do final do mês. Pode calcular a sua fatura da seguinte forma:

|**Custo de armazenamento** | |
|----|----|
|Registo de média de tamanho (KB) |1 |
|Número de registos  |100,000,000  |
|Armazenamento total (GB)  |100 |
|Custo mensal por GB  |$0.25  |
|Custo mensal esperado para o armazenamento   |$25.00  |

<br>

|**Custo de débito** | | | |
|----|----|----|----|
|Tipo de Operação| Pedidos/seg| Média RU/pedido| RUs necessário|
|Escrita| 100 | 5 | 500|
|Leitura| 400| 1| 400|

Total de RU/s: 500 + 400 = 900 custo por hora: 900/100 * US $0.008 = US $0.072 esperado custo mensal de débito (assumindo 31 dias): $0.072 * 24 * 31 = us $53.57

**Custo mensal total**

Custo mensal total = custo mensal para armazenamento + custo mensal de débito de custo mensal Total = $25,00 + us $53.57 = us $78.57

*Os preços podem variar por região. Para preços atualizadas, veja a [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Capacidade de reserva de faturação com o Azure Cosmos DB

Capacidade de Cosmos DB reservada do Azure permite-lhe comprar o débito aprovisionado com antecedência (uma capacidade de reserva ou uma reserva) que pode ser aplicado a todas as bases de dados do Azure Cosmos DB e contentores (para qualquer modelo de dados ou de API) em todas as regiões do Azure. Uma vez que o preço do débito aprovisionado varia por região, ele ajuda a pensar em capacidade de reserva como um crédito monetário que comprou com desconto, que pode ser extraído de para o débito aprovisionado pelo respectivo preço em cada região. Por exemplo, digamos que tenha um Cosmos do Azure conta com um único contentor aprovisionado com 50 mil RU/seg e replicados globalmente duas regiões - E.U.A. leste e leste do Japão. Se escolher a opção pay as you go, pagaria:  

* na região E.U.A. Leste: para 50 mil RU/seg, a taxa de US $0.008 por 100 RU/seg nessa região 

* no Leste do Japão: para 50 mil RU/seg, a taxa de US $0.009 por 100 RU/seg nessa região

Total (sem capacidade de reserva) será (assumindo 30 dias ou horas de 720): 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**|**Montante faturado (por hora)**| **Montante faturado (mensal)**|
|----|----|----|----|----|
|EUA Leste|$0.008 |50 K|$4|$2,880 |
|Leste do Japão|$0.009 |50 K| $4.50 |$3,240 |
|Total|||$8.50|$6,120 |

Vamos considerar que comprou capacidade de reserva em vez disso. Pode adquirir capacidade de reserva para 100 mil RU/seg ao preço de US $56,064 durante um ano (com 20% de desconto) ou 6.40 us $ por hora. Ver preços de capacidade de reserva no [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Custo de débito (pay as you go): 100 000 RU/seg/100 * $0.008/hora * 8760 horas num ano = us $70,080 

* Custo de débito (com capacidade de reserva) us $70,080 com desconto de 20% = us $56,064 

O que efetivamente comprou é um crédito de 8 $ por hora, por 100 mil RU/seg, com o preço de lista na região E.U.A. leste, com o preço de US $6.40 por hora. Em seguida, pode desenhar para baixo desta reserva débito pré-pago à hora para a capacidade de débito aprovisionado em qualquer região do Azure global nos respetivos preços lista regionais definido para a sua subscrição. Neste exemplo, onde Aprovisiona 50 mil RU/seg cada nas regiões E.U.A. leste e leste do Japão, será capaz de desenhar us $8.00 que vale a pena do débito aprovisionado por hora e será cobrado o excesso de US $0,50 por hora (ou 360 us $/ mês). 

|**Região**| **Preço por hora por 100 RU/s**|**Unidades (RU/s)**| **Montante faturado (por hora)**| **Montante faturado (mensal)**|
|----|----|----|----|----|
|EUA Leste|$0.008 |50 K|$4|$2,880 |
|Leste do Japão|$0.009 |50 K| $4.50 |$3,240 |
|||"Pay-as-you-go"|$8.50|$6120|
|Capacidade de Reserva Comprada|US $0.0064 (20% de desconto) |100 RU/seg ou capacidade de 8 $ previamente adquiridas |-$8|-$5,760 |
|Fatura Líquida|||$0.50 |$360 |

## <a name="next-steps"></a>Próximos Passos

Em seguida, pode avançar para saber mais sobre otimização de custos no Azure Cosmos DB com os seguintes artigos:

* Saiba mais sobre [como Cosmos DB de modelo de preços é económico para clientes](total-cost-ownership.md)
* Saiba mais sobre [otimizar para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [otimizar o custo de débito](optimize-cost-throughput.md)
* Saiba mais sobre [otimizar o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [otimizar o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [otimizar o custo de contas do Cosmos do Azure de várias regiões](optimize-cost-regions.md)
