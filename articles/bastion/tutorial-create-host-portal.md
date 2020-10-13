---
title: 'Criar um anfitrião Azure Bastion: Windows VM: portal'
description: Neste artigo, aprende-se a criar um hospedeiro Azure Bastion e a ligar-se a um Windows VM.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/09/2020
ms.author: cherylmc
ms.openlocfilehash: e4f8bc38a79fdbb84624e126c84d679a906a701b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91980172"
---
# <a name="tutorial-create-an-azure-bastion-host-and-connect-to-a-windows-vm"></a>Tutorial: Criar um hospedeiro Azure Bastion e ligar-se a um VM do Windows

Pode ligar-se a uma máquina virtual do Windows (VM) através de um browser através do portal Azure. Conecte-se ao seu VM diretamente através do navegador através do TLS usando um endereço IP privado. Não precisa de instalar e configurar o software do cliente localmente.

Para se ligar a um VM utilizando o Bastion, é necessário providenciar o serviço Azure Bastion na sua rede virtual (VNet). Uma vez prestado o serviço, este tipo de ligação está disponível para todos os VMs que se encontram no VNet, dependendo da configuração de segurança do VM. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Crie um anfitrião de bastião para o seu VNet
> * Ligar a uma máquina virtual do Windows

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma rede virtual.
* Uma máquina virtual Windows na rede virtual.
* As seguintes funções exigidas:
  * Papel do leitor na máquina virtual.
  * Função do leitor no NIC com IP privado da máquina virtual.
  * Papel do leitor no recurso Azure Bastion.

* Portas: Para ligar ao Windows VM, tem de ter as seguintes portas abertas no seu Windows VM:
  * Portas de entrada: RDP (3389)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um hospedeiro de bastião

Esta secção ajuda-o a criar o objeto de bastião no seu VNet. Isto é necessário para criar uma ligação segura a um VM no VNet.

1. A partir da página **Inicial,** selecione **+ Criar um recurso.**
1. Na página **Nova,** na caixa 'Procurar', **escreva Bastion,** selecione **Enter** para obter os resultados da pesquisa. Sobre o resultado para **Bastion,** verifique se a editora é a Microsoft.
1. Selecione **Criar**.
1. Na página **Criar um Bastião,** configurar um novo recurso Bastonário.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-basics.png" alt-text="Criar um anfitrião do Bastion" lightbox="./media/tutorial-create-host-portal/bastion-basics.png":::

    * **Subscrição**: A subscrição Azure que pretende utilizar para criar um novo recurso Bastion.
    * **Grupo de Recursos**: O grupo de recursos Azure no qual será criado o novo recurso Bastion. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome**: O nome do novo recurso Bastion.
    * **Região**: Região Azure que o recurso será criado.
    * **Rede virtual**: A rede virtual na qual o recurso Bastion será criado. Pode criar uma nova rede virtual no portal durante este processo ou utilizar uma rede virtual existente. Se estiver a utilizar uma rede virtual existente, certifique-se de que a rede virtual existente dispõe de espaço de endereço gratuito suficiente para acomodar os requisitos da sub-rede Bastion. Se não vir a sua rede virtual a partir do dropdown, certifique-se de que selecionou o Grupo de Recursos correto.
    * **Sub-rede**: Uma vez que crie ou selecione uma rede virtual, aparecerá o campo da sub-rede. A sub-rede da sua rede virtual onde o novo anfitrião bastion será implantado. A sub-rede será dedicada ao anfitrião do Bastião. **Selecione Gerir a configuração da sub-rede** e criar a sub-rede Azure Bastion. Selecione **+Subnet** e crie uma sub-rede utilizando as seguintes diretrizes:

         * A sub-rede deve ser denominada **AzureBastionSubnet**.
         * A sub-rede deve ser pelo menos /27 ou maior.

      Não precisas de preencher campos adicionais. Selecione **OK** e, em seguida, no topo da página, selecione **Criar um Bastião** para voltar à página de configuração de Bastion.
    * **Endereço IP público**: O IP público do recurso Bastião no qual o PDR/SSH será acedido (sobre a porta 443). Criar um novo IP público. O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar. Este endereço IP não tem nada a ver com nenhum dos VMs a que pretende ligar. É o IP público para o recurso de hospedeiro basso.
    * **Nome do endereço IP público**: O nome do recurso de endereço IP público. Para este tutorial, pode deixar o padrão.
    * **Endereço IP público SKU**: Esta definição é pré-uto por defeito ao **Standard**. Azure Bastion utiliza/suporta apenas o SKU IP PÚBLICO Padrão.
    * **Atribuição**: Esta definição é pré-uto por defeito à **Estática**.

1. Quando terminar de especificar as definições, selecione **Review + Create**. Isto valida os valores. Uma vez que a validação passe, pode criar o recurso Bastion.
1. Selecione **Criar**.
1. Verá uma mensagem a dizer-lhe que a sua implantação está em curso. O estado será exibido nesta página à medida que os recursos forem criados. Leva cerca de 5 minutos para o recurso Bastion ser criado e implantado.

## <a name="connect-to-a-vm"></a>Ligar a uma VM

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se não continuar a utilizar esta aplicação, elimine os seus recursos utilizando os seguintes passos:

1. Insira o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal. Quando vir o seu grupo de recursos nos resultados da pesquisa, selecione-o.
1. Selecione **Eliminar grupo de recursos**.
1. Introduza o nome do seu grupo de recursos para **TYPE THE RESOURCE GROUP NAME:** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um Bastion Host e associou-o a uma rede virtual, e ligou-se a um Windows VM. Pode optar por utilizar grupos de segurança de rede com a sua sub-rede Azure Bastion. Para tal, consulte [Work with NSGs](bastion-nsg.md).
