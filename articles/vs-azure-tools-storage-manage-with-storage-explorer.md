---
title: Começa com o Storage Explorer Microsoft Docs
description: Comece a gerir os recursos de armazenamento da Azure com o Storage Explorer. Faça o download e instale o Azure Storage Explorer, ligue-se a uma conta de armazenamento ou serviço, e muito mais.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: be9b2d9a31d4affc9615f5d2f4b2585b7533a0f6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545912"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Explorador de Armazenamento

## <a name="overview"></a>Descrição geral

O Explorador de Armazenamento do Microsoft Azure é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento do Microsoft Azure no Windows, macOS e Linux. Neste artigo, você vai aprender várias formas de ligar e gerir as suas contas de armazenamento Azure.

![Explorador de Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windows"></a>[Windows](#tab/windows)

As seguintes versões do Explorador de Armazenamento de Suporte do Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, o Storage Explorer requer .NET Framework 4.7.2 no mínimo.

# <a name="macos"></a>[macOS](#tab/macos)

As seguintes versões do suporte ao macOS Storage Explorer:

* macOS 10.12 Sierra e versões posteriores

# <a name="linux"></a>[Linux](#tab/linux)

O Storage Explorer está disponível na [Snap Store](https://snapcraft.io/storage-explorer) para distribuição mais comum do Linux. Recomendamos a Snap Store para esta instalação. O snap do Storage Explorer instala todas as suas dependências e atualizações quando novas versões são publicadas na Snap Store.

Para distribuição suportada, consulte a [página de instalação estaladiço](https://snapcraft.io/docs/installing-snapd).

O Storage Explorer requer a utilização de um gestor de passwords. Pode ter de ligar-se manualmente a um gestor de passwords. Pode ligar o Storage Explorer ao gestor de passwords do seu sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

O Storage Explorer também está disponível como *.tar.gz* download. Tem que instalar dependências manualmente. As seguintes distribuições de suporte Linux *.tar.gz* instalação:

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

A *instalação .tar.gz* pode funcionar em outras distribuições, mas apenas estas listadas são oficialmente suportadas.

Para obter mais ajuda para instalar o Storage Explorer no Linux, consulte as [dependências do Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) no guia de resolução de problemas do Azure Storage Explorer.

---

## <a name="download-and-install"></a>Transferir e instalar

Para descarregar e instalar o Storage Explorer, consulte [o Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento. Em geral, pode:

* [Inscreva-se no Azure para aceder às suas subscrições e aos seus recursos](#sign-in-to-azure)
* [Anexar um recurso específico de armazenamento ou CosmosDB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

> [!NOTE]
> Para aceder totalmente aos recursos após a sua sinstrução, o Storage Explorer requer tanto permissões de gestão (Azure Resource Manager) como de camadas de dados. Isto significa que precisa de permissões do Azure Ative Directory (Azure AD), que lhe dão acesso à sua conta de armazenamento, aos contentores da conta e aos dados nos contentores. Se tiver permissões apenas na camada de dados, considere [adicionar um recurso através do Azure AD](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões específicas que o Storage Explorer necessita, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. No Explorador de Armazenamento, selecione **Ver**  >  **Gestão de Conta** ou selecione o botão **'Gerir contas'.**

    ![Gerir Contas][1]

1. **A CONTA MANAGEMENT** apresenta agora todas as contas Azure a que se inscreveu. Para ligar a outra conta, **selecione Adicionar uma conta**.

1. No **Connect to Azure Storage**, selecione uma nuvem Azure do ambiente **Azure** para iniciar sação numa nuvem nacional ou numa Pilha Azure. Depois de escolher o seu ambiente, selecione **Seguinte**.

    ![Opção de iniciar sinssução][2]

    O Storage Explorer abre uma página para iniciar sação. Para obter mais informações, consulte [o explorador de armazenamento Connect a uma subscrição ou conta de armazenamento do Azure Stack.](/azure-stack/user/azure-stack-storage-connect-se)

1. Depois de iniciar seduca com sucesso com uma conta Azure, a conta e as subscrições Azure associadas a essa conta aparecem na **GESTÃO DE CONTA**. Selecione **Todas as subscrições** para alternar a sua seleção entre todas ou nenhuma das subscrições Azure listadas. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**.

    ![Selecionar subscrições do Azure][3]

    **O EXPLORER** exibe as contas de armazenamento associadas às subscrições Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico

Existem várias formas de anexar a um recurso no Storage Explorer:

* [Adicione um recurso via Azure AD](#add-a-resource-via-azure-ad). Se tiver permissões apenas na camada de dados, utilize esta opção para adicionar um recipiente blob ou um recipiente de armazenamento Azure Data Lake Gen2 Blob.
* [Utilize uma cadeia de ligação.](#use-a-connection-string) Utilize esta opção se tiver um fio de ligação a uma conta de armazenamento. O Storage Explorer suporta as cordas de ligação [de assinaturas](./storage/common/storage-sas-overview.md) de acesso chave e partilhado.
* [Utilize uma assinatura de acesso partilhado URI](#use-a-shared-access-signature-uri). Se tiver uma [assinatura de acesso partilhado URI](./storage/common/storage-sas-overview.md) a um recipiente de bolhas, partilha de ficheiros, fila ou mesa, utilize-a para anexar o recurso. Para obter uma assinatura de acesso partilhado URI, pode utilizar [o Storage Explorer](#generate-a-sas-in-storage-explorer) ou o portal [Azure.](https://portal.azure.com)
* [Use um nome e uma chave.](#use-a-name-and-key) Se souber de alguma das chaves da conta da sua conta de armazenamento, pode utilizar esta opção para se ligar rapidamente. Encontre as suas chaves na página da conta de armazenamento selecionando **as**  >  **teclas de acesso** de Definições no portal [Azure](https://portal.azure.com).
* [Ligue-se a um emulador local.](#attach-to-a-local-emulator) Se estiver a utilizar um dos emuladores de armazenamento Azure disponíveis, utilize esta opção para se ligar facilmente ao seu emulador.
* [Ligue-se a uma conta DB da Azure Cosmos utilizando uma cadeia de ligação](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Utilize esta opção se tiver uma cadeia de ligação a uma instância CosmosDB.
* [Ligue-se à Azure Data Lake Store by URI](#connect-to-azure-data-lake-store-by-uri). Utilize esta opção se tiver um URI para a Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Adicione um recurso via Azure AD

1. Selecione o símbolo **'Ligar'** para abrir **o armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Se ainda não o fez, utilize a opção **Adicionar uma Conta Azure** para iniciar sação na conta Azure que tenha acesso ao recurso. Depois de iniciar sação, volte a **Ligar ao Azure Storage**.

1. **Selecione Adicionar um recurso via Azure Ative Directory (Azure AD)** e, em seguida, selecione **Next**.

1. Selecione uma conta Azure e inquilino. Estes valores devem ter acesso ao recurso de Armazenamento a que pretende anexar. Selecione **Seguinte**.

1. Escolha o tipo de recurso que pretende anexar. Insira a informação necessária para ligar. 

   A informação que introduz nesta página depende do tipo de recurso que está a adicionar. Certifique-se de que escolhe o tipo correto de recurso. Depois de introduzir as informações necessárias, selecione **Next**.

1. Reveja o **Resumo da Ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** para as páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece nos recipientes de & de armazenamento **Local & Attached**  >  **anexos**  >  **locais (contentores**  >  **anexos) .** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar a ligação com sucesso, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-connection-string"></a>Utilizar uma cadeia de ligação

1. Selecione o símbolo **'Ligar'** para abrir **o armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][9]

1. **Selecione Utilize uma cadeia de ligação** e, em seguida, selecione **Seguinte**.

1. Escolha um nome de visualização para a sua ligação e introduza a sua cadeia de ligação. Em seguida, selecione **Seguinte**.

1. Reveja o **Resumo da Ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** para as páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece nas contas de armazenamento **anexadas &**  >  **Locais.** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar a ligação com sucesso, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="use-a-shared-access-signature-uri"></a>Utilizar um URI de assinatura de acesso partilhado

1. Selecione o símbolo **'Ligar'** para abrir **o armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione **Utilize uma assinatura de acesso partilhado (SAS) URI** e, em seguida, selecione **Next**.

1. Escolha um nome de exibição para a sua ligação e introduza a sua assinatura de acesso partilhado URI. O ponto final de serviço para o tipo de recurso que está a anexar deve ser preenchido automaticamente. Se está a usar um ponto final personalizado, é possível que não. Selecione **Seguinte**.

1. Reveja o **Resumo da Ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** para as páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece em & Contas de Armazenamento **Anexadas**  >  **Storage Accounts**  >  **(Contentores Anexos)**  >  *o nó de serviço para o tipo de recipiente que anexou*. Se o Storage Explorer não conseguir adicionar a sua ligação, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md). Consulte o guia de resolução de problemas se não conseguir aceder aos seus dados depois de ter acedido com sucesso a ligação.

#### <a name="use-a-name-and-key"></a>Utilizar um nome e uma chave

1. Selecione o símbolo **'Ligar'** para abrir **o armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][9]

1. Selecione **Utilize o nome e a chave da conta de armazenamento** e, em seguida, selecione **Seguinte**.

1. Escolha um nome de exibição para a sua ligação.

1. Insira o nome da sua conta de armazenamento e qualquer uma das suas chaves de acesso.

1. Escolha o **domínio de Armazenamento** para utilizar e, em seguida, selecione **Seguinte**.

1. Reveja o **Resumo da Ligação** para se certificar de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** para as páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O recurso aparece nas contas de armazenamento **anexadas &**  >  **Locais.** Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar a ligação com sucesso, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Storage Explorer suporta atualmente dois emuladores oficiais de armazenamento:

* [Emulador de Armazenamento Azure](storage/common/storage-use-emulator.md) (apenas para windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o seu emulador estiver a ouvir nas portas predefinidos, pode utilizar o nó **Emulador - Portas Padrão** para aceder ao seu emulador. Procure **emulador - Portas Padrão** em **Contas de Armazenamento &**  >  **Locais anexas**.

Se pretender utilizar um nome diferente para a sua ligação, ou se o seu emulador não estiver a funcionar nas portas predefinidas, siga estes passos:

1. Comece o seu emulador. Introduza o comando `AzureStorageEmulator.exe status` para visualizar as portas para cada tipo de serviço.

   > [!IMPORTANT]
   > O Storage Explorer não inicia automaticamente o seu emulador. Deve ligá-lo manualmente.

1. Selecione o símbolo **'Ligar'** para abrir **o armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][9]

1. **Selecione Fixe-se a um emulador local** e, em seguida, selecione **Seguinte**.

1. Escolha um nome de visor para a sua ligação e introduza as portas onde o seu emulador está a ouvir para cada tipo de serviço. **A fixação a um Emulador Local** sugere os valores de porta padrão para a maioria dos emuladores. **A porta de ficheiros** está em branco, porque nenhum dos emuladores oficiais suporta atualmente o serviço Ficheiros. Se o emulador que está a utilizar suportar ficheiros, pode entrar na porta para utilizar. Em seguida, selecione **Seguinte**.

1. Reveja o **Resumo da Ligação** e certifique-se de que todas as informações estão corretas. Se for, selecione **Connect**. Caso contrário, selecione **Voltar** para as páginas anteriores para corrigir qualquer informação incorreta.

Após a ligação ser adicionada com sucesso, a árvore de recursos vai para o nó que representa a ligação. O nó deve aparecer nas contas de armazenamento **&**  >  **Locais anexas**. Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar a ligação com sucesso, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ligue-se a uma conta DB Azure Cosmos usando uma cadeia de ligação

Em vez de gerir as contas DB da Azure Cosmos através de uma subscrição do Azure, pode ligar-se ao Azure Cosmos DB utilizando uma cadeia de ligação. Para se ligar, siga estes passos:

1. No **âmbito do EXPLORER**, expanda as contas de & **Locais anexadas,** clique à direita, **e** selecione Connect **to Azure Cosmos DB**.

    ![Ligar ao Azure Cosmos DB através de uma cadeia de ligação][21]

1. Selecione a Azure Cosmos DB API, introduza os dados **da sua Cadeia de Ligação** e, em seguida, selecione **OK** para ligar a conta DB Azure Cosmos. Para obter informações sobre como recuperar a cadeia de ligação, consulte [Gerir uma conta Azure Cosmos](./cosmos-db/how-to-manage-database-account.md).

    ![Cadeia de ligação][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Ligue-se à Azure Data Lake Store by URI

Pode aceder a um recurso que não está na sua assinatura. Precisa de alguém que tenha acesso a esse recurso para lhe dar o recurso URI. Depois de iniciar sinsuata, ligue-se à Data Lake Store utilizando o URI. Para se ligar, siga estes passos:

1. No **âmbito do EXPLORER**, expanda & **Locais Anexados**.

1. Clique à direita no **Data Lake Storage Gen1** e selecione **Connect to Data Lake Storage Gen1**.

    ![Conecte-se ao menu de contexto da Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Introduza o URI e, em seguida, selecione **OK**. A sua Loja data lake aparece no **armazenamento de data lake.**

    ![Ligue-se ao resultado da Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

Este exemplo utiliza data lake storage gen1. AZure Data Lake Storage Gen2 já está disponível. Para mais informações, consulte [o que é Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md).

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Gere uma assinatura de acesso partilhado no Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Assinatura de acesso partilhado ao nível da conta

1. Clique à direita na conta de armazenamento que deseja partilhar e, em seguida, **selecione Obter Assinatura de Acesso Partilhado**.

    ![Obtenha opção de menu de contexto de assinatura de acesso compartilhado][14]

1. Na **Assinatura de Acesso Partilhado**, especifique o prazo e as permissões desejadas para a conta e, em seguida, selecione **Criar**.

    ![Obtenha uma assinatura de acesso compartilhado][15]

1. Copie a **cadeia de ligação** ou a **cadeia de consulta** bruta para a sua área de transferência.

### <a name="service-level-shared-access-signature"></a>Assinatura de acesso compartilhado de nível de serviço

Pode obter uma assinatura de acesso partilhado ao nível do serviço. Para obter mais informações, [consulte o SAS para obter um recipiente de bolhas.](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage

Para encontrar um recurso de armazenamento, pode pesquisar no painel **EXPLORER.**

Ao introduzir texto na caixa de pesquisa, o Storage Explorer exibe todos os recursos que correspondem ao valor de pesquisa que introduziu até esse ponto. Este exemplo mostra uma procura por **pontos finais:**

![Procurar conta de armazenamento][23]

> [!NOTE]
> Para acelerar a sua pesquisa, utilize a **Gestão** de Contas para desmarcar quaisquer subscrições que não contenham o item que procura. Também pode clicar no botão direito de um nó e selecionar **Search From Here** para começar a procurar a partir de um nó específico.
>
>

## <a name="next-steps"></a>Passos seguintes

* [Gerir os recursos de armazenamento Azure Blob com o Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
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