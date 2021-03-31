---
title: Crie uma preferência personalizada em Azure Automanage para VMs
description: Aprenda a ajustar a configuração do ambiente em Azure Automanage e desajuste as suas preferências.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 584a3503bf736fcf727a169611e6c79e0c374c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647971"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Crie uma preferência personalizada em Azure Automanage para VMs

A azure Automanagem para as melhores práticas da máquina virtual tem ambientes padrão que podem ser ajustados se necessário. Este artigo explicará como pode definir as suas próprias preferências quando ativa a auto-gestão num VM novo ou existente.

Atualmente, apoiamos personalizações no [Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [no Microsoft Antimalware.](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration)


> [!NOTE]
> Não é possível alterar o ambiente ou a preferência no seu VM enquanto a automanagem estiver ativada. Terá de desativar a auto-mutilação para esse VM e, em seguida, voltar a ativar a Automanagem com o ambiente e preferências de configuração pretendidos.


## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de teste gratuitas não têm acesso às máquinas virtuais utilizadas neste tutorial. Por favor, atualize para uma subscrição Pay-As-You-Go.

> [!IMPORTANT]
> É necessária a seguinte permissão Azure RBAC para ativar a autoadministração: função **do proprietário** ou **colaborador,** juntamente com as funções **de Administrador de Acesso ao Utilizador.**


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Ativar a auto-mutilação dos VMs num VM existente

1. Na barra de pesquisa, procure e selecione **Automanage – Azure machine best practices**.

2. **Selecione a Ativação em VM existente**.

3. Na lâmina **'Selecção' de máquinas:**
    1. Filtrar a lista de VMs pelo seu grupo **de subscrição** e **recursos.**
    1. Verifique a caixa de verificação de cada máquina virtual que pretende embarcar.
    1. Clique no botão **Seleção.**

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selecione vM existente da lista de VMs disponíveis.":::

    > [!NOTE]
    > Clique no **Show de máquinas não elegíveis** para ver a lista de máquinas não suportadas e o raciocínio. 

4. Em **Configuração**, clique em **Comparar Ambientes**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Compare Ambientes.":::

5. No **Ambiente detalha** a lâmina, selecione um ambiente do menu drop down: *Dev/Test* para testes, *Prod* para produção e clique **em OK**

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Navegue pelo ambiente de produção.":::

6. Depois de selecionar o seu ambiente, pode selecionar **preferências de Configuração**. Por padrão, será utilizada a preferência Azure Best Practices. Esta preferência contém as definições recomendadas para cada serviço. Modifique estas definições criando uma preferência personalizada: 
    1. Clique **em Criar novas preferências.**
    1. Na lâmina **de preferência de configuração,** preencha o separador Básicos:
        1. Subscrição
        1. Grupo de recursos
        1. Nome de preferência
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Preencha as preferências de configuração.":::

7. Ajuste as preferências de configuração desejada.
        
    > [!NOTE]
    > Apenas serão permitidos ajustes que ainda se enquadrem nas nossas melhores práticas, limites superiores e inferiores, quando se alteram as configurações do ambiente.

8. Reveja os seus dados de configuração.
9. Clique no botão **Criar**.

10. Clique no botão **Ativar**.


## <a name="disable-automanage-for-vms"></a>Desativar a auto-mutilação para VMs

Pare rapidamente de utilizar a auto-gestão Azure para máquinas virtuais desativando a auto-gestão.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Desativar a auto-produção numa máquina virtual.":::

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