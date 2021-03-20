---
title: Gerir ações de ficheiros Azure usando o Azure Storage Explorer
description: Utilize este início rápido para saber como utilizar o Explorador de Armazenamento do Azure para gerir Ficheiros do Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629789"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Início rápido: criar e gerir partilhas de ficheiros do Azure com o Explorador de Armazenamento do Azure
Este guia orienta-o pelas noções básicas de utilizar [partilhas de ficheiros do Azure](storage-files-introduction.md) com o Explorador de Armazenamento do Azure. As partilhas de ficheiros do Azure são como outras partilhas de ficheiros, mas armazenadas na cloud e apoiadas pela plataforma do Azure. As partilhas de ficheiros do Azure suportam o protocolo SMB padrão do setor e permite a partilha de ficheiros entre várias máquinas, aplicações e instâncias. 

O Explorador de Armazenamento do Azure é uma ferramenta de cliente popular que está disponível para Windows, macOS e Linux. Pode utilizar o Explorador de Armazenamento para gerir partilhas de ficheiros do Azure e outros recursos de armazenamento.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Este início rápido requer a instalação do Explorador de Armazenamento. Para transferi-lo e instalá-lo, aceda ao [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Não pode utilizar o Explorador de Armazenamento para criar novos recursos. Para efeitos desta demonstração, crie a conta de armazenamento no [portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Ligar o Explorador de Armazenamento aos recursos do Azure
Ao iniciar o Explorador de Armazenamento, surge a janela **Explorador de Armazenamento do Microsoft Azure - Ligar**. O Explorador de Armazenamento proporciona várias formas de ligar a contas de armazenamento: 

- **Inicie sessão com a sua conta do Azure**: pode iniciar sessão com as credenciais de utilizador para a sua organização ou a sua conta Microsoft. 
- **Ligar a uma conta de armazenamento específica com uma cadeia de ligação ou um token SAS**: uma cadeia de ligação é uma cadeia especial que contém um nome de conta de armazenamento e o token de SAS/chave da conta de armazenamento. Com o token, o Explorador de Armazenamento acede diretamente à conta de armazenamento (em vez de simplesmente ver todas as contas de armazenamento numa conta do Azure). Para saber mais sobre as cadeias de ligação, veja [Configure Azure storage connection strings](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Configurar cadeias de ligação do Armazenamento do Azure).
- **Ligar a uma conta de armazenamento específica com o nome e a chave da mesma**: utilize o nome e a chave da conta de armazenamento da sua conta de armazenamento para ligar ao armazenamento do Azure.

Para efeitos deste início rápido, inicie sessão com a sua conta do Azure. Selecione **Adicionar uma Conta do Azure** e selecione **Iniciar sessão**. Siga os avisos para iniciar sessão na conta do Azure.

![Uma captura de ecrã do Explorador de Armazenamento do Microsoft Azure – Janela Ligar](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Para criar a sua primeira partilha de ficheiros do Azure na conta de armazenamento `storageacct<random number>`:

1. Expanda a conta de armazenamento que criou.
2. Clique com o botão direito do rato em **Partilhas de Ficheiros** e, em seguida, selecione **Criar Partilha de Ficheiros**.  
    ![Captura de ecrã da pasta de partilhas de ficheiros e do menu de contexto em contexto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Para a partilha de ficheiros, introduza *myshare* e, em seguida, prima Enter.

Os nomes das partilhas só podem ter letras minúsculas, números e hífenes (mas não podem começar com um hífen). Para obter detalhes completos sobre ações e ficheiros de ficheiros de [nomeação, consulte naming e referências de ações, diretórios, ficheiros e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Depois de criar a partilha de ficheiros, é aberto um separador para a partilha de ficheiros no painel direito. 

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
Agora que criou uma partilha de ficheiros do Azure, pode montá-la com SMB no [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Em alternativa, pode trabalhar com a partilha de ficheiros do Azure com o Explorador de Armazenamento do Azure. A vantagem de utilizar o Explorador de Armazenamento do Azure em vez de montar a partilha de ficheiros com o SMB é que todos os pedidos realizados com o Explorador de Armazenamento do Azure são feitos através da API REST de Ficheiros. Pode utilizar a API REST do Ficheiro para criar, modificar e eliminar ficheiros e diretórios em clientes que não têm acesso ao SMB.

### <a name="create-a-directory"></a>Criar um diretório
Adicionar um diretório proporciona uma estrutura hierárquica para gerir a sua partilha de ficheiros. Pode criar vários níveis no seu diretório. No entanto, tem de se certificar que os diretórios principais existem antes de criar subdiretórios. Por exemplo, para o caminho myDirectory/mySubDirectory, tem primeiro de criar o diretório *myDirectory*. Em seguida, pode criar *mySubDirectory*. 

1. No separador da partilha de ficheiros, no menu superior, selecione o botão **Nova Pasta**. O painel **Criar Novo Diretório** abre.
    ![Uma captura de ecrã do botão Nova Pasta em contexto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Para o nome do diretório, introduza *myDirectory* e, em seguida, selecione **OK**. 

O diretório *myDirectory* é listado no separador da partilha de ficheiros *myshare*.

### <a name="upload-a-file"></a>Carregar um ficheiro 
Pode carregar um ficheiro a partir do seu computador local para o diretório novo na partilha de ficheiros. Pode carregar uma pasta inteira ou um único ficheiro.

1. No menu superior, selecione **Carregar**. Isto dá-lhe a opção de carregar uma pasta ou um ficheiro.
2. Selecione **Carregar Ficheiro** e, em seguida, selecione um ficheiro para carregar a partir do seu computador local.
3. Em **Carregar para um diretório**, introduza *myDirectory* e, em seguida, selecione **Carregar**. 

Quando tiver terminado, o ficheiro é apresentado na lista no painel *myDirectory*.

### <a name="download-a-file"></a>Transferir um ficheiro
Para transferir uma cópia de um ficheiro a partir da partilha de ficheiros, clique com o botão direito do rato no ficheiro e, em seguida, selecione **Transferir**. Selecione onde pretende colocar o ficheiro no seu computador local e, em seguida, selecione **Guardar**.

O progresso da transferência aparece no painel **Atividades** na parte inferior da janela.

## <a name="clean-up-resources"></a>Limpar os recursos
Não pode utilizar o Explorador de Armazenamento para remover recursos. Para limpar este início rápido, pode utilizar o [portal do Azure](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)