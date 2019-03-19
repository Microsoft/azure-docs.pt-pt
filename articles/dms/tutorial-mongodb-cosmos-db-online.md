---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para migrar MongoDB à API do Azure Cosmos DB para o MongoDB online | Documentos da Microsoft'
description: Aprenda a migrar do MongoDB local para a API do Azure Cosmos DB para o MongoDB online ao utilizar o serviço de migração de base de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: dd14ec4f4b6395e5733f4f17165e94ca7e77f883
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008643"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms-preview"></a>Tutorial: Migrar o MongoDB para API do Azure Cosmos DB para o MongoDB online com o DMS (pré-visualização)
Pode utilizar o serviço de migração de base de dados do Azure para efetuar uma migração de online (período de indisponibilidade mínimo) de bases de dados a partir de um local ou na cloud a instância do MongoDB à API do Azure Cosmos DB para o MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.
> * Conclua a migração quando estiver pronto.

Neste tutorial, migrar um conjunto de dados MongoDB alojada numa máquina Virtual para a API do Azure Cosmos DB para o MongoDB com o período de indisponibilidade mínimo ao utilizar o serviço de migração de base de dados do Azure. Se não tiver uma origem de MongoDB já configurar, consulte o artigo [instalar e configurar o MongoDB numa VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou localizações geográficas pode abrandar o processo de migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do MongoDB à API do Azure Cosmos DB do MongoDB. Para uma migração offline, consulte [migrar MongoDB à API do Azure Cosmos DB para o MongoDB offline com o DMS](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:
- [Criar um Azure Cosmos DB API para MongoDB conta](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Criar uma rede Virtual do Azure (VNET) para o serviço de migração de base de dados do Azure com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local, utilizando um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Durante a configuração VNET, se utilizar o ExpressRoute com peering de rede para a Microsoft, adicione o seguinte serviço [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) à sub-rede na qual o serviço será aprovisionado:
    > - Ponto de extremidade de destino da base de dados (por exemplo, ponto de extremidade do SQL, ponto final do Cosmos DB etc.)
    > - Ponto final de armazenamento
    > - Ponto final de barramento de serviço
    >
    > Esta configuração é necessária porque o serviço de migração de base de dados do Azure não tem conectividade à internet.

- Certifique-se de que as regras do grupo de segurança de rede de VNET não bloqueiam as seguintes portas de comunicação: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Filtrar tráfego de rede com grupos de segurança de rede).
- Altere a firewall do servidor de origem para permitir que o serviço de migração de base de dados do Azure aceder ao servidor MongoDB de origem, que, por predefinição, é a porta TCP 27017.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration
1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)
       
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.
 
    ![Mostrar fornecedores de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)
    
3.  Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.
 
    ![Registar o fornecedor de recursos](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância
1.  No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2.  No ecrã **Azure Database Migration Service**, selecione **Criar**.
 
    ![Criar instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3.  No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma rede virtual (VNET) já existente ou crie uma nova.

    A VNET fornece o serviço de migração de base de dados do Azure com acesso à instância de MongoDB de origem e o destino de conta do Azure Cosmos DB.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um SKU de escalão de preços Premium.

    > [!NOTE]
    > Migrações online são suportadas apenas quando utiliza o escalão Premium. Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    Se precisar de ajuda para escolher o escalão certo do serviço de migração de base de dados do Azure, veja as recomendações na mensagem de blogue [aqui](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7.  Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.
 
    ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

    Em alternativa, pode detetar a instância do serviço de migração de base de dados do Azure do painel de pesquisa no portal do Azure.

    ![Utilizar o painel de pesquisa no portal do Azure](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selecione + **Novo Projeto de Migração**.

4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **MongoDB**, no **tipo de servidor de destino**  caixa de texto, selecione **cosmos dB (MongoDB API)** e, em seguida, para **Escolher tipo de atividade**, selecione **migração de dados Online [pré-visualização]**.

    ![Criar projeto de serviço de migração de base de dados](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5.  Selecione **salvar**e, em seguida, selecione **criar e a execução de atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem
1. Sobre o **detalhes de origem** ecrã, especifique os detalhes de ligação para o servidor do MongoDB de origem.

    Existem três modos para ligar a uma origem:
   * **Modo padrão**, que aceita um nome de domínio completamente qualificado ou um endereço IP, número de porta e credenciais de ligação.
   * **Modo de cadeia de ligação**, que aceita uma cadeia de ligação do MongoDB, conforme descrito no artigo [formato de URI de cadeia de ligação](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Dados do armazenamento do Azure**, que aceita um URL de SAS do contentor de Blobs. Selecione **Blob contém despejos BSON** se o contentor de BLOBs tem despejos BSON produzidos pelo MongoDB [ferramenta bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/)e desmarcá-la se o contentor contém ficheiros JSON.

     Se selecionar esta opção, certifique-se de que a cadeia de ligação de conta de armazenamento é apresentado no formato:

     ```
     https://blobnameurl/container?SASKEY
     ```
     Além disso, com base nas informações de informação de tipo no armazenamento do Azure, tenha os seguintes detalhes em mente.

     * Para informações de BSON, os dados dentro do contentor de blob tem de ser no formato de bsondump, de modo a que os arquivos de dados são colocados em pastas com o nome depois das bases de dados que contém o collection.bson de formato. Arquivos de metadados (se houver) devem ser nomeados usando o formato *coleção*. metadata.json.

     * Para informações de JSON, os ficheiros no contentor de BLOBs devem ser colocados em pastas com o nome depois das bases de dados que contêm. Em cada pasta de base de dados, ficheiros de dados tem de ser colocados numa subpasta chamada de "dados" e com o nome usando o formato *coleção*. JSON. Arquivos de metadados (se houver) devem ser colocados numa subpasta denominada "metadados" e com o mesmo formato, o nome *coleção*. JSON. Os arquivos de metadados tem de estar no mesmo formato que produzidos pela ferramenta bsondump MongoDB.

   Pode usar o endereço IP para situações em que DNS resolução de nomes não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Selecione **Guardar**.

   > [!NOTE]
   > O endereço do servidor de origem deve ser o endereço dos principais, se a origem é um conjunto de réplicas e o roteador se a origem for um cluster do MongoDB em partição horizontal. Para um cluster do MongoDB em partição horizontal, o Azure Database Migration Service tem de ser capaz de ligar a partições horizontais individuais no cluster, que poderão necessitar de abrir a firewall em máquinas mais.          

## <a name="specify-target-details"></a>Especificar os detalhes do destino
1. Sobre o **detalhes do destino de migração** ecrã, especifique os detalhes de ligação para a conta do Azure Cosmos DB, que é o previamente aprovisionado do Azure Cosmos DB API para a conta do MongoDB para o qual está a migrar os dados da MongoDB de destino.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino
1. Sobre o **mapa para bases de dados de destino** ecrã, mapeie a origem e a base de dados de destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    Se a cadeia de caracteres **criar** é apresentado junto ao nome da base de dados, ele indica que o serviço de migração de base de dados do Azure não encontrou a base de dados de destino e o serviço irá criar a base de dados para si.

    Nesta fase da migração, se quiser partilhar o débito no banco de dados, especifique um débito RU. No Cosmos DB, pode aprovisionar o débito ao nível da base de dados ou individualmente para cada coleção. Débito é medido em [unidades de pedido](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs). Saiba mais sobre [preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapear para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Selecione **Guardar**.

3. Sobre o **definição de recolha** ecrã, expanda as coleções de listagem e, em seguida, reveja a lista de coleções que serão migrados.

    Tenha em atenção que o automático do Azure Database Migration Service seleciona todas as coleções que existem na instância de MongoDB de origem que não existem no destino conta do Azure Cosmos DB. Se quiser remigrate coleções que já incluem dados, tem de selecionar explicitamente as coleções neste ecrã.

    Pode especificar o número de RUs que pretende que as coleções a utilizar. Na maioria dos casos, um valor entre 500 (mínimo de 1000 para coleções em partição horizontal) e 4000 deve ser suficiente. O Azure Database Migration Service sugere predefinições inteligentes com base no tamanho de coleção.

    Também pode especificar uma chave de partição horizontal para aproveitar [criação de partições no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para escalabilidade ideal. Certifique-se de que reveja os [melhores práticas para a seleção de uma chave de partição horizontal/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Se não tiver uma chave de partição, pode sempre utilizar **ID** como a chave de partição horizontal para melhor débito.

    ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Executar a migração
- Selecione **Executar a migração**.

   É apresentada a janela de atividade de migração e o **estado** da atividade é apresentada.

   ![Estado de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração
- No ecrã de atividade de migração, selecione **Atualize** para atualizar a apresentação até a **estado** da migração é apresentado como **Replaying**.

   > [!NOTE]
   > Pode selecionar a atividade para obter detalhes das métricas de migração de nível da base de dados e da coleção.

   ![Reprodução de estado de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Certifique-se de dados no Cosmos DB

1. Faça as alterações à sua base de dados do MongoDB de origem.
2. Ligar ao COSMOS DB para verificar se os dados são replicados do servidor do MongoDB de origem.

    ![Reprodução de estado de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)
 
## <a name="complete-the-migration"></a>Concluir a migração

* Depois de todos os documentos a partir da origem estão disponíveis no destino do COSMOS DB, selecione **concluir** no menu de contexto da atividade de migração para concluir a migração.

    Esta ação irá concluir a reprodução de todas as alterações pendentes e concluir a migração.

    ![Reprodução de estado de atividade](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="additional-resources"></a>Recursos adicionais

 * [Informações de serviço do cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passos Seguintes
- Reveja a documentação de orientação de migração para cenários adicionais no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).