---
title: 'Quickstart: Ligue o Azure Data Explorer a um espaço de trabalho Azure Synapse Analytics'
description: Ligue um cluster Azure Data Explorer a um espaço de trabalho Azure Synapse Analytics utilizando Apache Spark para Azure Synapse Analytics.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172271"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Ligue-se ao Azure Data Explorer usando Apache Spark para Azure Synapse Analytics

Este artigo descreve como aceder a uma base de dados do Azure Data Explorer do Synapse Studio com Apache Spark para Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* [Crie um cluster e base de dados Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
* Tenha um espaço de trabalho Azure Synapse Analytics existente, ou crie um novo espaço de trabalho seguindo os passos em [Quickstart: Criar um espaço de trabalho Azure Synapse](./quickstart-create-workspace.md).
* Tenha uma piscina Apache Spark existente, ou crie uma nova piscina seguindo os passos em [Quickstart: Crie uma piscina Apache Spark utilizando o portal Azure](./quickstart-create-apache-spark-pool-portal.md).
* [Criar uma aplicação Azure Ative Directory (Azure AD) através da disponibilização de uma aplicação AD Azure](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app).
* Conceda o acesso da sua aplicação AZure à sua base de dados seguindo os passos nas [permissões de base de dados Do Azure Data Explorer](/azure/data-explorer/manage-database-permissions).

## <a name="go-to-synapse-studio"></a>Ir para o Estúdio Synapse

A partir de um espaço de trabalho Azure Synapse, selecione **Launch Synapse Studio**. Na página inicial do Estúdio Synapse, selecione **Dados** para ir ao **Data Object Explorer**.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Ligue uma base de dados do Azure Data Explorer a um espaço de trabalho Azure Synapse

A ligação de uma base de dados do Azure Data Explorer a um espaço de trabalho é feita através de um serviço ligado. Com um serviço ligado ao Azure Data Explorer, pode navegar e explorar dados, ler e escrever a partir de Apache Spark para Azure Synapse. Também pode gerir trabalhos de integração num oleoduto.

A partir do Data Object Explorer, siga estes passos para ligar diretamente um cluster Azure Data Explorer:

1. Selecione o **+** ícone perto de **Data**.
1. Selecione **Connect** para ligar a dados externos.
1. Selecione **Azure Data Explorer (Kusto)**.
1. Selecione **Continuar**.
1. Use um nome amigável para nomear o serviço ligado. O nome aparecerá no Data Object Explorer e é utilizado pelo Azure Synapse para ligar à base de dados.
1. Selecione o cluster Azure Data Explorer a partir da sua subscrição ou introduza o URI.
1. Insira a chave principal de **ID** e **serviço de serviço.** Certifique-se de que este responsável de serviço tem acesso à base de dados para ler operação e acesso ou acesso para ingerir dados.
1. Introduza o nome da base de dados Azure Data Explorer.
1. Selecione **a ligação de teste** para garantir que tem as permissões certas.
1. Selecione **Criar**.

    ![Screenshot que mostra um novo serviço ligado.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Opcional) **A ligação** de teste não valida o acesso por escrito. Certifique-se de que o seu iD principal de serviço tem acesso à base de dados Azure Data Explorer.

1. Os clusters e bases de dados do Azure Data Explorer aparecem no separador **Linked** na secção **Azure Data Explorer.**

    ![Screenshot que mostra navegação para clusters.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > Na versão atual, os objetos de base de dados são preenchidos com base nas permissões da sua conta Azure AD nas bases de dados do Azure Data Explorer. Quando executar os cadernos Apache Spark ou empregos de integração, a credencial no serviço de ligação será usada (por exemplo, principal de serviço).

## <a name="quickly-interact-with-code-generated-actions"></a>Interaja rapidamente com ações geradas por código

Quando clica corretamente numa base de dados ou numa tabela, aparece uma lista de amostras de cadernos Spark. Selecione uma opção para ler, escrever ou transmitir dados para Azure Data Explorer.

[![Screenshot que mostra novos cadernos de amostras.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Aqui está um exemplo de ler dados. Prenda o caderno à sua piscina spark e execute a cela.

[![Screenshot que mostra um novo caderno de leitura.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > A primeira execução pode levar mais de três minutos para iniciar a sessão de Faíscas. As execuções subsequentes serão significativamente mais rápidas.

## <a name="limitations"></a>Limitações

O conector Azure Data Explorer não é atualmente suportado com redes virtuais geridas pela Azure Synapse.

## <a name="next-steps"></a>Passos seguintes

* [Código de amostra com opções avançadas](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Conector de faísca Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark)