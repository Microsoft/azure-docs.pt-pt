---
title: 'Crie um anfitrião do Bastião Azure: portal'
description: Neste artigo, aprenda a criar um anfitrião do Azure Bastion usando o portal
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366148"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Crie um anfitrião do Bastião Azure usando o portal

Este artigo mostra-lhe como criar um anfitrião do Azure Bastion usando o portal Azure. Uma vez que você disponibiliza o serviço Azure Bastion na sua rede virtual, a experiência RDP/SSH sem emenda está disponível para todos os VMs na mesma rede virtual. A implantação do Azure Bastion é por rede virtual, não por subscrição/conta ou máquina virtual.

Pode criar um novo recurso de anfitrião de bastião no portal, quer especificando manualmente todas as definições, quer utilizando as definições que correspondem a um VM existente. Para criar um anfitrião de bastião utilizando as definições de VM, consulte o artigo [quickstart.](quickstart-host-portal.md) Opcionalmente, pode utilizar o [Azure PowerShell](bastion-create-host-powershell.md) para criar um anfitrião do Azure Bastion.

## <a name="before-you-begin"></a>Antes de começar

A Bastião está disponível nas seguintes regiões públicas de Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Criar um anfitrião de bastião

Esta secção ajuda-o a criar um novo recurso Azure Bastion a partir do portal Azure.

1. No menu do [portal Azure](https://portal.azure.com) ou na página **Inicial,** selecione **Criar um recurso**.

1. Na página **Nova,** no campo *Search the Marketplace,* digite **Bastion,** em seguida, clique **em Entrar** para obter os resultados da pesquisa.

1. A partir dos resultados, clique em **Bastion**. Certifique-se de que a editora é *a Microsoft* e a categoria é *Networking*.

1. Na página **bastião,** clique **em Criar** para abrir a página **Criar um bastião.**

1. Na página **Criar um bastião,** configure um novo recurso Bastião. Especifique as definições de configuração para o seu recurso Bastion.

    ![criar um bastião](./media/bastion-create-host-portal/settings.png)

    * **Subscrição**: A subscrição Azure que pretende utilizar para criar um novo recurso Bastião.
    * **Grupo de Recursos**: O grupo de recursos Azure no qual será criado o novo recurso Bastion. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome**: O nome do novo recurso Bastião
    * **Região**: A região pública de Azure onde o recurso será criado.
    * **Rede virtual**: A rede virtual em que o recurso Bastião será criado. Pode criar uma nova rede virtual no portal durante este processo, ou utilizar uma rede virtual existente. Se estiver a utilizar uma rede virtual existente, certifique-se de que a rede virtual existente tem espaço de endereço gratuito suficiente para acomodar os requisitos da subnet bastião.
    * **Subnet**: A subrede na sua rede virtual para a qual será implantado o novo recurso de hospedeiro bastião. Deve criar uma sub-rede utilizando o valor de nome **AzureBastionSubnet**. Este valor permite ao Azure saber para que sub-rede implantar os recursos da Bastião. Isto é diferente de uma sub-rede gateway. Deve utilizar uma sub-rede de pelo menos /27 ou maior (/27, /26, e assim por diante).
    
       Crie a **AzureBastionSubnet** sem tabelas ou delegações de rotas. Se utilizar grupos de segurança de rede na **AzureBastionSubnet,** consulte o artigo [Trabalho com NSGs.](bastion-nsg.md)
    * **Endereço IP público**: O IP público do recurso Bastião em que o RDP/SSH será acedido (sobre o porto 443). Crie um novo IP público, ou use um existente. O endereço IP público deve estar na mesma região que o recurso Bastião que está a criar.
    * **Nome de endereço IP público**: O nome do endereço IP público.
    * **Endereço IP público SKU**: Esta definição é pré-povoada por padrão para **a Norma**. O Azure Bastion utiliza/suporta apenas o IP Público Padrão SKU.
    * **Atribuição**: Esta definição é prépovoada por padrão à **Estática**.

1. Quando terminar de especificar as definições, clique em **Rever + Criar**. Isto valida os valores. Uma vez que a validação passe, você pode iniciar o processo de criação.
1. Na página **Criar um bastião,** clique em **Criar**.
1. Verá uma mensagem a dizer-lhe que a sua implantação está em andamento. O estado será exibido nesta página à medida que os recursos forem criados. Leva cerca de 5 minutos para que o recurso Bastião seja criado e implantado.

## <a name="next-steps"></a>Passos seguintes

* Leia o [Bastião FAQ](bastion-faq.md) para obter informações adicionais.

* Para utilizar grupos de segurança de rede com a subnet Azure Bastion, consulte [Trabalhar com NSGs](bastion-nsg.md).