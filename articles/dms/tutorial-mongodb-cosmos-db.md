---
title: 'Tutorial: Migrar o MongoDB offline para Azure Cosmos DB API para o MongoDB'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar de MongoDB no local para Azure Cosmos DB API para MongoDB offline usando o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 08fa94dbe71299a6653df0b40aa5083375526172
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78255581"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Tutorial: Migrar a MongoDB para a API da Azure Cosmos para a MongoDB offline usando DMS

Você pode usar o Azure Database Migration Service para realizar uma migração offline (uma vez) de bases de dados de um local ou exemplo em nuvem de MongoDB para API da Azure Cosmos DB para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

Neste tutorial, você migra um conjunto de dados em MongoDB hospedado em uma máquina virtual Azure para Azure Cosmos DB's API para MongoDB usando O Serviço de Migração de Bases de Dados Azure. Se ainda não tiver uma fonte MongoDB configurada, consulte o artigo [Instalar e configurar o MongoDB num Windows VM em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Complete os passos [de pré-migração,](../cosmos-db/mongodb-pre-migration.md) tais como a estimativa da produção, a escolha de uma chave de partição e a política de indexação.
* [Crie uma API da Azure Cosmos DB para a conta MongoDB.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos servidores de origem no local, utilizando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [o VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede virtual (NSG) não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor MongoDB de origem, que por padrão é a porta TCP 27017.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure. 

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à origem da instância MongoDB e à conta DB do Azure Cosmos.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. No ecrã dos **Serviços de Migração da Base de Dados Azure,** procure o nome da instância do Serviço de Migração da Base de Dados Azure que criou e, em seguida, selecione o caso.

3. Selecione + **Novo Projeto de Migração**.

4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Fonte,** selecione **MongoDB,** na caixa de texto **do tipo do servidor Target,** selecione **CosmosDB (MongoDB API)** e, em seguida, para **Escolher tipo de atividade**, selecione migração de **dados offline**. 

    ![Criar projeto de Serviço de Migração de Bases de Dados](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã de **detalhes da Fonte,** especifique os detalhes da ligação para o servidor MongoDB de origem.

   > [!IMPORTANT]
   > O Azure Database Migration Service não suporta a Azure Cosmos DB como fonte.

    Existem três modos para ligar a uma fonte:
   * **Modo standard**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, número de porta e credenciais de conexão.
   * **Modo de cadeia de ligação**, que aceita uma cadeia de ligação MongoDB como descrito no artigo [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento Azure,** que aceita um RECIPIENTE DE BLOB SAS URL. Select **Blob contém depósitos BSON** se o recipiente blob tiver depósitos BSON produzidos pela [ferramenta de bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)MongoDB , e desescolá-lo se o recipiente contiver ficheiros JSON.

     Se selecionar esta opção, certifique-se de que o fio de ligação da conta de armazenamento aparece no formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Esta cadeia de ligação SAS do recipiente blob pode ser encontrada no explorador de armazenamento Azure. A criação do SAS para o recipiente em causa fornecer-lhe-á o URL no formato acima solicitado.
     
     Além disso, com base nas informações de despejo do tipo no Azure Storage, tenha em mente os seguintes detalhes.

     * Para os depósitos de BSON, os dados dentro do recipiente blob devem estar em formato bsondump, de modo a que os ficheiros de dados sejam colocados em pastas com o nome das bases de dados que contêm no formato collection.bson. Os ficheiros de metadados (caso existam) devem ser nomeados utilizando a *recolha* de formatos.metadata.js.

     * Para os despejos do JSON, os ficheiros do recipiente blob devem ser colocados em pastas com o nome das bases de dados que contêm. Dentro de cada pasta de base de dados, os ficheiros de dados devem ser colocados numa sub-dobragem chamada "dados" e nomeados utilizando a *recolha*de formatos .json. Os ficheiros de metadados (se houver) devem ser colocados numa sub-dobradeira chamada "metadados" e nomeados utilizando o mesmo formato, *recolha*.json. Os ficheiros de metadados devem estar no mesmo formato que os produzidos pela ferramenta bsondump MongoDB.

    > [!IMPORTANT]
    > É desencorajado a utilização de um certificado auto-assinado no servidor de Mongo. No entanto, se for utilizado, por favor ligue-se ao servidor utilizando o **modo de cadeia de ligação** e certifique-se de que a sua cadeia de ligação tem ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de **detalhes do alvo da migração,** especifique os detalhes da ligação para a conta DB do Azure Cosmos, que é a API do Azure Cosmos DB pré-a provisionada para a conta MongoDB para a qual está a migrar os seus dados MongoDB.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino

1. No Mapa para o rastreio **de bases de dados de destino,** mapear a fonte e a base de dados-alvo para a migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    Se o string **Create** aparecer ao lado do nome da base de dados, indica que o Serviço de Migração da Base de Dados Azure não encontrou a base de dados-alvo e o serviço criará a base de dados para si.

    Nesta altura da migração, pode [prever o rendimento.](https://docs.microsoft.com/azure/cosmos-db/set-throughput) Na Cosmos DB, pode proviscar a produção a nível da base de dados ou individualmente para cada coleção. A produção é medida em [Unidades de Pedido](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs). Saiba mais sobre [os preços da Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapear para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecione **Guardar**.
3. No ecrã de definição de **Coleção,** expanda a listagem de coleções e, em seguida, reveja a lista de coleções que serão migradas.

    O Azure Database Migration Service seleciona todas as coleções existentes na origem da origem mongoDB que não existem na conta DB target Azure Cosmos. Se quiser remigrar as coleções que já incluem dados, tem de selecionar explicitamente as coleções desta lâmina.

    Pode especificar a quantidade de RUs que pretende que as coleções utilizem. O Azure Database Migration Service sugere falhas inteligentes com base no tamanho da recolha.

    > [!NOTE]
    > Execute a migração e recolha da base de dados em paralelo utilizando múltiplas instâncias do Serviço de Migração da Base de Dados Azure, se necessário, para acelerar a execução.

    Também pode especificar uma chave de fragmento para aproveitar [a partição em Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para uma escalabilidade ideal. Certifique-se de rever as [melhores práticas para selecionar uma chave de fragmento/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selecione tabelas de coleções](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela de atividade de migração aparece, e o **Estado** da atividade não é **iniciado**.

    ![Estado da atividade](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

* No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

   > [!NOTE]
   > Pode selecionar a Atividade para obter detalhes das métricas de migração de base de dados e de nível de recolha.

    ![Estado de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verificar dados em Cosmos DB

* Após a conclusão da migração, pode consultar a sua conta DB Azure Cosmos para verificar se todas as coleções foram migradas com sucesso.

    ![Estado de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Otimização pós-migração

Depois de migrar os dados armazenados na base de dados mongoDB para a API da Azure Cosmos DB para o MongoDB, pode ligar-se à Azure Cosmos DB e gerir os dados. Também pode executar outros passos de otimização pós-migração, tais como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para a sua conta DB Azure Cosmos. Para mais informações, consulte o artigo [de otimização pós-migração.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Informações de serviço do Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Próximos passos

* Reveja as orientações de migração para cenários adicionais no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft .
