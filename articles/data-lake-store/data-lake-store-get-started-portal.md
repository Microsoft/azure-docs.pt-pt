---
title: Utilizar o portal do Azure para começar a utilizar com a geração 1 de armazenamento do Azure Data Lake | Documentos da Microsoft
description: Utilize o portal do Azure para criar uma conta de geração 1 de armazenamento do Azure Data Lake e executar operações básicas na conta de geração 1 de armazenamento do Data Lake.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: a99d8a265cdc72b6bd1dd45ab670f514d828f808
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58005695"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como utilizar o portal do Azure para criar uma conta de geração 1 de armazenamento do Azure Data Lake e executar operações básicas, tais como criar pastas, carregar e transferem ficheiros de dados, eliminar a conta, etc. Para obter mais informações, consulte [descrição geral do Azure Data Lake armazenamento Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta de geração 1 de armazenamento do Data Lake

1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Clique em **criar um recurso > armazenamento > Data Lake Storage Gen1**.
3. Na **novo Data Lake Storage geração 1** painel, forneça os valores, conforme mostrado na captura de ecrã seguinte:
   
    ![Criar uma nova conta de geração 1 de armazenamento do Data Lake](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "criar uma nova conta de geração 1 de armazenamento do Data Lake")
   
   * **Nome**. Introduza um nome exclusivo para a conta de geração 1 de armazenamento do Data Lake.
   * **Subscrição**. Selecione a subscrição sob a qual pretende criar uma nova conta de geração 1 de armazenamento do Data Lake.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Para obter mais informações, veja [Grupos de Recursos no Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Localização**: Selecione uma localização onde pretende criar a conta de geração 1 de armazenamento do Data Lake.
   * **Definições de Encriptação**. Existem três opções:
     
     * **Não ativar a encriptação**.
     * **Utilizar chaves geridas pelo Data Lake Storage Gen1**, se pretender que o Data Lake Storage Gen1 para gerir as chaves de encriptação.
     * **Utilizar chaves a partir do seu Cofre de chaves**. Pode selecionar um Azure Key Vault existente ou criar um novo. Para utilizar as teclas de um cofre de chaves, tem de atribuir permissões para a conta de geração 1 de armazenamento do Data Lake aceder ao Cofre de chaves do Azure. Para obter as instruções, veja [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Encriptação do Data Lake armazenamento Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "encriptação de geração 1 de armazenamento do Data Lake")
       
        Clique em **OK** no painel **Definições de Encriptação**.

        Para obter mais informações, consulte [encriptação de dados no Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Clique em **Criar**. Se optar por afixar a conta para o dashboard, é direcionado novamente para o dashboard e pode ver o progresso do aprovisionamento da conta sua geração 1 de armazenamento do Data Lake. Assim que a conta de geração 1 de armazenamento do Data Lake está aprovisionada, o painel de conta é exibida.

## <a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault
Se utilizou chaves a partir de um Azure Key Vault para configurar a encriptação na conta do Data Lake Storage Gen1, tem de configurar o acesso entre a conta de geração 1 de armazenamento do Data Lake e a conta do Azure Key Vault. Para fazê-lo, execute os passos seguintes.

1. Se utilizou chaves a partir do Cofre de chaves do Azure, o painel para a conta do Data Lake Storage Gen1 apresenta um aviso na parte superior. Clique no aviso para abrir a **Encriptação**.
   
    ![Encriptação do Data Lake armazenamento Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "encriptação de geração 1 de armazenamento do Data Lake")
2. O painel mostra duas opções para configurar o acesso.

    ![Encriptação do Data Lake armazenamento Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "encriptação de geração 1 de armazenamento do Data Lake")
   
   * Na primeira opção, clique em **Conceder Permissões** para configurar o acesso. A primeira opção só estará ativada se o utilizador que criou a conta de geração 1 de armazenamento do Data Lake também é um administrador para o Azure Key Vault.
   * A outra opção consiste em executar o cmdlet do PowerShell apresentado no painel. Tem de ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para o painel e clique em **Ativar** para configurar o acesso.

> [!NOTE]
> Também pode criar uma conta de geração 1 de armazenamento do Data Lake com modelos Azure Resource Manager. Estes modelos são acessíveis a partir dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> - Sem encriptação de dados: [Implementar a conta de geração 1 de armazenamento do Azure Data Lake sem criptografia de dados](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> - Com a encriptação de dados através da geração 1 de armazenamento do Data Lake: [Implementar a conta de geração 1 de armazenamento do Data Lake com encriptação (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> - Com a encriptação de dados com o Azure Key Vault: [Implementar a conta de geração 1 de armazenamento do Data Lake com encriptação (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Criar pastas numa conta do Data Lake Storage Gen1
Pode criar pastas na sua conta de geração 1 de armazenamento do Data Lake para gerir e armazenar dados.

1. Abra a conta de geração 1 do Data Lake armazenamento que criou. No painel esquerdo, clique em **Todos os recursos** e, em seguida, no painel Todos os recursos, clique no nome da conta sob o qual pretende criar as pastas. Se afixou a conta ao startboard, clique no mosaico dessa conta.
2. No painel da conta do Data Lake Storage Gen1, clique em **Data Explorer**.
   
    ![Criar pastas numa conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "criar pastas numa conta do Data Lake Storage Gen1")
3. No painel do Data Explorer, clique em **Nova Pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.
   
    ![Criar pastas numa conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "criar pastas numa conta do Data Lake Storage Gen1")
   
    A pasta recém-criada é listada no painel **Data Explorer**. Pode criar pastas aninhadas até qualquer nível.
   
    ![Criar pastas numa conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "criar pastas numa conta do Data Lake")

## <a name="uploaddata"></a>Carregar dados para uma conta de geração 1 de armazenamento do Data Lake
Pode carregar os dados para uma conta do Data Lake Storage Gen1 diretamente no nível de raiz ou para uma pasta que criou na conta. 

1. No painel **Data Explorer**, clique em **Carregar**. 
2. No painel **Carregar ficheiros**, navegue para os ficheiros que pretende carregar e, em seguida, clique em **Adicionar ficheiros selecionados**. Também pode selecionar mais do que um ficheiro para carregar.

    ![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Ações disponíveis nos dados armazenados
Clique no ícone de reticências contra um ficheiro e, no menu de pop-up, clique na ação que pretende realizar nos dados.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados") 

## <a name="secure-your-data"></a>Proteger os dados
Pode proteger os dados armazenados na sua conta de geração 1 de armazenamento do Data Lake com o Azure Active Directory e o controlo de acesso (ACLs). Para obter instruções sobre como fazer isso, consulte [proteger dados no Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminar uma conta de geração 1 de armazenamento do Data Lake
Para eliminar uma conta de geração 1 de armazenamento do Data Lake, do painel do Data Lake Storage Gen1, clique em **eliminar**. Para confirmar a ação, ser-lhe-á pedido para introduzir o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Eliminar conta do Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "conta Delete Data Lake")

## <a name="next-steps"></a>Passos Seguintes
* [Utilize a geração 1 de armazenamento do Azure Data Lake para requisitos de grandes volumes de dados](data-lake-store-data-scenarios.md) 
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Utilizar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

