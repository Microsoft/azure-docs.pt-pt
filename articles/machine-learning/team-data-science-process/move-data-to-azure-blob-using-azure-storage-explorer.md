---
title: Mover dados de armazenamento de Blobs com o Explorador de armazenamento do Azure – Team Data Science Process
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720916"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados para e do armazenamento de Blobs do Azure com o Explorador de armazenamento do Azure
Explorador de armazenamento do Azure é uma ferramenta gratuita da Microsoft que lhe permite trabalhar com dados de armazenamento do Azure no Windows, macOS e Linux. Este tópico descreve como usá-lo para carregar e transferir dados do armazenamento de Blobs do Azure. A ferramenta pode ser transferida a partir [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar VM que foi configurada com os scripts fornecidos pela [máquinas virtuais de ciência de dados no Azure](virtual-machines.md), em seguida, o Explorador de armazenamento do Azure já está instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa para o armazenamento de Blobs do Azure, consulte [Noções básicas de Blobs do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço de Blobs do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de fazer o upload/download de dados, deve saber o nome da sua conta de Armazenamento Azure e a chave da conta. 

* Para configurar uma subscrição do Azure, veja [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e chave, consulte as contas de [Armazenamento Azure](../../storage/common/storage-create-storage-account.md). Tome nota da chave de acesso para a sua conta de armazenamento, conforme necessário esta chave para ligar à conta com a ferramenta do Explorador de armazenamento do Azure.
* A ferramenta do Explorador de armazenamento do Azure pode ser transferida a partir [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/). Aceite as predefinições durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Utilizar o Explorador de armazenamento do Azure
Os seguintes passos de documentos como carregar/transferir dados com o Explorador de armazenamento do Azure. 

1. Inicie o Explorador de armazenamento do Microsoft Azure.
2. Para abrir o **iniciar sessão na sua conta...**  assistente, selecione **definições de conta do Azure** ícone, em seguida, **adicionar uma conta** e introduza as credenciais. 
![Adicionar uma conta de armazenamento Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para elevar o assistente **de armazenamento Connect to Azure,** selecione o ícone Connect to **Azure Storage.** ![Clique em "Connect to Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a tecla de acesso da sua conta de Armazenamento Azure no assistente **de armazenamento Connect to Azure** e, em **seguida, Seguinte**. ![Introduza a chave de acesso da conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome da conta de armazenamento na **nome da conta** caixa e, em seguida, selecione **próxima**. ![Anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve agora ser apresentada. Para criar um contentor de BLOBs numa conta de armazenamento, clique com botão direito a **contentores de BLOBs** nó nessa conta, selecione **criar contentor de BLOBs**e introduza um nome.
7. Para fazer o upload dos dados para um recipiente, selecione o recipiente-alvo e clique no botão **Upload.**
![Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique no **...**  à direita do **arquivos** caixa, selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **carregar** para começar a carregar os ficheiros.![ Carregar ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para transferir dados, selecionando o blob no contentor correspondente para transferir e clique em **transferir**. ![Transferir ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

