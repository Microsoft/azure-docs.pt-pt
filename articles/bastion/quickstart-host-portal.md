---
title: 'Quickstart: Ligue-se a uma máquina virtual usando um endereço IP privado: Azure Bastion'
description: Neste artigo, aprenda a criar um anfitrião Azure Bastion a partir de uma máquina virtual e conecte-se de forma segura usando um endereço IP privado.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985597"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Ligue-se a uma máquina virtual usando um endereço IP privado e Azure Bastion

Este artigo de arranque rápido mostra-lhe como se conectar a uma máquina virtual usando um endereço IP privado. Quando se conecta através de Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Os passos deste artigo ajudam-no a implantar o Bastion na sua rede virtual através da sua máquina virtual no portal. Uma vez prestado o serviço, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual.

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma rede virtual Azure.
* Uma máquina virtual Azure localizada na rede virtual com porta 3389 aberta.

### <a name="example-values"></a>Valores de exemplo

|**Nome** | **Valor** |
| --- | --- |
| Name |  VNet1Bastion |
| Região | eastus |
| Rede virtual |  VNet1 |
| + Nome da sub-rede | AzureBastionSubnet |
| Endereços AzureBastionSubnet |  10.1.254.0/27 |
| Endereço IP público |  Criar novo |
| Nome do endereço IP público | VNet1BastionPIP  |
| Endereço IP público SKU |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um hospedeiro de bastião

Quando cria um hospedeiro de bastião no portal utilizando uma máquina virtual existente, várias configurações serão automaticamente padrão para corresponder à sua máquina virtual e/ou rede virtual.

1. Abra o [portal do Azure](https://portal.azure.com). Vá à sua máquina virtual e, em seguida, clique em **Connect**.

   ![configurações de máquinas virtuais](./media/quickstart-host-portal/vm-settings.png)
1. A partir do dropdown, **selecione Bastion**.
1. Na página 'Ligar', **selecione Use Bastion**.

   ![selecionar Bastião](./media/quickstart-host-portal/select-bastion.png)

1. Na página de Bastion, preencha os seguintes campos de definições:

   * **Nome**: Nomeie o anfitrião do bastião
   * **Sub-rede**: A sub-rede dentro da sua rede virtual para a qual o recurso Bastion será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. O nome permite ao Azure saber a que sub-rede implantar o recurso Bastion. Isto é diferente de uma sub-rede Gateway. Utilize uma sub-rede de pelo menos /27 ou maior (/27, /26, /25, e assim por diante).
   
      * **Selecione Gerir a configuração da sub-rede**e, em seguida, selecionar **+ Sub-rede**.
      * Na página de sub-rede Adicionar, **digite AzureBastionSubnet**.
      * Especifique o intervalo de endereços na notação CIDR. Por exemplo, 10.1.254.0/27.
      * Selecione **OK** para criar a sub-rede. No topo da página, volte a Bastion para completar o resto das definições.

         ![navegar para definições de bastião](./media/quickstart-host-portal/navigate-bastion.png)
   * **Endereço IP público**: Este é o IP público do recurso Bastião em que o PDR/SSH será acedido (sobre a porta 443). Crie um novo IP público, ou use um existente. O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar.
   * **Nome do endereço IP público**: O nome do recurso de endereço IP público.
1. No ecrã de validação, clique em **Criar**. Aguarde cerca de 5 minutos para que o recurso Bastion crie e implemente.

   ![criar hospedeiro de bastião](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Ligar

Depois de Bastion ter sido implantado na rede virtual, o ecrã muda para a página de ligação.

1. Digite o nome de utilizador e a palavra-passe para a sua máquina virtual. Em seguida, selecione **Connect**.

   ![A screenshot mostra o Connect usando o diálogo Azure Bastion, o que lhe pede um nome de utilizador e palavra-passe.](./media/quickstart-host-portal/connect.png)
1. A ligação RDP a esta máquina virtual via Bastion abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   ![Ligação RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza *o TestRG1* na caixa **de Busca** no topo do portal e selecione **TestRG1** a partir dos resultados da pesquisa.

2. Selecione **Eliminar grupo de recursos**.

3. Introduza *o TestRG1* para **o TIPO O NOME DO GRUPO DE RECURSOS** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hospedeiro Bastion para a sua rede virtual e, em seguida, ligado a uma máquina virtual de forma segura através do anfitrião Bastion.

* Para saber mais sobre O Bastião de Azure, leia a [Visão Geral](bastion-overview.md) do Bastião e as FAQ do [Bastião.](bastion-faq.md)
* Para utilizar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Work with NSGs](bastion-nsg.md).
* Para obter instruções que contenham explicações sobre as definições do anfitrião Azure Bastion, consulte o [Tutorial](bastion-create-host-portal.md).
* Para ligar a um conjunto de balança de máquina virtual, consulte [Connect a um conjunto de balança de máquina virtual utilizando Azure Bastion](bastion-connect-vm-scale-set.md).