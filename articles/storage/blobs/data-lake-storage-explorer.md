---
title: Use o Explorador de Armazenamento Azure com Azure Data Lake Storage Gen2
description: Utilize o Azure Storage Explorer para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e46bb87788de27916860720284087643db7153dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913407"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use Azure Storage Explorer to manage directories, files, and ACLs in Azure Data Lake Storage Gen2 (Utilizar o Explorador de Armazenamento do Azure para gerir diretórios, ficheiros e ACLs no Azure Data Lake Storage Gen2)

Este artigo mostra-lhe como usar [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](../common/storage-account-create.md) instruções para criar uma.
> * Azure Storage Explorer instalado no seu computador local. Para instalar o Explorador de Armazenamento do Azure para Windows, Macintosh ou Linux, consulte [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Inscreva-se no Storage Explorer

Ao iniciar o Explorador de Armazenamento, surge a janela **Explorador de Armazenamento do Microsoft Azure - Ligar**. Embora o Storage Explorer forneça várias formas de se conectar às contas de armazenamento, apenas uma forma é suportada para gerir acLs.

|Tarefa|Objetivo|
|---|---|
|Adicionar uma Conta do Azure | Redireciona-o para a página de inscrição da sua organização para autenticá-lo para Azure. Atualmente este é o único método de autenticação suportado se quiser gerir e definir ACLs.|
|Utilizar uma cadeia de ligação ou um URI de assinatura de acesso partilhado | Pode ser utilizado para aceder diretamente a um contentor ou conta de armazenamento com um token SAS ou uma cadeia de ligação partilhada. |
|Utilizar o nome e a chave de uma conta de armazenamento| Utilize o nome e a chave da conta de armazenamento para ligar ao armazenamento do Azure.|

**Selecione Adicionar uma Conta Azure** e clique em Iniciar **sôm.** Siga as instruções no ecrã para iniciar súm na sua conta Azure.

![Screenshot que mostra o Microsoft Azure Storage Explorer e destaca a opção Adicionar uma Conta Azure e o botão Sinal no botão.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Quando a ligação for concluída, o Explorador de Armazenamento do Azure é carregado com o separador **Explorador** mostrado. Esta vista dá-lhe uma visão de todas as suas contas de armazenamento Azure, bem como armazenamento local configurado através do [emulador de armazenamento Azurite,](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) [contas cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou ambientes [Azure Stack.](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

![Explorador de Armazenamento do Microsoft Azure – Janela Ligar](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Criar um contentor

Um contentor contém diretórios e ficheiros. Para criar uma, expanda a conta de armazenamento que criou no passo em curso. Selecione **Contentores de Blobs**, clique com o botão direito do rato e selecione **Criar Contentor de Blobs**. Insira o nome do seu recipiente. Consulte a secção [De Conjunto de Contentores](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições sobre os recipientes de nomeação. Quando estiver concluído, prima **Enter** para criar o recipiente. Uma vez criado o recipiente com sucesso, é apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

![Microsoft Azure Storage Explorer - Criação de um recipiente](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione o recipiente que criou no passo de curso. Na fita do recipiente, escolha o botão **Pasta Nova.** Insira o nome do seu diretório. Quando estiver concluído, prima **Enter** para criar o diretório. Uma vez criado o diretório com sucesso, aparece na janela do editor.

![Microsoft Azure Storage Explorer - Criação de um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar bolhas para o diretório

Na fita do diretório, escolheu o botão **upload.** Esta operação dá-lhe a opção de carregar uma pasta ou um ficheiro.

Escolha os ficheiros ou pasta a carregar.

![Explorador de Armazenamento do Microsoft Azure – Carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Quando seleciona **OK**, os ficheiros selecionados são colocados em fila para carregamento. Quando o carregamento estiver concluído, os resultados são apresentados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Ver bolhas num diretório

Na aplicação **Azure Storage Explorer,** selecione um diretório numa conta de armazenamento. O painel principal mostra uma lista das bolhas no diretório selecionado.

![Microsoft Azure Storage Explorer - liste blobs em um diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Transferir blobs

Para descarregar ficheiros utilizando **o Azure Storage Explorer,** com um ficheiro selecionado, selecione **Descarregue** a partir da fita. É aberta uma caixa de diálogo de ficheiro que lhe permite introduzir um nome de ficheiro. **Selecione Guardar** para iniciar o download de um ficheiro para a localização local.

## <a name="managing-access"></a>Gerir o acesso

Pode definir permissões na raiz do seu recipiente. Para tal, tem de iniciar sessão no Azure Storage Explorer com a sua conta individual com direitos para o fazer (ao contrário de uma cadeia de ligação). Clique com o botão direito no seu recipiente e **selecione Permissões de Gestão,** trazendo a caixa de diálogo **de 'Gestão' de permissão.**

![Microsoft Azure Storage Explorer - Gerir o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A caixa de diálogo **'Obter Permissão'** permite-lhe gerir permissões para o proprietário e o grupo de proprietários. Também permite adicionar novos utilizadores e grupos à lista de controlo de acessos para os quais poderá gerir permissões.

Para adicionar um novo utilizador ou grupo à lista de controlo de acesso, selecione o utilizador Adicionar ou o campo **de grupo.**

Introduza a entrada correspondente do Azure Ative Directory (AAD) que pretende adicionar à lista e, em seguida, selecione **Adicionar**.

O utilizador ou grupo irá agora aparecer nos **Utilizadores e grupos:** campo, permitindo-lhe começar a gerir as suas permissões.

> [!NOTE]
> É uma boa prática, e recomendada, criar um grupo de segurança em AAD e manter permissões no grupo em vez de utilizadores individuais. Para mais detalhes sobre esta recomendação, bem como outras boas práticas, consulte as [melhores práticas para data lake storage gen2](data-lake-storage-best-practices.md).

Existem duas categorias de permissões que pode atribuir: aceder a ACLs e ACLs predefinidos.

* **Acesso**: Acesso ACLs controlam o acesso a um objeto. Os ficheiros e diretórios têm acesso a ACLs.

* **Padrão**: Um modelo de ACLs associado a um diretório que determina o acesso ACLs para quaisquer itens infantis que são criados sob esse diretório. Os ficheiros não têm ACLs predefinidos.

Em ambas as categorias, existem três permissões que pode atribuir em ficheiros ou diretórios: **Ler,** **Escrever** e **Executar**.

>[!NOTE]
> Fazer seleções aqui não definirá permissões em qualquer item atualmente existente dentro do diretório. Deve ir a cada item individual e definir as permissões manualmente, se o ficheiro já existir.

Pode gerir permissões em diretórios individuais, bem como em ficheiros individuais, que são o que lhe permite um controlo de acesso com grãos finos. O processo de gestão de permissões para diretórios e ficheiros é o mesmo que descrito acima. Clique com o direito no ficheiro ou diretório em que deseja gerir permissões e seguir o mesmo processo.

## <a name="next-steps"></a>Passos seguintes

Aprenda as listas de controlo de acesso na Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Access control in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md) (Controlo de acesso no Azure Data Lake Storage Gen2)