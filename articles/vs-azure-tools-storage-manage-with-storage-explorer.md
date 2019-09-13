---
title: Introdução ao Gerenciador de Armazenamento | Microsoft Docs
description: Gerenciar recursos de armazenamento do Azure com o Gerenciador de Armazenamento
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: c4aad2f2f5bca25ead03518b2de9ac9315172052
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934750"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Gerenciador de Armazenamento

## <a name="overview"></a>Descrição geral

Gerenciador de Armazenamento do Microsoft Azure é um aplicativo autônomo que permite que você trabalhe facilmente com os dados do armazenamento do Azure no Windows, no macOS e no Linux. Neste artigo, você aprende várias maneiras de se conectar e gerenciar suas contas de armazenamento do Azure.

![Explorador de Armazenamento do Microsoft Azure][0]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Gerenciador de Armazenamento tem suporte nas seguintes versões do Windows:

* Windows 10 (recomendado)
* Windows 8
* Windows 7

Para todas as versões do Windows, .NET Framework 4.6.2 ou superior é necessário.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Gerenciador de Armazenamento tem suporte nas seguintes versões do macOS:

* macOS 10,12 "Sierra" e versões posteriores

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Gerenciador de Armazenamento está disponível no [repositório de snap](https://snapcraft.io/storage-explorer) para as distribuições mais comuns do Linux e é o método recomendado de instalação. O snap Gerenciador de Armazenamento instala automaticamente todas as suas dependências e atualizações à medida que novas versões são publicadas no repositório de instantâneos.

Para obter uma lista de distribuições com suporte, visite a [página de instalação do snapD](https://snapcraft.io/docs/installing-snapd).

Gerenciador de Armazenamento requer o uso de um Gerenciador de senhas, que pode precisar ser conectado manualmente antes que Gerenciador de Armazenamento funcione corretamente. Você pode se conectar Gerenciador de Armazenamento ao Gerenciador de senhas do seu sistema com o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Gerenciador de Armazenamento também está disponível como um download. tar. gz, mas você precisará instalar as dependências manualmente. A instalação do. tar. gz tem suporte nas seguintes distribuições do Linux:

* Ubuntu 18, 4 x64
* Ubuntu 16, 4 x64
* Ubuntu 14, 4 x64

A instalação do. tar. gz pode funcionar em outras distribuições, mas somente aquelas listadas acima têm suporte oficial.

Para obter mais ajuda para instalar o Gerenciador de Armazenamento no Linux, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Transferir e instalar

[Transferir e instalar o Explorador de Armazenamento](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento. Em geral, você pode:

* [Entre no Azure para acessar suas assinaturas e seus recursos](#sign-in-to-azure)
* [Anexar um recurso de armazenamento ou CosmosDB específico](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

> [!NOTE]
> Para acessar totalmente os recursos depois de entrar, Gerenciador de Armazenamento requer as permissões de gerenciamento (ARM) e de camada de dados. Isso significa que você precisa de permissões do Azure AD que lhe dão acesso à sua conta de armazenamento, aos contêineres na conta e aos dados nos contêineres. Se você só tiver permissões na camada de dados, considere o uso [de Attach com o Azure ad](#add-a-resource-via-azure-ad). Para obter mais informações sobre as permissões exatas Gerenciador de Armazenamento requer, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).

1. Em Gerenciador de Armazenamento, selecione **gerenciar contas** para acessar o **painel de gerenciamento de conta**.

    ![Gerenciar contas][1]

2. O painel esquerdo agora exibe todas as contas do Azure às quais você se conectou. Para se conectar a outra conta, selecione **Adicionar uma conta**

3. Se você quiser entrar em uma nuvem nacional ou em um Azure Stack, clique na lista suspensa **ambiente do Azure** para selecionar qual nuvem do Azure você deseja usar. Depois de escolher o ambiente, clique no botão **entrar...** . Para obter mais informações, consulte [conectar Gerenciador de armazenamento a uma assinatura do Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

    ![Opção de entrada][2]

4. Depois de entrar com êxito com uma conta do Azure, a conta e as assinaturas do Azure associadas a essa conta serão adicionadas ao painel esquerdo. Selecione as assinaturas do Azure com as quais você deseja trabalhar e, em seguida, selecione **aplicar** (selecionando **todas as assinaturas:** alterna a seleção de todas ou nenhuma das assinaturas do Azure listadas).

    ![Selecionar subscrições do Azure][3]

    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

### <a name="attach-a-specific-resource"></a>Anexar um recurso específico

Você pode anexar a um recurso no Gerenciador de Armazenamento usando diferentes opções:

* [Adicionar um recurso por meio do Azure ad](#add-a-resource-via-azure-ad): Se você só tiver permissões na camada de dados, poderá usar essa opção para adicionar um contêiner de BLOBs ou um contêiner de BLOBs ADLS Gen2.
* [Use uma cadeia de conexão](#use-a-connection-string): Se você tiver uma cadeia de conexão para uma conta de armazenamento. Gerenciador de Armazenamento dá suporte às cadeias de conexão de chave e [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) .
* [Usar um URI de SAS](#use-a-sas-uri): Se você tiver um URI de [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) para um contêiner de BLOB, compartilhamento de arquivos, fila ou tabela. Para obter um URI de SAS, você pode usar [Gerenciador de armazenamento](#generate-a-sas-in-storage-explorer) ou o [portal do Azure](https://portal.azure.com).
* [Use um nome e uma chave](#use-a-name-and-key): Se você souber uma das chaves de conta para sua conta de armazenamento, poderá usar essa opção para se conectar rapidamente. As chaves para sua conta de armazenamento estão localizadas no painel **chaves de acesso** da conta de armazenamento na [portal do Azure](https://portal.azure.com).
* [Anexar a um emulador local](#attach-to-a-local-emulator): Se você estiver usando um dos emuladores de armazenamento do Azure disponíveis, use esta opção para se conectar facilmente ao seu emulador.
* [Conectar-se a uma conta de Azure Cosmos DB usando uma cadeia de conexão](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Se você tiver uma cadeia de conexão para uma instância do CosmosDB.
* [Conecte-se a Azure data Lake Store pelo URI](#connect-to-azure-data-lake-store-by-uri): Se você tiver um URI para um Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Adicionar um recurso por meio do Azure AD

1. Abra a **caixa de diálogo Conectar** clicando no **botão conectar** à esquerda, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Se você ainda não tiver feito isso, use a opção **Adicionar uma conta do Azure** para entrar na conta do Azure que tem acesso ao recurso. Depois de entrar, retorne para a **caixa de diálogo Conectar**.

3. Selecione a opção **Adicionar um recurso via Azure Active Directory (Azure AD)** e clique em **Avançar**.

4. Selecione a conta do Azure e o locatário que tem acesso ao recurso de armazenamento ao qual você deseja anexar. Clique em **Seguinte**.

5. Escolha o tipo de recurso que você deseja anexar e, em seguida, insira as informações necessárias para se conectar. As entradas nesta página serão alteradas dependendo do tipo de recurso que você está adicionando. Certifique-se de escolher o tipo correto de recurso. Depois de preencher as informações necessárias, clique em **Avançar**.

6. Examine o resumo da conexão e verifique se todas as informações estão corretas. Se todas as informações parecerem corretas, clique em **conectar**. Caso contrário, retorne às páginas anteriores com o botão **voltar** para corrigir todas as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos navegará automaticamente para o nó que representa a conexão. Você também pode olhar em **Local & conectado** **contas de armazenamento** → → **(contêineres anexados)** → **contêineres de blob** se por alguma razão isso não acontecer. Se Gerenciador de Armazenamento não puder adicionar sua conexão, ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-connection-string"></a>Utilize uma cadeia de ligação

1. Abra a **caixa de diálogo Conectar** clicando no **botão conectar** à esquerda, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione a opção **usar uma cadeia de conexão** e clique em **Avançar**.

3. Escolha um nome de exibição para a conexão e insira na cadeia de conexão. Clique depois em **Seguinte**.

4. Examine o resumo da conexão e verifique se todas as informações estão corretas. Se todas as informações parecerem corretas, clique em **conectar**, caso contrário, retorne às páginas anteriores com o botão **voltar** para corrigir as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos navegará automaticamente para o nó que representa a conexão. Você também pode olhar em **Local &** contas de **armazenamento** → anexadas se por alguma razão ela não. Se Gerenciador de Armazenamento não puder adicionar sua conexão, ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-sas-uri"></a>Usar um URI de SAS

1. Abra a **caixa de diálogo Conectar** clicando no **botão conectar** à esquerda, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione a opção **usar um URI de assinatura de acesso compartilhado (SAS)** e clique em **Avançar**.

3. Escolha um nome de exibição para a conexão e insira no URI da SAS. O ponto de extremidade de serviço para o tipo de recurso que você está anexando deve fazer preenchimento automático. Se você estiver usando um ponto de extremidade personalizado, é possível que ele não possa. Clique em **Seguinte**.

4. Examine o resumo da conexão e verifique se todas as informações estão corretas. Se todas as informações parecerem corretas, clique em **conectar**, caso contrário, retorne às páginas anteriores com o botão **voltar** para corrigir qualquer informação incorreta.

Depois que a conexão for adicionada com êxito, a árvore de recursos navegará automaticamente para o nó que representa a conexão. Você também pode procurar em **Local &** contas de **armazenamento** → anexadas → **(contêineres anexados)** → **o nó de serviço para o tipo de contêiner que você anexou** se por alguma razão não. Se Gerenciador de Armazenamento não puder adicionar sua conexão, ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="use-a-name-and-key"></a>Usar um nome e uma chave

1. Abra a **caixa de diálogo Conectar** clicando no **botão conectar** à esquerda, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

2. Selecione a opção **usar um nome e chave de conta de armazenamento** e clique em **Avançar**.

3. Escolha um nome de exibição para a conexão.

4. Insira o nome da conta de armazenamento e qualquer uma de suas chaves de acesso.

5. Escolha o **domínio de armazenamento** a ser usado e clique em **Avançar**.

4. Examine o resumo da conexão e verifique se todas as informações estão corretas. Se todas as informações parecerem corretas, clique em **conectar**, caso contrário, retorne às páginas anteriores com o botão **voltar** para corrigir as informações incorretas.

Depois que a conexão for adicionada com êxito, a árvore de recursos navegará automaticamente para o nó que representa a conexão. Você também pode olhar em **Local &** contas de **armazenamento** → anexadas se por alguma razão ela não. Se Gerenciador de Armazenamento não puder adicionar sua conexão, ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="attach-to-a-local-emulator"></a>Anexar a um emulador local

O Gerenciador de Armazenamento atualmente dá suporte a dois emuladores de armazenamento oficiais:
* [Emulador de armazenamento do Azure](storage/common/storage-use-emulator.md) (Somente Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o emulador estiver escutando nas portas padrão, você poderá usar o nó **emulador-portas padrão** (encontrado em local & **contas de armazenamento**→ **anexadas** ) para acessar rapidamente seu emulador.

Se você quiser usar um nome diferente para sua conexão, ou se o emulador não estiver em execução nas portas padrão:

1. Inicie o emulador. Ao fazer isso, anote em quais portas o emulador está escutando para cada tipo de serviço.

   > [!IMPORTANT]
   > Gerenciador de Armazenamento não inicia automaticamente o emulador. Você deve iniciá-lo por conta própria.

2. Abra a **caixa de diálogo Conectar** clicando no **botão conectar** à esquerda, barra de ferramentas vertical.

    ![Ligar à opção de armazenamento do Azure][9]

3. Selecione a opção **anexar a um emulador local** e clique em **Avançar**.

4. Escolha um nome de exibição para a conexão e insira nas portas em que o emulador está escutando para cada tipo de serviço. As caixas de texto serão iniciadas com os valores de porta padrão para a maioria dos emuladores. A **porta de arquivos** é deixada em branco, pois nenhum dos emuladores oficiais dão suporte atualmente ao serviço de arquivos. Se o emulador que você está usando tiver suporte para arquivos, você poderá inserir a porta que está sendo usada. Clique em **Seguinte**.

5. Examine o resumo da conexão e verifique se todas as informações estão corretas. Se todas as informações estiverem corretas, clique em **conectar**; caso contrário, retorne às páginas anteriores com o botão **voltar** para corrigir qualquer informação incorreta.

Depois que a conexão for adicionada com êxito, a árvore de recursos navegará automaticamente para o nó que representa a conexão. Você também pode olhar em **Local &** contas de **armazenamento** → anexadas se por alguma razão ela não. Se Gerenciador de Armazenamento não puder adicionar sua conexão, ou se você não puder acessar seus dados depois de adicionar a conexão com êxito, consulte o [Guia de solução de problemas](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) para obter ajuda.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Conectar-se a uma conta de Azure Cosmos DB usando uma cadeia de conexão

Além de gerenciar contas de Azure Cosmos DB por meio da assinatura do Azure, uma maneira alternativa de se conectar a um Azure Cosmos DB é usar uma cadeia de conexão. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localize **Local e Ligada** na árvore à esquerda, clique com o botão direito do rato em **Contas do Azure Cosmos DB** e selecione **Ligar ao Azure Cosmos DB...**

    ![conectar-se a Azure Cosmos DB pela cadeia de conexão][21]

2. Escolha Azure Cosmos DB API, Cole a **cadeia de conexão**e clique em **OK** para conectar Azure Cosmos DB conta. Para obter informações sobre como obter a cadeia de ligação, veja [Obter a cadeia de ligação](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Conectar-se a Azure Data Lake Store por URI

Se precisar de acesso a um recurso que não esteja em sua assinatura, você precisará de alguém com acesso para fornecer o URI do recurso. Depois de entrar, você pode se conectar a Data Lake Store usando o URI seguindo estas etapas:

1. Abra o Explorador de Armazenamento.
2. No painel esquerdo, expanda **Local e Ligado**.
3. Clique com botão direito do rato em **Data Lake Store** e, no menu de contexto, selecione **Ligar ao Data Lake Store...** .

    ![menu de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Introduza o Uri e, em seguida, a ferramenta navega para a localização do URL que acabou de introduzir.

    ![caixa de diálogo de contexto Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![resultado de Ligar ao Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Gerar uma SAS no Gerenciador de Armazenamento

### <a name="account-level-sas"></a>SAS no nível da conta

1. Clique com o botão direito do mouse na conta de armazenamento que você deseja compartilhar e selecione **obter assinatura de acesso compartilhado...** .

    ![Obter a opção de menu de contexto SAS][14]

2. Na caixa de diálogo **gerar assinatura de acesso compartilhado** , especifique o intervalo de tempo e as permissões desejadas para a conta. Clique em **Criar**.

    ![Caixa de diálogo obter SAS][15]

3. Agora você pode copiar a **cadeia de conexão** ou a cadeia de **caracteres de consulta** bruta para a área de transferência.

### <a name="service-level-sas"></a>SAS de nível de serviço

[Como obter uma SAS para um contêiner de blob no Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage

Se você precisar localizar um recurso de armazenamento e não souber onde ele está, poderá usar a caixa Pesquisar na parte superior do painel esquerdo para pesquisar o recurso.

À medida que você digita na caixa de pesquisa, o painel esquerdo exibe todos os recursos que correspondem ao valor de pesquisa que você inseriu até esse ponto. Por exemplo, uma pesquisa por **pontos de extremidade** é mostrada na seguinte captura de tela:

![Procurar conta de armazenamento][23]

> [!NOTE]
> Use o **painel gerenciamento de conta** para anular a seleção de qualquer assinatura que não contenha o item que você está procurando para melhorar o tempo de execução de sua pesquisa. Você também pode clicar com o botão direito do mouse em um nó e escolher **Pesquisar aqui** para iniciar a pesquisa de um nó específico.
>
>

## <a name="next-steps"></a>Passos seguintes

* [Gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de Armazenamento](vs-azure-tools-storage-explorer-blobs.md)
* [Gerenciar Azure Cosmos DB no Gerenciador de Armazenamento (versão prévia)](./cosmos-db/storage-explorer.md)
* [Gerenciar Azure Data Lake Store recursos com Gerenciador de Armazenamento](./data-lake-store/data-lake-store-in-storage-explorer.md)

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
