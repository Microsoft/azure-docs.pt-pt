---
title: Gerenciar laboratórios de sala de aula em Azure Lab Services | Microsoft Docs
description: Saiba como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula, compartilhar o link de registro com um usuário de laboratório ou excluir um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: ad7fd664f0dce08e4482b4fb2cba2831208396fc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264836"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula em Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Ele também mostra como exibir todos os laboratórios de sala de aula em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta do laboratório, tem de ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Um proprietário de laboratório pode adicionar outros utilizadores à função Criador de Laboratório com os passos do seguinte artigo: [Adicionar um utilizador à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). O Internet Explorer 11 ainda não tem suporte. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. Selecione ou insira uma **ID de usuário** que seja membro da função de **criador de laboratório** na conta de laboratório e digite a senha. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Selecione o **tamanho das máquinas virtuais** que você precisa para a classe. Para obter a lista de tamanhos disponíveis, consulte a seção [tamanhos de VM](#vm-sizes) . 
    3. Selecione a **imagem de máquina virtual** que você deseja usar para o laboratório de sala de aula. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, consulte [habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    4. Examine o **preço total por hora** exibido na página. 
    6. Selecione **Guardar**.

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **credenciais da máquina virtual** , especifique as credenciais padrão para todas as VMs no laboratório.
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Este passo é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para Ubuntu. Quando você seleciona VM **Ubuntu** , essa configuração é desabilitada, portanto, os alunos serão solicitados a definir uma senha quando entrarem pela primeira vez.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.    
    4. Em seguida, selecione **Avançar** na página **credenciais da máquina virtual** . 
5. Na página **políticas de laboratório** , insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora da hora agendada para o laboratório e selecione **concluir**. 

    ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deve ver a tela a seguir que mostra o status da criação da VM do modelo. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Status da criação da VM do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **modelo** , execute as seguintes etapas: essas etapas são **opcionais** para o tutorial.

    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for uma VM de modelo do Linux, escolha se deseja se conectar usando SSH ou RDP (se o RDP estiver habilitado).
    1. Selecione **Redefinir senha** para redefinir a senha para a VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
10. Na página **modelo** , selecione **publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
8. Na página **Publicar modelo** , insira o número de máquinas virtuais que você deseja criar no laboratório e, em seguida, selecione **publicar**. 

    ![Publicar modelo-número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status da publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Alterne para a página do **pool de máquinas virtuais** selecionando máquinas virtuais no menu à esquerda ou selecionando máquinas virtuais bloco. Confirme que você vê as máquinas virtuais que estão em estado não **atribuído** . Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Você executa as seguintes tarefas nesta página (não execute estas etapas para o tutorial. Estas etapas são apenas para suas informações.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **capacidade do laboratório** na barra de ferramentas.
    2. Para iniciar todas as VMs de uma só vez, selecione **Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar uma VM específica, selecione a seta para baixo no **status**e, em seguida, selecione **Iniciar**. Você também pode iniciar uma VM selecionando uma VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.                

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno | 2 | 3,5 GB | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | 4 | 7 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. Esse tamanho também dá suporte à virtualização aninhada. <p>Esse tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| Grande | 8 | 32 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. Esse tamanho também dá suporte à virtualização aninhada |  
| GPU pequena (visualização) | 6 | 56 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 
| GPU pequena (computação) | 6 | 56 GB | Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aplicativos de aprendizado profundo. | 
| GPU média (visualização) | 12 | 112 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 

> [!NOTE]
> Azure Lab Services instala e configura automaticamente os drivers de GPU necessários para você quando você cria um laboratório com imagens de GPU.  

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Azure Lab Services portal](https://labs.azure.com).
2. Selecione **entrar**. Selecione ou insira uma **ID de usuário** que seja membro da função de **criador de laboratório** na conta de laboratório e digite a senha. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Confirme que você vê todos os laboratórios na conta de laboratório selecionada. No bloco do laboratório, você verá o número de máquinas virtuais no laboratório e a cota de cada usuário (fora da hora agendada).

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você vê os laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco do laboratório, selecione três pontos (...) no canto e, em seguida, selecione **excluir**. 

    ![Botão de eliminar](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **excluir laboratório** , selecione **excluir** para continuar com a exclusão. 

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula
Para alternar para outro laboratório de sala de aula do atual, selecione a lista suspensa de laboratórios na conta do laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)

Você também pode criar um novo laboratório usando o **novo laboratório** nesta lista suspensa. 

> [!NOTE]
> Você também pode usar o módulo do PowerShell AZ. LabServices (versão prévia) para gerenciar laboratórios. Para obter mais informações, consulte [AZ. LabServices Home Page no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para alternar para uma conta de laboratório diferente, selecione a lista suspensa ao lado da conta de laboratório e selecione a outra conta de laboratório. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como proprietário do laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)

