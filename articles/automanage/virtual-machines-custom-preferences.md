---
title: Crie uma preferência personalizada em Azure Automanage para VMs
description: Aprenda a ajustar o perfil de configuração em Azure Automanage para VMs e desajuste as suas preferências.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 377677c9e5e81487059241db68baff639a3de033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715040"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Crie uma preferência personalizada em Azure Automanage para VMs

A Azure Automanage para as melhores práticas da máquina virtual tem perfis de configuração predefinidos que podem ser ajustados se necessário. Este artigo explicará como pode definir as suas próprias preferências de perfil de configuração quando ativar a auto-gestão num VM novo ou existente.

Atualmente, apoiamos personalizações no [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [no Microsoft Antimalware.](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)


> [!NOTE]
> Não é possível alterar o perfil de configuração ou a preferência no seu VM enquanto a automanagem estiver ativada. Terá de desativar a auto-mutilação para esse VM e, em seguida, voltar a ativar a Automanage com o perfil e preferências de configuração pretendidos.


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

> [!IMPORTANT]
> É necessária a seguinte permissão Azure RBAC para ativar a autoadministração: função **do proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Ativar a auto-mutilação dos VMs num VM existente

1. Na barra de pesquisa, procure e selecione **Automanage – Azure virtual machine boas práticas.**

2. **Selecione a Ativação em VM existente**.

3. Na lâmina **'Selecção' de máquinas:**
    1. Filtrar a lista de VMs pelo seu grupo **de subscrição** e **recursos.**
    1. Verifique a caixa de verificação de cada máquina virtual que pretende embarcar.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

4. No **perfil de Configuração,** clique **em procurar e altere perfis e preferências.**

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

5. No **perfil de configuração Select + preferences** blade, selecione um perfil no lado esquerdo: *Dev/Test* para teste, *Prod* para produção.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

6. No perfil escolhido, nas **preferências de Configuração** existe um dropdown onde pode ajustar-se a determinados serviços.
    1. Clique **em Criar novas preferências.**
    1. Na lâmina **de preferência de configuração,** preencha o separador Básicos:
        1. Subscrição
        1. Grupo de recursos
        1. Nome de preferência
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

7. Vá ao separador Preferências e ajuste as preferências de configuração desejada.
        
    > [!NOTE]
    > Apenas serão permitidos ajustes que ainda se enquadrem nas nossas melhores práticas, limites superiores e inferiores, quando se alterar as configurações de perfil.

8. Reveja o seu perfil de configuração.
9. Clique no botão **Criar**.

10. Clique no botão **Ativar**.


## <a name="disable-automanage-for-vms"></a>Desativar a auto-mutilação para VMs

Pare rapidamente de utilizar a auto-gestão Azure para máquinas virtuais desativando a auto-gestão.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

1. Vá à página **de boas práticas da máquina virtual Automanage – Azure** que lista todos os seus VMs geridos automaticamente.
1. Selecione a caixa de verificação ao lado da máquina virtual que pretende desativar.
1. Clique no botão **de desativação desativar.**
1. Leia atentamente através das mensagens no pop-up resultante antes de concordar com o **Desactivamento**.


## <a name="clean-up-resources"></a>Limpar os recursos

Se criou um novo grupo de recursos para experimentar a Azure Automanage para máquinas virtuais e já não precisa, pode eliminar o grupo de recursos. A eliminação do grupo também elimina o VM e todos os recursos do grupo de recursos.

A Azure Automanage cria grupos de recursos predefinidos para armazenar recursos em. Consulte os grupos de recursos que têm a convenção de nomeação "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **grupo De Recursos**.
1. Na página para o grupo de recursos, **selecione Delete**.
1. Quando solicitado, confirme o nome do grupo de recursos e, em seguida, **selecione Delete**.


## <a name="next-steps"></a>Passos seguintes 

Obtenha as perguntas mais frequentemente respondidas nas nossas FAQ. 

> [!div class="nextstepaction"]
> [Perguntas Mais Frequentes](faq.md)