---
title: Como testar a sua app em Azure Microsoft Docs
description: Aprenda a criar uma partilha de ficheiros num laboratório e monte-a na sua máquina local e numa máquina virtual no laboratório, e depois implemente aplicações de desktop/web para a partilha de ficheiros e teste-as.
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
ms.openlocfilehash: f8c57b9e1fabbd04a7d9c92484b0f52f074c2577
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "65872442"
---
# <a name="test-your-app-in-azure"></a>Testar a aplicação no Azure 
Este artigo fornece passos para testar a sua aplicação em Azure usando DevTest Labs. Primeiro, crias uma partilha de ficheiros dentro de um laboratório e montas-a como um impulso na tua máquina de desenvolvimento local e um VM dentro de um laboratório. Depois, usa o Visual Studio 2019 para implementar a sua aplicação na partilha de ficheiros para que possa executar a aplicação no VM em laboratório.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
1. [Crie uma subscrição Azure](https://azure.microsoft.com/free/) se ainda não tiver uma, e assine no [portal Azure.](https://portal.azure.com)
2. Siga as instruções [deste artigo](devtest-lab-create-lab.md) para criar um laboratório utilizando o Azure DevTest Labs. Coloque o laboratório no seu painel de instrumentos para que possa encontrá-lo facilmente da próxima vez que iniciar a sua inscrição. A Azure DevTest Labs permite-lhe criar recursos dentro do Azure rapidamente, minimizando os resíduos e controlando os custos. Para saber mais sobre devTest Labs, consulte [a visão geral.](devtest-lab-overview.md) 
3. Crie uma conta de Armazenamento Azure no grupo de recursos do laboratório seguindo instruções no artigo criar uma conta de [armazenamento.](../storage/common/storage-create-storage-account.md) Na página da **conta de armazenamento Create,** selecione **Use existente** para o **grupo Recursos**, e selecione o grupo de recursos **do laboratório**. 
4. Crie uma partilha de ficheiros no seu armazenamento Azure seguindo instruções no artigo Create a file share no artigo [Do Azure Files.](../storage/files/storage-how-to-create-file-share.md) 

## <a name="mount-the-file-share-on-your-local-machine"></a>Monte a parte do ficheiro na sua máquina local
1. Na sua máquina local, utilize o script de credenciais de partilha de [ficheiros Do Indo Azure na](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows) secção Windows de Use uma partilha de [ficheiros Azure com](../storage/files/storage-how-to-use-files-windows.md) o artigo do Windows. 
2. Em seguida, use `net use` o comando para montar a parte do ficheiro na sua máquina. Aqui está o comando da amostra: Especifique o nome de armazenamento do Azure e o nome da partilha de ficheiros antes de executar o comando. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Criar uma VM no laboratório
1. Na página **de partilha de Ficheiros,** selecione o grupo **de recursos** no menu breadcrumb no topo. Vê a página do **grupo Resource.** 
    
    ![Selecione grupo de recursos do menu breadcrumb](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na página do **grupo Resource,** selecione o **laboratório** que criou nos Laboratórios DevTest.

    ![Selecionar o laboratório](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na página **DevTest Lab** para o seu laboratório, selecione **+ Adicione** na barra de ferramentas. 

    ![Adicione o botão para o laboratório](media/test-app-in-azure/add-button-in-lab.png)
4. Na página **'Escolha uma** página base,' procure **smalldisk**e selecione **[smalldisk] Windows Server 2016 Data Center**. 

    ![Escolha o pequeno servidor Windows do disco](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na página da **máquina Virtual,** especifique o nome da **máquina virtual,** o nome do **utilizador,** **a palavra-passe**e selecione **Criar**.    
    
    ![Criar página de máquina virtual](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Monte a parte do ficheiro no seu VM
1. Depois de a máquina virtual ser criada com sucesso, selecione a **máquina virtual** da lista.    

    ![Selecione o VM do laboratório](media/test-app-in-azure/select-lab-vm.png)
2. Selecione **Ligar** na barra de ferramentas para ligar ao VM. 
3. [Instale o Azure PowerShell](/powershell/azure/install-az-ps).
4. Siga as instruções no Monte a secção de partilha de ficheiros. 

## <a name="publish-your-app-from-visual-studio"></a>Publique a sua aplicação no Visual Studio
Nesta secção, publica a sua aplicação do Visual Studio para um Teste VM na nuvem.

1. Crie uma aplicação de desktop/web utilizando o Visual Studio 2019.
2. Crie a sua aplicação.
3. Para publicar a sua aplicação, clique no seu projeto no **Solution Explorer**e selecione **Publicar**. 
4. No **assistente publicar,** introduza a **unidade** que está mapeada para a sua parte de ficheiro.

    **Aplicativo de ambiente de trabalho:**

    ![Aplicação de ambiente de trabalho](media/test-app-in-azure/desktop-app.png)

    **Aplicação web:**

    ![Aplicação Web](media/test-app-in-azure/web-app.png)

1. Selecione **Next** para completar o fluxo de trabalho de publicação e selecione **Terminar**. Quando terminar os passos do assistente, o Visual Studio constrói a sua aplicação e publica-a na sua parte de ficheiro. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Teste a aplicação no seu teste VM no laboratório

1. Navegue para a página de máquinavirtual para o seu VM no laboratório. 
2. Selecione **Iniciar** na barra de ferramentas para iniciar o VM se estiver em estado de paragem. Pode configurar políticas de arranque automático e de paragem automática para o seu VM para evitar iniciar e parar cada vez. 
3. Selecione **Ligar**.

    ![Página de máquina virtual](media/test-app-in-azure/virtual-machine-page.png)
4. Dentro da máquina virtual, lance **o File Explorer**e selecione Este **PC** para encontrar a sua partilha de ficheiros.

    ![Encontre parte em VM](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Por qualquer motivo, se não conseguir encontrar a sua parte de ficheiro na sua máquina virtual `net use` ou na sua máquina local, pode remontá-la executando o comando. Pode encontrar `net use` o comando no **Assistente de Ligação** da sua Partilha de **Ficheiros** no portal Azure.
1. Abra a partilha de ficheiros e confirme que vê a aplicação que implementou no Visual Studio. 

    ![Open share na VM](media/test-app-in-azure/open-file-share.png)

    Agora pode aceder e testar a sua aplicação dentro do VM de teste que criou no Azure.

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos para aprender a usar VMs em laboratório. 

- [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)
- [Reiniciar um VM de laboratório](devtest-lab-restart-vm.md)
- [Redimensionar um VM de laboratório](devtest-lab-resize-vm.md)
