---
title: Mover dados de armazenamento de BLOBs com o processo de ciência de dados de Gerenciador de Armazenamento do Azure de equipe
description: Saiba como usar Gerenciador de Armazenamento do Azure para carregar e baixar dados do armazenamento de BLOBs do Azure.
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
ms.openlocfilehash: 880b81c07aeed7359dfe35e1361a20ecb11e27dd
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053961"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Mover dados de e para o armazenamento de BLOBs do Azure usando Gerenciador de Armazenamento do Azure
Gerenciador de Armazenamento do Azure é uma ferramenta gratuita da Microsoft que permite que você trabalhe com os dados do armazenamento do Azure no Windows, no macOS e no Linux. Este tópico descreve como usá-lo para carregar e baixar dados do armazenamento de BLOBs do Azure. A ferramenta pode ser baixada de [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se você estiver usando uma VM que foi configurada com os scripts fornecidos pelas [máquinas virtuais de ciência de dados no Azure](virtual-machines.md), o Gerenciador de armazenamento do Azure já estará instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa ao armazenamento de BLOBs do Azure, consulte [noções básicas de blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [serviço blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura do Azure, uma conta de armazenamento e a chave de armazenamento correspondente para essa conta. Antes de carregar/baixar dados, você deve saber o nome da conta de armazenamento do Azure e a chave de conta. 

* Para configurar uma assinatura do Azure, consulte [avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obter informações de conta e chave, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). Anote a chave de acesso para sua conta de armazenamento, pois você precisa dessa chave para se conectar à conta com a ferramenta de Gerenciador de Armazenamento do Azure.
* A ferramenta de Gerenciador de Armazenamento do Azure pode ser baixada de [Gerenciador de armazenamento do Microsoft Azure](https://storageexplorer.com/). Aceite os padrões durante a instalação.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Usar Gerenciador de Armazenamento do Azure
As etapas a seguir documentam como carregar/baixar dados usando Gerenciador de Armazenamento do Azure. 

1. Iniciar Gerenciador de Armazenamento do Microsoft Azure.
2. Para abrir o assistente **entrar em sua conta...** , selecione o ícone **configurações de conta do Azure** e, em seguida, **adicione uma conta** e insira suas credenciais. ![adicionar uma conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Para exibir o assistente **conectar ao armazenamento do Azure** , selecione o ícone **conectar ao armazenamento do Azure** . ![clique em "conectar ao armazenamento do Azure"](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Insira a chave de acesso de sua conta de armazenamento do Azure no assistente **conectar ao armazenamento do Azure** e, em seguida, em **Avançar**. ![inserir a chave de acesso da conta de armazenamento do Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Insira o nome da conta de armazenamento na caixa **nome da conta** e, em seguida, selecione **Avançar**. ![anexar armazenamento externo](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. A conta de armazenamento adicionada deve agora estar listada. Para criar um contêiner de BLOB em uma conta de armazenamento, clique com o botão direito do mouse no nó **contêineres de blob** nessa conta, selecione **criar contêiner de blob**e insira um nome.
7. Para carregar dados em um contêiner, selecione o contêiner de destino e clique no botão **carregar** .![contas de armazenamento](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Clique em **...** à direita da caixa **arquivos** , selecione um ou vários arquivos para carregar do sistema de arquivos e clique em **carregar** para começar a carregar os arquivos.![carregar arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Para baixar dados, selecione o blob no contêiner correspondente para baixar e clique em **baixar**. ![baixar arquivos](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

