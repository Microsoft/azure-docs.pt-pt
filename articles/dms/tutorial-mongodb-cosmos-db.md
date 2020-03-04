---
title: 'Tutorial: Migrar MongoDB offline para Azure Cosmos DB API para MongoDB'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar de MongoDB no local para API DB Azure Cosmos para MongoDB offline utilizando o Serviço de Migração de Bases de Dados Azure.
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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255581"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Tutorial: Migrar MongoDB para API da Azure Cosmos DB para MongoDB offline usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para realizar uma migração offline (única vez) de bases de dados de uma instância de MongoDB para API da Azure Cosmos DB para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Serviço de Migração de Bases de Dados Azure.
> * Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

Neste tutorial, você migra um conjunto de dados em MongoDB hospedado em uma Máquina Virtual Azure para a API da Azure Cosmos DB para MongoDB utilizando o Serviço de Migração de Bases de Dados Azure. Se ainda não tiver uma fonte mongoDB, consulte o artigo [Instale e configure o MongoDB num Windows VM em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Complete os passos [pré-migratórios,](../cosmos-db/mongodb-pre-migration.md) tais como estimar o resultado, escolher uma chave de partição e a política de indexação.
* [Crie uma API da Azure Cosmos DB para a conta MongoDB.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    >
    > * Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual (NSG) não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Abra a sua firewall windows para permitir que o Serviço de Migração de Bases de Dados Azure aceda ao servidor MongoDB de origem, que por padrão é a porta TCP 27017.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.

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

4. Selecione a localização em que pretende criar a instância do Serviço de Migração de Bases de Dados Azure. 

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte mongoDB e à conta-alvo Azure Cosmos DB.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. No ecrã dos Serviços de Migração da Base de **Dados Azure,** procure o nome da instância do Serviço de Migração de Bases de Dados Azure que criou e, em seguida, selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **MongoDB,** na caixa de texto **do tipo servidor Target,** selecione **CosmosDB (MongoDB API)** e, em seguida, para **Escolher tipo de atividade,** selecione **migração de dados offline**. 

    ![Criar projeto de Serviço de Migração de Bases de Dados](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **de detalhes da Fonte,** especifique os detalhes de ligação para o servidor MongoDB de origem.

   > [!IMPORTANT]
   > O Serviço de Migração de Bases de Dados Azure não suporta o Azure Cosmos DB como fonte.

    Existem três modos para ligar a uma fonte:
   * **Modo standard**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, número de porta e credenciais de ligação.
   * **Modo de corda**de ligação , que aceita uma cadeia de ligação MongoDB como descrito no [formato De Linha de Ligação URI](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento azure,** que aceita um url sas do recipiente blob. Select **Blob contém lixeiras BSON** se o recipiente de blob tiver lixeiras BSON produzidas pela [ferramenta bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)MongoDB e desseleccionar se o recipiente contiver ficheiros JSON.

     Se selecionar esta opção, certifique-se de que a cadeia de ligação à conta de armazenamento aparece no formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Esta cadeia de ligação sas do recipiente blob pode ser encontrada no explorador de armazenamento Azure. A criação do SAS para o recipiente em causa fornecer-lhe-á o URL em formato acima solicitado.
     
     Além disso, com base nas informações de despejo do tipo no Armazenamento Azure, tenha em mente o seguinte detalhe.

     * No caso dos despejos da BSON, os dados dentro do recipiente blob devem estar em formato bsondump, de modo a que os ficheiros de dados sejam colocados em pastas com o nome das bases de dados contendo na coleção de formatos.bson. Os ficheiros de metadados (caso algum) devem ser nomeados utilizando a *recolha*de formato .metadata.json.

     * Para as lixeiras JSON, os ficheiros do recipiente de bolhas devem ser colocados em pastas com o nome das bases de dados que contêm. Dentro de cada pasta de base de dados, os ficheiros de dados devem ser colocados numa subpasta chamada "dados" e nomeados utilizando a *recolha*do formato .json. Os ficheiros de metadados (caso sejam os quais houver) devem ser colocados numa subpasta denominada "metadados" e nomeados utilizando o mesmo formato, *recolha*.json. Os ficheiros de metadados devem estar no mesmo formato que produzidos pela ferramenta bsondump MongoDB.

    > [!IMPORTANT]
    > É desencorajado usar um certificado auto-assinado no servidor mongo. No entanto, se um for utilizado, por favor ligue-se ao servidor utilizando o modo de corda de **ligação** e certifique-se de que a sua cadeia de ligação tem ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de detalhes do **alvo da Migração,** especifique os detalhes da ligação para a conta-alvo Azure Cosmos DB, que é a API do Azure Cosmos DB pré-provisionado para a conta MongoDB para a qual está a migrar os seus dados MongoDB.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino

1. No **mapa para direcionar o** ecrã das bases de dados, mapeie a fonte e a base de dados-alvo para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    Se o string **Create** aparecer ao lado do nome da base de dados, indica que o Serviço de Migração de Bases de Dados Azure não encontrou a base de dados do alvo e o serviço criará a base de dados para si.

    Nesta altura da migração, é possível [fornecer o seu contributo.](https://docs.microsoft.com/azure/cosmos-db/set-throughput) No Cosmos DB, pode fornecer a entrada tanto ao nível da base de dados como individualmente para cada coleção. A entrada é medida em [Unidades](https://docs.microsoft.com/azure/cosmos-db/request-units) de Pedido (RUs). Saiba mais sobre [os preços do Azure Cosmos DB.](https://azure.microsoft.com/pricing/details/cosmos-db/)

    ![Mapear para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecione **Guardar**.
3. No ecrã de definição da **Coleção,** expanda a listagem de coleções e, em seguida, reveja a lista de coleções que serão migradas.

    O Azure Database Migration Service auto seleciona todas as coleções existentes na fonte MongoDB que não existem na conta-alvo Azure Cosmos DB. Se pretender reemigrar coleções que já incluam dados, precisa selecionar explicitamente as coleções desta lâmina.

    Pode especificar a quantidade de RUs que pretende que as coleções utilizem. O Serviço de Migração de Bases de Dados Azure sugere incumprimentos inteligentes com base no tamanho da recolha.

    > [!NOTE]
    > Execute a migração e recolha da base de dados em paralelo utilizando múltiplas instâncias do Serviço de Migração de Bases de Dados Azure, se necessário, para acelerar a execução.

    Também pode especificar uma chave de fragmentos para aproveitar a [partilha em Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para uma escalabilidade ótima. Certifique-se de que revê as [melhores práticas para selecionar uma tecla de fragmento/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selecione mesas de coleções](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela da atividade migratória aparece, e o **Estado** da atividade não é **iniciado**.

    ![Estado de atividade](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

* No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

   > [!NOTE]
   > Pode selecionar a Atividade para obter detalhes das métricas de migração de nível de base de dados e de recolha.

    ![Estado de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verifique os dados em Cosmos DB

* Após a migração concluída, pode consultar a sua conta Azure Cosmos DB para verificar se todas as coleções foram migradas com sucesso.

    ![Estado de atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Otimização pós-migração

Depois de migrar os dados armazenados na base de dados da MongoDB para a API da Azure Cosmos DB para o MongoDB, pode ligar-se ao Azure Cosmos DB e gerir os dados. Também pode realizar outros passos de otimização pós-migração, tais como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para a sua conta Azure Cosmos DB. Para mais informações, consulte o artigo de [otimização pós-migração.](../cosmos-db/mongodb-post-migration.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Informações de serviço Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passos seguintes

* Reveja as orientações de migração para cenários adicionais no Guia de [Migração](https://datamigration.microsoft.com/)da Base de Dados da Microsoft .
