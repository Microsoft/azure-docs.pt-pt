---
title: Começa com o Storage Explorer | Microsoft Docs
description: Comece a gerir os recursos de armazenamento da Azure com o Storage Explorer. Faça o download e instale o Azure Storage Explorer, ligue-se a uma conta de armazenamento ou serviço, e muito mais.
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 3a8fe3ded6608059cc6ad50901ffe6df5dcf1b08
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441593"
---
# <a name="get-started-with-storage-explorer"></a>Introdução ao Explorador de Armazenamento

## <a name="overview"></a>Descrição geral

O Explorador de Armazenamento do Microsoft Azure é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento do Microsoft Azure no Windows, macOS e Linux.

Neste artigo, você vai aprender várias formas de ligar e gerir as suas contas de armazenamento Azure.

:::image type="content" alt-text="Explorador de Armazenamento do Microsoft Azure" source="./vs-storage-explorer-overview.png":::

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

Para distribuição suportada, consulte a [ `snapd` página de instalação](https://snapcraft.io/docs/installing-snapd).

O Storage Explorer requer a utilização de um gestor de passwords. Pode ter de ligar-se manualmente a um gestor de passwords. Pode ligar o Storage Explorer ao gestor de passwords do seu sistema executando o seguinte comando:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

O Storage Explorer também está disponível como *.tar.gz* download. Se utilizar o *.tar.gz,* deve instalar as dependências manualmente. As seguintes distribuições de suporte Linux *.tar.gz* instalação:

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

A *instalação .tar.gz* pode funcionar em outras distribuições, mas apenas estas listadas são oficialmente suportadas.

Para obter mais ajuda para instalar o Storage Explorer no Linux, consulte as [dependências do Linux](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies) no guia de resolução de problemas do Azure Storage Explorer.

---

## <a name="download-and-install"></a>Transferir e instalar

Para descarregar e instalar o Storage Explorer, consulte [o Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage

O Storage Explorer fornece várias formas de se conectar aos recursos da Azure:

* [Inscreva-se no Azure para aceder às suas subscrições e aos seus recursos](#sign-in-to-azure)
* [Anexar a um recurso de armazenamento Azure individual](#attach-to-an-individual-resource)
* [Anexar a um recurso CosmosDB](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

> [!NOTE]
> Para aceder totalmente aos recursos após a sua sinstrução, o Storage Explorer requer tanto permissões de gestão (Azure Resource Manager) como de camadas de dados. Isto significa que precisa de permissões do Azure Ative Directory (Azure AD) para aceder à sua conta de armazenamento, aos contentores da conta e aos dados nos contentores. Se tiver permissões apenas na camada de dados, considere escolher o Sinal na utilização da opção **Azure Ative Directory (Azure AD)** ao anexar um recurso. Para obter mais informações sobre as permissões específicas que o Storage Explorer necessita, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues).

1. No Explorador de Armazenamento, selecione **Ver**  >  **Gestão de Conta** ou selecione o botão **'Gerir contas'.**

    :::image type="content" alt-text="Gerir Contas" source ="./vs-storage-explorer-manage-accounts.png":::

1. **A CONTA MANAGEMENT** apresenta agora todas as contas Azure a que está inscrito. Para ligar a outra conta, **selecione Adicionar uma conta...**.

1. O diálogo **de armazenamento "Connect to Azure"** abre. No painel **'Selecionar Recursos',** selecione **Subscrição.**

    :::image type="content" alt-text="Conecte o diálogo" source="./vs-storage-explorer-connect-dialog.png":::

1. No painel **Select Azure Environment,** selecione um ambiente Azure para iniciar sação. Você pode entrar no Azure global, uma nuvem nacional ou um exemplo de Azure Stack. Em seguida, selecione **Seguinte**.

    :::image type="content" alt-text="Opção de iniciar sinssução" source="./vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > Para obter mais informações sobre o Azure Stack, consulte [o Connect Storage Explorer numa conta de subscrição ou armazenamento do Azure Stack.](/azure-stack/user/azure-stack-storage-connect-se)

1. O Storage Explorer abrirá uma página web para iniciar serção.

1. Depois de iniciar seduca com sucesso com uma conta Azure, a conta e as subscrições Azure associadas a essa conta aparecem na **GESTÃO DE CONTA**. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**.

    :::image type="content" alt-text="Selecionar subscrições do Azure" source="./vs-storage-explorer-account-panel.png":::

1. **O EXPLORER** exibe as contas de armazenamento associadas às subscrições Azure selecionadas.

    :::image type="content" alt-text="Subscrições do Azure selecionadas" source="./vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>Anexar a um recurso individual

O Storage Explorer permite-lhe conectar-se a recursos individuais, como um recipiente Azure Data Lake Storage Gen2, utilizando vários métodos de autenticação. Alguns métodos de autenticação são suportados apenas para certos tipos de recursos.

| Tipo de recurso    | Azure AD | Nome e chave da conta | Assinatura de Acesso Partilhado (SAS)  | Público (anónimo) |
|------------------|----------|----------------------|--------------------------------|--------------------|
| Contas de armazenamento | Yes      | Yes                  | Sim (cadeia de ligação ou URL) | No                 |
| Contentores de blobs  | Yes      | No                   | Sim (URL)                      | Yes                |
| Contentores Gen2  | Yes      | No                   | Sim (URL)                      | Yes                |
| Diretórios da Gen2 | Yes      | No                   | Sim (URL)                      | Yes                |
| Partilhas de ficheiros      | No       | No                   | Sim (URL)                      | No                 |
| Filas           | Yes      | No                   | Sim (URL)                      | No                 |
| Tables           | No       | No                   | Sim (URL)                      | No                 |
 
O Explorador de Armazenamento também pode ligar-se a um [emulador de armazenamento local](#local-storage-emulator) utilizando as portas configuradas do emulador.

Para ligar a um recurso individual, selecione o botão **Ligar** na barra de ferramentas à esquerda. Em seguida, siga as instruções do tipo de recurso a que pretende ligar.

:::image type="content" alt-text="Ligar à opção de armazenamento do Azure" source="./vs-storage-explorer-connect-button.png":::

Quando uma ligação a uma conta de armazenamento é adicionada com sucesso, um novo nó de árvore aparecerá sob **contas de** armazenamento &  >  **Locais anexas**.

Para outros tipos de recursos, um novo nó é adicionado ao abrigo de Contas de Armazenamento **Anexas &**  >    >  **Locais (Contentores Anexos)**. O nó aparecerá sob um nó de grupo que corresponda ao seu tipo. Por exemplo, uma nova ligação a um recipiente Azure Data Lake Storage Gen2 aparecerá sob **recipientes Blob**.

Se o Storage Explorer não conseguir adicionar a sua ligação, ou se não conseguir aceder aos seus dados depois de adicionar a ligação com sucesso, consulte o [guia de resolução de problemas do Azure Storage Explorer](./storage/common/storage-explorer-troubleshooting.md).

As secções seguintes descrevem os diferentes métodos de autenticação que pode utilizar para se ligar a recursos individuais.

#### <a name="azure-ad"></a>Azure AD

O Storage Explorer pode utilizar a sua conta Azure para se ligar aos seguintes tipos de recursos:
* Contentores de blobs
* Recipientes Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen2 directies
* Filas
 
A azure AD é a opção preferida se tiver acesso à camada de dados ao seu recurso, mas sem acesso à camada de gestão.

1. Inscreva-se em pelo menos uma conta Azure utilizando os [passos acima descritos](#sign-in-to-azure).
1. No painel de **Recursos Selecionados** do diálogo **de armazenamento Connect to Azure,** selecione **o recipiente Blob**, **o recipiente ADLS Gen2** ou **a fila**.
1. Selecione **Iniciar sação utilizando o Diretório Ativo Azure (Azure AD)** e selecione **Next**.
1. Selecione uma conta Azure e inquilino. A conta e o inquilino devem ter acesso ao recurso de armazenamento a que pretende anexar. Selecione **Seguinte**.
1. Introduza um nome de exibição para a sua ligação e o URL do recurso. Selecione **Seguinte**.
1. Reveja as informações de ligação no painel **Resumo.** Se a informação de ligação estiver correta, selecione **Connect**.

#### <a name="account-name-and-key"></a>Nome da conta e chave

O Storage Explorer pode ligar-se a uma conta de armazenamento utilizando o nome e a chave da conta de armazenamento.

Pode encontrar as chaves da sua conta no [portal Azure.](https://portal.azure.com) Abra a sua página de conta de armazenamento e selecione **Definições**  >  **As teclas de acesso**.

1. No painel **de Recursos Selecionados** do diálogo **de armazenamento Connect to Azure,** selecione **a conta de Armazenamento**.
1. Selecione **o nome e a tecla da conta** e selecione **Seguinte**.
1. Introduza um nome de exibição para a sua ligação, o nome da conta e uma das chaves da conta. Selecione o ambiente Azure apropriado. Selecione **Seguinte**.
1. Reveja as informações de ligação no painel **Resumo.** Se a informação de ligação estiver correta, selecione **Connect**.

#### <a name="shared-access-signature-sas-connection-string"></a>Cadeia de conexão de assinatura de acesso partilhado (SAS)

O Storage Explorer pode ligar-se a uma conta de armazenamento utilizando uma cadeia de ligação com uma Assinatura de Acesso Partilhado (SAS). Uma cadeia de ligação SAS é assim:

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. No painel **de Recursos Selecionados** do diálogo **de armazenamento Connect to Azure,** selecione **a conta de Armazenamento**.
1. Selecione **assinatura de acesso partilhado (SAS)** e selecione **Seguinte**.
1. Introduza um nome de exibição para a sua ligação e o fio de ligação SAS para a conta de armazenamento. Selecione **Seguinte**.
1. Reveja as informações de ligação no painel **Resumo.** Se a informação de ligação estiver correta, selecione **Connect**.

#### <a name="shared-access-signature-sas-url"></a>URL de assinatura de acesso compartilhado (SAS)

O Explorador de Armazenamento pode ligar-se aos seguintes tipos de recursos utilizando um SAS URI:
* Contentor de blobs
* Recipiente ou diretório da Azure Data Lake Storage Gen2
* Partilha de ficheiros
* Fila
* Tabela

Um SAS URI é assim:

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. No painel **de Recursos Selecionados** do diálogo **de armazenamento "Ligar ao Azure",** selecione o recurso a que pretende ligar.
1. Selecione **assinatura de acesso partilhado (SAS)** e selecione **Seguinte**.
1. Introduza um nome de exibição para a sua ligação e o SAS URI para o recurso. Selecione **Seguinte**.
1. Reveja as informações de ligação no painel **Resumo.** Se a informação de ligação estiver correta, selecione **Connect**.

#### <a name="local-storage-emulator"></a>Emulador de armazenamento local

O Explorador de Armazenamento pode ligar-se a um emulador de armazenamento Azure. Atualmente, existem dois emuladores suportados:

* [Emulador de Armazenamento Azure](storage/common/storage-use-emulator.md) (apenas para windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS ou Linux)

Se o seu emulador estiver a ouvir nas portas predefinidos, pode utilizar o nó de Contas de Armazenamento **Anexo & Local**- Nó de  >  **portas**  >  **predefinidos** para aceder ao seu emulador.

Se quiser usar um nome diferente para a sua ligação, ou se o seu emulador não estiver a funcionar nas portas predefinidas:

1. Comece o seu emulador.

   > [!IMPORTANT]
   > O Storage Explorer não inicia automaticamente o seu emulador. Deve ligá-lo manualmente.

1. No painel **de Recursos Selecionados** do diálogo **de armazenamento Connect to Azure,** selecione **o emulador de armazenamento local**.
1. Introduza um nome de visualização para a sua ligação e o número de porta para cada serviço emulsionado que pretende utilizar. Se não quiser utilizar um serviço, deixe a porta correspondente em branco. Selecione **Seguinte**.
1. Reveja as informações de ligação no painel **Resumo.** Se a informação de ligação estiver correta, selecione **Connect**.

### <a name="connect-to-azure-cosmos-db"></a>Ligar ao Azure Cosmos DB

O Storage Explorer também suporta a ligação aos recursos DB da Azure Cosmos.

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

## <a name="next-steps"></a>Passos seguintes

* [Gerir os recursos de armazenamento Azure Blob com o Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Trabalhar com dados com o Explorador de Armazenamento do Azure](./cosmos-db/storage-explorer.md)
* [Gerir os recursos da Azure Data Lake Store com o Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
