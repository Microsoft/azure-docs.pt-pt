---
title: Gerir laboratórios de salas de aula em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a criar e configurar um laboratório de sala de aula, veja todos os laboratórios de sala de aula, partilhe a ligação de registo com um utilizador de laboratório, ou apague um laboratório.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 126a6f2f26d8eaaf6ea1a85f029e56bc8f4432f3
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996767"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerir laboratórios de salas de aula em Serviços de Laboratório Azure 
Este artigo descreve como criar e apagar um laboratório de sala de aula. Também mostra como ver todos os laboratórios numa conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta do laboratório, tem de ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Um proprietário de laboratório pode adicionar outros utilizadores à função Criador de Laboratório com os passos do seguinte artigo: [Adicionar um utilizador à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). O Internet Explorer 11 ainda não é suportado. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. Selecione ou introduza um ID de **utilizador** que seja membro do papel de Criador de **Laboratório** na conta de laboratório e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **Novo laboratório.** 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Selecione o **tamanho das máquinas virtuais** que precisa para a aula. Para a lista de tamanhos disponíveis, consulte a secção [Tamanhos VM.](#vm-sizes) 
    3. Selecione a **imagem virtual** da máquina que pretende utilizar para o laboratório de sala de aula. Se selecionar uma imagem Linux, verá uma opção para ativar a ligação remota para o ambiente de trabalho. Para mais detalhes, consulte [Ativar a ligação remota para o ambiente de trabalho para o Linux](how-to-enable-remote-desktop-linux.md).

        Se assinou usando credenciais do proprietário da conta de laboratório, verá uma opção para permitir mais imagens para o laboratório. Para mais informações, consulte [Imagens Enable no momento da criação do laboratório.](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    4. Reveja o **preço total por hora** exibido na página. 
    6. Selecione **Guardar**.

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Você vê uma opção para selecionar um local para o seu laboratório se a conta de laboratório foi configurada para permitir que o criador do laboratório escolha a opção de localização do [laboratório.](allow-lab-creator-pick-lab-location.md) 
4. Na página de credenciais de **máquina virtual,** especifique credenciais padrão para todos os VMs no laboratório.
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Desative Utilize a **mesma palavra-passe para todas as máquinas virtuais** se quiser que os alunos desempenem as suas próprias palavras-passe. Este passo é **opcional.** 

        Um professor pode optar por usar a mesma palavra-passe para todos os VMs do laboratório, ou permitir que os alunos desembotem senhas para os seus VMs. Por predefinição, esta definição está ativada para todas as imagens Windows e Linux, com exceção do Ubuntu. Ao selecionar **Ubuntu** VM, esta definição é desativada, pelo que os alunos serão solicitados a definir uma palavra-passe quando iniciarem o seu início.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Em seguida, selecione **Next** na página de credenciais de **máquina virtual.** 
5. Na página de políticas do **Laboratório,** faça os seguintes passos:
    1. Introduza o número de horas atribuídos a cada utilizador **(quota para cada utilizador**) fora da hora programada para o laboratório. 
    2. Para a opção **de encerramento automático de máquinas virtuais,** especifique se pretende que o VM seja desligado automaticamente quando o utilizador se desligar. Também pode especificar quanto tempo o VM deve esperar que o utilizador se reconecte antes de desligar automaticamente. Para obter mais informações, consulte [Ativar o encerramento automático de VMs na desconexão](how-to-enable-shutdown-disconnect.md).
    3. Em seguida, selecione **Terminar**. 

        ![Quota para cada utilizador](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Deve ver o seguinte ecrã que mostra o estado da criação vM do modelo. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Estado da criação vm do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na página **Modelo,** faça os seguintes passos: Estes passos são **opcionais** para o tutorial.

    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for um VM de modelo Linux, escolha se pretende ligar-se utilizando SSH ou RDP (se o RDP estiver ativado).
    1. Selecione **Redefinir a palavra-passe** para redefinir a palavra-passe para o VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
10. Na página **'Modelo',** **selecione Publicar** na barra de ferramentas. 

    ![Publicar botão de modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
8. Na página do **modelo Publicar,** introduza o número de máquinas virtuais que pretende criar em laboratório e, em seguida, selecione **Publicar**. 

    ![Publicar modelo - número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você vê o **estado de publicar** o modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Mude para a página de **piscina de máquinas virtuais** selecionando máquinas virtuais no menu esquerdo ou selecionando azulejos de máquinas virtuais. Confirme que vê máquinas virtuais que estão em estado **não atribuído.** Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Faça as seguintes tarefas nesta página (não faça estes passos para o tutorial. Estes passos são apenas para a sua informação.): 
    
    1. Para alterar a capacidade do laboratório (número de VMs no laboratório), selecione **a capacidade** do Laboratório na barra de ferramentas.
    2. Para iniciar todos os VMs de uma só vez, selecione **Iniciar tudo** na barra de ferramentas. 
    3. Para iniciar um VM específico, selecione a seta para baixo no **Estado**, e, em seguida, selecione **Iniciar**. Também pode iniciar um VM selecionando um VM na primeira coluna e, em seguida, selecionando **Iniciar** na barra de ferramentas.                

### <a name="vm-sizes"></a>Tamanhos de VM  

| Tamanho | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno | 2 | 3,5 GB | Este tamanho é mais adequado para linha de comando, abertura do navegador web, servidores web de tráfego baixo, bases de dados pequenas a médias. |
| Médio | 4 | 7 GB | Este tamanho é mais adequado para bases de dados relacionais, caches na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Este tamanho é mais adequado para bases de dados relacionais, caches na memória e análise. Este tamanho também suporta a virtualização aninhada. <p>Este tamanho pode ser usado em cenários onde cada aluno precisa de vários VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequena dimensão dentro da máquina virtual. </p> |
| Pequena GPU (Computação) | 6 | 56 GB | <p>Este tamanho é mais adequado para aplicações intensivas de computação e de rede intensiva, como inteligência artificial e aplicações de aprendizagem profunda.</p><p>A Azure Lab Services instala e configura automaticamente os controladores GPU necessários para si quando cria um laboratório com imagens de GPU. </p> | 
| Pequena GPU (Visualização) | 6 | 56 GB | Este tamanho é mais adequado para visualização remota, streaming, gaming, codificação usando quadros como OpenGL e DirectX. | 
| Grande | 8 | 16 GB | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápido, melhor desempenho do disco local, grandes bases de dados, grandes caches de memória. |
| Grande (virtualização aninhada) | 8 | 32 GB | Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápido, melhor desempenho do disco local, grandes bases de dados, grandes caches de memória. Este tamanho também suporta a virtualização aninhada. |  
| Média GPU (Visualização) | 12 | 112 GB | Este tamanho é mais adequado para visualização remota, streaming, gaming, codificação usando quadros como OpenGL e DirectX. | 

> [!NOTE]
> Pode não ver alguns destes tamanhos vm na lista ao criar um laboratório de sala de aula. A lista é povoada com base na capacidade atual da localização do laboratório. Se o criador da conta de laboratório permitir que os criadores de [laboratório escolham um local para o laboratório,](allow-lab-creator-pick-lab-location.md)pode tentar escolher um local diferente para o laboratório e ver se o tamanho do VM está disponível. 


## <a name="view-all-classroom-labs"></a>Ver todos os laboratórios de sala de aula
1. Navegue para [o portal Azure Lab Services.](https://labs.azure.com)
2. Selecione **Iniciar sessão**. Selecione ou introduza um ID de **utilizador** que seja membro do papel de Criador de **Laboratório** na conta de laboratório e introduza a palavra-passe. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Confirme que vê todos os laboratórios na conta de laboratório selecionada. No azulejo do laboratório, vê-se o número de máquinas virtuais no laboratório e a quota para cada utilizador (fora do horário programado).

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista de drop-down no topo para selecionar uma conta de laboratório diferente. Vê laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Apagar um laboratório de sala de aula
1. No azulejo para o laboratório, selecione três pontos (...) no canto e, em seguida, **selecione Delete**. 

    ![Eliminar botão](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo de **laboratório Delete,** selecione **Eliminar** para continuar com a eliminação. 

## <a name="switch-to-another-classroom-lab"></a>Mude para outro laboratório de sala de aula
Para mudar para outro laboratório de sala de aula da corrente, selecione a lista de laboratórios na conta de laboratório no topo.

![Selecione o laboratório da lista de drop-down no topo](../media/how-to-manage-classroom-labs/switch-lab.png)

Também pode criar um novo laboratório usando o **novo laboratório** nesta lista de entrega. 

> [!NOTE]
> Também pode utilizar o módulo Az.LabServices PowerShell (pré-visualização) para gerir laboratórios. Para mais informações, consulte a página inicial da [Az.LabServices no GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Para mudar para uma conta de laboratório diferente, selecione a queda ao lado da conta do laboratório e selecione a outra conta de laboratório. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)

