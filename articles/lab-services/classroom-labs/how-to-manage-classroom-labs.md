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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873574"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula em Azure Lab Services 
Este artigo descreve como criar e excluir um laboratório de sala de aula. Ele também mostra como exibir todos os laboratórios de sala de aula em uma conta de laboratório. 

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula numa conta do laboratório, tem de ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que utilizou para criar a conta de laboratório é adicionada automaticamente a esta função. Um proprietário de laboratório pode adicionar outros usuários à função de criador de laboratório usando as etapas no seguinte artigo: [Adicione um usuário à função de criador de laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Observe que o Internet Explorer 11 ainda não tem suporte. 
2. Selecione **entrar**. Selecione ou insira uma **ID de usuário** que seja membro da função de **criador de laboratório** na conta de laboratório e digite a senha. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Especifique o **número máximo de máquinas virtuais** no laboratório. Você pode aumentar ou diminuir o número de máquinas virtuais no laboratório posteriormente. 
    6. Selecione **Guardar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, execute os passos seguintes:
    1. Selecione um **tamanho** para máquinas virtuais (VM) criadas no laboratório. Atualmente, os tamanhos **pequeno**, **médio**, **médio (virtualização)** , **grande**e **GPU** são permitidos. Para obter detalhes, consulte a seção [tamanhos de VM](#vm-sizes) .
    1. Selecione a **região** na qual pretende que as VM sejam criadas. 
    1. Selecione a **imagem de VM** a ser utilizado para criar as VM no laboratório. Se você selecionar uma imagem do Linux, verá uma opção para habilitar a conexão de área de trabalho remota para ela. Para obter detalhes, consulte [habilitar conexão de área de trabalho remota para Linux](how-to-enable-remote-desktop-linux.md).
    1. Selecione **Seguinte**.

        ![Especificar as especificações de VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais** , especifique as credenciais predefinidas para todas as VM do laboratório. 
    1. Especifique o **nome de utilizador** de todas as VM do laboratório.
    2. Especifique a **palavra-passe** do utilizador. 

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Desabilitar a opção **usar a mesma senha para todas as máquinas virtuais** se desejar que os alunos definam suas próprias senhas. Este passo é **opcional**. 

        Um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definam senhas para suas VMs. Por padrão, essa configuração é habilitada para todas as imagens do Windows e do Linux, exceto para Ubuntu. Quando você seleciona VM **Ubuntu** , essa configuração é desabilitada, portanto, os alunos serão solicitados a definir uma senha quando entrarem pela primeira vez.
    1. Selecione **Criar**. 

        ![Definir credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, verá o estado do processo de criação do laboratório. A criação do modelo no laboratório demora no máximo 20 minutos. Um modelo num laboratório é a imagem de máquina virtual de base da qual todas as máquinas virtuais dos utilizadores são criadas. Configure a máquina virtual do modelo para que seja configurado com exatamente o que pretende fornecer aos utilizadores do laboratório.  

    ![Configurar modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Depois de concluída a configuração do modelo, verá a página seguinte: 

    ![Configurar a página do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Os passos seguintes são opcionais neste tutorial: 
    2. Ligue-se à VM de modelo através da seleção de **Ligar**. Se for uma VM de modelo do Linux, escolha se deseja se conectar usando SSH ou RDP (se o RDP estiver habilitado).
    1. Selecione **Redefinir senha** para redefinir a senha para a VM. 
    1. Instalar e configurar o software na sua VM de modelo. 
    1. **Parar** a VM.  
    1. Introduzir uma **descrição** do modelo
9. Selecione **Seguinte** na página do modelo. 
10. Na página **Publicar o modelo**, execute as ações seguintes. 
    1. Para publicar o modelo imediatamente, selecione a caixa de verificação *Compreendo que não é possível modificar o modelo após a publicação. Este processo pode ser feito apenas uma vez e pode demorar até uma hora*e selecione **Publicar**.  Publique o modelo para tornar as instâncias da VM de modelo disponíveis para os seus utilizadores de laboratório.

        > [!WARNING]
        > Depois de publicar, não é possível anular a publicação. 
    2. Para publicar mais tarde, selecione **Guardar para utilização posterior**. Pode publicar a VM de modelo depois de concluir o assistente. Para obter detalhes sobre como configurar e publicar após a conclusão do assistente, consulte para obter detalhes sobre como configurar e publicar após a conclusão do assistente, confira a seção publicar o modelo no artigo [como gerenciar laboratórios de sala de aula](how-to-manage-classroom-labs.md) .

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Verá o **progresso da publicação** do modelo. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Verá a página seguinte, quando o modelo for publicado com êxito. Selecione **Done** (Concluído).

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Verá o **dashboard** para o seu laboratório. 
    
    ![Dashboard de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Mude para a página **Máquinas virtuais** e confirme que vê as máquinas virtuais que estão no estado **Não atribuído**. Estas VMs ainda não estão atribuídas a estudantes. Elas devem estar no estado **Parado**. Pode iniciar uma VM de estudante, ligar à VM, parar a VM e eliminar a VM nesta página. Pode iniciá-las nesta página ou permitir que os seus estudantes iniciem as VMs. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Tamanhos de VM  

| Size | Núcleos | RAM | Descrição | 
| ---- | ----- | --- | ----------- | 
| Pequeno | 2 | 3,5 GB | Esse tamanho é mais adequado para linha de comando, abertura de navegador da Web, servidores Web de tráfego baixo, bancos de dados pequenos a médios. |
| Médio | 4 | 7 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise | 
| Médio (virtualização aninhada) | 4 | 16 GB | Esse tamanho é mais adequado para bancos de dados relacionais, cache na memória e análise. Esse tamanho também dá suporte à virtualização aninhada. <p>Esse tamanho pode ser usado em cenários em que cada aluno precisa de várias VMs. Os professores podem usar a virtualização aninhada para configurar algumas máquinas virtuais aninhadas de pequeno tamanho dentro da máquina virtual. </p> |
| Grande | 8 | 32 GB | Esse tamanho é mais adequado para aplicativos que precisam de CPUs mais rápidas, melhor desempenho de disco local, bancos de dados grandes, caches de memória grande. Esse tamanho também dá suporte à virtualização aninhada |  
| GPU pequena (visualização) | 6 | 56 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 
| GPU pequena (computação) | 6 | 56 GB | Esse tamanho é mais adequado para aplicativos com uso intensivo de computação e rede, como inteligência artificial e aplicativos de aprendizado profundo. | 
| GPU média (visualização) | 12 | 112 GB | Esse tamanho é mais adequado para visualização remota, streaming, jogos, codificação usando estruturas como OpenGL e DirectX. | 

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Azure Lab Services portal](https://labs.azure.com).
2. Selecione **entrar**. Selecione ou insira uma **ID de usuário** que seja membro da função de **criador de laboratório** na conta de laboratório e digite a senha. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Confirme que você vê todos os laboratórios na conta de laboratório selecionada. 

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você vê os laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco do laboratório, selecione três pontos (...) no canto. 

    ![Selecionar o laboratório](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecione **Eliminar**. 

    ![Botão de eliminar](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **excluir laboratório** , selecione **excluir**. 

    ![Caixa de diálogo Excluir](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Alternar para outro laboratório de sala de aula
Para alternar para outro laboratório de sala de aula do atual, selecione a lista suspensa de laboratórios na conta do laboratório na parte superior.

![Selecione o laboratório na lista suspensa na parte superior](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como proprietário do laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como proprietário de um laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como usuário do laboratório, acesse laboratórios da sala de aula](how-to-use-classroom-lab.md)

