---
title: Introdução ao Explorador de armazenamento | Documentos da Microsoft
description: Gerir recursos de armazenamento do Azure com o Explorador de armazenamento
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 61bb5601cf20e9f6e4fba1300d8f84c9d039e564
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225098"
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

Para obter mais ajuda a instalar o Explorador de armazenamento no Linux, consulte a [guia de resolução de problemas](https://docs.microsoft.com/en-us/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

O Azure Storage Explorer [notas de versão](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) conter passos específicos para algumas distribuições.

[Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento. Pode, por exemplo:

* Ligar a contas de armazenamento associadas às suas subscrições do Azure.
* Ligar a contas e serviços de armazenamento que são partilhados com outras subscrições do Azure.
* Ligar e gerir o armazenamento local através do Emulador do Armazenamento do Azure.

Além disso, pode trabalhar com contas de armazenamento no Azure global e nacional:

* [Ligar a uma subscrição do Azure](#connect-to-an-azure-subscription): Gerir recursos de armazenamento que pertencem à sua subscrição do Azure.
* [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage): Gerir o armazenamento local utilizando o emulador de armazenamento do Azure.
* [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account): Gerir recursos de armazenamento que pertencem a outra subscrição do Azure ou que são Clouds nacionais do Azure com o nome, a chave e os pontos finais da conta de armazenamento.
* [Anexar uma conta de armazenamento com SAS](#attach-a-storage-account-by-using-a-shared-access-signature-sas): Gerir recursos de armazenamento que pertencem a outra subscrição do Azure através de uma assinatura de acesso partilhado (SAS).
* [Anexar um serviço com SAS](#attach-a-service-by-using-a-shared-access-signature-sas): Gerir um serviço de armazenamento específico (contentor de blob, fila ou tabela) que pertence a outra subscrição do Azure com SAS.
* [Ligar a uma conta do Azure Cosmos DB com uma cadeia de ligação](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Gerir conta do Cosmos DB com uma cadeia de ligação.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

> [!NOTE]
> Se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Explorador de armazenamento, selecione **gerir contas** Ir para o **painel de gestão de conta**.

    ![Gerir contas][1]

2. O painel esquerdo apresenta agora todas as contas do Azure, que iniciou a sessão para. Para ligar a outra conta, selecione **adicionar uma conta**

3. Se quiser entrar numa nuvem nacional ou uma pilha do Azure, clique nas **ambiente do Azure** lista pendente para selecionar a cloud do Azure que pretende utilizar. Depois de escolher seu ambiente, clique no **iniciar sessão...**  botão. Se estiver a iniciar sessão no Azure Stack, veja [ligar o Explorador de armazenamento a uma subscrição do Azure Stack](azure-stack/user/azure-stack-storage-connect-se.md) para obter mais informações.

    ![Inicie sessão na opção][2]

4. Depois de iniciar com êxito com uma conta do Azure, a conta e as subscrições do Azure associadas à mesma são adicionadas ao painel do lado esquerdo. Selecione as subscrições do Azure que pretende trabalhar com e, em seguida, selecione **aplicar** (selecionando **todas as subscrições:** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas).

    ![Selecionar subscrições do Azure][3]

    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

## <a name="work-with-local-development-storage"></a>Trabalhar com armazenamento de desenvolvimento local

Com o Explorador de armazenamento, pode trabalhar com armazenamento local utilizando um emulador. Essa abordagem permite que pode simular a trabalhar com o armazenamento do Azure sem ter necessariamente uma conta de armazenamento implementada no Azure.

A partir da versão 1.1.0, emulador de armazenamento local é suportado em todas as plataformas. Explorador de armazenamento, pode ligar-se a qualquer serviço emulado ouvir seus pontos de extremidade de armazenamento local padrão.

> [!NOTE]
> Suporte para funcionalidades e serviços de armazenamento pode variar bastante, dependendo de sua escolha do emulador de. Certifique-se de que o emulador suporta os serviços e funcionalidades que pretende trabalhar com.

1. Configure os serviços de seu emulador à escolha para escutar uma porta não utilizada.

   Serviço emulado | Ponto final predefinido
   -----------------|-------------------------
   Blobs            | `http://127.0.0.1:10000`
   Filas           | `http://127.0.0.1:10001`
   Tabelas           | `http://127.0.0.1:10002`

2. Inicie o emulador.
   > [!IMPORTANT]
   > Explorador de armazenamento não inicia automaticamente o emulador. Deverá inicializá-lo por conta própria.

3. No Explorador de armazenamento, clique com o **adicionar conta** botão. Selecione **anexar a um emulador local** e clique em **próxima**.

4. Introduza os números de porta para os serviços que configurou acima (deixe em branco, caso não pretenda usá-lo). Clique em **próxima** , em seguida, **Connect** para criar a ligação.

5. Expanda a **Local e ligado** > **contas de armazenamento** > nós, em seguida, expanda os nós de serviço sob o nó correspondente para a ligação do emulador.

   Pode utilizar este nó para criar e trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta de armazenamento, consulte os seguintes guias:

   * [Gerir recursos de armazenamento de Blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
   * [Gerir recursos de armazenamento de ficheiros do Azure](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Anexar ou desanexar uma conta do Storage externo

Com o Explorador de armazenamento, pode anexar a contas do storage externo para que as contas de armazenamento podem ser partilhadas facilmente. Esta secção explica como anexar (e desanexar) de contas do Storage externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta do Storage

Para partilhar uma conta de armazenamento externo, o proprietário dessa conta tem primeiro de obter as credenciais (nome da conta e chave) para a conta e, em seguida, partilhar que informações com a pessoa que quer anexar a disse conta. Pode obter as credenciais da conta de armazenamento através do portal do Azure ao seguir estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **Procurar**.

3. Selecione **Contas do Storage**.

4. Na lista de **contas de armazenamento**, selecione a conta de armazenamento pretendido.

5. Em **Definições**, selecione **Chaves de acesso**.

    ![Opção Chaves de acesso][7]

6. Copiar o **nome da conta de armazenamento** e **chave1**.

    ![Chaves de acesso][8]

### <a name="attach-to-an-external-storage-account"></a>Anexar a uma conta do Storage externo

Para anexar a uma conta de armazenamento externa, é necessário o nome e a chave da conta. A secção “Obter as credenciais da conta de armazenamento” explica como obter estes valores a partir do portal do Azure. No entanto, no portal, a chave de conta é designada por **key1**. Então, quando o Explorador de armazenamento lhe pedir uma chave de conta, introduza o **chave1** valor.

1. No Explorador de armazenamento, abra a **ligue-se a caixa de diálogo**.

    ![Ligar à opção de armazenamento do Azure][9]

2. Na **ligue-se a caixa de diálogo**, escolha **utilize um nome de conta de armazenamento e a chave**

    ![Adicionar com o nome e a opção de chave][10]

3. Cole o nome da sua conta no **nome da conta** texto caixa e cole a chave da conta (a **key1** valor a partir do portal do Azure) para o **chave da conta de** caixa de texto e, em seguida, selecione **Seguinte**.

    ![Nome e a página chave][11]

    > [!NOTE]
    > Para utilizar um nome e chave de uma cloud nacional, utilize o **domínio de pontos finais de armazenamento:** lista pendente para selecionar o domínio de pontos de extremidade apropriados:
    >
    >

4. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se quiser alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas.

5. Selecione **Ligar**.

6. Depois da conta de armazenamento foi anexada com êxito, a conta de armazenamento é apresentada com **(externo)** anexado ao respetivo nome.

    ![Resultado da ligação a uma conta de armazenamento externo][12]

### <a name="detach-from-an-external-storage-account"></a>Desanexar de uma conta do Storage externo

1. Clique com o botão direito do rato na conta de armazenamento externo que pretende desanexar e selecione **Desanexar**.

    ![Anular a exposição da opção de armazenamento][13]

2. Na mensagem de confirmação, selecione **Sim** para confirmar a desanexação da conta de armazenamento externo.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Anexar uma conta de armazenamento com uma assinatura de acesso partilhado (SAS)

Uma assinatura de acesso partilhado, ou [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), permite que o administrador de uma subscrição do Azure de conceder acesso temporário a uma conta de armazenamento sem ter de fornecer credenciais de subscrição do Azure.

Para ilustrar este cenário, vamos supor que o UtilizadorA é um administrador de uma subscrição do Azure e que pretende permitir que o UtilizadorB aceda a uma conta de armazenamento por um período limitado de tempo com determinadas permissões:

1. O UtilizadorA gera uma cadeia de ligação SAS para um período de tempo específico e com as permissões pretendidas.

2. O UtilizadorA partilha a SAS com a pessoa (UtilizadorB, neste exemplo) que quer aceder à conta de armazenamento.

3. O UtilizadorB utiliza o Explorador de armazenamento para anexar à conta que pertence ao utilizadora através da SAS fornecida.

### <a name="generate-a-sas-query-string-for-the-account-you-want-to-share"></a>Gerar uma cadeia de caracteres de consulta SAS para a conta que pretende partilhar

1. No Explorador de armazenamento, clique com botão direito a conta de armazenamento que pretende partilhar e, em seguida, selecione **obter assinatura de acesso partilhado...** .

    ![Obter a opção de menu de contexto SAS][14]

2. Na **gerar assinatura de acesso partilhado** caixa de diálogo caixa, especifique o período de tempo e as permissões que pretende para a conta e, em seguida, clique nas **criar** botão.

    ![Obter caixa de diálogo SAS][15]

3. Junto a **cadeia de consulta** caixa de texto, selecione **cópia** copiá-lo para a área de transferência e, em seguida, clique em **fechar**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Anexar a uma conta de armazenamento com uma cadeia de ligação de SAS

1. No Explorador de armazenamento, abra a **ligue-se a caixa de diálogo**.

    ![Ligar à opção de armazenamento do Azure][9]

2. Na **ligue-se a caixa de diálogo** caixa de diálogo, escolha **utilizar uma cadeia de ligação ou URI de assinatura de acesso partilhado** e, em seguida, clique em **seguinte**.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][16]

3. Escolher **utilizar uma cadeia de ligação** e cole a cadeia de ligação para o **cadeia de ligação:** campo. Clique nas **seguinte** botão.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][17]

4. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Para fazer alterações, selecione **Regressar** e introduza as definições que pretende.

5. Selecione **Ligar**.

6. Depois da conta de armazenamento foi anexada com êxito, a conta de armazenamento é apresentada com **(SAS)** anexado ao respetivo nome.

    ![Resultado de anexação a uma conta com SAS][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Anexar um serviço com uma assinatura de acesso partilhado (SAS)

A secção "Anexar uma conta de armazenamento com uma SAS" explica como um administrador da subscrição do Azure pode conceder acesso temporário a uma conta de armazenamento por geração e a partilha uma SAS para a conta de armazenamento. Da mesma forma, é possível gerar uma SAS para um serviço específico (contentor de blob, fila, tabela ou partilha de ficheiros) dentro de uma conta de armazenamento.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Gerar uma SAS para o serviço que pretende partilhar

Neste contexto, um serviço pode ser um contentor de blob, fila, tabela ou partilha de ficheiros. Para gerar a SAS para um serviço listado, veja:

* [Obter a SAS para um contentor de blobs](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Anexar ao serviço de conta partilhada com um URI de SAS

1. No Explorador de armazenamento, abra a **ligue-se a caixa de diálogo**.

    ![Ligar à opção de armazenamento do Azure][9]

2. Na **ligue-se a caixa de diálogo** caixa de diálogo caixa, escolha **utilizar uma cadeia de ligação ou URI de assinatura de acesso partilhado** e, em seguida, clique em **seguinte**.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][16]

3. Escolher **utilizar um URI de SAS** e cole o URI para o **URI:** campo. Clique nas **seguinte** botão.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][19]

4. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Para fazer alterações, selecione **Regressar** e introduza as definições que pretende.

5. Selecione **Ligar**.

6. Depois do serviço é anexado com êxito, o serviço é apresentado sob o **(Serviços de SAS-Attached)** nó.

    ![Resultado da anexação a um serviço partilhado com SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ligar a uma conta do Azure Cosmos DB com uma cadeia de ligação

Além disso, gerir contas do Azure Cosmos DB através da subscrição Azure, uma forma alternativa de ligar ao Azure Cosmos DB é usar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Azure Cosmos DB** e selecione **Ligar ao Azure Cosmos DB...**

    ![ligar ao Azure Cosmos DB através de uma cadeia de ligação][21]

2. Escolha a API do Azure Cosmos DB, cole sua **cadeia de ligação**e, em seguida, clique em **OK** para ligar a conta do Azure Cosmos DB. Para obter informações sobre como obter a cadeia de ligação, veja [Obter a cadeia de ligação](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Ligar ao Azure Data Lake Store por URI

Se pretender obter acesso a recursos que não existem na sua subscrição. Mas outros concedem-lhe permissão para obter o Uri dos recursos. Neste caso, pode ligar ao Data Lake Store com o Uri, depois de ter sessão iniciada. Consulte os seguintes passos.

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda **Local e Ligado**.
3. Clique com botão direito do rato em **Data Lake Store** e, no menu de contexto, selecione **Ligar ao Data Lake Store...**.

    ![menu de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Introduza o Uri e, em seguida, a ferramenta navega para a localização do URL que acabou de introduzir.

    ![caixa de diálogo de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultado de Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

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
