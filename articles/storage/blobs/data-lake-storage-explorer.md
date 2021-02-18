---
title: Use o Explorador de Armazenamento Azure com Azure Data Lake Storage Gen2
description: Utilize o Azure Storage Explorer para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652280"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Use o Azure Storage Explorer para gerir diretórios e ficheiros em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para criar e gerir diretórios e ficheiros em contas de armazenamento que têm espaço hierárquico (HNS) ativado.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](../common/storage-account-create.md) instruções para criar uma.

- Azure Storage Explorer instalado no seu computador local. Para instalar o Explorador de Armazenamento do Azure para Windows, Macintosh ou Linux, consulte [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> O Storage Explorer utiliza tanto o Blob (blob) & data lake storage gen2 [(dfs)](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) quando trabalha com a Azure Data Lake Storage Gen2. Se o acesso ao Azure Data Lake Storage Gen2 estiver configurado utilizando pontos finais privados, certifique-se de que são criados dois pontos finais privados para a conta de armazenamento: um com o sub-recurso-alvo `blob` e o outro com o sub-recurso alvo `dfs` .

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

Um contentor contém diretórios e ficheiros. Para criar uma, expanda a conta de armazenamento que criou no passo em curso. Selecione **Blob Containers**, click direito e selecione **Create Blob Container**. Insira o nome do seu recipiente. Consulte a secção [De Conjunto de Contentores](storage-quickstart-blobs-dotnet.md#create-a-container) para obter uma lista de regras e restrições sobre os recipientes de nomeação. Quando estiver concluído, prima **Enter** para criar o recipiente. Uma vez criado o recipiente com sucesso, é apresentado sob a pasta **Blob Containers** para a conta de armazenamento selecionada.

![Microsoft Azure Storage Explorer - Criação de um recipiente](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Criar um diretório

Para criar um diretório, selecione o recipiente que criou no passo de curso. Na fita do recipiente, escolha o botão **Pasta Nova.** Insira o nome do seu diretório. Quando estiver concluído, prima **Enter** para criar o diretório. Uma vez criado o diretório com sucesso, aparece na janela do editor.

![Microsoft Azure Storage Explorer - Criação de um diretório](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Carregar bolhas para o diretório

Na fita do diretório, escolha o **botão Upload.** Esta operação dá-lhe a opção de carregar uma pasta ou um ficheiro.

Escolha os ficheiros ou pasta a carregar.

![Explorador de Armazenamento do Microsoft Azure – Carregar um blob](media/data-lake-storage-explorer/upload-file.png)

Quando seleciona **OK**, os ficheiros selecionados são colocados em fila para carregamento. Quando o carregamento estiver concluído, os resultados são apresentados na janela **Atividades**.

## <a name="view-blobs-in-a-directory"></a>Ver bolhas num diretório

Na aplicação **Azure Storage Explorer,** selecione um diretório numa conta de armazenamento. O painel principal mostra uma lista das bolhas no diretório selecionado.

![Microsoft Azure Storage Explorer - liste blobs em um diretório](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Transferir blobs

Para descarregar ficheiros utilizando **o Azure Storage Explorer,** com um ficheiro selecionado, selecione **Descarregue** a partir da fita. É aberta uma caixa de diálogo de ficheiro que lhe permite introduzir um nome de ficheiro. **Selecione Guardar** para iniciar o download de um ficheiro para a localização local.

## <a name="next-steps"></a>Passos seguintes

Saiba como gerir a permissão de ficheiros e diretórios definindo listas de controlo de acesso (ACLs)

> [!div class="nextstepaction"]
> [Use o Azure Storage Explorer para gerir ACLs em Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
