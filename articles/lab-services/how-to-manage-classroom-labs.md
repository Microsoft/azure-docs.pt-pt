---
title: Gerir laboratórios nos Serviços Azure Lab Microsoft Docs
description: Aprenda a criar e configurar um laboratório de sala de aula, ver todos os laboratórios, partilhar a ligação de registo com um utilizador de laboratório ou apagar um laboratório.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: db7a2f58f99252a7e5076dd86c37b65bbe8ea37a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434214"
---
# <a name="manage-labs-in-azure-lab-services"></a>Gerir laboratórios em Azure Lab Services 
Este artigo descreve como criar e apagar um laboratório de sala de aula. Também mostra como ver todos os laboratórios numa conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta do laboratório, tem de ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Um proprietário de laboratório pode adicionar outros utilizadores à função Criador de Laboratório com os passos do seguinte artigo: [Adicionar um utilizador à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). O Internet Explorer 11 ainda não está suportado. 
1. Selecione **Iniciar sessão** e introduza as suas credenciais. Selecione ou introduza um **ID** de utilizador que seja um membro da função **de Criador** de Laboratório na conta de laboratório e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
1. Selecione **Novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    1. Selecione o **tamanho das máquinas virtuais necessárias** para a aula. Para obter a lista de tamanhos disponíveis, consulte a secção [tamanhos VM.](#vm-sizes) 
    1. Selecione a **imagem da máquina virtual** que pretende usar para o laboratório de sala de aula. Se selecionar uma imagem Linux, vê uma opção para ativar a **ligação remota do ambiente de trabalho**. Para obter mais informações, consulte [Ativar a ligação ao ambiente de trabalho remoto para Linux](how-to-enable-remote-desktop-linux.md).

        Se se inscreveu usando credenciais de proprietário de conta de laboratório, verá uma opção para ativar mais imagens para o laboratório. Para obter mais informações, consulte [Ativar as imagens no momento da criação do laboratório.](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    1. Reveja o **preço total por hora** exibido na página. 
    1. Selecione **Guardar**.

        ![Screenshot que mostra a janela do "Novo Laboratório".](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Você vê uma opção para selecionar um local para o seu laboratório se a conta do laboratório foi configurada para permitir que o criador do laboratório escolha a opção [de localização](allow-lab-creator-pick-lab-location.md) do laboratório. 
4. Na página **de credenciais de máquina Virtual,** especifique credenciais padrão para todos os VMs no laboratório.
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Desative **Utilize a mesma palavra-passe para todas as máquinas virtuais** se quiser que os alunos desemotivuem as suas próprias palavras-passe. Este passo é **opcional.** 

        Um educador pode optar por usar a mesma palavra-passe para todos os VMs do laboratório, ou permitir que os alunos estabeleçam senhas para os seus VMs. Por predefinição, esta definição está ativada para todas as imagens Windows e Linux, com exceção do Ubuntu. Quando selecionar **O VM Ubuntu,** esta definição é desativada, para que os alunos sejam solicitados a definir uma palavra-passe quando iniciarem sação pela primeira vez.  

        ![Nova janela do laboratório](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Em seguida, selecione **Seguinte** na página **de credenciais de máquina virtual.** 
5. Na página de políticas do **Laboratório,** faça os seguintes passos:
    1. Introduza o número de horas atribuídas a cada utilizador **(quota para cada utilizador**) fora da hora prevista para o laboratório. 
    2. Para a opção **de encerramento automático das máquinas virtuais, especifique** se pretende que o VM seja desligado automaticamente quando o utilizador desligar. Também pode especificar quanto tempo o VM deve esperar que o utilizador se reconecte antes de desligar automaticamente.. Para obter mais informações, consulte [Ativar o encerramento automático dos VMs na desconexão](how-to-enable-shutdown-disconnect.md).
    3. Em seguida, selecione **Finish** (Concluir). 

        ![Quota para cada utilizador](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Deve ver o seguinte ecrã que mostra o estado da criação do modelo VM. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Estado da criação do modelo VM](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Do Modelo,** faça os seguintes passos: Estes passos são **opcionais** para o tutorial.

    1. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um VM de modelo Linux, escolha se pretende ligar usando SSH ou um ambiente de trabalho remoto GUI.  É necessária uma configuração adicional para utilizar um ambiente de trabalho remoto GUI. Consulte [ativar o ambiente de trabalho remoto gráfico para máquinas virtuais Linux](how-to-use-remote-desktop-linux-student.md) para obter mais informações.
    1. Selecione Redefinir a **palavra-passe** para redefinir a palavra-passe para o VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
9.  Na página **do modelo,** selecione **Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
10. Na página do **modelo publicar,** insira o número de máquinas virtuais que pretende criar no laboratório e, em seguida, selecione **Publicar**. 

    ![Modelo de publicação - número de VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Vê o **estado de publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Mude para a página da **piscina das máquinas virtuais** selecionando máquinas virtuais no menu esquerdo ou selecionando azulejos de máquinas virtuais. Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Faça as seguintes tarefas nesta página (não faça estes passos para o tutorial. Estes passos são apenas para a sua informação.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **a capacidade de laboratório** na barra de ferramentas.
    2. Para iniciar todos os VMs de uma só vez, **selecione Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar um VM específico, selecione a seta para baixo no **Estado** e, em seguida, selecione **Iniciar**. Também pode iniciar um VM selecionando um VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.                

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno | 2 | 3,5 GB | Este tamanho é mais adequado para linha de comando, abertura de navegador, servidores web de baixo tráfego, bases de dados pequenas a médias. |
| Médio | 4 | 7 GB | Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise. Este tamanho também suporta a virtualização aninhada. <p>Este tamanho pode ser usado em cenários onde cada aluno precisa de vários VMs. Os educadores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno porte dentro da máquina virtual. </p> |
| Pequena GPU (Computação) | 6 | 56 GB | <p>Este tamanho é mais adequado para aplicações intensivas de computação e de rede, como inteligência artificial e aplicações de aprendizagem profunda.</p><p>A Azure Lab Services instala e configura automaticamente os controladores de GPU necessários para si quando cria um laboratório com imagens de GPU. </p> | 
| Pequeno GPU (Visualização) | 6 | 56 GB | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. | 
| Grande | 8 | 16 GB | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes. |
| Grande (virtualização aninhada) | 8 | 32 GB | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes. Este tamanho também suporta a virtualização aninhada. |  
| GPU médio (Visualização) | 12 | 112 GB | Este tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando quadros como OpenGL e DirectX. | 

> [!NOTE]
> Pode não ver alguns destes tamanhos de VM na lista ao criar um laboratório de sala de aula. A lista é povoada com base na capacidade atual da localização do laboratório. Se o criador da conta de laboratório permitir que os criadores de [laboratório escolham um local para o laboratório,](allow-lab-creator-pick-lab-location.md)pode tentar escolher um local diferente para o laboratório e ver se o tamanho do VM está disponível. 

## <a name="view-all-labs"></a>Ver todos os laboratórios

1. Navegue para o [portal Azure Lab Services](https://labs.azure.com).
1. Selecione **Iniciar sessão**. Selecione ou introduza um **ID** de utilizador que seja um membro da função **de Criador** de Laboratório na conta de laboratório e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Confirme que vê todos os laboratórios na conta de laboratório selecionada. No azulejo do laboratório, vê-se o número de máquinas virtuais no laboratório e a quota para cada utilizador (fora da hora programada).

    ![Todos os laboratórios](./media/how-to-manage-classroom-labs/all-labs.png)
1. Use a lista de drop-down no topo para selecionar uma conta de laboratório diferente. Vês laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Apagar um laboratório de sala de aula

1. No azulejo do laboratório, selecione três pontos (...) no canto e, em seguida, selecione **Delete**. 

    ![Eliminar botão](./media/how-to-manage-classroom-labs/delete-button.png)
1. Na caixa de diálogo **de laboratório Delete,** selecione **Delete** para continuar com a eliminação. 

## <a name="switch-to-another-classroom-lab"></a>Mude para outro laboratório de sala de aula

Para mudar para outro laboratório de sala de aula da corrente, selecione a lista de laboratórios na conta de laboratório no topo.

![Selecione o laboratório da lista de drop-down no topo](./media/how-to-manage-classroom-labs/switch-lab.png)

Também pode criar um novo laboratório usando o **novo laboratório** nesta lista de espera. 

> [!NOTE]
> Também pode utilizar o módulo PowerShell (pré-visualização) do Az.LabServices para gerir laboratórios. Para mais informações, consulte a página inicial do [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para mudar para outra conta de laboratório, selecione a entrega ao lado da conta do laboratório e selecione a outra conta de laboratório. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e publique modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceder a laboratórios](how-to-use-classroom-lab.md)

