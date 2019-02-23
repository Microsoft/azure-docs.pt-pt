---
title: Como testar a sua aplicação no Azure | Documentos da Microsoft
description: Saiba como criar uma partilha de ficheiros num laboratório e montá-la no seu computador local e uma máquina virtual no laboratório e, em seguida, implementar aplicações de ambiente de trabalho/web para a partilha de ficheiros e testá-las.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: 9ca1c4a1ed1841f82bc386cff48315dd15ef91bb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730414"
---
# <a name="test-your-app-in-azure"></a>Testar a aplicação no Azure 
Este artigo fornece passos para testar a sua aplicação no Azure com o DevTest Labs. Comece por configurar uma partilha de ficheiros num laboratório e montá-la como uma unidade na sua máquina de desenvolvimento local e uma VM dentro de um laboratório. Em seguida, utilizar o Visual Studio 2017 para implementar a sua aplicação para a partilha de ficheiros, para que pode executar a aplicação na VM no laboratório.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
1. [Criar uma subscrição do Azure](https://azure.microsoft.com/free/) se já não tiver uma e inicie sessão no [portal do Azure](https://portal.azure.com).
2. Siga as instruções em [este artigo](devtest-lab-create-lab.md) para criar um laboratório com o Azure DevTest Labs. Afixe o laboratório ao seu dashboard para que pode encontrá-la facilmente próxima vez que iniciar sessão. Os laboratórios DevTest do Azure permite-lhe criar recursos no Azure rapidamente, minimiza desperdícios e controla os custos. Para saber mais sobre o DevTest Labs, veja [descrição geral](devtest-lab-overview.md). 
3. Criar uma conta de armazenamento do Azure no grupo de recursos do laboratório ao seguir as instruções no [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md) artigo. Sobre o **criar conta de armazenamento** página, selecione **utilizar existente** para **grupo de recursos**e selecione o **grupo de recursos do laboratório**. 
4. Criar uma partilha de ficheiros no armazenamento do Azure ao seguir as instruções no [criar uma partilha de ficheiros nos ficheiros do Azure](../storage/files/storage-how-to-create-file-share.md) artigo. 

## <a name="mount-the-file-share-on-your-local-machine"></a>Montar a partilha de ficheiros no seu computador local
1. No seu computador local, utilize o script a partir [credenciais de partilha de ficheiros do Azure de persistência no Windows](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) secção [utilizar uma partilha de ficheiros do Azure com o Windows](../storage/files/storage-how-to-use-files-windows.md) artigo. 
2. Em seguida, utilize `net use` comandos para montar a partilha de ficheiros no seu computador. Eis o comando de exemplo: Especifique o nome de armazenamento do Azure e o nome da partilha de ficheiros antes de executar o comando. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Criar uma VM no laboratório
1. Sobre o **partilha de ficheiros** página, selecione a **grupo de recursos** no menu de trilho na parte superior. Verá o **grupo de recursos** página. 
    
    ![Selecione o grupo de recursos a partir do menu de trilho](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Sobre o **grupo de recursos** página, selecione a **laboratório** criadas no DevTest Labs.

    ![Selecionar o laboratório](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Sobre o **laboratório Dev/Test** página para seu laboratório, selecione **+ adicionar** na barra de ferramentas. 

    ![Adicionar botão para o laboratório](media/test-app-in-azure/add-button-in-lab.png)
4. Na **Vyberte bázi** página, procure **smalldisk**e selecione **[smalldisk] Centro de dados do Windows Server 2016**. 

    ![Escolha pequeno disco do servidor Windows](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Sobre o **Máquina Virtual** , especifique **nome da máquina virtual**, **nome de utilizador**, **palavra-passe**e selecione **Create** .    
    
    ![Criar a página de máquina virtual](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Montar a partilha de ficheiros na sua VM
1. Depois da máquina virtual é criada com êxito, selecione o **máquina virtual** da lista.    

    ![Selecione a VM de laboratório](media/test-app-in-azure/select-lab-vm.png)
2. Selecione **Connect** na barra de ferramentas para ligar à VM. 
3. [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).
4. Siga as instruções de montagem a secção de partilha de ficheiros. 

## <a name="publish-your-app-from-visual-studio"></a>Publique a sua aplicação a partir do Visual Studio
Nesta secção, publica a aplicação a partir do Visual Studio para uma VM na cloud de teste.

1. Crie uma aplicação de ambiente de trabalho/web usando o Visual Studio 2017.
2. Crie a sua aplicação.
3. Para publicar a aplicação, clique com botão direito do rato no **Explorador de soluções**e selecione **Publish**. 
4. Na **Assistente de publicação**, introduza o **unidade** que está mapeado para a partilha de ficheiros.

    **Aplicação de ambiente de trabalho:**

    ![Aplicativo de Desktop](media/test-app-in-azure/desktop-app.png)

    **Aplicação Web:**

    ![Aplicação Web](media/test-app-in-azure/web-app.png)

1. Selecione **próxima** para concluir o fluxo de trabalho de publicar e selecione **concluir**. Quando concluir os passos do assistente, o Visual Studio cria seu aplicativo e publica-o para a partilha de ficheiros. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Testar a aplicação na sua VM de teste no laboratório

1. Navegue para a página de máquina virtual para a sua VM no laboratório. 
2. Selecione **iniciar** na barra de ferramentas para iniciar a VM se estiver no parado estado. Pode definir políticas de início automático e o encerramento automático para a sua VM evitar iniciar e parar de cada vez. 
3. Selecione **Ligar**.

    ![Página de máquina virtual](media/test-app-in-azure/virtual-machine-page.png)
4. Dentro da máquina virtual, inicie **Explorador de ficheiros**e selecione **este PC** para encontrar a partilha de ficheiros.

    ![Encontrar a partilha de VM](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Por qualquer motivo, se não for possível encontrar a partilha de ficheiros na sua máquina virtual ou no seu computador local, pode remontá-lo ao executar o `net use` comando. Pode encontrar os `net use` comando o **Connect** Assistente de sua **partilha de ficheiros** no portal do Azure.
1. Abra a partilha de ficheiros e confirme que consegue ver a aplicação que implementou a partir do Visual Studio. 

    ![Abrir a partilha na VM](media/test-app-in-azure/open-file-share.png)

    Agora pode aceder e testar seu aplicativo na VM que criou no Azure de teste.

## <a name="next-steps"></a>Passos Seguintes
Veja os artigos seguintes para aprender a utilizar VMs num laboratório. 

- [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)
- [Reiniciar uma VM de laboratório](devtest-lab-restart-vm.md)
- [Redimensionar uma VM de laboratório](devtest-lab-resize-vm.md)
