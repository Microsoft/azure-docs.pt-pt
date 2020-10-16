---
title: 'Quickstart: Ligue o Azure Data Explorer a um espaço de trabalho da Sinapse'
description: Como ligar um cluster Azure Data Explorer a um espaço de trabalho da Sinapse usando Azure Synapse Apache Spark
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 99ccc2f4d7e3adbba704f784025abfdfa8b96f52
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090663"
---
# <a name="connect-to-azure-data-explorer-using-synapse-apache-spark"></a>Ligue-se ao Azure Data Explorer usando a Faísca Apache Sinapse

Este artigo descreve como aceder a uma base de dados do Azure Data Explorer do Synapse Studio com o Synapse Apache Spark. 

## <a name="prerequisites"></a>Pré-requisitos

* [Crie um cluster e base de dados Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
* Espaço de trabalho synapse existente ou criar um novo espaço de trabalho após este [arranque rápido](./quickstart-create-workspace.md) 
* Piscina Apache Spark existente ou criar uma nova piscina após este [arranque rápido](./quickstart-create-apache-spark-pool-portal.md)
* [Crie uma aplicação AD Azure através da disponibilização de uma aplicação AD Azure.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Conceda à sua aplicação Azure AD aceda à sua base de dados seguindo [permissões de base de dados Do Azure Data Explorer](/azure/data-explorer/manage-database-permissions)

## <a name="navigate-to-synapse-studio"></a>Navegue para o Estúdio Synapse

A partir de um espaço de trabalho synapse, selecione **Launch Synapse Studio**. Na página inicial do Estúdio Synapse, selecione **Data**, que o levará ao **Explorador de Objetos de Dados.**

## <a name="connect-an-azure-data-explorer-database-to-a-synapse-workspace"></a>Ligue uma base de dados do Azure Data Explorer a um espaço de trabalho da Sinapse

A ligação de uma base de dados do Azure Data Explorer a um espaço de trabalho é feita através de um serviço ligado. Um serviço Azure Data Explore ligado permite que os utilizadores naveguem e explorem dados, leiam e escrevam a partir de Apache Spark para Azure Synapse Analytics e executar trabalhos de integração num oleoduto.

A partir do Data Object Explorer, siga estes passos para ligar diretamente um cluster Azure Data Explorer:

1. Selecione **+** ícone perto de Dados
2. Selecione **Ligar** aos dados externos
3. Selecione **Azure Data Explorer (Kusto)**
5. Selecione **Continuar**
6. Diga o nome do serviço ligado. O nome será apresentado no Object Explorer e utilizado pela Synapse para ligar à base de dados. Recomendamos o uso de um nome amigável
7. Selecione o cluster Azure Data Explore a partir da sua subscrição ou introduza o URI.
8. Introduza o "ID principal de serviço" e "Chave principal de serviço" (certifique-se de que este chefe de serviço tem acesso à base de dados para operação de leitura e acesso para ingerir dados)
9. Introduza o nome da base de dados do Azure Data Explorer
10. Clique **na ligação De teste** para garantir que tem as permissões certas
11. Selecione **Criar**

    ![Novo Serviço Ligado](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Opcional) A ligação de teste não valida o acesso por escrito, certifique-se de que o seu ID principal de serviço tem acesso à base de dados Azure Data Explorer.

12. Os clusters e bases de dados do Azure Data Explorer são visíveis no separador  **Linked** na secção Azure Data Explorer. 

    ![Procurar Clusters](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE] 
    > Na versão atual, os objetos de base de dados são preenchidos com base nas permissões da sua conta AAD nas bases de dados do Azure Data Explorer. Quando executar os cadernos Apache Spark ou empregos de integração, a credencial no serviço de ligação será utilizada (ou seja, diretor de serviço).


## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por código

* Quando clicar à direita numa base de dados ou numa tabela, terá uma lista de gestos que desencadearão uma amostra do caderno Spark para ler dados, escrever dados, transmitir dados para o Azure Data Explorer. 
    [![Novos cadernos de amostras](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

* Aqui está um exemplo de ler dados. Anexar o Caderno à sua piscina spark e executar o novo [ ![ caderno de leitura](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox) da célula

   > [!NOTE] 
   > A primeira execução pode demorar mais de três minutos a iniciar a sessão de Faíscas. As execuções subsequentes serão significativamente mais rápidas.  


## <a name="limitations"></a>Limitações
O conector Azure Data Explorer não é suportado atualmente com O VNET gerido Azure Synapse.


## <a name="next-steps"></a>Passos seguintes

* [Código de amostra com opções avançadas](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Conector de faísca Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark)