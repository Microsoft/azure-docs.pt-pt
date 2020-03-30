---
title: 'Quickstart: Ligue-se a uma máquina virtual usando um endereço IP privado: Azure Bastion'
description: Neste artigo, aprenda a criar um anfitrião do Azure Bastion a partir de uma máquina virtual e conecte-se de forma segura usando um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 0b4f0a1fa4f9cce05aa544bb24e045209a6d24ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137463"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Ligue-se a uma máquina virtual usando um endereço IP privado e bastião Azure

Este artigo de arranque rápido mostra-lhe como se conectar a uma máquina virtual usando um endereço IP privado. Quando se conecta através do Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Os passos neste artigo ajudam-no a implantar a Bastion na sua rede virtual através da sua máquina virtual no portal. Uma vez que o serviço é prestado, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual.

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual Azure.
* Uma máquina virtual Azure localizada na rede virtual com porta 3389 aberta.

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Nome |  VNet1Bastião |
| Região | eastus |
| Rede virtual |  VNet1 |
| + Nome da subnet | AzureBastionSubnet |
| Endereços AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar novo |
| Nome do endereço IP público | VNet1BastionPIP  |
| Endereço IP público SKU |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um anfitrião do Bastion

Quando criar um anfitrião de bastião no portal utilizando uma máquina virtual existente, várias definições serão automaticamente definidas para corresponder à sua máquina virtual e/ou rede virtual.

1. Abra o [portal Azure.](https://portal.azure.com) Vá à sua máquina virtual e clique em **Connect**.

   ![configurações de máquinavirtual](./media/quickstart-host-portal/vm-settings.png)
1. A partir da queda, selecione **Bastion**.
1. Na página Connect, selecione **Use Bastion**.

   ![selecionar Bastião](./media/quickstart-host-portal/select-bastion.png)

1. Na página bastião, preencha os seguintes campos de definições:

   * **Nome**: Nomeie o anfitrião do bastião
   * **Subnet**: A subrede dentro da sua rede virtual para a qual o recurso Bastion será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. O nome permite ao Azure saber para que sub-rede implantar o recurso Bastião. Isto é diferente de uma sub-rede gateway. Utilize uma sub-rede de pelo menos /27 ou maior (/27, /26, /25, e assim por diante).
   
      * Selecione Gerir a **configuração da sub-rede**e, em seguida, selecione **+ Subnet**.
      * Na página add subnet, **digite AzureBastionSubnet**.
      * Especifique o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Selecione **OK** para criar a sub-rede. No topo da página, navegue de volta a Bastion para completar o resto das definições.

         ![navegar para configurações de bastião](./media/quickstart-host-portal/navigate-bastion.png)
   * **Endereço IP público**: Este é o IP público do recurso Bastião em que rdp/SSH será acedido (sobre a porta 443). Crie um novo IP público, ou use um existente. O endereço IP público deve estar na mesma região que o recurso Bastião que está a criar.
   * **Nome de endereço IP público**: O nome do endereço IP público.
1. No ecrã de validação, clique em **Criar**. Aguarde cerca de 5 minutos para que o recurso Bastion crie e desloque.

   ![criar anfitrião bastião](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Ligar

Depois de bastion ter sido implantado na rede virtual, o ecrã muda para a página de ligação.

1. Digite o nome de utilizador e a palavra-passe para a sua máquina virtual. Em seguida, selecione **Connect**.

   ![ligar](./media/quickstart-host-portal/connect.png)
1. A ligação RDP a esta máquina virtual via Bastion abrirá diretamente no portal Azure (mais HTML5) utilizando a porta 443 e o serviço Bastião.

   ![Ligação RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar a utilização da rede virtual e das máquinas virtuais, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o *TestRG1* na caixa **de pesquisa** na parte superior do portal e selecione **TestRG1** a partir dos resultados da pesquisa.

2. Selecione **Eliminar grupo de recursos**.

3. Introduza *o TestRG1* para **ESCREVER O NOME DO GRUPO DE RECURSOS** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um anfitrião bastião para a sua rede virtual e, em seguida, ligado a uma máquina virtual de forma segura através do anfitrião bastião.

* Para saber mais sobre o Bastião Azure, leia a visão geral do [Bastião](bastion-overview.md) e o [Bastião FAQ.](bastion-faq.md)
* Para utilizar grupos de segurança de rede com a subnet Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).
* Para obter instruções que contenham explicações sobre as configurações do anfitrião do Bastião Azure, consulte o [Tutorial](bastion-create-host-portal.md).
* Para se ligar a um conjunto de escala de máquina virtual, consulte A Ligação a um conjunto de escala de [máquina virtual utilizando o Bastião Azure](bastion-connect-vm-scale-set.md).