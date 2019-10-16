---
title: Habilitar ambientes de várias VMs no Azure Lab Services | Microsoft Docs
description: Saiba como criar um ambiente com várias máquinas virtuais dentro de uma máquina virtual de modelo em um laboratório de sala de aula de Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332333"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Criar um ambiente com várias VMs dentro de uma VM de modelo de um laboratório de sala de aula
Atualmente Azure Lab Services permite que você configure uma máquina virtual de modelo em um laboratório e disponibilize uma única cópia para cada um de seus usuários. Mas se você for um professor ensinando uma classe de ti sobre como configurar firewalls ou servidores, talvez seja necessário fornecer a cada um de seus alunos um ambiente no qual várias máquinas virtuais possam se comunicar entre si por meio de uma rede.

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada usuário do laboratório uma máquina virtual configurada com várias VMs dentro dela.

## <a name="what-is-nested-virtualization"></a>O que é virtualização aninhada?
A virtualização aninhada permite que você crie máquinas virtuais em uma máquina virtual. A virtualização aninhada é feita por meio do Hyper-V e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os seguintes artigos:

- [Virtualização aninhada no Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Usar a virtualização aninhada no Azure Lab Services
As etapas importantes são:

1. Crie uma **grande** máquina de modelo do **Windows** para o laboratório. 
2. Conecte-se a ele e [habilite a virtualização aninhada](../../virtual-machines/windows/nested-virtualization.md).


O procedimento a seguir fornece as etapas detalhadas: 

1. Crie uma conta de laboratório se você ainda não tiver uma. Para obter instruções, consulte [tutorial: configurar uma conta de laboratório com Azure Lab Services](tutorial-setup-lab-account.md).
1. Navegue para o [site do Azure Lab Services](https://labs.azure.com). Observe que o Internet Explorer 11 ainda não tem suporte. 
2. Selecione **Iniciar sessão** e introduza as suas credenciais. O Azure Lab Services suporta contas organizacionais e contas Microsoft. 
3. Selecione **novo laboratório**. 
    
    ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Na janela **Novo Laboratório**, realize as ações abaixo: 
    1. Especifique um **nome** para o seu laboratório. 
    2. Selecione **grande (virtualização aninhada)** ou **média (virtualização aninhada)** para o **tamanho da máquina virtual**.
    6. Selecione uma **imagem** do Windows que você deseja usar. A virtualização aninhada só está disponível em computadores Windows. 
    4. Em seguida, selecione **Seguinte**. 

        ![Criar um laboratório de sala de aula](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Na página **credenciais da máquina virtual** , especifique as credenciais padrão para todas as VMs no laboratório. Especifique o **nome** e a **senha** para o usuário e, em seguida, selecione **Avançar**.  

        ![Nova janela de laboratório](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Registe o nome de utilizador e a palavra-passe. Estas não serão apresentadas novamente.
    3. Na página **políticas de laboratório** , insira o número de horas alocadas para cada usuário (**cota para cada usuário**) fora da hora agendada para o laboratório e selecione **concluir**. 

        ![Cota para cada usuário](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Você deve ver a tela a seguir que mostra o status da criação da VM do modelo. A criação do modelo no laboratório demora no máximo 20 minutos. 

    ![Status da criação da VM do modelo](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Na página **modelo** , selecione **Personalizar modelo** na barra de ferramentas. 

    ![Botão Personalizar modelo](../media/how-to-create-manage-template/customize-template-button.png)
2. Na caixa de diálogo **Personalizar modelo** , selecione **continuar**. Depois de iniciar o modelo e fazer alterações, ele não terá mais a mesma configuração que as máquinas virtuais publicadas pela última vez para os usuários. As alterações de modelo não serão refletidas nas máquinas virtuais existentes de seus usuários até que você publique novamente.

    ![Caixa de diálogo Personalizar](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selecione o botão **conectar ao modelo** na barra de ferramentas para se conectar à VM do modelo para configurar a virtualização aninhada. e siga as instruções. Se for um computador Windows, você verá uma opção para baixar o arquivo RDP. 

    ![Ligar à VM do modelo](../media/how-to-create-manage-template/connect-template-vm.png) 
9. Dentro da máquina virtual de modelo, configure a virtualização aninhada e configure uma rede virtual com várias máquinas virtuais. Para obter instruções passo a passo detalhadas, consulte [como habilitar a virtualização aninhada em uma VM do Azure](../../virtual-machines/windows/nested-virtualization.md). Aqui está um resumo rápido das etapas: 
    1. Habilite o recurso Hyper-V na máquina virtual do modelo.
    2. Configurar uma rede virtual interna com conectividade com a Internet para as máquinas virtuais aninhadas
    3. Criar máquinas virtuais por meio do Gerenciador do Hyper-V
    4. Atribuir um endereço IP às máquinas virtuais
10. Na página **modelo** , selecione **publicar** na barra de ferramentas. 

    ![Botão Publicar modelo](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Depois de publicar, não é possível anular a publicação. 
8. Na página **Publicar modelo** , insira o número de máquinas virtuais que você deseja criar no laboratório e, em seguida, selecione **publicar**. 

    ![Publicar modelo-número de VMs](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Você verá o **status da publicação** do modelo na página. Este processo pode demorar até uma hora. 

    ![Publicar modelo – progresso](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Passos seguintes

Agora, cada usuário Obtém uma única máquina virtual que inclui um ambiente de várias VMs dentro dela. Para saber como adicionar usuários ao laboratório e enviar um link de registro para eles, consulte o seguinte artigo: [Adicionar usuários ao laboratório](tutorial-setup-classroom-lab.md#add-users-to-the-lab).