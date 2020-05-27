---
title: Tutorial - Saiba como aceder à sua nuvem privada
description: Saiba como aceder a uma nuvem privada Azure VMware Solution (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 332b1b01f05ca97b03264201384ee38ac266470f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873608"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Tutorial: Saiba como aceder a uma nuvem privada Azure VMware Solution (AVS)

Durante a pré-visualização, o AVS não lhe permite gerir a sua nuvem privada com o seu vCenter no local. Você precisará realizar configuração adicional e conexão a uma instância vCenter local através de uma caixa de salto. 

Neste tutorial, cria uma máquina virtual Windows para uma caixa de salto no grupo de recursos que criou no tutorial anterior [Tutorial: Configure a rede para a sua nuvem privada VMware em Azure](tutorial-configure-networking.md) e assine no vCenter. Este é um VM na mesma rede virtual que criou e fornece acesso ao vCenter e ao NSX Manager. 

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Criar uma máquina virtual windows para usar para ligar ao vCenter
> * Login para vCenter da sua máquina virtual

## <a name="create-a-new-windows-virtual-machine"></a>Criar uma nova máquina virtual do Windows

No grupo de recursos, selecione **+ Adicione** e, em seguida, procure e selecione microsoft **Windows 10**, e, em seguida, clique em **Criar**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Adicione um novo VM do Windows 10 para uma caixa de salto" border="true":::

Introduza as informações necessárias nos campos e, em seguida, selecione **Rever + criar**. Para mais informações sobre os campos, consulte a tabela seguinte.

| Campo | Valor |
| --- | --- |
| **Subscrição** | Este valor já está povoado com a Subscrição a que o Grupo de Recursos pertence. |
| **Grupo de recursos** | Este valor já está povoado para o atual Grupo de Recursos. Este deve ser o Grupo de Recursos que criou num tutorial anterior. |
| **Nome da máquina virtual** | Introduza um nome único para o VM. |
| **Região** | Selecione a localização geográfica do VM. |
| **Opções de disponibilidade** | Deixe o valor predefinido selecionado. |
| **Imagem** | Selecione a imagem VM. |
| **Tamanho** | Deixe o valor do tamanho padrão. |
| **Tipo de autenticação**  | Selecione **Password**. |
| **Nome de utilizador** | Introduza o nome de utilizador para iniciar sessão no VM. |
| **Palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
| **Confirmar palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
| **Portos de entrada pública** | selecione **Nenhuma**. Se selecionar Nenhum, só pode utilizar o acesso do [JIT](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) para controlar o acesso ao VM quando quiser aceder ao mesmo.  |

Depois de ter introduzido as informações apropriadas, clique em **Rever + criar**. Uma vez que a validação passe, selecione **Criar** para iniciar o processo de criação de máquina virtual.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Crie um novo VM Windows 10 para uma caixa de salto" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Ligue-se ao vCenter local da sua nuvem privada

Desde o sinal de caixa de salto até ao cliente vSphere com VMware vCenter SSO. Inscreva-se no Cliente vSphere usando um nome de utilizador de administrador de nuvem; aceitar o risco de segurança e continuar quando vir um aviso sobre um potencial risco de segurança; Inscreva-se no VMware vCenter com credenciais de sinal único e verifique se a interface do utilizador exibe com sucesso.

No portal Azure, selecione a sua nuvem privada e, em seguida, na visão **geral,** selecione **Identidade > Padrão**. Os URLs e credenciais de login para o vCenter de nuvem privada e o gestor NSX-T são apresentados.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Mostrar vCenter de nuvem privada e URLs e credenciais do NSX Manager" border="true":::

Navegue para a máquina virtual que criou no passo anterior e ligue-se à máquina virtual. Para dar passos detalhados sobre como se conectar à máquina virtual, consulte [a ligação a uma máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

No Windows VM, abra um navegador e navegue para os URLs de Manger vCenter e NSX-T em dois separadores. No separador vCenter, introduza as `cloudadmin@vmcp.local` credenciais do utilizador a partir do passo anterior.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Inscreva-se no vCenter de nuvem privada" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vPortal vCenter" border="true":::

No segundo separador do navegador, inscreva-se no gestor NSX-T.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Nuvem privada local NSX Manger casa" border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma máquina virtual windows para usar para ligar ao vCenter
> * Login para vCenter da sua máquina virtual

Continue para o próximo tutorial para aprender a escalar a sua nuvem privada AVS.

> [!div class="nextstepaction"]
> [Escala rés em nuvem privada AVS](tutorial-scale-private-cloud.md)
