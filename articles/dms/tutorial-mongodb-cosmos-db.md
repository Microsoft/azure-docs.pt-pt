---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para migrar MongoDB à API do Azure Cosmos DB para o MongoDB offline | Documentos da Microsoft'
description: Aprenda a migrar do MongoDB local para a API do Azure Cosmos DB para o MongoDB offline utilizando o serviço de migração de base de dados do Azure.
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
ms.openlocfilehash: 62acab231455b19d37b0800ead172950b5ee0378
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176281"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Tutorial: Migrar o MongoDB para API do Azure Cosmos DB para o MongoDB offline com o DMS
Pode utilizar o serviço de migração de base de dados do Azure para efetuar uma migração offline (única) de bases de dados a partir no local ou na cloud a instância do MongoDB à API do Azure Cosmos DB para o MongoDB.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.

Neste tutorial, migrar um conjunto de dados MongoDB alojada numa máquina Virtual para a API do Azure Cosmos DB para o MongoDB ao utilizar o serviço de migração de base de dados do Azure. Se não tiver uma origem de MongoDB já configurar, consulte o artigo [instalar e configurar o MongoDB numa VM do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

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
- Abra a firewall do Windows para permitir que o serviço de migração de base de dados do Azure aceder ao servidor MongoDB de origem, que, por predefinição, é a porta TCP 27017.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration
1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.
 
    ![Mostrar fornecedores de recursos](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.
 
    ![Registar o fornecedor de recursos](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância
1.  No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  No ecrã **Azure Database Migration Service**, selecione **Criar**.
 
    ![Criar instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma rede virtual (VNET) já existente ou crie uma nova.

    A VNET fornece o serviço de migração de base de dados do Azure com acesso à instância de MongoDB de origem e o destino de conta do Azure Cosmos DB.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    Se precisar de ajuda para escolher o escalão certo do serviço de migração de base de dados do Azure, veja as recomendações na mensagem de blogue [aqui](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.
 
      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **MongoDB**, no **tipo de servidor de destino**  caixa de texto, selecione **cosmos dB (MongoDB API)** e, em seguida, para **Escolher tipo de atividade**, selecione **migração de dados Offline**. 

    ![Criar projeto de serviço de migração de base de dados](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem
1. Sobre o **detalhes de origem** ecrã, especifique os detalhes de ligação para o servidor do MongoDB de origem.
    
   Também pode utilizar o modo de cadeia de ligação e fornecer uma localização para um contêiner de arquivos de armazenamento de BLOBs no qual já despejadas os dados de coleção que pretende migrar.

   > [!NOTE]
   > O serviço de migração de base de dados do Azure também pode migrar documentos de bson ou documentos json para a API do Azure Cosmos DB para coleções de MongoDB.
    
   Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

   ![Especificar os detalhes da origem](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino
1. Sobre o **detalhes do destino de migração** ecrã, especifique os detalhes de ligação para a conta do Azure Cosmos DB, que é o previamente aprovisionado do Azure Cosmos DB API para a conta do MongoDB para o qual está a migrar os dados da MongoDB de destino.

    ![Especificar os detalhes do destino](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecione **Guardar**.

## <a name="map-to-target-databases"></a>Mapear para as bases de dados de destino
1. Sobre o **mapa para bases de dados de destino** ecrã, mapeie a origem e a base de dados de destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    Se a cadeia de caracteres **criar** é apresentado junto ao nome da base de dados, ele indica que o serviço de migração de base de dados do Azure não encontrou a base de dados de destino e o serviço irá criar a base de dados para si.

    Neste momento na migração, pode [débito aprovisionar](https://docs.microsoft.com/azure/cosmos-db/set-throughput). No Cosmos DB, pode aprovisionar o débito ao nível da base de dados ou individualmente para cada coleção. Débito é medido em [unidades de pedido](https://docs.microsoft.com/azure/cosmos-db/request-units) (RUs). Saiba mais sobre [preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapear para as bases de dados de destino](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecione **Guardar**.
3. Sobre o **definição de recolha** ecrã, expanda as coleções de listagem e, em seguida, reveja a lista de coleções que serão migrados.

    Tenha em atenção que o automático do Azure Database Migration Service seleciona todas as coleções que existem na instância de MongoDB de origem que não existem no destino conta do Azure Cosmos DB. Se quiser remigrate coleções que já incluem dados, tem de selecionar explicitamente as coleções neste painel.

    Pode especificar a quantidade de RUs que pretende que as coleções a utilizar. O Azure Database Migration Service sugere predefinições inteligentes com base no tamanho de coleção.

    Também pode especificar uma chave de partição horizontal para aproveitar [criação de partições no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) para escalabilidade ideal. Certifique-se de que reveja os [melhores práticas para a seleção de uma chave de partição horizontal/partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Selecionar tabelas de coleções](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecione **Guardar**.

5. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

    ![Resumo da migração](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração
- Selecione **Executar a migração**.

    É apresentada a janela de atividade de migração e o **Status** da atividade é **não iniciado**.

    ![Estado de atividade](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração
- No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

   > [!NOTE]
   > Pode selecionar a atividade para obter detalhes das métricas de migração de nível da base de dados e da coleção.

    ![Estado da atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Certifique-se de dados no Cosmos DB

- Depois de concluir a migração, pode verificar a sua conta do Azure Cosmos DB para verificar se todas as coleções foram migradas com êxito.

    ![Estado da atividade concluído](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>Recursos adicionais

 * [Informações de serviço do cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Passos Seguintes
- Reveja a documentação de orientação de migração para cenários adicionais no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).
