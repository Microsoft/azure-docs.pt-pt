---
title: 'Tutorial: Migrar o MongoDB offline para Azure Cosmos DB API para o MongoDB'
titleSuffix: Azure Database Migration Service
description: Migrar de MongoDB no local para Azure Cosmos DB API para MongoDB offline, usando o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443872"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Tutorial: Migrar o MongoDB para a Azure Cosmos DB API para o MongoDB offline

Utilize o Azure Database Migration Service para realizar uma migração única e offline de bases de dados de um local ou exemplo em nuvem de MongoDB para o API AZure Cosmos DB para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

Neste tutorial, migra um conjunto de dados em MongoDB que está hospedado numa máquina virtual Azure. Ao utilizar o Azure Database Migration Service, migra o conjunto de dados para o Azure Cosmos DB API para o MongoDB. Se ainda não tiver uma fonte MongoDB configurada, consulte [instalar e configurar o MongoDB num Windows VM em Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Complete os passos [de pré-migração,](../cosmos-db/mongodb-pre-migration.md) tais como estimar a produção e escolher uma chave de partição.
* [Crie uma conta para a Azure Cosmos DB API para a MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o Azure Resource Manager. Este modelo de implementação fornece conectividade site-to-site aos seus servidores de origem no local, utilizando a [Azure ExpressRoute](../expressroute/expressroute-introduction.md) ou [a VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [documentação da Rede Virtual Azure,](../virtual-network/index.yml)especialmente os artigos de "arranque rápido" com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL ou ponto final da Azure Cosmos DB)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do seu grupo de segurança de rede (NSG) para a sua rede virtual não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para obter mais informações, veja [Filtrar o tráfego de rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor MongoDB de origem, que por padrão é a porta TCP 27017.
* Quando estiver a utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.

## <a name="configure-the-server-side-retry-feature"></a>Configure a função de reagem ao lado do servidor

Você pode beneficiar das capacidades de governação de recursos se você migrar de MongoDB para Azure Cosmos DB. Com estas capacidades, pode utilizar plenamente as suas unidades de pedido (RU/s) de produção. A Azure Cosmos DB poderá acelerar um determinado pedido do Serviço de Migração da Base de Dados no decurso da migração, se esse pedido exceder o RU/s abastado pelo contentor. Então o pedido tem de ser novamente julgado.

O Serviço de Migração de Bases de Dados é capaz de realizar recauchutagens. É importante entender que o tempo de ida e volta envolvido no salto de rede entre o Serviço de Migração da Base de Dados e o DB Azure Cosmos afeta o tempo de resposta global desse pedido. A melhoria do tempo de resposta dos pedidos acelerados pode encurtar o tempo total necessário para a migração.

A funcionalidade de Retry side do servidor da Azure Cosmos DB permite ao serviço intercetar códigos de erro do acelerador e tentar novamente com um tempo de ida e volta muito mais baixo, melhorando drasticamente os tempos de resposta do pedido.

Para utilizar o Servidor Side Retry, no portal DB Azure Cosmos, selecione **Features**  >  **Server Side Retry**.

![Screenshot que mostra onde encontrar a função de Retry do lado do servidor.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Se a função estiver desativada, selecione **Ative**.

![Screenshot que mostra como ativar a retrip lateral do servidor.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Screenshot que mostra subscrições do portal.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Screenshot que mostra fornecedores de recursos.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Screenshot que mostra como registar o fornecedor de recursos.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Captura de ecrã a mostrar o Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Screenshot que mostra como criar uma instância do Serviço de Migração de Bases de Dados Azure.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. No **Serviço de Migração Create**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure. 

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à origem da instância MongoDB e à conta DB do Azure Cosmos.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte [Criar uma rede virtual utilizando o portal Azure.](../virtual-network/quick-create-portal.md)

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Screenshot que mostra configurações de configuração para o exemplo do Serviço de Migração da Base de Dados Azure.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar o serviço, localize-o dentro do portal Azure e abra-o. Em seguida, criar um novo projeto de migração.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Screenshot que mostra como localizar todas as instâncias do Serviço de Migração da Base de Dados Azure.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. No ecrã dos **Serviços de Migração da Base de Dados Azure,** procure o nome da instância do Serviço de Migração da Base de Dados Azure que criou e, em seguida, selecione o caso.

3. Selecione **+ Novo Projeto de Migração.**

4. No **novo projeto de migração**, especifique um nome para o projeto, e na caixa de texto do tipo servidor **Source,** selecione **MongoDB**. Na caixa de texto **do tipo do servidor Target,** selecione **CosmosDB (MongoDB API)** e, em seguida, para **Escolher o tipo de atividade**, selecione migração de **dados offline**. 

    ![Screenshot que mostra opções de projeto.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã de **detalhes da Fonte,** especifique os detalhes da ligação para o servidor MongoDB de origem.

   > [!IMPORTANT]
   > O Azure Database Migration Service não suporta a Azure Cosmos DB como fonte.

    Existem três modos para ligar a uma fonte:
   * **Modo standard**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, número de porta e credenciais de conexão.
   * **Modo de cadeia de ligação**, que aceita uma cadeia de ligação MongoDB como descrito no [formato URI de cadeia de ligação](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento Azure,** que aceita um RECIPIENTE DE BLOB SAS URL. Select **Blob contém depósitos BSON** se o recipiente blob tiver depósitos BSON produzidos pela [ferramenta de bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)MongoDB . Não selecione esta opção se o recipiente contiver ficheiros JSON.

     Se selecionar esta opção, certifique-se de que o fio de ligação da conta de armazenamento aparece no seguinte formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Pode encontrar esta cadeia de ligação SAS do recipiente blob no explorador de armazenamento Azure. A criação do SAS para o recipiente em causa fornece-lhe o URL no formato solicitado.
     
     Além disso, com base nas informações de despejo de tipo no Azure Storage, tenha em mente o seguinte:

     * Para os depósitos de BSON, os dados dentro do recipiente blob devem estar no formato bsondump. Coloque os ficheiros de dados em pastas com o nome das bases de dados que contêm no formato *collection.bson*. Nomeie quaisquer ficheiros de metadados utilizando o formato *collection.metadata.jsem*.

     * Para os despejos do JSON, os ficheiros do recipiente blob devem ser colocados em pastas com o nome das bases de dados que contêm. Dentro de cada pasta de base de dados, os ficheiros de dados devem ser colocados numa sub-dobragem chamada *dados*, e nomeados utilizando o formato *collection.jsem*. Coloque quaisquer ficheiros de metadados numa sub-dobradeira chamada *metadados,* e nomeados utilizando o mesmo formato, *collection.jsem*. Os ficheiros de metadados devem estar no mesmo formato que os produzidos pela ferramenta bsondump MongoDB.

    > [!IMPORTANT]
    > Não recomendamos que utilize um certificado auto-assinado no servidor MongoDB. Se tiver de utilizar um, ligue-se ao servidor utilizando o modo de cadeia de ligação e certifique-se de que a sua cadeia de ligação tem aspas ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Também pode utilizar o endereço IP para situações em que a resolução do nome DNS não seja possível.

   ![Screenshot que mostra especificar detalhes de origem.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de **detalhes do alvo da migração,** especifique os detalhes da ligação para a conta DB do Azure Cosmos. Esta conta é a API DB API pré-a provisionada da Azure Cosmos para a conta MongoDB para a qual está a migrar os seus dados mongoDB.

    ![Screenshot que mostra especificar detalhes do alvo.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino

1. No Mapa para o rastreio **de bases de dados de destino,** mapear a fonte e a base de dados-alvo para a migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    Se **a Create** aparecer ao lado do nome da base de dados, indica que o Azure Database Migration Service não encontrou a base de dados-alvo e que o serviço criará a base de dados para si.

    Nesta altura da migração, pode [prever o rendimento.](../cosmos-db/set-throughput.md) Em Azure Cosmos DB, você pode provisões para o nível da base de dados ou individualmente para cada coleção. A produção é medida nas [unidades de pedido](../cosmos-db/request-units.md). Saiba mais sobre [os preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Screenshot que mostra mapeamento para bases de dados alvo.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecione **Guardar**.
3. No ecrã de definição de **Coleção,** expanda a listagem de coleções e, em seguida, reveja a lista de coleções que serão migradas.

    O Azure Database Migration Service seleciona automaticamente todas as coleções existentes na origem da origem mongoDB que não existem na conta DB target Azure Cosmos. Se quiser remigrar as coleções que já incluem dados, tem de selecionar explicitamente as coleções neste painel.

    Pode especificar o número de RUs que pretende que as coleções utilizem. O Azure Database Migration Service sugere falhas inteligentes com base no tamanho da recolha.

    > [!NOTE]
    > Execute a migração e recolha da base de dados em paralelo. Se necessário, pode utilizar várias instâncias do Serviço de Migração da Base de Dados Azure para acelerar a execução.

    Também pode especificar uma chave de fragmento para aproveitar [a partição em Azure Cosmos DB](../cosmos-db/partitioning-overview.md) para uma escalabilidade ideal. Reveja as [melhores práticas para selecionar uma chave de fragmento/partição](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Screenshot que mostra a seleção de tabelas de coleções.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Screenshot que mostra o resumo da nigration.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

Selecione **Executar a migração**. A janela de atividade de migração aparece, e o estado da atividade não é **iniciado**.

![Screenshot que mostra o estado da atividade.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

No ecrã de atividades de migração, **selecione Refresh** para atualizar o visor até que o estado da migração apareça como **concluído**.

> [!NOTE]
> Pode selecionar a atividade para obter detalhes das métricas de migração de bases de dados e de nível de recolha.

![Screnshot que mostra o estado de atividade concluído.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Verificar dados em Azure Cosmos DB

Após o fim da migração, pode consultar a sua conta DB Azure Cosmos para verificar se todas as coleções foram migradas com sucesso.

![Screenshot que mostra onde verificar a sua conta DB Azure Cosmos para verificar se todas as coleções foram migradas com sucesso.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Otimização pós-migração

Depois de migrar os dados armazenados na base de dados mongoDB para a API API API do Azure Cosmos para a MongoDB, pode ligar-se à Azure Cosmos DB e gerir os dados. Também pode executar outros passos de otimização pós-migração. Estes podem incluir otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para a sua conta DB Azure Cosmos. Para obter mais informações, consulte [a otimização pós-migração.](../cosmos-db/mongodb-post-migration.md)

## <a name="next-steps"></a>Passos seguintes

Reveja as orientações de migração para cenários adicionais no Guia de [Migração da Base de Dados do Azure.](https://datamigration.microsoft.com/)



