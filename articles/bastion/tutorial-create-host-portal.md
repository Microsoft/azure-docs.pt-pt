---
title: 'Tutorial: Criar um anfitrião Azure Bastion: Windows VM: portal'
description: Neste artigo, aprende-se a criar um hospedeiro Azure Bastion e a ligar-se a um Windows VM.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 54aca389ea56594b004212c1479d5cc2568a2527
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560882"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm-through-a-browser"></a>Tutorial: Configurar Bastião e ligar-se a um Windows VM através de um browser

Este tutorial mostra-lhe como se conectar a uma máquina virtual através do seu navegador usando Azure Bastion e o portal Azure. No portal Azure, implementa Bastion para a sua rede virtual. Depois de implementar o Bastion, liga-se a um VM através do seu endereço IP privado utilizando o portal Azure. O seu VM não necessita de um endereço IP público ou de um software especial. Uma vez prestado o serviço, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para mais informações sobre Azure Bastion, veja [o que é Azure Bastion?](bastion-overview.md)

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

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine os seus recursos utilizando os seguintes passos:

1. Insira o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal. Quando vir o seu grupo de recursos nos resultados da pesquisa, selecione-o.
1. Selecione **Eliminar grupo de recursos**.
1. Introduza o nome do seu grupo de recursos para **TYPE THE RESOURCE GROUP NAME:** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um hospedeiro Bastion e associou-o a uma rede virtual, ligando-o depois a um Windows VM. Pode optar por utilizar grupos de segurança de rede com a sua sub-rede Azure Bastion. Para tal, consulte:

> [!div class="nextstepaction"]
> [Trabalhar com NSGs](bastion-nsg.md)
