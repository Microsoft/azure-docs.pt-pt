---
title: Como testar a sua aplicação em Azure | Microsoft Docs
description: Aprenda a criar uma partilha de ficheiros num laboratório e monte-a na sua máquina local e numa máquina virtual em laboratório e, em seguida, implemente aplicações de desktop/web para a partilha de ficheiros e teste-as.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87282250"
---
# <a name="test-your-app-in-azure"></a>Testar a aplicação no Azure 
Este artigo fornece passos para testar a sua aplicação em Azure usando a DevTest Labs. Primeiro, cria-se uma partilha de ficheiros dentro de um laboratório e monta-se como uma unidade na sua máquina de desenvolvimento local e um VM dentro de um laboratório. Em seguida, utiliza o Visual Studio 2019 para implementar a sua app na partilha de ficheiros para que possa executar a aplicação no VM em laboratório.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
1. [Crie uma subscrição Azure](https://azure.microsoft.com/free/) se ainda não tiver uma e inscreva-se no [portal Azure](https://portal.azure.com).
2. Siga as instruções [deste artigo](devtest-lab-create-lab.md) para criar um laboratório usando a Azure DevTest Labs. Pin o laboratório no seu painel de instrumentos para que possa encontrá-lo facilmente da próxima vez que iniciar sôs. A Azure DevTest Labs permite-lhe criar rapidamente recursos dentro do Azure, minimizando o desperdício e controlando os custos. Para saber mais sobre a DevTest Labs, consulte [a visão geral.](devtest-lab-overview.md) 
3. Crie uma conta de Armazenamento Azure no grupo de recursos do laboratório seguindo instruções no artigo da [conta de armazenamento Criar.](../storage/common/storage-account-create.md) Na página de **conta de armazenamento Criar,** selecione **Utilizar o** grupo **de recursos** e selecione o grupo de recursos **do laboratório**. 
4. Crie uma partilha de ficheiros no seu armazenamento Azure seguindo as instruções no artigo criar um ficheiro no artigo [do Azure Files.](../storage/files/storage-how-to-create-file-share.md) 

## <a name="mount-the-file-share-on-your-local-machine"></a>Monte a partilha de ficheiros na sua máquina local
1. Na sua máquina local, utilize o script da secção de partilha de [ficheiros Azure do Mount the Azure](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) [Use um ficheiro Azure share com](../storage/files/storage-how-to-use-files-windows.md) o artigo do Windows. 
2. Em seguida, utilize `net use` o comando para montar a partilha de ficheiros na sua máquina. Aqui está o comando da amostra: Especifique o nome de armazenamento Azure e o nome da partilha de ficheiros antes de executar o comando. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Criar uma VM no laboratório
1. Na página **de partilha de ficheiros,** selecione o grupo **de recursos** no menu de migalhas de pão no topo. Vê a página do **grupo de recursos.** 
    
    ![Selecione grupo de recursos a partir do menu breadcrumb](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Na página do **grupo de Recursos,** selecione o **laboratório** que criou nos Laboratórios DevTest.

    ![Selecionar o laboratório](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Na página **DevTest Lab** para o seu laboratório, selecione **+ Adicione** na barra de ferramentas. 

    ![Adicione botão para o laboratório](media/test-app-in-azure/add-button-in-lab.png)
4. Na página **base,** procure **por smalldisk** e selecione **[smalldisk] Windows Server 2016 Data Center**. 

    ![Escolha o servidor windows de pequeno disco](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Na página **da máquina Virtual,** especifique o **nome da máquina virtual,** **o nome do utilizador,** **a palavra-passe** e selecione **Criar**.    
    
    ![Criar página de máquina virtual](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>Monte a partilha de ficheiros no seu VM
1. Depois de a máquina virtual ser criada com sucesso, selecione a **máquina virtual** da lista.    

    ![Selecione o VM de laboratório](media/test-app-in-azure/select-lab-vm.png)
2. Selecione **Ligar** na barra de ferramentas para ligar ao VM. 
3. [Instalar o Azure PowerShell](/powershell/azure/install-az-ps).
4. Siga as instruções na secção "Montagem" da secção de partilha de ficheiros. 

## <a name="publish-your-app-from-visual-studio"></a>Publique a sua aplicação no Visual Studio
Nesta secção, publica a sua aplicação do Visual Studio para um teste VM na nuvem.

1. Crie uma aplicação desktop/web utilizando o Visual Studio 2019.
2. Crie a sua aplicação.
3. Para publicar a sua aplicação, clique com o botão direito no seu projeto no **Solution Explorer** e selecione **Publicar**. 
4. No **assistente publicar,** insira a **unidade** que está mapeada para a sua parte de ficheiro.

    **Aplicativo de ambiente de trabalho:**

    ![Aplicação de ambiente de trabalho](media/test-app-in-azure/desktop-app.png)

    **Aplicativo web:**

    ![Aplicação Web](media/test-app-in-azure/web-app.png)

1. Selecione **Seguinte** para completar o fluxo de trabalho de publicação e selecione **Terminar**. Quando termina os passos de assistente, o Visual Studio constrói a sua aplicação e publica-a na sua parte de ficheiros. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Teste a aplicação no seu VM de teste em laboratório

1. Navegue na página da máquina virtual para o seu VM no laboratório. 
2. Selecione **Iniciar** a barra de ferramentas para iniciar o VM se estiver em estado de paragem. Pode configurar políticas de arranque automático e de paragem automática para o seu VM para evitar o arranque e paragem de cada vez. 
3. Selecione **Ligar**.

    ![Página de máquina virtual](media/test-app-in-azure/virtual-machine-page.png)
4. Dentro da máquina virtual, lance **o File Explorer** e selecione Este **PC** para encontrar a sua partilha de ficheiros.

    ![Encontre a partilha em VM](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Por qualquer motivo, se não conseguir encontrar a sua partilha de ficheiros na sua máquina virtual ou na sua máquina local, pode remontá-la executando o `net use` comando. Pode encontrar o `net use` comando no **'Connect** Wizard' da sua Partilha de **Ficheiros** no portal Azure.
1. Abra a partilha de ficheiros e confirme que vê a aplicação que implementou a partir do Visual Studio. 

    ![Partilhar em VM](media/test-app-in-azure/open-file-share.png)

    Agora pode aceder e testar a sua aplicação dentro do teste VM que criou no Azure.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos para aprender a usar VMs em laboratório. 

- [Adicionar uma VM a um laboratório](devtest-lab-add-vm.md)
- [Reiniciar um VM de laboratório](devtest-lab-restart-vm.md)
- [Redimensionar um VM de laboratório](devtest-lab-resize-vm.md)
