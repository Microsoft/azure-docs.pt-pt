---
title: 'Quickstart: Criar um anfitrião bastonário a partir de um VM e ligar através de endereço IP privado'
titleSuffix: Azure Bastion
description: Neste artigo, aprenda a criar um anfitrião Azure Bastion a partir de uma máquina virtual e conecte-se de forma segura usando um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 0ff85e6ceb6867db417d8d8202ff3281478e5687
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996949"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Ligue-se a uma máquina virtual usando um endereço IP privado e Azure Bastion

Este artigo de arranque rápido mostra-lhe como se conectar a uma máquina virtual usando um endereço IP privado através do seu navegador usando Azure Bastion e o portal Azure. Os passos deste artigo ajudam-no a implantar o Bastion na sua rede virtual a partir da sua máquina virtual e, em seguida, a ligar-se ao VM. Uma vantagem de criar um anfitrião Bastion para o seu VNet diretamente do seu VM é que muitas das configurações são pré-voos para si.

Uma vez prestado o serviço, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para mais informações sobre Azure Bastion, veja [o que é Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual.
* Uma máquina virtual Windows na rede virtual.
* As seguintes funções exigidas:
  * Papel do leitor na máquina virtual.
  * Função do leitor no NIC com IP privado da máquina virtual.

* Portas: Para ligar ao VM, tem de ter as seguintes portas abertas no VM:
  * Portas de entrada: RDP (3389)

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Name |  TestVNet1-bastião |
| Rede virtual |  TestVNet1 (baseado no VM) |
| + Nome da sub-rede | AzureBastionSubnet |
| Endereços AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar novo |
| Nome do endereço IP público | VNet1BastionPIP  |
| Endereço IP público SKU |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um hospedeiro de bastião

Quando cria um hospedeiro de bastião no portal Azure utilizando uma máquina virtual existente, várias configurações serão automaticamente padrão para corresponder à sua máquina virtual e/ou rede virtual.

1. Abra o [portal do Azure](https://portal.azure.com). Vá à sua máquina virtual e, em seguida, selecione **Connect**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="configurações de máquinas virtuais" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. A partir do dropdown, **selecione Bastion**.
1. No **TestVM / Ligue a página**, selecione **Use Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="configurações de máquinas virtuais" border="false":::

1. Na página **de Bastion,** preencha os seguintes campos de definições:

   * **Nome**: Nomeie o anfitrião do bastião.
   * **Sub-rede**: A sub-rede dentro da sua rede virtual para a qual o recurso Bastion será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. O nome permite ao Azure saber a que sub-rede implantar o recurso Bastion. Isto é diferente de uma sub-rede de gateway. Utilize uma sub-rede de pelo menos /27 ou maior (/27, /26, /25, e assim por diante).
   
      * **Selecione Gerir a configuração da sub-rede**.
      * Selecione **a AzureBastionSubnet**.
      * Se necessário, ajuste o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Não ajuste quaisquer outras definições. Selecione **OK** para aceitar e guardar as alterações da sub-rede, ou selecione **x** no topo da página se não quiser fazer quaisquer alterações.
1. Clique no botão de trás do seu navegador para navegar de volta para a página **de Bastion** e continuar a especificar valores.
   * **Nome do endereço IP público**: O nome do recurso de endereço IP público.
   * **Endereço IP público**: Este é o IP público do recurso Bastião em que o PDR/SSH será acedido (sobre a porta 443). Criar um novo IP público.
1. Selecione **Criar** para criar o anfitrião do bastião. O Azure valida as suas definições e, em seguida, cria o anfitrião. O hospedeiro e os seus recursos demoram cerca de 5 minutos a criar e a implantar.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="configurações de máquinas virtuais":::

## <a name="connect"></a><a name="connect"></a>Ligar

Depois de Bastion ter sido implantado na rede virtual, o ecrã muda para a página de ligação.

1. Digite o nome de utilizador e a palavra-passe para a sua máquina virtual. Em seguida, selecione **Connect**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="configurações de máquinas virtuais":::
1. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="configurações de máquinas virtuais":::

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal e selecione-o a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o seu grupo de recursos para **TYPE THE RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hospedeiro Bastion para a sua rede virtual e, em seguida, ligado a uma máquina virtual de forma segura através do anfitrião Bastion.

* Para saber mais sobre O Bastião Azure, veja o [Bastião de Azure e](bastion-overview.md) o FaQ do [Bastião.](bastion-faq.md)
* Para ligar a um conjunto de balança de máquina virtual, consulte [Connect a um conjunto de balança de máquina virtual utilizando Azure Bastion](bastion-connect-vm-scale-set.md).