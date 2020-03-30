---
title: Começar com o Storage Explorer [ Explorador de Armazenamento] Microsoft Docs
description: Gerir os recursos de armazenamento azure com o Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279796"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Explorador de Armazenamento

## <a name="overview"></a>Descrição geral

O Microsoft Azure Storage Explorer é uma aplicação autónoma que facilita o trabalho com dados do Azure Storage no Windows, macOS e Linux. Neste artigo, você vai aprender várias maneiras de conectar e gerir as suas contas de armazenamento Azure.

![Explorador de Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windows"></a>[Windows](#tab/windows)

As seguintes versões do Windows suportam o Storage Explorer:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, o Storage Explorer requer .NET Framework 4.6.2 ou posterior.

# <a name="macos"></a>[macOS](#tab/macos)

As seguintes versões do macOS suportam O Storage Explorer:

* macOS 10.12 Sierra e versões posteriores

# <a name="linux"></a>[Linux](#tab/linux)

O Storage Explorer está disponível na [Snap Store](https://snapcraft.io/storage-explorer) para a distribuição mais comum do Linux. Recomendamos a Snap Store para esta instalação. O snap Storage Explorer instala todas as suas dependências e atualizações quando novas versões são publicadas na Snap Store.

Para distribuição suportada, consulte a página de [instalação de encaixe](https://snapcraft.io/docs/installing-snapd).

O Storage Explorer requer a utilização de um gestor de passwords. Talvez tenha sondar um gestor de passwords manualmente. Pode ligar o Storage Explorer ao gestor de passwords do seu sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

O Storage Explorer também está disponível como um download *.tar.gz.* Tem que instalar dependências manualmente. As seguintes distribuições de suporte Linux *.tar.gz* instalação:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

A instalação *.tar.gz* pode funcionar em outras distribuições, mas apenas estas listadas são oficialmente suportadas.

Para obter mais ajuda na instalação do Storage Explorer em Linux, consulte [as dependências](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) do Linux no guia de resolução de problemas do Azure Storage Explorer.

---

## <a name="download-and-install"></a>Transferir e instalar

Para descarregar e instalar o Storage Explorer, consulte [o Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento. Em geral, pode:

* [Inscreva-se no Azure para aceder às suas subscrições e seus recursos](#sign-in-to-azure)
* [Anexar um recurso específico de Armazenamento ou CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

> [!NOTE]
> Para aceder totalmente aos recursos após a sua inscrição, o Storage Explorer requer permissões de gestão (Gestor de Recursos Azure) e de camada de dados. Isto significa que necessita de permissões de Diretório Ativo Azure (Azure AD), que lhe dão acesso à sua conta de armazenamento, aos contentores na conta e aos dados nos contentores. Se tiver permissões apenas na camada de dados, considere adicionar um recurso através do [Azure AD](#add-a-resource-via-azure-ad). Para mais informações sobre as permissões específicas que o Explorador de Armazenamento requer, consulte o guia de resolução de problemas do Explorador de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. No Explorer de Armazenamento, selecione **'Ver** > **Gestão de Conta'** ou selecione o botão **'Gerir contas'.**

    ![Gerir Contas][1]

1. **GESTÃO DE CONTAS** apresenta agora todas as contas Azure que assinou. Para se ligar a outra conta, selecione **Adicionar uma conta**.

1. Em **Connect to Azure Storage,** selecione uma nuvem Azure do **ambiente Azure** para iniciar sessão numa nuvem nacional ou numa Stack Azure. Depois de escolher o seu ambiente, selecione **Next**.

    ![Opção de iniciar sessão][2]

    O Storage Explorer abre uma página para que você assine. Para mais informações, consulte [O explorador de armazenamento Connect para uma conta de subscrição ou armazenamento Do Stack Azure](/azure-stack/user/azure-stack-storage-connect-se)Stack .

1. Depois de iniciar sessão com sucesso com uma conta Azure, a conta e as assinaturas Azure associadas a essa conta aparecem no âmbito da **GESTÃO DE CONTA.** Selecione **Todas as subscrições** para alternar a sua seleção entre todas ou nenhuma das subscrições do Azure listadas. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**.

    ![Selecionar subscrições do Azure][3]

    O **EXPLORER** exibe as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico

Existem várias formas de anexar a um recurso no Explorador de Armazenamento:

* [Adicione um recurso via Azure AD](#add-a-resource-via-azure-ad). Se tiver permissões apenas na camada de dados, utilize esta opção para adicionar um recipiente de blob ou um recipiente de armazenamento de blob De Armazenamento de Lago Sinuoso Azure Data.
* [Utilize uma corda de ligação](#use-a-connection-string). Utilize esta opção se tiver uma cadeia de ligação a uma conta de armazenamento. O Storage Explorer suporta as cordas de ligação de assinatura de assinatura de [acesso](storage/common/storage-dotnet-shared-access-signature-part-1.md) partilhado.
* [Utilize uma assinatura](#use-a-shared-access-signature-uri)de acesso partilhado URI . Se tiver uma assinatura de [acesso partilhado URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) a um recipiente de blob, partilha de ficheiros, fila ou mesa, utilize-a para se ligar ao recurso. Para obter uma assinatura de acesso partilhado URI, pode utilizar o [Storage Explorer](#generate-a-sas-in-storage-explorer) ou o [portal Azure](https://portal.azure.com).
* [Use um nome e uma chave.](#use-a-name-and-key) Se souber que alguma das chaves da conta da sua conta de armazenamento pode utilizar esta opção para se ligar rapidamente. Encontre as chaves na página da conta de armazenamento selecionando**as teclas de acesso** **definições** > no [portal Azure](https://portal.azure.com).
* [Anexar a um emulador local.](#attach-to-a-local-emulator) Se estiver a utilizar um dos emuladores de armazenamento do Azure disponíveis, utilize esta opção para se ligar facilmente ao seu emulador.
* [Ligue-se a uma conta Azure Cosmos DB utilizando uma cadeia de ligação](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Utilize esta opção se tiver uma cadeia de ligação a uma instância cosmosDB.
* [Ligue-se à Azure Data Lake Store por URI](#connect-to-azure-data-lake-store-by-uri). Utilize esta opção se tiver um URI para a Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Adicione um recurso via Azure AD

1. Selecione o símbolo **De Ligar** para abrir **o Connect to Azure Storage**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Se ainda não o fez, utilize a opção **Adicionar uma Conta Azure** para iniciar sessão na conta Azure que tenha acesso ao recurso. Depois de iniciar sessão, volte a **Ligar ao Armazenamento Azure**.

1. **Selecione Adicionar um recurso via Azure Ative Directory (Azure AD)** e, em seguida, selecione **Next**.

1. Selecione uma conta Azure e um inquilino. Estes valores devem ter acesso ao recurso de armazenamento a que pretende anexar. Selecione **Next**.

1. Escolha o tipo de recurso que pretende anexar. Introduza as informações necessárias para se ligar. 

   A informação que introduzir nesta página depende do tipo de recurso que está a adicionar. Certifique-se de escolher o tipo correto de recurso. Depois de ter introduzido as informações necessárias, selecione **Next**.

1. Reveja o resumo da **ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** às páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece em**recipientes** > de depósito anexados **&** > locais **(recipientes anexos)** > **blob**. Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar com sucesso a ligação, consulte o guia de resolução de problemas do Explorador de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Use uma corda de ligação

1. Selecione o símbolo **De Ligar** para abrir **o Connect to Azure Storage**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione Utilize uma corda de **ligação**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para a sua ligação e introduza a sua cadeia de ligação. Em seguida, selecione **Next**.

1. Reveja o resumo da **ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** às páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece nas Contas de > **Armazenamento** **Anexadas & Local.** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar com sucesso a ligação, consulte o guia de resolução de problemas do Explorador de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Use uma assinatura de acesso partilhado URI

1. Selecione o símbolo **De Ligar** para abrir **o Connect to Azure Storage**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione Utilize uma assinatura de **acesso partilhado (SAS) URI**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para a sua ligação e introduza a sua assinatura de acesso partilhado URI. O ponto final de serviço para o tipo de recurso que está a prender deve encher automaticamente. Se estáa a usar um ponto final personalizado, é possível que não. Selecione **Next**.

1. Reveja o resumo da **ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** às páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece em Contas de Armazenamento **Anexadas** > &**Locais** > **(Contentores Anexos)** > *o nó de serviço para o tipo de recipiente que anexou*. Se o Storage Explorer não conseguir adicionar a sua ligação, consulte o guia de resolução de problemas do [Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Consulte o guia de resolução de problemas se não conseguir aceder aos seus dados depois de adicionar com sucesso a ligação.

#### <a name="use-a-name-and-key"></a>Use um nome e uma chave

1. Selecione o símbolo **De Ligar** para abrir **o Connect to Azure Storage**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione Utilize o nome e a **chave da conta de armazenamento**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para a sua ligação.

1. Insira o nome da sua conta de armazenamento e qualquer uma das suas chaves de acesso.

1. Escolha o **domínio de Armazenamento** para utilizar e, em seguida, selecione **Seguinte**.

1. Reveja o resumo da **ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** às páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece nas Contas de > **Armazenamento** **Anexadas & Local.** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar com sucesso a ligação, consulte o guia de resolução de problemas do Explorador de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Storage Explorer suporta atualmente dois emuladores oficiais de armazenamento:

* [Emulador de armazenamento azure](storage/common/storage-use-emulator.md) (apenas windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o emulador estiver a ouvir as portas predefinidas, pode utilizar o nó do **Emulador - Portas Padrão** para aceder ao seu emulador. Procure **emulador - Portas padrão** sob as contas de armazenamento **anexadas** > &**locais**.

Se pretender utilizar um nome diferente para a sua ligação, ou se o seu emulador não estiver a funcionar nas portas predefinidas, siga estes passos:

1. Inicie o seu emulador. Introduza `AzureStorageEmulator.exe status` o comando para exibir as portas para cada tipo de serviço.

   > [!IMPORTANT]
   > O Storage Explorer não inicia automaticamente o seu emulador. Tem de ligá-lo manualmente.

1. Selecione o símbolo **De Ligar** para abrir **o Connect to Azure Storage**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione **Anexe-se a um emulador local**e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para a sua ligação e introduza as portas que o emulador está a ouvir para cada tipo de serviço. **Anexar a um Emulador Local** sugere os valores de porta padrão para a maioria dos emuladores. A porta de **ficheiros** está em branco, porque nenhum dos emuladores oficiais suporta atualmente o serviço Ficheiros. Se o emulador que está a utilizar apoiar ficheiros, pode introduzir a porta para utilizar. Em seguida, selecione **Next**.

1. Reveja o resumo da **ligação** e certifique-se de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** às páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O nó deve aparecer nas contas de armazenamento **anexadas** > &**locais.** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar com sucesso a ligação, consulte o guia de resolução de problemas do Explorador de [Armazenamento Azure](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ligue-se a uma conta Azure Cosmos DB usando uma cadeia de ligação

Em vez de gerir as contas do Azure Cosmos DB através de uma subscrição azure, pode ligar-se ao Azure Cosmos DB utilizando uma cadeia de ligação. Para se ligar, siga estes passos:

1. Em **EXPLORER**EXPLORER, expanda **o & Local Anexado,** clique à direita **nas Contas DB cosmos,** e selecione **Connect to Azure Cosmos DB**.

    ![Ligar ao Azure Cosmos DB através de uma cadeia de ligação][21]

1. Selecione o API DB Azure Cosmos, introduza os seus dados **de Linha de Ligação** e, em seguida, selecione **OK** para ligar a conta Azure Cosmos DB. Para obter informações sobre como recuperar a cadeia de ligação, consulte [Gerir uma conta Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Cadeia de ligação][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Ligue-se à Azure Data Lake Store por URI

Pode aceder a um recurso que não está na sua subscrição. Precisa de alguém que tenha acesso a esse recurso para lhe dar o recurso URI. Depois de iniciar sessão, ligue-se à Data Lake Store utilizando o URI. Para se ligar, siga estes passos:

1. Sob **o EXPLORER,** expandir **& Anexados locais.**

1. Clique à direita **data Lake Storage Gen1**, e selecione **Connect to Data Lake Storage Gen1**.

    ![Ligue-se ao menu de contexto data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Introduza o URI e, em seguida, selecione **OK**. A sua Loja data Lake aparece no armazenamento de **Data Lake.**

    ![Ligue-se ao resultado da Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Este exemplo utiliza data lake storage Gen1. Azure Data Lake Storage Gen2 já está disponível. Para mais informações, consulte [o que é Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Gerar uma assinatura de acesso partilhado no Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Assinatura de acesso partilhado ao nível da conta

1. Clique na conta de armazenamento que deseja partilhar e, em seguida, selecione Obter Assinatura de **Acesso Partilhado**.

    ![Obtenha opção de menu de menu de contexto de assinatura de acesso partilhado][14]

1. Na **Assinatura de Acesso Partilhado,** especifique o prazo e as permissões que deseja para a conta e, em seguida, selecione **Criar**.

    ![Obtenha uma assinatura de acesso partilhado][15]

1. Copie a **corda de ligação** ou a corda de **consulta crua** à sua prancheta.

### <a name="service-level-shared-access-signature"></a>Assinatura de acesso partilhado de nível de serviço

Pode obter uma assinatura de acesso partilhado ao nível do serviço. Para mais informações, consulte [o SAS para obter um recipiente de bolhas](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage

Para encontrar um recurso de armazenamento, pode pesquisar no painel **EXPLORER.**

Ao introduzir texto na caixa de pesquisa, o Storage Explorer exibe todos os recursos que correspondam ao valor de pesquisa que inseriu até esse ponto. Este exemplo mostra uma procura de **pontos finais:**

![Procurar conta de armazenamento][23]

> [!NOTE]
> Para acelerar a sua pesquisa, utilize a **Gestão de Conta** para desseleccionar quaisquer subscrições que não contenham o item que procura. Também pode clicar num nó e selecionar **Search From Here** para começar a procurar a partir de um nó específico.
>
>

## <a name="next-steps"></a>Passos seguintes

* [Gerir os recursos de armazenamento da Blob Azure com o Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Trabalhar com dados com o Explorador de Armazenamento do Azure](./cosmos-db/storage-explorer.md)
* [Gerir os recursos da Azure Data Lake Store com o Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
