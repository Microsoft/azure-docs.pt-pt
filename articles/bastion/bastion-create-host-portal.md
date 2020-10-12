---
title: 'Criar um anfitrião Azure Bastion: portal'
description: Neste artigo, aprenda a criar um anfitrião Azure Bastion, que fornece conectividade RDP/SSH a todas as máquinas virtuais numa rede virtual.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88270755"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Criar um anfitrião Azure Bastion usando o portal

Este artigo mostra-lhe como criar um hospedeiro Azure Bastion usando o portal Azure. Uma vez que fornece o serviço Azure Bastion na sua rede virtual, a experiência PDR/SSH sem costura está disponível para todos os VMs na mesma rede virtual. A implementação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Pode criar um novo recurso de anfitrião de bastião no portal, especificando manualmente todas as definições, quer utilizando as definições que correspondem a um VM existente. Para criar um hospedeiro de bastião utilizando as definições de VM, consulte o artigo [de arranque rápido.](quickstart-host-portal.md) Opcionalmente, pode utilizar [a Azure PowerShell](bastion-create-host-powershell.md) para criar um hospedeiro Azure Bastion.

## <a name="before-you-begin"></a>Antes de começar

O bastião está disponível nas seguintes regiões públicas do Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um hospedeiro de bastião

Esta secção ajuda-o a criar um novo recurso Azure Bastion a partir do portal Azure.

1. No menu do [portal do Azure](https://portal.azure.com) ou a partir da **Home page**, selecione **Criar um recurso**.

1. Na página **Nova,** no campo *Procurar o Mercado,* **digitar Bastion,** clique em **Entrar** para obter os resultados da pesquisa.

1. A partir dos resultados, clique em **Bastion.** Certifique-se de que o editor é a *Microsoft* e a categoria é *Networking*.

1. Na página **Bastion,** clique em **Criar** para abrir a página **de bastião** Criar.

1. Na página **Criar um bastião,** configurar um novo recurso Bastonário. Especifique as definições de configuração para o seu recurso Bastion.

    ![criar um bastião](./media/bastion-create-host-portal/settings.png)

    * **Subscrição**: A subscrição Azure que pretende utilizar para criar um novo recurso Bastion.
    * **Grupo de Recursos**: O grupo de recursos Azure no qual o novo recurso Bastion será criado. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome**: O nome do novo recurso Bastion
    * **Região**: Região Azure que o recurso será criado.
    * **Rede virtual**: A rede virtual na qual o recurso Bastion será criado. Pode criar uma nova rede virtual no portal durante este processo ou utilizar uma rede virtual existente. Se estiver a utilizar uma rede virtual existente, certifique-se de que a rede virtual existente dispõe de espaço de endereço gratuito suficiente para acomodar os requisitos da sub-rede Bastion.
    * **Sub-rede**: A sub-rede da sua rede virtual onde será implantado o novo anfitrião Bastion. A sub-rede será dedicada ao anfitrião do Bastião e deve ser nomeada como **AzureBastionSubnet**. Esta sub-rede deve ser pelo menos /27 ou maior.
    
       **A AzureBastionSubnet** não suporta [rotas definidas pelo utilizador,](../virtual-network/virtual-networks-udr-overview.md#custom-routes)mas suporta [grupos de segurança de rede.](bastion-nsg.md)
    * **Endereço IP público**: O IP público do recurso Bastião no qual o PDR/SSH será acedido (sobre a porta 443). Crie um novo IP público, ou use um existente. O endereço IP público deve estar na mesma região que o recurso Bastion que está a criar.
    * **Nome do endereço IP público**: O nome do recurso de endereço IP público.
    * **Endereço IP público SKU**: Esta definição é pré-uto por defeito ao **Standard**. Azure Bastion utiliza/suporta apenas o SKU IP PÚBLICO Padrão.
    * **Atribuição**: Esta definição é pré-uto por defeito à **Estática**.

1. Quando terminar de especificar as definições, clique em **'Rever + Criar' (Revisão)** Isto valida os valores. Uma vez que a validação passe, você pode iniciar o processo de criação.
1. Na página **Criar um bastião,** clique em **Criar**.
1. Verá uma mensagem a dizer-lhe que a sua implantação está em curso. O estado será exibido nesta página à medida que os recursos forem criados. Leva cerca de 5 minutos para o recurso Bastion ser criado e implantado.

## <a name="next-steps"></a>Passos seguintes

* Leia as [FAQ de Bastião](bastion-faq.md) para obter informações adicionais.

* Para utilizar grupos de segurança de rede com a sub-rede Azure Bastion, consulte [Work with NSGs](bastion-nsg.md).
