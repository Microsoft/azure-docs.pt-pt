---
title: Tutorial - Saiba como aceder à sua nuvem privada
description: Saiba como aceder a uma nuvem privada Azure VMware Solution
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750533"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>Tutorial: Saiba como aceder a uma nuvem privada Azure VMware Solution

Durante a pré-visualização, a Azure VMware Solution não lhe permite gerir a sua nuvem privada com o seu vCenter no local. Você precisará realizar configuração e conexão adicionais a um vCenter local através de uma caixa de salto. 

Neste tutorial, cria uma máquina virtual Windows para uma caixa de salto no grupo de recursos que criou no [tutorial anterior: Configurar a rede para a sua nuvem privada VMware em Azure](tutorial-configure-networking.md) e assinar no vCenter. Este é um VM na mesma rede virtual que criou e fornece acesso ao vCenter e ao NSX Manager. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma máquina virtual windows para usar para ligar ao vCenter
> * Faça login no vCenter a partir da sua máquina virtual

## <a name="create-a-new-windows-virtual-machine"></a>Criar uma nova máquina virtual do Windows

1. No grupo de recursos, **selecione + Adicione** em seguida procurar e selecionar o Microsoft Windows **10**e, em seguida, clique em **Criar**.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Adicione um novo Windows 10 VM para uma caixa de salto." border="true":::

1. Introduza as informações necessárias nos campos e, em seguida, **selecione Review + create**. Para mais informações sobre os campos, consulte a tabela seguinte.

   | Campo | Valor |
   | --- | --- |
   | **Subscrição** | Este valor já está preenchido com a Subscrição a que pertence o Grupo de Recursos. |
   | **Grupo de recursos** | Este valor já está preenchido para o grupo de recursos atual. Este deve ser o Grupo de Recursos que criou num tutorial anterior. |
   | **Nome da máquina virtual** | Insira um nome único para o VM. |
   | **Região** | Selecione a localização geográfica do VM. |
   | **Opções de disponibilidade** | Deixe selecionado o valor predefinido. |
   | **Imagem** | Selecione a imagem VM. |
   | **Tamanho** | Deixe o valor de tamanho padrão. |
   | **Tipo de autenticação**  | Selecione **palavra-passe**. |
   | **Nome de Utilizador** | Introduza o nome de utilizador para iniciar sessão no VM. |
   | **Palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
   | **Confirmar palavra-passe** | Introduza a palavra-passe para iniciar sessão no VM. |
   | **Portas de entrada públicas** | Selecione **Nenhuma**. Se selecionar Nenhum, pode utilizar [o acesso JIT](../security-center/security-center-just-in-time.md#jit-configure) para controlar o acesso ao VM apenas quando pretende aceder ao mesmo.  |

1. Depois de ter introduzido as informações apropriadas, clique em **Rever + criar**. 
1. Assim que a validação passar, **selecione Criar** para iniciar o processo de criação de máquinas virtuais.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Crie um novo Windows 10 VM para uma caixa de salto." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Ligue-se ao vCenter local da sua nuvem privada

1. Desde o login da caixa de salto para o vSphere Client com VMware vCenter SSO. iniciar serção no vSphere Client usando um nome de utilizador de administrador de nuvem; aceitar o risco de segurança e continuar quando vir um aviso sobre um risco potencial de segurança; iniciar sposição no VMware vCenter com credenciais sign-on únicas e verificar se a interface do utilizador apresenta com sucesso.

1. No portal Azure, selecione a sua nuvem privada e, em seguida, na **vista geral,** selecione **Identidade > Padrão**. São apresentados os URLs e credenciais de login para o vCenter de nuvem privada e o gestor NSX-T.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Exiba urls e credenciais de cloud vCenter e NSX Manager." border="true":::

1. Navegue para a máquina virtual que criou no passo anterior e ligue-se à máquina virtual. Para passos detalhados sobre como ligar à máquina virtual, consulte [a ligação a uma máquina virtual](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

1. No Windows VM, abra um navegador e navegue para os URLs vCenter e NSX-T Manger em dois separadores. No separador vCenter, introduza `cloudadmin@vmcp.local` as credenciais de utilizador do passo anterior.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Inscreva-se na nuvem privada vCenter." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="portal vCenter." border="true":::

1. No segundo separador do navegador, inscreva-se no gestor NSX-T.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="No segundo separador do navegador, inscreva-se no gestor NSX-T." border="true":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma máquina virtual windows para usar para ligar ao vCenter
> * Faça login no vCenter a partir da sua máquina virtual

Continue até ao próximo tutorial para aprender a escalar a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Escalar uma nuvem privada Azure VMware Solution](tutorial-scale-private-cloud.md)
