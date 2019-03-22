---
title: Mover dados de armazenamento de Blobs com o Explorador de armazenamento do Azure – Team Data Science Process
description: Mover dados de e para o Armazenamento de Blobs do Azure com o Explorador do Armazenamento do Azure
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8ec4289c641b3f140950575ac2f8016f894f2c9c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879859"
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
Este documento parte do princípio de que tem uma subscrição do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregamento/transferência de dados, tem de saber a sua chave de conta e o nome da conta de armazenamento do Azure. 

* Para configurar uma subscrição do Azure, veja [durante um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obtenção de conta e informações da chave, consulte [sobre as contas de armazenamento](../../storage/common/storage-create-storage-account.md). Tome nota da chave de acesso para a sua conta de armazenamento, conforme necessário esta chave para ligar à conta com a ferramenta do Explorador de armazenamento do Azure.
* A ferramenta do Explorador de armazenamento do Azure pode ser transferida a partir [Explorador de armazenamento do Microsoft Azure](https://storageexplorer.com/). Aceite as predefinições durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Utilizar o Explorador de armazenamento do Azure
Os seguintes passos de documentos como carregar/transferir dados com o Explorador de armazenamento do Azure. 

1. Inicie o Explorador de armazenamento do Microsoft Azure.
2. Para abrir o **iniciar sessão na sua conta...**  assistente, selecione **definições de conta do Azure** ícone, em seguida, **adicionar uma conta** e introduza as credenciais. ![Adicionar uma conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para abrir o **ligar ao armazenamento do Azure** assistente, selecione a **ligar ao armazenamento do Azure** ícone. ![Clique em "Ligar ao armazenamento do Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Introduza a chave de acesso da conta de armazenamento do Azure **ligar ao armazenamento do Azure** assistente e, em seguida **próxima**. ![Introduza a chave de acesso da conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Introduza o nome da conta de armazenamento na **nome da conta** caixa e, em seguida, selecione **próxima**. ![Anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Agora deverá ser listada a conta de armazenamento adicionada. Para criar um contentor de BLOBs numa conta de armazenamento, clique com botão direito a **contentores de BLOBs** nó nessa conta, selecione **criar contentor de BLOBs**e introduza um nome.
7. Para carregar dados para um contentor, selecione o contentor de destino e clique nas **carregue** botão.![ Contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique no **...**  à direita do **arquivos** caixa, selecione um ou vários ficheiros para carregar a partir do sistema de ficheiros e clique em **carregar** para começar a carregar os ficheiros.![ Carregar ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para transferir dados, selecionando o blob no contentor correspondente para transferir e clique em **transferir**. ![Transferir ficheiros](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

