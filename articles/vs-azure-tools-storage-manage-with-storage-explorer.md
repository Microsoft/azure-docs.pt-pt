---
title: Introdução ao Explorador de armazenamento | Documentos da Microsoft
description: Gerir recursos de armazenamento do Azure com o Explorador de armazenamento
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508732"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Explorador de armazenamento

## <a name="overview"></a>Descrição geral

Explorador de armazenamento do Azure é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, macOS e Linux. Neste artigo, irá aprender as várias formas de ligar e gerir as contas de armazenamento do Azure.

![Explorador de Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Explorador de armazenamento do Azure é suportado nas seguintes versões do Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, o .NET Framework 4.6.2 ou superior é obrigatório.

[Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Explorador de armazenamento do Azure é suportado nas seguintes versões do macOS:

* o macOS 10.12 "Sierra" e versões posteriores

[Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Explorador de armazenamento do Azure tem suporte nas seguintes distribuições do Linux:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Explorador de armazenamento do Azure podem funcionar em outras distribuições, mas são suportados oficialmente únicos listados acima.

Para obter mais ajuda a instalar o Explorador de armazenamento no Linux, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

O Azure Storage Explorer [notas de versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) conter passos específicos para algumas distribuições.

[Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento. Em geral, pode optar por:

* [Inicie sessão no Azure para aceder às suas subscrições e seus recursos](#sign-in-to-azure)
* [Anexar um recurso específico do armazenamento ou cosmos DB](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

> [!NOTE]
> Para aceder a recursos totalmente depois de iniciar sessão, o Explorador de armazenamento requer gestão (ARM) e as permissões de camada de dados. Isso significa que precisa de permissões do Azure AD, que proporciona acesso a sua conta de armazenamento, os contentores na conta e os dados nos contentores. Se tiver apenas permissões na camada de dados, considere utilizar [Attach com o Azure AD](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões exatas requer o Explorador de armazenamento, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. No Explorador de armazenamento, selecione **gerir contas** Ir para o **painel de gestão de conta**.

    ![Gerir contas][1]

2. O painel esquerdo apresenta agora todas as contas do Azure, que iniciou a sessão para. Para ligar a outra conta, selecione **adicionar uma conta**

3. Se quiser entrar numa nuvem nacional ou uma pilha do Azure, clique nas **ambiente do Azure** lista pendente para selecionar a cloud do Azure que pretende utilizar. Depois de escolher seu ambiente, clique no **iniciar sessão...**  botão. Se estiver a iniciar sessão no Azure Stack, veja [ligar o Explorador de armazenamento a uma subscrição do Azure Stack](/azure-stack/user/azure-stack-storage-connect-se) para obter mais informações.

    ![Inicie sessão na opção][2]

4. Depois de iniciar com êxito com uma conta do Azure, a conta e as subscrições do Azure associadas à mesma são adicionadas ao painel do lado esquerdo. Selecione as subscrições do Azure que pretende trabalhar com e, em seguida, selecione **aplicar** (selecionando **todas as subscrições:** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas).

    ![Selecionar subscrições do Azure][3]

    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico
    
Existem diversas opções para anexar um recurso para o Explorador de armazenamento. Pode:

* [Adicionar um recurso através do Azure AD](#add-a-resource-via-azure-ad): Se tiver apenas permissões na camada de dados, em seguida, pode utilizar esta opção para adicionar um contentor de BLOBs ou de um contentor de BLOBs de geração 2 do ADLS.
* [Utilize uma cadeia de ligação](#use-a-connection-string): Se tiver uma cadeia de ligação para uma conta de armazenamento. Explorador de armazenamento suporta a chave e [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) cadeias de ligação.
* [Utilizar um URI de SAS](#use-a-sas-uri): Se tiver um [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI para um contentor de BLOBs, a partilha de ficheiros, a fila ou a tabela. Para obter um URI de SAS, pode utilizar [Explorador de armazenamento](#generate-a-sas-in-storage-explorer) ou o [portal do Azure](https://portal.azure.com).
* [Utilizar um nome e chave](#use-a-name-and-key): Se souber a qualquer uma das chaves de conta para a sua conta de armazenamento, pode utilizar esta opção para ligar-se rapidamente. As chaves para a sua conta de armazenamento estão localizadas na conta de armazenamento **chaves de acesso** painel com o [portal do Azure](https://portal.azure.com).
* [Anexar a um emulador local](#attach-to-a-local-emulator): Se estiver a utilizar um dos emuladores de armazenamento do Azure disponíveis, utilize esta opção para ligar facilmente a sua emulador.
* [Ligar a uma conta do Azure Cosmos DB com uma cadeia de ligação](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Se tiver uma cadeia de ligação para uma instância de cosmos DB.
* [Ligar ao Azure Data Lake Store pelo URI](#connect-to-azure-data-lake-store-by-uri): Se tiver um URI de um Store do Azure Data Lake.

#### <a name="add-a-resource-via-azure-ad"></a>Adicionar um recurso através do Azure AD

1. Abra o **ligar-se a caixa de diálogo** ao clicar no **botão Ligar** por lado esquerdo, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Se ainda não o fez, utilize o **adicionar uma conta do Azure** opção de iniciar sessão conta do Azure que tem acesso ao recurso. Depois de entrar em troca da **ligue-se a caixa de diálogo**.

3. Selecione o **adicionar um recurso através do Azure Active Directory (Azure AD)** opção e clique em **próxima**.

4. Selecione a conta do Azure que tem acesso para o recurso de armazenamento que pretende anexar e a subscrição que o recurso está no e, em seguida, clique em **seguinte**.

5. Escolha o tipo de recurso que pretende anexar e, em seguida, introduza as informações necessárias para ligar. As entradas nesta página serão alterado consoante o tipo de recurso que está a adicionar. Certifique-se de escolher o tipo correto de recursos. Assim que tiver preenchido o informações necessárias clique **seguinte**.

6. Reveja o resumo da ligação e certifique-se de que todas as informações estão corretas. Se todas as informações parecer correto, em seguida, clique em **Connect**, caso contrário, volte às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Assim que a ligação é adicionada com êxito, a árvore de recursos automaticamente irá navegar para o nó que representa a ligação. Se por alguma razão não, procure em **Local e ligado** → **contas de armazenamento** → **(contentores anexado)** → **contentores de BLOBs** . Se não foi possível adicionar a ligação Explorador de armazenamento, ou se não pode aceder aos seus dados depois de adicionar a ligação com êxito, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-connection-string"></a>Utilize uma cadeia de ligação

1. Abra o **ligar-se a caixa de diálogo** ao clicar no **botão Ligar** por lado esquerdo, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione o **utilizar uma cadeia de ligação** opção e clique em **próxima**.

3. Escolha um nome a apresentar para a sua ligação e introduza a cadeia de ligação. Clique depois em **Seguinte**.

4. Reveja o resumo da ligação e certifique-se de que todas as informações estão corretas. Se todas as informações parecer correto, em seguida, clique em **Connect**, caso contrário, volte às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Assim que a ligação é adicionada com êxito, a árvore de recursos automaticamente irá navegar para o nó que representa a ligação. Se por alguma razão não, procure em **Local e ligado** → **contas de armazenamento**. Se não foi possível adicionar a ligação Explorador de armazenamento, ou se não pode aceder aos seus dados depois de adicionar a ligação com êxito, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-sas-uri"></a>Utilizar um URI de SAS

1. Abra o **ligar-se a caixa de diálogo** ao clicar no **botão Ligar** por lado esquerdo, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione o **utilizar uma assinatura de acesso partilhado (SAS) URI** opção e clique em **próxima**.

3. Escolha um nome a apresentar para a sua ligação e introduza o URI de SAS. O ponto final de serviço para o tipo de recurso que associar deve preenchimento automático. Se estiver a utilizar um ponto final personalizado, em seguida, é possível que ele não pode. Clique em **Seguinte**.

4. Reveja o resumo da ligação e certifique-se de que todas as informações estão corretas. Se todas as informações parecer correto, em seguida, clique em **Connect**, caso contrário, volte às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Assim que a ligação é adicionada com êxito, a árvore de recursos automaticamente irá navegar para o nó que representa a ligação. Se por alguma razão não, procure em **Local e ligado** → **contas de armazenamento** → **(contentores anexado)** → **o nó de serviço para o tipo de contentor anexou**. Se não foi possível adicionar a ligação Explorador de armazenamento, ou se não pode aceder aos seus dados depois de adicionar a ligação com êxito, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-name-and-key"></a>Utilizar um nome e chave

1. Abra o **ligar-se a caixa de diálogo** ao clicar no **botão Ligar** por lado esquerdo, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione o **utilize um nome de conta de armazenamento e a chave** opção e clique em **próxima**.

3. Escolha um nome a apresentar para a sua ligação.

4. Introduza o nome da sua conta de armazenamento e de qualquer uma das respetivas chaves de acesso.

5. Escolha o **domínio de armazenamento** para utilizar e, em seguida, clique em **próxima**.

4. Reveja o resumo da ligação e certifique-se de que todas as informações estão corretas. Se todas as informações parecer correto, em seguida, clique em **Connect**, caso contrário, volte às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Assim que a ligação é adicionada com êxito, a árvore de recursos automaticamente irá navegar para o nó que representa a ligação. Se por alguma razão não, procure em **Local e ligado** → **contas de armazenamento**. Se não foi possível adicionar a ligação Explorador de armazenamento, ou se não pode aceder aos seus dados depois de adicionar a ligação com êxito, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

Explorador de armazenamento suporta emuladores em todas as plataformas. Os dois atualmente oficiais emuladores disponíveis são:
* [Emulador de armazenamento do Azure](storage/common/storage-use-emulator.md) (apenas Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o emulador está em execução nas portas predefinidas pode utilizar o **emulador - as portas predefinidas** nó, que sempre pode ser encontrado na **Local e ligado** → **contas de armazenamento** , para aceder rapidamente o emulador. Se pretender utilizar um nome diferente para a sua ligação ou se o emulador não está em execução nas portas predefinidas, em seguida, siga os passos abaixo.

1. Inicie o emulador. Quando, tome nota das portas de que o emulador está escutando para cada tipo de serviço. Terá de conhecer estas informações mais tarde.

   > [!IMPORTANT]
   > Explorador de armazenamento não inicia automaticamente o emulador. Deverá inicializá-lo por conta própria.

2. Abra o **ligar-se a caixa de diálogo** ao clicar no **botão Ligar** por lado esquerdo, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

3. Selecione o **anexar a um emulador local** opção e clique em **próxima**.

4. Escolha um nome a apresentar para a sua ligação e introduza as portas que o emulador está a escutar para cada tipo de serviço. Por predefinição, as caixas de texto irão conter os valores de porta predefinidos para a maioria dos emuladores. O **ficheiros porta** também for deixado em branco por predefinição, nenhum dos emuladores oficiais atualmente suporta o serviço de ficheiros. Se o emulador que estiver a utilizar de o suportar no entanto, em seguida, pode introduzir na porta que está a ser utilizada. Clique em **Seguinte**.

5. Reveja o resumo da ligação e certifique-se de que todas as informações estão corretas. Se todas as informações parecer correto, em seguida, clique em **Connect**, caso contrário, volte às páginas anteriores com o **volta** botão para corrigir quaisquer informações erradas.

Assim que a ligação é adicionada com êxito, a árvore de recursos automaticamente irá navegar para o nó que representa a ligação. Se por alguma razão não, procure em **Local e ligado** → **contas de armazenamento**. Se não foi possível adicionar a ligação Explorador de armazenamento, ou se não pode aceder aos seus dados depois de adicionar a ligação com êxito, consulte a [guia de resolução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ligar a uma conta do Azure Cosmos DB com uma cadeia de ligação

Além disso, gerir contas do Azure Cosmos DB através da subscrição Azure, uma forma alternativa de ligar ao Azure Cosmos DB é usar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Azure Cosmos DB** e selecione **Ligar ao Azure Cosmos DB...**

    ![ligar ao Azure Cosmos DB através de uma cadeia de ligação][21]

2. Escolha a API do Azure Cosmos DB, cole sua **cadeia de ligação**e, em seguida, clique em **OK** para ligar a conta do Azure Cosmos DB. Para obter informações sobre como obter a cadeia de ligação, veja [Obter a cadeia de ligação](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Ligar ao Azure Data Lake Store por URI

Se pretender obter acesso a recursos que não existem na sua subscrição. Mas outros concedem-lhe permissão para obter o Uri dos recursos. Neste caso, pode ligar ao Data Lake Store com o Uri, depois de ter sessão iniciada. Consulte os seguintes passos.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda **Local e Ligado**.
3. Clique com botão direito do rato em **Data Lake Store** e, no menu de contexto, selecione **Ligar ao Data Lake Store...** .

    ![menu de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Introduza o Uri e, em seguida, a ferramenta navega para a localização do URL que acabou de introduzir.

    ![caixa de diálogo de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultado de Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Gerar uma SAS no Explorador de armazenamento

### <a name="account-level-sas"></a>SAS de nível de conta

1. Com a conta de armazenamento que pretende partilhar e, em seguida, selecione o botão direito **obter assinatura de acesso partilhado...** .

    ![Obter a opção de menu de contexto SAS][14]

2. Na **gerar assinatura de acesso partilhado** caixa de diálogo caixa, especifique o período de tempo e as permissões que pretende para a conta e, em seguida, clique nas **criar** botão.

    ![Obter caixa de diálogo SAS][15]

3. Pode agora copie a **cadeia de ligação** ou o não processados **cadeia de consulta** para sua área de transferência.

### <a name="service-level-sas"></a>SAS de nível de serviço

[Como obter uma SAS para um contentor de BLOBs no Explorador de armazenamento](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage

Se precisar de encontrar um recurso de armazenamento e não souber onde se encontra, pode utilizar a caixa de pesquisa na parte superior do painel da esquerda para procurar o recurso.

À medida que escreve na caixa de pesquisa, o painel esquerdo apresenta todos os recursos que corresponde ao valor de pesquisa que introduziu até esse ponto. Por exemplo, uma pesquisa por **pontos de extremidade** é mostrado na captura de ecrã seguinte:

![Procurar conta de armazenamento][23]

> [!NOTE]
> Utilize o **painel de gestão de conta** para desmarcar quaisquer subscrições que não contêm o item de procura para melhorar o tempo de execução da sua pesquisa. Também pode num nó com o botão direito e escolher **pesquisa de aqui** para iniciar a procura de um nó específico.
>
>

## <a name="next-steps"></a>Passos Seguintes

* [Gerir recursos de armazenamento de Blobs do Azure com o Explorador de armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Gerir o Azure Cosmos DB no Explorador de armazenamento do Azure (pré-visualização)](./cosmos-db/storage-explorer.md)
* [Gerir recursos do Azure Data Lake Store com o Explorador de armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
