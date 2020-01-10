---
title: Perguntas frequentes sobre a taxa de transferência no modo de Azure Cosmos DB AutoPilot
description: Perguntas frequentes sobre o modo AutoPilot para bancos de dados Azure Cosmos DB e contêineres
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483313"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Perguntas frequentes sobre a taxa de transferência provisionada no modo AutoPilot Azure Cosmos DB (versão prévia)
Com o modo AutoPilot, Azure Cosmos DB gerenciará e dimensionará automaticamente as RU/s do seu contêiner ou banco de dados com base no uso. Este artigo responde às perguntas mais frequentes sobre o modo AutoPilot. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="is-autopilot-mode-supported-for-all-apis"></a>O modo AutoPilot tem suporte para todas as APIs?
Sim, o modo AutoPilot tem suporte para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>O modo AutoPilot tem suporte para contas de vários mestres?
Sim, o modo AutoPilot tem suporte para contas de vários mestres. O máximo de RU/s está disponível em cada região que é adicionada à conta Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Qual é o preço do AutoPilot?
Consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para obter detalhes. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Como fazer habilitar o AutoPilot para meus contêineres ou bancos de dados?
O modo AutoPilot pode ser habilitado em novos contêineres e bancos de dados criados usando o portal do Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Há suporte para CLI ou SDK para criar contêineres ou bancos de dados com o modo AutoPilot?
Atualmente, na versão de visualização, você só pode criar recursos com o modo de piloto automático do portal do Azure. O suporte para CLI e SDK ainda não está disponível.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Posso habilitar o AutoPilot em um contêiner ou banco de dados existente?
No momento, você pode habilitar o AutoPilot em novos contêineres e bancos de dados ao criá-los. O suporte para habilitar o modo AutoPilot em contêineres e bancos de dados existentes ainda não está disponível. Você pode migrar os contêineres existentes para um novo contêiner usando [Azure data Factory](../data-factory/connector-azure-cosmos-db.md) ou o [feed de alterações](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Posso desativar o modo AutoPilot em um contêiner ou banco de dados?
Sim, você pode desativar o AutoPilot alternando para a opção ' manual ' para a taxa de transferência provisionada. Na versão de visualização, depois de alternar do modo AutoPilot para o modo manual, você não pode habilitar o AutoPilot novamente para o mesmo recurso. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>O modo AutoPilot tem suporte para bancos de dados de taxa de transferência compartilhada?
Sim, o modo AutoPilot tem suporte para bancos de dados de taxa de transferência compartilhada. Para habilitar esse recurso, selecione modo de piloto automático e a opção **provisionar taxa de transferência** ao criar o banco de dados. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Qual é o número de coleções permitidas por banco de dados de produtividade compartilhado quando o AutoPilot está habilitado?
Para bancos de dados de taxa de transferência compartilhada com o modo AutoPilot habilitado, o número de coleções permitidas é: MIN (25, Max RU/s do banco de dados/1000). Por exemplo, se a taxa de transferência máxima do banco de dados for de 20.000 RU/s, o banco de dados poderá ter MIN (25, 20.000 RU/s/1000) = 20 coleções. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção máxima de RU/s?  
O limite de armazenamento em GB para cada máximo de RU/s é: máximo RU/s do banco de dados ou contêiner/100. Por exemplo, se o máximo de RU/s for 20.000 RU/s, o recurso poderá dar suporte a 200 GB de armazenamento. Consulte o artigo [limites do AutoPilot](provision-throughput-autopilot.md#autopilot-limits) para obter as opções máximas de ru/s e armazenamento disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontecerá se eu exceder o limite de armazenamento associado à minha taxa de transferência máxima?
Se o limite de armazenamento associado à taxa de transferência máxima do banco de dados ou contêiner for excedido, Azure Cosmos DB aumentará automaticamente a taxa de transferência máxima para a próxima camada mais alta que pode dar suporte a esse nível de armazenamento. Por exemplo, suponha que um banco de dados ou contêiner seja provisionado com a opção de taxa de transferência máxima de 4000 RU/s, que tem um limite de armazenamento de 50 GB. Se o armazenamento do recurso aumentar para 100 GB, o máximo de RU/s do banco de dados ou do contêiner será aumentado automaticamente para 20.000 RU/s, que pode dar suporte a até 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Com que rapidez o piloto automático será dimensionado e reduzido com base nos picos de tráfego?
O piloto automático escalará de forma instantânea ou reduzirá as RU/s no intervalo mínimo e máximo de RU/s, com base no tráfego de entrada. A cobrança é feita a uma granularidade de 1 hora, em que você é cobrado pelas RU/s mais altas em uma hora específica. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Posso especificar um valor de taxa de transferência máxima (RU/s) personalizado para o modo AutoPilot?
Atualmente, durante a versão de visualização, você pode selecionar entre [quatro opções](provision-throughput-autopilot.md#autopilot-limits) de taxa de transferência máxima (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Posso aumentar o máximo de RU/s (mover para uma camada superior) no banco de dados ou no contêiner? 
Sim. Na opção **dimensionar & configurações** para seu contêiner ou opção de **escala** para seu banco de dados, você pode selecionar um máximo de ru/s mais alto para o piloto automático. Essa é uma operação de expansão assíncrona que pode levar algum tempo para ser concluída (geralmente 4-6 horas, dependendo do RU/s selecionado), pois o serviço provisiona mais recursos para dar suporte à escala mais alta. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Posso reduzir o máximo de RU/s (mover para uma camada inferior) no banco de dados ou no contêiner?
Sim. Contanto que o armazenamento atual do banco de dados ou do contêiner esteja abaixo do [limite de armazenamento](#what-is-the-storage-limit-associated-with-each-max-rus-option) associado à camada máxima de ru/s para a qual você deseja reduzir, reduza o máximo de ru/s para essa camada. Por exemplo, se você selecionou 20.000 RU/s como o máximo de RU/s, poderá reduzir verticalmente o máximo de RU/s para 4000 RU/s se tiver menos de 50 GB de armazenamento (o limite de armazenamento associado a 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre as partições máximas RU/s e físicas?
Quando você seleciona primeiro o máximo de RU/s, o Azure Cosmos DB provisionar: Max RU/s/10.000 RU/s = n º de partições físicas. Cada [partição física](partition-data.md#physical-partitions) pode dar suporte a até 10.000 ru/s e armazenamento de 50 GB. À medida que o tamanho do armazenamento cresce, Azure Cosmos DB irá dividir automaticamente as partições para adicionar mais partições físicas para lidar com o aumento de armazenamento ou aumentar a camada máxima de RU/s se [o armazenamento exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O máximo de RU/s do banco de dados ou contêiner é dividido uniformemente em todas as partições físicas. Assim, a taxa de transferência total que qualquer partição física pode dimensionar é: máximo RU/s do banco de dados ou do contêiner/# partições físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se as solicitações de entrada excederem o máximo de RU/s do banco de dados ou do contêiner?
Se os RU/s consumidos geral excederem o máximo de RU/s do contêiner ou banco de dados, as solicitações que excederem o máximo de RU/s serão limitadas e retornarão um código de status 429. As solicitações que resultam em mais de 100% de utilização normalizada também serão limitadas. A utilização normalizada é definida como o máximo da utilização de RU/s em todas as partições físicas. Por exemplo, suponha que a taxa de transferência máxima seja de 20.000 RU/s e que você tenha duas partições físicas, P_1 e P_2, cada uma capaz de dimensionar para 10.000 RU/s. Em um determinado segundo, se P_1 tiver usado 6000 RUs e P_2 8000 RUs, a utilização normalizada será máxima (6000 RU/10.000 RU, 8000 RU/10.000 RU) = 0,8.

> [!NOTE]
> Os SDKs de cliente do Azure Cosmos DB e as ferramentas de importação de dados (Azure Data Factory, biblioteca de executores em massa) tentam automaticamente novamente em 429s, para que 429s ocasionais sejam bem. Um alto número de 429s sustentado pode indicar que você precisa aumentar o máximo de RU/s ou examinar sua estratégia de particionamento para uma [partição ativa](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Ainda é possível ver 429s (limitação/limitação de taxa) quando o AutoPilot está habilitado? 
Sim. É possível ver 429s em dois cenários. Primeiro, quando as RU/s consumidas geral excederem o máximo de RU/s do contêiner ou do banco de dados, o serviço limitará as solicitações de acordo. 

Em segundo lugar, se houver uma partição ativa, ou seja, um valor de chave de partição lógica que tenha uma quantidade desproporcionalmente maior de solicitações comparada a outros valores de chave de partição, é possível que a partição física subjacente exceda seu orçamento de RU/s. Como prática recomendada, para evitar partições ativas, [escolha uma boa chave de partição](partitioning-overview.md#choose-partitionkey) que resulte em uma distribuição uniforme do armazenamento e da taxa de transferência. 

Por exemplo, se você selecionar a opção de taxa de transferência máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro partições físicas, cada partição física poderá ser dimensionada para até 5000 RU/s. Se houver uma partição ativa em uma chave de partição lógica específica, você verá 429s quando a partição física subjacente na qual ela reside excede 5000 RU/s, ou seja, exceder 100% de utilização normalizada.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [habilitar o AutoPilot em um contêiner ou banco de dados Cosmos do Azure](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Saiba mais sobre os [benefícios do AutoPilot](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Saiba mais sobre [partições lógicas e físicas](partition-data.md).
