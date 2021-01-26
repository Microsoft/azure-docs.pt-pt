---
title: Mover dados de armazenamento blob com Azure Storage Explorer - Processo de ciência de dados de equipa
description: Saiba como usar o Azure Storage Explorer para carregar e transferir dados a partir do Azure Blob Storage.
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
ms.openlocfilehash: 53cb8cdd1c5f9824b07b16b8b6c70648603b9f38
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788914"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados de e para Azure Blob Storage usando O Explorador de Armazenamento Azure
O Azure Storage Explorer é uma ferramenta gratuita da Microsoft que permite trabalhar com dados de Armazenamento Azure no Windows, macOS e Linux. Este tópico descreve como usá-lo para carregar e transferir dados do Azure Blob Storage. A ferramenta pode ser descarregada a partir do [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se estiver a utilizar VM que foi configurado com os scripts fornecidos pelas [máquinas virtual data science em Azure](../data-science-virtual-machine/overview.md), então o Azure Storage Explorer já está instalado no VM.
> 
> [!NOTE]
> Para uma introdução completa ao Azure Blob Storage, consulte o [Azure Blob Basics](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e [o Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que tem uma subscrição Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/descarregar dados, deve conhecer o nome da sua conta e a chave da conta do Azure Storage. 

* Para configurar uma subscrição do Azure, consulte [o teste gratuito de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre a criação de uma conta de armazenamento e para obter informações de conta e informações fundamentais, consulte [as contas de Armazenamento Azure](../../storage/common/storage-account-create.md). Tome nota da chave de acesso da sua conta de armazenamento, pois necessita desta chave para ligar à conta com a ferramenta Azure Storage Explorer.
* A ferramenta Azure Storage Explorer pode ser descarregada a partir do [Microsoft Azure Storage Explorer](https://storageexplorer.com/). Aceite os predefinidos durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Utilizar o Explorador de Armazenamento do Azure
Os passos a seguir documentam como carregar/transferir dados usando o Azure Storage Explorer. 

1. Lançar Microsoft Azure Storage Explorer.
2. Para apresentar o **S-S-S.A. na sua conta...** assistente, selecione o ícone **de definições de conta Azure,** em seguida, **Adicione uma conta** e introduza as suas credenciais. 
![Adicione uma conta de Armazenamento Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para apresentar o assistente **de armazenamento Connect to Azure,** selecione o ícone **'Ligar ao Azure Storage'.** ![Clique em "Connect to Azure Storage"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a chave de acesso a partir da sua conta de Armazenamento Azure no assistente **de armazenamento Connect to Azure** e, em seguida, em **seguida.** ![Introduza a chave de acesso a partir da conta de Armazenamento Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome da conta de armazenamento na caixa de **nome da conta** e, em seguida, selecione **Seguinte**. ![Anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve agora ser exibida. Para criar um recipiente blob numa conta de armazenamento, clique com o botão direito no nó **de Recipientes Blob** nessa conta, selecione **Create Blob Container** e introduza um nome.
7. Para enviar dados para um recipiente, selecione o recipiente-alvo e clique no botão **Upload.**
![Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique na **...** à direita da caixa **Ficheiros,** selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **Upload** para começar a carregar os ficheiros. ![ Carregar ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para descarregar dados, selecione a bolha no recipiente correspondente para descarregar e clicar em **Baixar.** ![Transferir ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)
