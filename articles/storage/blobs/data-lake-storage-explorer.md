---
title: Use o Explorador de Armazenamento Azure com o Azure Data Lake Storage Gen2
description: Utilize o Azure Storage Explorer para gerir listas de diretórios e de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255551"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o Azure Storage Explorer para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado.

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * Azure Storage Explorer instalado no seu computador local. Para instalar o Explorador de Armazenamento do Azure para Windows, Macintosh ou Linux, consulte [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Inscreva-se no Explorador de Armazenamento

Ao iniciar o Explorador de Armazenamento, surge a janela **Explorador de Armazenamento do Microsoft Azure - Ligar**. Embora o Storage Explorer forneça várias formas de se conectar às contas de armazenamento, apenas uma forma é suportada atualmente para a gestão de ACLs.

|Tarefa|Objetivo|
|---|---|
|Adicionar uma Conta do Azure | Redireciona-o para a página de inscrição da sua organização para o autenticar ao Azure. Atualmente este é o único método de autenticação suportado se quiser gerir e definir ACLs.|
|Utilizar uma cadeia de ligação ou um URI de assinatura de acesso partilhado | Pode ser utilizado para aceder diretamente a um contentor ou conta de armazenamento com um token SAS ou uma cadeia de ligação partilhada. |
|Utilizar o nome e a chave de uma conta de armazenamento| Utilize o nome e a chave da conta de armazenamento para ligar ao armazenamento do Azure.|

Selecione **Adicionar uma Conta Azure** e clique **em Iniciar sessão..**. Siga as indicações no ecrã para iniciar sessão na sua conta Azure.

![Explorador de Armazenamento do Microsoft Azure – Janela Ligar](media/storage-quickstart-blobs-storage-explorer/connect.png)

Quando a ligação for concluída, o Explorador de Armazenamento do Azure é carregado com o separador **Explorador** mostrado. Esta vista fornece-lhe informações detalhadas de todas as contas de armazenamento do Azure, bem como do armazenamento local configurado através do [Emulador de Armazenamento do Azure](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), contas do [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) ou ambientes do [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Explorador de Armazenamento do Microsoft Azure – Janela Ligar](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Criar um contentor

Um contentor contém diretórios e ficheiros. Para criar uma, expanda a conta de armazenamento que criou na etapa de curso. Selecione **Contentores de Blobs**, clique com o botão direito do rato e selecione **Criar Contentor de Blobs**. Insira o nome do seu recipiente. Consulte a secção Criar uma secção de [contentores](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições à nomeação de contentores. Quando estiver concluído, prima **Introduza** para criar o recipiente. Uma vez criado com sucesso o recipiente, este é apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

![Microsoft Azure Storage Explorer - Criação de um recipiente](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione o recipiente que criou na etapa de curso. Na fita do recipiente, escolha o botão **New Folder.** Insira o nome para o seu diretório. Quando estiver concluído, prima **Enter** para criar o diretório. Uma vez criado o diretório com sucesso, aparece na janela do editor.

![Microsoft Azure Storage Explorer - Criação de um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Faça upload de bolhas para o diretório

Na fita de diretório, escolheu o botão **Upload.** Esta operação dá-lhe a opção de carregar uma pasta ou um ficheiro.

Escolha os ficheiros ou pasta a carregar.

![Explorador de Armazenamento do Microsoft Azure – Carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Quando seleciona **OK**, os ficheiros selecionados são colocados em fila para carregamento. Quando o carregamento estiver concluído, os resultados são apresentados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Ver bolhas num diretório

Na aplicação **Azure Storage Explorer,** selecione um diretório sob uma conta de armazenamento. O painel principal mostra uma lista das bolhas no diretório selecionado.

![Microsoft Azure Storage Explorer - lista de bolhas num diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Transferir blobs

Para descarregar ficheiros utilizando o **Azure Storage Explorer,** com um ficheiro selecionado, selecione **Download** a partir da fita. É aberta uma caixa de diálogo de ficheiro que lhe permite introduzir um nome de ficheiro. Selecione **Guardar** para iniciar o download de um ficheiro para a localização local.

## <a name="managing-access"></a>Gerir o acesso

Pode definir permissões na raiz do seu recipiente. Para tal, tem de ser registado no Azure Storage Explorer com a sua conta individual com direitos de o fazer (em oposição a uma cadeia de ligação). Clique no seu recipiente e selecione **'Controlar permissões',** trazendo a caixa de diálogo **'Manage Permission'.**

![Microsoft Azure Storage Explorer - Gerir o acesso ao diretório](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

A caixa de diálogo **Manage Permission** permite-lhe gerir permissões para o proprietário e o grupo de proprietários. Também permite adicionar novos utilizadores e grupos à lista de controlo de acesso para quem depois pode gerir permissões.

Para adicionar um novo utilizador ou grupo à lista de controlo de acesso, selecione o **campo de utilizador ou grupo Add.**

Introduza a entrada correspondente do Diretório Ativo Azure (AAD) que pretende adicionar à lista e, em seguida, selecione **Adicionar**.

O utilizador ou grupo irá agora aparecer nos **Utilizadores e grupos:** campo, permitindo-lhe começar a gerir as suas permissões.

> [!NOTE]
> É uma boa prática, e recomendado, criar um grupo de segurança em AAD e manter permissões no grupo e não em utilizadores individuais. Para mais detalhes sobre esta recomendação, bem como outras boas práticas, consulte [as melhores práticas para data lake storage Gen2](data-lake-storage-best-practices.md).

Existem duas categorias de permissões que pode atribuir: aceder a ACLs e ACLs predefinidos.

* **Acesso**: Acesso aos ACLs controla o acesso a um objeto. Os ficheiros e diretórios têm ambos acesso a ACLs.

* **Predefinição**: Um modelo de ACLs associado a um diretório que determina os ACLs de acesso para quaisquer itens infantis que sejam criados sob esse diretório. Os ficheiros não têm ACLs padrão.

Em ambas as categorias, existem três permissões que pode atribuir em ficheiros ou diretórios: **Ler,** **Escrever**e **Executar**.

>[!NOTE]
> Fazer seleções aqui não definirá permissões em qualquer item existente dentro do diretório. Deve ir a cada item individual mente e definir as permissões manualmente, se o ficheiro já existir.

Pode gerir permissões em diretórios individuais, bem como ficheiros individuais, que são o que lhe permite um controlo de acesso fino. O processo de gestão de permissões tanto para diretórios como para ficheiros é o mesmo que acima descrito. Clique no ficheiro ou no diretório que pretende gerir as permissões e seguir o mesmo processo.

## <a name="next-steps"></a>Passos seguintes

Aprenda as listas de controlo de acesso no Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) (Controlo de acesso no Azure Data Lake Storage Gen2)
