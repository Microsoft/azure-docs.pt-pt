---
title: Introdução ao Azure Data Lake Storage Gen1-Portal
description: Use o portal do Azure para criar uma conta de Data Lake Storage Gen1 e executar operações básicas na conta.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438749"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar o portal do Azure para criar uma conta de Data Lake Storage Gen1 e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para obter mais informações, consulte [visão geral do Azure data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1

1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso > armazenamento > data Lake Storage Gen1**.
3. Na folha **novo data Lake Storage Gen1** , forneça os valores conforme mostrado na seguinte captura de tela:

    ![Criar uma nova conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta de Data Lake Storage Gen1")

   * **Nome**. Insira um nome exclusivo para a conta de Data Lake Storage Gen1.
   * **Subscrição**. Selecione a assinatura sob a qual você deseja criar uma nova conta de Data Lake Storage Gen1.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Para obter mais informações, veja [Grupos de Recursos no Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Local**: selecione um local onde você deseja criar a conta de data Lake Storage Gen1.
   * **Definições de Encriptação**. Existem três opções:

     * **Não ativar a encriptação**.
     * **Use chaves gerenciadas pelo data Lake Storage Gen1**, se você quiser que data Lake Storage Gen1 gerencie suas chaves de criptografia.
     * **Utilizar chaves a partir do seu Cofre de chaves**. Pode selecionar um Azure Key Vault existente ou criar um novo. Para usar as chaves de um Key Vault, você deve atribuir permissões para a conta de Data Lake Storage Gen1 acessar o Azure Key Vault. Para obter as instruções, veja [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Data Lake Storage Gen1 criptografia](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 criptografia")

        Clique em **OK** no painel **Definições de Encriptação**.

        Para obter mais informações, consulte [criptografia de dados em Azure data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Clique em **Criar**. Se você optar por fixar a conta no painel, será levado de volta ao painel e poderá ver o progresso de seu Data Lake Storage Gen1 provisionamento de conta. Depois que a conta de Data Lake Storage Gen1 for provisionada, a folha da conta será exibida.

## <a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault

Se você usou chaves de um Azure Key Vault para configurar a criptografia na conta de Data Lake Storage Gen1, deverá configurar o acesso entre a conta de Data Lake Storage Gen1 e a conta de Azure Key Vault. Para fazê-lo, execute os passos seguintes.

1. Se você usou chaves do Azure Key Vault, a folha da conta de Data Lake Storage Gen1 exibirá um aviso na parte superior. Clique no aviso para abrir a **Encriptação**.

    ![Data Lake Storage Gen1 criptografia](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 criptografia")
2. O painel mostra duas opções para configurar o acesso.

    ![Data Lake Storage Gen1 criptografia](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 criptografia")

   * Na primeira opção, clique em **Conceder Permissões** para configurar o acesso. A primeira opção é habilitada somente quando o usuário que criou a conta de Data Lake Storage Gen1 também é um administrador para o Azure Key Vault.
   * A outra opção consiste em executar o cmdlet do PowerShell apresentado no painel. Tem de ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para o painel e clique em **Ativar** para configurar o acesso.

> [!NOTE]
> Você também pode criar uma conta de Data Lake Storage Gen1 usando modelos de Azure Resource Manager. Estes modelos são acessíveis a partir dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Sem criptografia de dados: [implante Azure data Lake Storage Gen1 conta sem criptografia de dados](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Com a criptografia de dados usando Data Lake Storage Gen1: [implantar conta de data Lake Storage Gen1 com criptografia (data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Com a criptografia de dados usando Azure Key Vault: [implantar conta de data Lake Storage Gen1 com criptografia (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="createfolder"></a>Criar pastas

Você pode criar pastas em sua conta do Data Lake Storage Gen1 para gerenciar e armazenar dados.

1. Abra a conta de Data Lake Storage Gen1 que você criou. No painel esquerdo, clique em **todos os recursos**e, em seguida, na folha **todos os recursos** , clique no nome da conta sob a qual você deseja criar pastas. Se afixou a conta ao startboard, clique no mosaico dessa conta.
2. Na folha da conta do Data Lake Storage Gen1, clique em **Data Explorer**.

    ![Criar pastas em uma conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas em uma conta de Data Lake Storage Gen1")
3. No painel do Data Explorer, clique em **Nova Pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.

    ![Criar pastas em uma conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas em uma conta de Data Lake Storage Gen1")

    A pasta recém-criada é listada no painel **Data Explorer**. Você pode criar pastas aninhadas até qualquer nível.

    ![Criar pastas em uma conta de Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas em uma conta de Data Lake")

## <a name="uploaddata"></a>Carregar dados

Você pode carregar seus dados em uma conta de Data Lake Storage Gen1 diretamente no nível raiz ou em uma pasta que você criou na conta.

1. No painel **Data Explorer**, clique em **Carregar**.
2. No painel **Carregar ficheiros**, navegue para os ficheiros que pretende carregar e, em seguida, clique em **Adicionar ficheiros selecionados**. Também pode selecionar mais do que um ficheiro para carregar.

    ![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Ações disponíveis nos dados armazenados

Clique no ícone de reticências contra um ficheiro e, no menu de pop-up, clique na ação que pretende realizar nos dados.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

## <a name="secure-your-data"></a>Proteger os dados

Você pode proteger os dados armazenados em sua conta de Data Lake Storage Gen1 usando o Azure Active Directory e o controle de acesso (ACLs). Para obter instruções sobre como fazer isso, consulte [protegendo dados em Azure data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Eliminar a sua conta

Para excluir uma conta de Data Lake Storage Gen1, na folha Data Lake Storage Gen1, clique em **excluir**. Para confirmar a ação, ser-lhe-á pedido para introduzir o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Excluir conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar conta do Data Lake")

## <a name="next-steps"></a>Passos seguintes

* [Usar Azure Data Lake Storage Gen1 para requisitos de Big Data](data-lake-store-data-scenarios.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Usar Azure Data Lake Analytics com Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
