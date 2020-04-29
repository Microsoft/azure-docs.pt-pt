---
title: Mova os dados de armazenamento blob com o Azure Storage Explorer - Team Data Science Process
description: Saiba como usar o Azure Storage Explorer para carregar e transferir dados do armazenamento de blob azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bfc63c6f5aca92fb7fda9e3ecf63ce4c332b12ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720916"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mova dados de e para o Armazenamento De Blob Azure usando o Azure Storage Explorer
O Azure Storage Explorer é uma ferramenta gratuita da Microsoft que lhe permite trabalhar com dados do Armazenamento Azure no Windows, macOS e Linux. Este tópico descreve como usá-lo para carregar e transferir dados do armazenamento de blob Azure. A ferramenta pode ser descarregada do [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar vM que foi configurado com os scripts fornecidos pelas [máquinas Data Science Virtual em Azure,](virtual-machines.md)então o Azure Storage Explorer já está instalado no VM.
> 
> [!NOTE]
> Para uma introdução completa ao armazenamento de blob Azure, consulte [o Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [o Serviço Blob Azure.](https://msdn.microsoft.com/library/azure/dd179376.aspx)   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que tem uma subscrição Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de fazer o upload/download de dados, deve saber o nome da sua conta de Armazenamento Azure e a chave da conta. 

* Para configurar uma subscrição Azure, consulte o [teste gratuito de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e chave, consulte as contas de [Armazenamento Azure](../../storage/common/storage-create-storage-account.md). Tome nota a chave de acesso da sua conta de armazenamento, pois necessita desta chave para se ligar à conta com a ferramenta Azure Storage Explorer.
* A ferramenta Azure Storage Explorer pode ser descarregada do [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Aceite as predefinições durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Use o Explorador de Armazenamento Azure
Os seguintes passos documentam como carregar/descarregar dados utilizando o Azure Storage Explorer. 

1. Lançamento do Microsoft Azure Storage Explorer.
2. Para trazer o **Signin na sua conta...** assistente, selecione ícone de definições de **conta Azure,** em seguida, **Adicione uma conta** e insira as suas credenciais. 
![Adicione uma conta de Armazenamento Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para elevar o assistente **de armazenamento Connect to Azure,** selecione o ícone Connect to **Azure Storage.** ![Clique em "Ligar ao Armazenamento Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a tecla de acesso da sua conta de Armazenamento Azure no assistente **de armazenamento Connect to Azure** e, em **seguida, Seguinte**. ![Introduza a chave de acesso a partir da conta de Armazenamento Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome da conta de armazenamento na caixa de **nomes da conta** e, em seguida, selecione **Seguinte**. ![Anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve agora ser apresentada. Para criar um recipiente de bolha numa conta de armazenamento, clique no nó **blob Containers** nessa conta, selecione **Create Blob Container**, e introduza um nome.
7. Para fazer o upload dos dados para um recipiente, selecione o recipiente-alvo e clique no botão **Upload.**
![Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique no **...** à direita da caixa **Deficheiros,** selecione um ou vários ficheiros para fazer o upload do sistema de ficheiros e clique em **Carregar** para começar a carregar os ficheiros. ![Upload de ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para descarregar dados, selecione a bolha no recipiente correspondente para descarregar e clicar em **Baixar**. ![Transferir ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

