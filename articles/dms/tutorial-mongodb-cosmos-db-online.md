---
title: 'Tutorial: migrar o MongoDB online para Azure Cosmos DB API para MongoDB'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar do MongoDB local para Azure Cosmos DB API para o MongoDB online usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 896ae35e1039548ea56967ff73d6a1781aa3c8a6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751393"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Tutorial: migrar o MongoDB para a API do Azure Cosmos DB para MongoDB online usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para executar uma migração online (tempo de inatividade mínimo) de bancos de dados de uma instância local ou de nuvem do MongoDB para a API de Azure Cosmos DB para MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorizar a migração.
> * Conclua a migração quando estiver pronto.

Neste tutorial, você migra um conjunto de dados no MongoDB hospedado em uma máquina virtual do Azure para a API de Azure Cosmos DB para MongoDB com tempo de inatividade mínimo usando o serviço de migração de banco de dados do Azure. Se você ainda não tiver uma fonte MongoDB configurada, consulte o artigo [instalar e configurar o MongoDB em uma VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do serviço de migração de banco de dados do Azure na mesma região do Azure que o banco de dados de destino. Mover dados entre regiões ou geografia pode retardar o processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do MongoDB para a API do Azure Cosmos DB para MongoDB. Para uma migração offline, consulte [migrar MongoDB para a API do Azure Cosmos DB para MongoDB offline usando DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* [Conclua as etapas de](../cosmos-db/mongodb-pre-migration.md) pré-migração, como estimativa de taxa de transferência, escolha de uma chave de partição e a política de indexação.
* [Crie uma API de Azure Cosmos DB para a conta do MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando Azure Resource Manager modelo de implantação, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Durante a configuração de rede virtual, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.

* Verifique se suas regras de NSG (grupo de segurança de rede) de rede virtual não bloqueiam as seguintes portas de comunicação: 53, 443, 445, 9354 e 10000-20000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o servidor MongoDB de origem, que por padrão é a porta TCP 27017.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione o local no qual você deseja criar a instância do serviço de migração de banco de dados do Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

   A rede virtual fornece o serviço de migração de banco de dados do Azure com acesso à instância do MongoDB de origem e à conta de Azure Cosmos DB de destino.

   Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione uma SKU do tipo de preço premium.

    > [!NOTE]
    > Há suporte para migrações online somente ao usar a camada Premium. Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do serviço de migração de banco de dados do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Na tela **serviços de migração de banco de dados do Azure** , procure o nome da instância do serviço de migração de banco de dados do Azure que você criou e, em seguida, selecione a instância.

    Como alternativa, você pode descobrir a instância do serviço de migração de banco de dados do Azure no painel de pesquisa no portal do Azure.

    ![Use o painel de pesquisa na portal do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selecione + **Novo Projeto de Migração**.

4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **MongoDB**, na caixa de texto tipo de **servidor de destino** , selecione **CosmosDB (API do MongoDB)** e, em seguida, para **escolher tipo de atividade**, selecione **migração de dados online [visualização]** .

    ![Criar projeto de serviço de migração de banco de dados](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Selecione **salvar**e, em seguida, selecione **criar e executar atividade** para criar o projeto e executar a atividade de migração.

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

     Além disso, com base nas informações de despejo de tipo no armazenamento do Azure, tenha em mente os detalhes a seguir.

     * Para despejos de BSON, os dados dentro do contêiner de blob devem estar no formato bsondump, de modo que os arquivos de dados sejam colocados em pastas nomeadas após os bancos que os contêm no formato Collection. BSON. Os arquivos de metadados (se houver) devem ser nomeados usando o formato *Collection*. Metadata. JSON.

     * Para despejos JSON, os arquivos no contêiner de blob devem ser colocados em pastas nomeadas após os bancos de dados que os contêm. Dentro de cada pasta de banco de dados, os arquivos devem ser colocados em uma subpasta chamada "data" e nomeadas usando o formato *Collection*. JSON. Os arquivos de metadados (se houver) devem ser colocados em uma subpasta chamada "Metadata" e nomeadas usando o mesmo formato, *Collection*. JSON. Os arquivos de metadados devem estar no mesmo formato que o produzido pela ferramenta bsondump do MongoDB.

    > [!IMPORTANT]
    > Não é recomendável usar um certificado autoassinado no servidor Mongo. No entanto, se um for usado, conecte-se ao servidor usando o **modo de cadeia de conexão** e verifique se a cadeia de conexão tem ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Você pode usar o endereço IP para situações em que a resolução de nomes DNS não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Selecione **Guardar**.

   > [!NOTE]
   > O endereço do servidor de origem deve ser o endereço do primário se a origem for um conjunto de réplicas e o roteador se a origem for um cluster MongoDB fragmentado. Para um cluster MongoDB fragmentado, o serviço de migração de banco de dados do Azure deve ser capaz de se conectar aos fragmentos individuais no cluster, o que pode exigir a abertura do firewall em mais computadores.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Na tela **detalhes do destino de migração** , especifique os detalhes de conexão para a conta de Azure Cosmos DB de destino, que é a API do Azure Cosmos DB pré-provisionada para a conta do MongoDB para a qual você está migrando os dados do MongoDB.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino

1. Na tela **mapa para bancos de** dados de destino, mapeie a origem e o banco de dados de destino para migração.

   Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

   Se a cadeia de caracteres **Create** aparecer ao lado do nome do banco de dados, isso indica que o serviço de migração de banco de dados do Azure não encontrou o banco de dados de destino e o serviço criará o banco de dados para você.

   Neste ponto da migração, se você quiser uma taxa de transferência de compartilhamento no banco de dados, especifique uma taxa de transferência de RU. No Cosmos DB, é possível provisionar a taxa de transferência no nível do banco de dados ou individualmente para cada coleção. A taxa de transferência é medida em [unidades de solicitação](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs). Saiba mais sobre [preços de Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Mapear para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Selecione **Guardar**.

3. Na tela de **configuração da coleção** , expanda a listagem coleções e examine a lista de coleções que serão migradas.

   O serviço de migração de banco de dados do Azure seleciona automaticamente todas as coleções que existem na instância do MongoDB de origem que não existem na conta de Azure Cosmos DB de destino. Se você quiser remigrar coleções que já incluem dados, você precisa selecionar explicitamente as coleções nesta tela.

   Você pode especificar o número de RUs que deseja que as coleções usem. Na maioria dos casos, um valor entre 500 (mínimo de 1000 para coleções fragmentadas) e 4000 deve ser suficiente. O serviço de migração de banco de dados do Azure sugere padrões inteligentes com base no tamanho da coleção.

    > [!NOTE]
    > Execute a migração de banco de dados e a coleção em paralelo usando várias instâncias do serviço de migração de banco de dados do Azure, se necessário, para acelerar a execução.

   Você também pode especificar uma chave de fragmentação para aproveitar o [particionamento no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para obter a escalabilidade ideal. Certifique-se de examinar as [práticas recomendadas para selecionar uma chave de fragmentação/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Se você não tiver uma chave de partição, sempre poderá usar **_id** como a chave de fragmentação para obter uma melhor taxa de transferência.

   ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

   A janela atividade de migração é exibida e o **status** da atividade é exibido.

   ![Estado de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

* Na tela atividade de migração, selecione **Atualizar** para atualizar a exibição até que o **status** da migração seja mostrado como **reproduzindo**.

   > [!NOTE]
   > Você pode selecionar a atividade para obter detalhes das métricas de migração no nível de banco de dados e de coleção.

   ![Reprodução de status de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Verificar dados no Cosmos DB

1. Faça alterações no banco de dados do MongoDB de origem.
2. Conecte-se ao COSMOS DB para verificar se os dados são replicados do servidor MongoDB de origem.

    ![Reprodução de status de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Concluir a migração

* Depois que todos os documentos da origem estiverem disponíveis no destino do COSMOS DB, selecione **concluir** no menu de contexto da atividade de migração para concluir a migração.

    Esta ação terminará de repetir todas as alterações pendentes e concluir a migração.

    ![Reprodução de status de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Otimização após a migração

Depois de migrar os dados armazenados no banco de dados MongoDB para a API do Azure Cosmos DB para MongoDB, você pode se conectar a Azure Cosmos DB e gerenciar os dados. Você também pode executar outras etapas de otimização após a migração, como otimizar a política de indexação, atualizar o nível de consistência padrão ou configurar a distribuição global para sua conta de Azure Cosmos DB. Para obter mais informações, consulte o artigo [otimização após a migração](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Recursos adicionais

* [Informações do serviço Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passos seguintes

* Examine as diretrizes de migração para ver cenários adicionais no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.
