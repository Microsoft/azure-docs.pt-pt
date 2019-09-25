---
title: 'Tutorial: Usar o serviço de migração de banco de dados do Azure para migrar o MongoDB para a API de Azure Cosmos DB para o MongoDB offline | Microsoft Docs'
description: Saiba como migrar do MongoDB local para a API do Azure Cosmos DB para o MongoDB offline usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 96540a8ea40efcc3a2d115980999c8d470b85180
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265955"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Tutorial: Migrar o MongoDB para a API do Azure Cosmos DB para MongoDB offline usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para executar uma migração offline (única) de bancos de dados de uma instância local ou de nuvem do MongoDB para a API de Azure Cosmos DB para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do serviço de migração de banco de dados do Azure.
> * Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorizar a migração.

Neste tutorial, você migra um conjunto de dados no MongoDB hospedado em uma máquina virtual do Azure para a API de Azure Cosmos DB para MongoDB usando o serviço de migração de banco de dados do Azure. Se você ainda não tiver uma fonte MongoDB configurada, consulte o artigo [instalar e configurar o MongoDB em uma VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Conclua as etapas de](../cosmos-db/mongodb-pre-migration.md) pré-migração, como estimativa de taxa de transferência, escolha de uma chave de partição e a política de indexação.
* [Crie uma API de Azure Cosmos DB para a conta do MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Crie uma VNet (rede virtual) do Azure para o serviço de migração de banco de dados do Azure usando Azure Resource Manager modelo de implantação, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNet, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.

* Certifique-se de que suas regras de NSG (grupo de segurança de rede) VNet não bloqueiem as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para obter mais detalhes sobre a filtragem de tráfego NSG VNet do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o servidor MongoDB de origem, que por padrão é a porta TCP 27017.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar fornecedor de recursos](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione o local no qual você deseja criar a instância do serviço de migração de banco de dados do Azure. 

5. Selecione uma VNet existente ou crie uma nova.

    A VNet fornece ao serviço de migração de banco de dados do Azure acesso à instância do MongoDB de origem e à conta de Azure Cosmos DB de destino.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do serviço de migração de banco de dados do Azure](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na tela **serviços de migração de banco de dados do Azure** , procure o nome da instância do serviço de migração de banco de dados do Azure que você criou e, em seguida, selecione a instância.

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **MongoDB**, na caixa de texto tipo de **servidor de destino** , selecione **CosmosDB (API do MongoDB)** e, em seguida, para **escolher tipo de atividade**, selecione **migração de dados offline**. 

    ![Criar projeto de serviço de migração de banco de dados](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. Na tela **detalhes da origem** , especifique os detalhes de conexão para o servidor MongoDB de origem.

   > [!IMPORTANT]
   > O serviço de migração de banco de dados do Azure não oferece suporte a Azure Cosmos DB como origem.

    Há três modos para se conectar a uma fonte:
   * **Modo padrão**, que aceita um nome de domínio totalmente qualificado ou um endereço IP, número da porta e credenciais de conexão.
   * **Modo de cadeia de conexão**, que aceita uma cadeia de conexão do MongoDB, conforme descrito no [formato URI da cadeia de conexão](https://docs.mongodb.com/manual/reference/connection-string/)do artigo.
   * **Dados do armazenamento do Azure**, que aceitam uma URL SAS do contêiner de BLOB. Selecionar **blob conterá despejos de BSON** se o contêiner de blob tiver despejos de BSON produzidos pela [ferramenta bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)do MongoDB e desselecioná-lo se o contêiner contiver arquivos JSON.

     Se você selecionar essa opção, verifique se a cadeia de conexão da conta de armazenamento aparece no formato:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Essa cadeia de conexão SAS do contêiner de blob pode ser encontrada no Gerenciador de armazenamento do Azure. A criação da SAS para o contêiner preocupado fornecerá a URL no formato solicitado acima.
     
     Além disso, com base nas informações de despejo de tipo no armazenamento do Azure, tenha em mente os detalhes a seguir.

     * Para despejos de BSON, os dados dentro do contêiner de blob devem estar no formato bsondump, de modo que os arquivos de dados sejam colocados em pastas nomeadas após os bancos que os contêm no formato Collection. BSON. Os arquivos de metadados (se houver) devem ser nomeados usando o formato *Collection*. Metadata. JSON.

     * Para despejos JSON, os arquivos no contêiner de blob devem ser colocados em pastas nomeadas após os bancos de dados que os contêm. Dentro de cada pasta de banco de dados, os arquivos devem ser colocados em uma subpasta chamada "data" e nomeadas usando o formato *Collection*. JSON. Os arquivos de metadados (se houver) devem ser colocados em uma subpasta chamada "Metadata" e nomeadas usando o mesmo formato, *Collection*. JSON. Os arquivos de metadados devem estar no mesmo formato que o produzido pela ferramenta bsondump do MongoDB.

    > [!IMPORTANT]
    > Não é recomendável usar um certificado autoassinado no servidor Mongo. No entanto, se um for usado, conecte-se ao servidor usando o **modo de cadeia de conexão** e verifique se a cadeia de conexão tem ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Na tela **detalhes do destino de migração** , especifique os detalhes de conexão para a conta de Azure Cosmos DB de destino, que é a API do Azure Cosmos DB pré-provisionada para a conta do MongoDB para a qual você está migrando os dados do MongoDB.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapa para as bases de dados de destino

1. Na tela **mapa para bancos de** dados de destino, mapeie a origem e o banco de dados de destino para migração.

    Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

    Se a cadeia de caracteres **Create** aparecer ao lado do nome do banco de dados, isso indica que o serviço de migração de banco de dados do Azure não encontrou o banco de dados de destino e o serviço criará o banco de dados para você.

    Neste ponto da migração, você pode [provisionar a taxa de transferência](https://docs.microsoft.com/azure/cosmos-db/set-throughput). No Cosmos DB, é possível provisionar a taxa de transferência no nível do banco de dados ou individualmente para cada coleção. A taxa de transferência é medida em [unidades de solicitação](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs). Saiba mais sobre [preços de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapa para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecione **Guardar**.
3. Na tela de **configuração da coleção** , expanda a listagem coleções e examine a lista de coleções que serão migradas.

    O serviço de migração de banco de dados do Azure seleciona automaticamente todas as coleções que existem na instância do MongoDB de origem que não existem na conta de Azure Cosmos DB de destino. Se você quiser remigrar coleções que já incluem dados, você precisa selecionar explicitamente as coleções nessa folha.

    Você pode especificar a quantidade de RUs que deseja que as coleções usem. O serviço de migração de banco de dados do Azure sugere padrões inteligentes com base no tamanho da coleção.

    > [!NOTE]
    > Execute a migração de banco de dados e a coleção em paralelo usando várias instâncias do serviço de migração de banco de dados do Azure, se necessário, para acelerar a execução.

    Você também pode especificar uma chave de fragmentação para aproveitar o [particionamento no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para obter a escalabilidade ideal. Certifique-se de examinar as [práticas recomendadas para selecionar uma chave de fragmentação/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela atividade de migração é exibida e o **status** da atividade **não é iniciado**.

    ![Estado de atividade](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

* No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

   > [!NOTE]
   > Você pode selecionar a atividade para obter detalhes das métricas de migração no nível de banco de dados e de coleção.

    ![Status da atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Verificar dados no Cosmos DB

* Após a conclusão da migração, você pode verificar sua conta de Azure Cosmos DB para verificar se todas as coleções foram migradas com êxito.

    ![Status da atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Otimização após a migração

Depois de migrar os dados armazenados no banco de dados MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar a Azure Cosmos DB e gerenciar os dados. Você também pode executar outras etapas de otimização após a migração, como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para sua conta de Azure Cosmos DB. Para obter mais informações, consulte o artigo [otimização após a migração](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Recursos adicionais

* [Informações do serviço Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passos seguintes

* Examine as diretrizes de migração para ver cenários adicionais no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.
