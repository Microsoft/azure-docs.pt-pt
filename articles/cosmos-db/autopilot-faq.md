---
title: Perguntas frequentes sobre a entrada no modo piloto automático da Azure Cosmos DB
description: Perguntas frequentes sobre o modo piloto automático para bases de dados e contentores da Azure Cosmos DB
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483313"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Perguntas frequentes sobre a entrada aprovisionada no modo de piloto automático Do MB D (Pré-visualização)
Com o modo piloto automático, o Azure Cosmos DB irá gerir e escalar automaticamente os RU/s do seu recipiente ou base de dados com base na utilização. Este artigo responde a perguntas comumente feitas sobre o modo piloto automático. 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="is-autopilot-mode-supported-for-all-apis"></a>O modo piloto automático é suportado para todas as APIs?
Sim, o modo piloto automático é suportado para todas as APIs: Core (SQL), Gremlin, Table, Cassandra e API para MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>O modo piloto automático é suportado para contas multi-master?
Sim, o modo piloto automático é suportado para contas multi-master. Os max RU/s estão disponíveis em cada região que é adicionado à conta Cosmos. 

### <a name="what-is-the-pricing-for-autopilot"></a>Qual é o preço do piloto automático?
Consulte a página de [preços](https://azure.microsoft.com/pricing/details/cosmos-db/) do Azure Cosmos DB para mais detalhes. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Como posso ativar o piloto automático para os meus contentores ou bases de dados?
O modo piloto automático pode ser ativado em novos recipientes e bases de dados criados através do portal Azure. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Existe suporte CLI ou SDK para criar contentores ou bases de dados com modo piloto automático?
Atualmente, no lançamento de pré-visualização, só é possível criar recursos com o modo piloto automático a partir do portal Azure. O suporte para CLI e SDK ainda não está disponível.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Posso ativar o piloto automático num contentor existente ou numa base de dados?
Atualmente, pode ativar o piloto automático em novos contentores e bases de dados ao criá-los. O suporte para ativar o modo de piloto automático nos contentores e bases de dados existentes ainda não está disponível. Pode migrar os contentores existentes para um novo contentor utilizando a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) ou [alterar o feed](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Posso desligar o modo piloto automático num contentor ou numa base de dados?
Sim, pode desligar o piloto automático mudando para a opção 'Manual' para a entrada aprovisionada. Na versão de pré-visualização, depois de mudar do modo piloto automático para o modo manual, não pode voltar a ativar o piloto automático para o mesmo recurso. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>O modo autopiloto é suportado para bases de dados de entrada partilhadas?
Sim, o modo piloto automático é suportado para bases de dados de entrada partilhadas. Para ativar esta funcionalidade, selecione o modo de piloto automático e a opção de **entrada de provisionamento** ao criar a base de dados. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Qual é o número de coleções permitidas por base de dados de entrada partilhada quando o piloto automático está ativado?
Para bases de dados de entradas partilhadas com modo piloto automático ativado, o número de coleções permitidas é: MIN(25, Max RU/s de base de dados / 1000). Por exemplo, se a entrada máxima da base de dados for de 20.000 RU/s, a base de dados pode ter MIN(25, 20.000 RU/s / 1000) = 20 coleções. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Qual é o limite de armazenamento associado a cada opção max RU/s?  
O limite de armazenamento em GB para cada ru/s max é: Max RU/s de base de dados ou contentor / 100. Por exemplo, se o max RU/s for de 20.000 RU/s, o recurso pode suportar 200 GB de armazenamento. Consulte o artigo [limites](provision-throughput-autopilot.md#autopilot-limits) de piloto automático para as opções de max RU/s e armazenamento disponíveis. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>O que acontece se eu exceder o limite de armazenamento associado à minha entrada máxima?
Se o limite de armazenamento associado à entrada máxima da base de dados ou do contentor for ultrapassado, o Azure Cosmos DB aumentará automaticamente a potência máxima para o nível mais alto que pode suportar esse nível de armazenamento. Por exemplo, suponha que uma base de dados ou recipiente esteja aprovisionado com a opção de entrada máxima 4000 RU/s, que tem um limite de armazenamento de 50 GB. Se o armazenamento do recurso aumentar para 100 GB, o max RU/s da base de dados ou do contentor será automaticamente aumentado para 20.000 RU/s, que pode suportar até 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>A rapidez com que o piloto automático vai escalar para cima e para baixo com base em picos de tráfego?
O piloto automático escalará instantaneamente para cima ou escalará o RU/s dentro do alcance mínimo e máximo de RU/s, com base no tráfego de entrada. A faturação é feita numa granularidade de 1 hora, onde é cobrado pelo RU/s mais alto numa hora particular. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Posso especificar um valor de potência máxima (RU/s) personalizado para o modo piloto automático?
Atualmente, durante o lançamento da pré-visualização, pode selecionar entre [quatro opções](provision-throughput-autopilot.md#autopilot-limits) para a potência máxima (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Posso aumentar o max RU/s (passar para um nível mais alto) na base de dados ou no contentor? 
Sim. A partir da opção **Escala & Definições** para o seu recipiente, ou opção **Escala** para a sua base de dados, pode selecionar um RU/s max mais elevado para piloto automático. Trata-se de uma operação de escala assíncrona que pode demorar algum tempo a ser concluída (normalmente 4-6 horas, dependendo do RU/s selecionado) uma vez que o serviço disponibiliza mais recursos para suportar a escala mais elevada. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Posso reduzir o max RU/s (passar para um nível inferior) na base de dados ou no recipiente?
Sim. Enquanto o armazenamento atual da base de dados ou do contentor estiver abaixo do limite de [armazenamento](#what-is-the-storage-limit-associated-with-each-max-rus-option) associado ao nível máximo ru/s a que pretende reduzir, pode reduzir o max RU/s para esse nível. Por exemplo, se tiver selecionado 20.000 RU/s como o max RU/s, pode reduzir o max RU/s a 4000 RU/s se tiver menos de 50 GB de armazenamento (o limite de armazenamento associado a 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Qual é o mapeamento entre o max RU/s e as divisórias físicas?
Quando selecionar pela primeira vez o max RU/s, o Azure Cosmos DB fornecerá: Max RU/s / 10.000 RU/s = # de divisórias físicas. Cada [partição física](partition-data.md#physical-partitions) pode suportar até 10.000 RU/s e 50 GB de armazenamento. À medida que o tamanho do armazenamento cresce, o Azure Cosmos DB dividirá automaticamente as divisórias para adicionar mais divisórias físicas para lidar com o aumento do armazenamento, ou aumentar o nível máximo de RU/s se o armazenamento [exceder o limite associado](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

O Max RU/s da base de dados ou contentor está dividido uniformemente em todas as divisórias físicas. Assim, a entrada total que qualquer partição física pode escalar é: Max RU/s de base de dados ou contentor / # divisórias físicas. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>O que acontece se os pedidos de entrada excederem o max RU/s da base de dados ou do contentor?
Se o RU/s globalmente consumido exceder o max RU/s do recipiente ou base de dados, os pedidos que excedam o max RU/s serão estrangulados e devolverão um código de estado 429. Os pedidos que resultem em utilização mais de 100% normalizada também serão acelerados. A utilização normalizada é definida como o máximo da utilização ru/s em todas as divisórias físicas. Por exemplo, suponha que a sua potência máxima seja de 20.000 RU/s e tenha duas divisórias físicas, P_1 e P_2, cada uma capaz de escalar para 10.000 RU/s. Num segundo, se P_1 utilizou 6000 RUs, e P_2 8000 RUs, a utilização normalizada é MAX (6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> O cliente DaMS e ferramentas de importação de dados da Azure Cosmos DB (Azure Data Factory, biblioteca de executora a granel) retry automaticamente em 429s, por isso ocasionalmente 429s estão bem. Um número elevado sustentado de 429s pode indicar que precisa de aumentar o max RU/s ou rever a sua estratégia de partição para uma [partição quente](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Ainda é possível ver 429s (limitação de aceleração/taxa) quando o piloto automático está ativado? 
Sim. É possível ver 429s em dois cenários. Em primeiro lugar, quando o RU/s consumido globalmente exceder o max RU/s do recipiente ou base de dados, o serviço irá acelerar os pedidos em conformidade. 

Em segundo lugar, se houver uma divisão quente, isto é, um valor-chave de partição lógica que tenha um montante desproporcionalmente maior de pedidos em comparação com outros valores-chave de partição, é possível que a partição física subjacente exceda o seu orçamento RU/s. Como uma boa prática, para evitar divisórias quentes, escolha uma boa chave de [partição](partitioning-overview.md#choose-partitionkey) que resulte numa distribuição uniforme tanto do armazenamento como da entrada. 

Por exemplo, se selecionar a opção de entrada máxima de 20.000 RU/s e tiver 200 GB de armazenamento, com quatro divisórias físicas, cada partição física pode ser autodimensionada até 5000 RU/s. Se houver uma partição quente numa determinada chave de partição lógica, verá 429s quando a partição física subjacente em que reside excede 5000 RU/s, ou seja, excede 100% de utilização normalizada.

## <a name="next-steps"></a>Passos seguintes

* Saiba como ativar o piloto automático num contentor ou base de [dados Azure Cosmos](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Conheça os [benefícios do piloto automático.](provision-throughput-autopilot.md#benefits-of-autopilot-mode)
* Saiba mais sobre [divisórias lógicas e físicas.](partition-data.md)
