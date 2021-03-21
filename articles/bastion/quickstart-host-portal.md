---
title: 'Quickstart: Configurar Azure Bastion e ligar a um VM através de endereço IP privado e um browser'
titleSuffix: Azure Bastion
description: Neste artigo de arranque rápido, aprenda a criar um anfitrião Azure Bastion a partir de uma máquina virtual e conecte-se ao VM de forma segura através do seu navegador através de um endereço IP privado.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 8aeba13954283ca35c3eb0060a0e588ba6a7adbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101707182"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Quickstart: Conecte-se a um VM de forma segura através de um browser através de um endereço IP privado

Pode ligar-se a uma máquina virtual (VM) através do seu navegador utilizando o portal Azure e o Azure Bastion. Este artigo de arranque rápido mostra-lhe como configurar o Azure Bastion com base nas definições de VM e, em seguida, ligar-se ao seu VM através do portal. O VM não precisa de um endereço IP público, software de cliente, agente ou uma configuração especial. Uma vez prestado o serviço, a experiência RDP/SSH está disponível para todas as máquinas virtuais na mesma rede virtual. Para mais informações sobre Azure Bastion, veja [o que é Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. Se não tiver um, [crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Para poder ligar-se a um VM através do seu navegador usando o Bastion, tem de conseguir iniciar sação no portal Azure.

* Uma máquina virtual Windows numa rede virtual. Se não tiver um VM, crie um usando [Quickstart: Crie um VM](../virtual-machines/windows/quick-create-portal.md).

  * Se necessitar de valores de exemplo, consulte os [valores de exemplo](#values)fornecidos .
  * Se já tiver uma rede virtual, certifique-se de selecioná-la no separador 'Rede' quando criar o seu VM.
  * Se ainda não tiver uma rede virtual, pode criar uma ao mesmo tempo que cria o seu VM.
  * Não precisa de ter um endereço IP público para este VM para se ligar através do Azure Bastion.

* Funções VM necessárias:
  * Papel do leitor na máquina virtual.
  * Função do leitor no NIC com IP privado da máquina virtual.
  
* Portas VM necessárias:
  * Portas de entrada: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Valores de exemplo

Pode utilizar os seguintes valores de exemplo ao criar esta configuração, ou pode substituir o seu próprio.

**Valores básicos de VNet e VM:**

|**Nome** | **Valor** |
| --- | --- |
| Máquina virtual| TestVM |
| Grupo de recursos | TestRG1 |
| Region | E.U.A. Leste |
| Rede virtual | VNet1 |
| Espaço de endereços | 10.1.0.0/16 |
| Sub-redes | FrontEnd: 10.1.0.0/24 |

**Valores do Bastião Azure:**

|**Nome** | **Valor** |
| --- | --- |
| Name | VNet1-bastião |
| + Nome da sub-rede | AzureBastionSubnet |
| Endereços AzureBastionSubnet | Uma sub-rede dentro do espaço do endereço VNet com uma máscara de sub-rede /27. Por exemplo, 10.1.1.0/27.  |
| Endereço IP público |  Criar novo |
| Nome do endereço IP público | VNet1-ip  |
| Endereço IP público SKU |  Standard  |
| Atribuição  | Estático |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Criar um hospedeiro de bastião

Há algumas maneiras diferentes de configurar um hospedeiro de bastião. Nos passos seguintes, irá criar um hospedeiro de bastião no portal Azure diretamente a partir do seu VM. Quando cria um hospedeiro a partir de um VM, várias configurações povoar-se-ão automaticamente correspondentes à sua máquina virtual e/ou rede virtual.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o VM a que pretende ligar e, em seguida, selecione **Connect**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Screenshot das definições de máquina virtual." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. A partir do dropdown, **selecione Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Screenshot do bastão dropdown." lightbox="./media/quickstart-host-portal/bastion.png":::
1. No **testVM | Ligue a página**, selecione **Use Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Screenshot de Use Bastion.":::

1. Na página 'Ligar' utilizando a página **Azure Bastion,** configure os valores.

   * **Passo 1:** Os valores são pré-povoados porque está a criar o hospedeiro de bastião diretamente a partir do seu VM.

   * **Passo 2:** O espaço do endereço é pré-povoado com um espaço de endereço sugerido. O AzureBastionSubnet deve ter um espaço de endereço de /27 ou maior (/26, /25, etc.).

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Screenshot de criar a sub-rede Bastion.":::

1. Clique **em Criar Sub-rede** para criar a AzureBastionSubnet.
1. Após a criação da sub-rede, a página avança automaticamente para o **Passo 3**. Para o passo 3, utilize os seguintes valores:

   * **Nome:** Diga o nome do anfitrião do bastião.
   * **Endereço IP público:** selecione **Criar novo**.
   * **Nome do endereço IP público:** O nome do recurso de endereço IP público.
   * **Endereço IP público SKU:** Pré-configurado como **Standard**
   * **Atribuição:** Pré-configurado para **Estática.** Não podes usar uma missão dinâmica para o Azure Bastion.
   * **Grupo de recursos**: O mesmo grupo de recursos que o VM.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Screenshot do passo 3.":::
1. Depois de completar os valores, **selecione Criar Bastião Azure utilizando predefinições**. O Azure valida as suas definições e, em seguida, cria o anfitrião. O hospedeiro e os seus recursos demoram cerca de 5 minutos a criar e a implantar.

## <a name="connect"></a><a name="connect"></a>Ligar

Depois de Bastion ter sido implantado na rede virtual, o ecrã muda para a página de ligação.

1. Digite o nome de utilizador e a palavra-passe para a sua máquina virtual. Em seguida, selecione **Connect**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="A screenshot mostra o Connect utilizando o diálogo Azure Bastion.":::
1. A ligação RDP a esta máquina virtual abrir-se-á diretamente no portal Azure (sobre HTML5) utilizando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Ligação RDP":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar a rede virtual e as máquinas virtuais, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o nome do seu grupo de recursos na caixa **de Pesquisa** no topo do portal e selecione-o a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o seu grupo de recursos para **TYPE THE RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um hospedeiro de bastião para a sua rede virtual e, em seguida, ligado a uma máquina virtual de forma segura através de Bastion. Em seguida, pode continuar com o passo seguinte se quiser ligar-se a um conjunto de balanças de máquina virtual.

> [!div class="nextstepaction"]
> [Ligue-se a um conjunto de escala de máquina virtual usando Azure Bastion](bastion-connect-vm-scale-set.md)
