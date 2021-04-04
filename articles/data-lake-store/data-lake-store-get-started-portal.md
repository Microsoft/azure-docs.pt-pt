---
title: Começar com Azure Data Lake Storage Gen1 - portal
description: Utilize o portal Azure para criar uma conta Gen1 de armazenamento de dados e realize operações básicas na conta.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578585"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Começa com o Azure Data Lake Storage Gen1 usando o portal Azure

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [CLI do Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Saiba como usar o portal Azure para criar uma conta Gen1 de armazenamento de dados e realizar operações básicas como criar pastas, carregar e descarregar ficheiros de dados, eliminar a sua conta, etc. Para mais informações, consulte [a visão geral da Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

* **Uma assinatura Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1

1. Inicie sessão no novo [portal do Azure](https://portal.azure.com).
2. Clique **em Criar um recurso > armazenamento > data lake storage gen1**.
3. Na lâmina **Gen1 de armazenamento de novos dados,** forneça os valores como mostrado na imagem seguinte:

    ![Criar uma nova conta de Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Criar uma nova conta de Data Lake Storage Gen1")

   * **Nome**. Insira um nome único para a conta Desasudamento gen1.
   * **Subscrição**. Selecione a subscrição sob a qual pretende criar uma nova conta Desarrumação Gen1 de Armazenamento de Dados.
   * **Grupo de Recursos**. Selecione um grupo de recursos existente ou selecione a opção **Criar novo** para criar um. Um grupo de recursos é um contentor que retém recursos relacionados para uma aplicação. Para obter mais informações, veja [Grupos de Recursos no Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Localização**: Selecione um local onde pretende criar a conta Desacosto de Armazenamento de Dados Gen1.
   * **Definições de encriptação**. Existem três opções:

     * **Não ativar a encriptação**.
     * **Utilize as teclas geridas pela Data Lake Storage Gen1,** se quiser que o Data Lake Storage Gen1 gere as suas chaves de encriptação.
     * **Utilizar chaves a partir do seu Cofre de chaves**. Pode selecionar um Azure Key Vault existente ou criar um novo. Para utilizar as chaves de um Cofre de Chaves, tem de atribuir permissões para a conta de Armazenamento de Dados Lake Gen1 aceder ao Cofre da Chave Azure. Para obter as instruções, veja [Atribuir permissões ao Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Screenshot da lâmina New Data Lake Storage Gen 1 e da lâmina de definição de encriptação.](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Encriptação Gen1 de armazenamento de dados")

        Clique em **OK** no painel **Definições de Encriptação**.

        Para obter mais informações, consulte [encriptação de dados na Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Clique em **Criar**. Se optar por fixar a conta no painel de instrumentos, é levado de volta para o painel de instrumentos e pode ver o progresso do fornecimento da sua conta de Armazenamento de Dados Lake Gen1. Uma vez que a conta de Armazenamento de Data Lake Gen1 é a for forada, a lâmina da conta aparece.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Atribuir permissões ao Azure Key Vault

Se usou as chaves de um Cofre de Chaves Azure para configurar a encriptação na conta Da Gen1 de armazenamento de dados, tem de configurar o acesso entre a conta Desequim de Armazenamento de Dados Gen1 e a conta Azure Key Vault. Para fazê-lo, execute os passos seguintes.

1. Se usou as chaves do Cofre da Chave Azure, a lâmina da conta Desebos de Armazenamento de Dados Gen1 apresenta um aviso no topo. Clique no aviso para abrir a **Encriptação**.

    ![Screenshot da lâmina da conta Gen1 de armazenamento de datas mostrando o aviso que diz: "Configuração de permissão do cofre de chaves necessária. Clique aqui para configurar.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Encriptação Gen1 de armazenamento de dados")
2. O painel mostra duas opções para configurar o acesso.

    ![Screenshot da lâmina de encriptação.](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Encriptação Gen1 de armazenamento de dados")

   * Na primeira opção, clique em **Conceder Permissões** para configurar o acesso. A primeira opção só é ativada quando o utilizador que criou a conta Desabose de Armazenamento de Dados Gen1 é também um administrador para o Cofre da Chave Azure.
   * A outra opção consiste em executar o cmdlet do PowerShell apresentado no painel. Tem de ser o proprietário do Cofre de Chaves do Azure ou ter a capacidade de conceder permissões no Cofre de Chaves do Azure. Depois de executar o cmdlet, volte para o painel e clique em **Ativar** para configurar o acesso.

> [!NOTE]
> Também pode criar uma conta Gen1 de armazenamento de dados utilizando modelos do Gestor de Recursos Azure. Estes modelos são acessíveis a partir dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Sem encriptação de dados: [Implementar a conta Azure Data Lake Storage Gen1 sem encriptação de dados](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * Com encriptação de dados usando data lake storage gen1: [implementar conta Desequim de Armazenamento de Dados Gen1 com encriptação (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * Com encriptação de dados usando Azure Key Vault: [Implementar conta Gen1 de armazenamento de dados com encriptação (Cofre chave)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Criar pastas

Pode criar pastas na sua conta De Armazenamento de Data Lake Gen1 para gerir e armazenar dados.

1. Abra a conta de Data Lake Storage Gen1 que criou. A partir do painel esquerdo, clique em **Todos os recursos**, e depois a partir da lâmina De Todos os **recursos,** clique no nome da conta sob a qual pretende criar pastas. Se afixou a conta ao startboard, clique no mosaico dessa conta.
2. Na sua lâmina de conta Gen1 de armazenamento de data lake, clique em **Data Explorer**.

    ![Screenshot da lâmina da conta Desear o Armazém de Dados Gen 1 com a opção data explorer chamada.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Criar pastas numa conta gen1 de armazenamento de data lake")
3. No painel do Data Explorer, clique em **Nova Pasta**, introduza um nome para a nova pasta e, em seguida, clique em **OK**.

    ![Screenshot da lâmina data explorer com a nova opção de pasta e a nova caixa de texto da pasta chamada.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Criar pastas numa conta gen1 de armazenamento de data lake")

    A pasta recém-criada é listada no painel **Data Explorer**. Pode criar pastas aninhadas até qualquer nível.

    ![Criar pastas numa conta do Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Criar pastas numa conta do Data Lake")

## <a name="upload-data"></a><a name="uploaddata"></a>Carregar dados

Pode fazer o upload dos seus dados para uma conta Desapoia de Armazenamento de Dados Gen1 diretamente ao nível de raiz ou a uma pasta que criou dentro da conta.

1. No painel **Data Explorer**, clique em **Carregar**.
2. No painel **Carregar ficheiros**, navegue para os ficheiros que pretende carregar e, em seguida, clique em **Adicionar ficheiros selecionados**. Também pode selecionar mais do que um ficheiro para carregar.

    ![Carregar dados](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Carregar dados")

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Ações disponíveis nos dados armazenados

Clique no ícone de reticências contra um ficheiro e, no menu de pop-up, clique na ação que pretende realizar nos dados.

![Propriedades nos dados](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Propriedades nos dados")

## <a name="secure-your-data"></a>Proteger os dados

Pode proteger os dados armazenados na sua conta Desacução da Gen1 de Armazenamento de Dados utilizando o Azure Ative Directory e o controlo de acesso (ACLs). Para obter instruções sobre como fazê-lo, consulte [os dados de segurança na Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Eliminar a sua conta

Para eliminar uma conta Gen1 de armazenamento de data lake, a partir da sua lâmina Gen1 de armazenamento de data lake, clique em **Eliminar**. Para confirmar a ação, ser-lhe-á pedido para introduzir o nome da conta que pretende eliminar. Introduza o nome da conta e, em seguida, clique em **Eliminar**.

![Apagar conta gen1 de armazenamento de dados](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminar conta do Data Lake")

## <a name="next-steps"></a>Passos seguintes

* [Use Azure Data Lake Storage Gen1 para grandes requisitos de dados](data-lake-store-data-scenarios.md)
* [Proteger dados no Armazenamento do Data Lake Ger1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics com data lake storage gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Use Azure HDInsight com data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
