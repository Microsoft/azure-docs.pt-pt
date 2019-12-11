---
title: Criar um host de bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como criar um host de bastiões do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: cherylmc
ms.openlocfilehash: 95f7d71c0de7570eee6e4c94e88fd65ff1d45ec8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973089"
---
# <a name="create-an-azure-bastion-host"></a>Criar um host de bastiões do Azure

Este artigo mostra como criar um host de bastiões do Azure. Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência ininterrupta de RDP/SSH estará disponível para todas as suas VMs na mesma rede virtual. Esta implementação é realizada por rede virtual e não por subscrição/conta ou máquina virtual.

Há duas maneiras de criar um recurso de host bastião:

* Crie um recurso de bastiões usando o portal do Azure.
* Crie um recurso de bastiões no portal do Azure usando as configurações de VM existentes.

## <a name="before-you-begin"></a>Antes de começar

A bastiões está disponível nas seguintes regiões públicas do Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Criar um host bastião

Esta seção ajuda você a criar um novo recurso de bastiões do Azure do portal do Azure.

1. No menu [portal do Azure](https://portal.azure.com) ou na **Home** Page do, selecione **criar um recurso**.

1. Na página **novo** , no campo *Pesquisar no Marketplace* , digite **bastiões**e clique em **Enter** para obter os resultados da pesquisa.

1. Nos resultados, clique em **bastiões**. Verifique se o Publicador é a *Microsoft* e se a categoria está em *rede*.

1. Na página de **bastiões** , clique em **criar** para abrir a página **criar uma bastiões** .

1. Na página **criar uma bastiões** , configure um novo recurso de bastiões. Especifique as definições de configuração para seu recurso de bastiões.

    ![criar uma bastiões](./media/bastion-create-host-portal/settings.png)

    * **Assinatura**: a assinatura do Azure que você deseja usar para criar um novo recurso de bastiões.
    * **Grupo de recursos**: o grupo de recursos do Azure no qual o novo recurso de bastiões será criado. Se você não tiver um grupo de recursos existente, poderá criar um novo.
    * **Nome**: o nome do novo recurso de bastiões
    * **Região**: a região pública do Azure em que o recurso será criado.
    * **Rede virtual**: a rede virtual na qual o recurso de bastiões será criado. Você pode criar uma nova rede virtual no portal durante esse processo, caso você não tenha ou não deseje usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, verifique se a rede virtual existente tem espaço de endereço livre suficiente para acomodar os requisitos de sub-rede de bastiões.
    * **Sub-rede**: a sub-rede em sua rede virtual para a qual o novo recurso de host bastião será implantado. Você deve criar uma sub-rede usando o nome valor **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede implantar os recursos de bastiões. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos uma sub-rede/27 ou maior (/27,/26 e assim por diante). Crie o **AzureBastionSubnet** sem nenhuma tabela ou delegação de rota. Ao usar grupos de segurança de rede no **AzureBastionSubnet**, consulte [trabalhar com NSGs](bastion-nsg.md).
    * **Endereço IP público**: o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um novo IP público ou use um existente. O endereço IP público deve estar na mesma região que o recurso de bastiões que você está criando.
    * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
    * **SKU de endereço IP público**: preenchida por padrão para **Standard**. A bastiões do Azure usa/dá suporte apenas à SKU de IP público padrão.
    * **Atribuição**: preenchida por padrão como **estática**.

1. Quando terminar de especificar as configurações, clique em **revisar + criar**. Isso valida os valores. Depois que a validação for aprovada, você poderá iniciar o processo de criação.
1. Na página criar uma bastiões, clique em **criar**.
1. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

## <a name="createvmset"></a>Criar um host bastião usando as configurações da VM

Se você criar um host de bastiões no portal usando uma VM existente, várias configurações serão automaticamente padrão correspondentes à máquina virtual e/ou à rede virtual.

1. Abra o [Portal do Azure](https://portal.azure.com). Vá para sua máquina virtual e clique em **conectar**.

   ![Conexão de VM](./media/bastion-create-host-portal/vmsettings.png)
1. Na barra lateral direita, clique em **bastiões**e, em seguida, **use bastiões**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na página de bastiões, preencha os seguintes campos de configuração:

   * **Nome**: o nome do host de bastiões que você deseja criar.
   * **Sub-rede**: a sub-rede dentro de sua rede virtual para a qual o recurso de bastiões será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. Isso permite que o Azure saiba em qual sub-rede implantar o recurso de bastiões. Isso é diferente de uma sub-rede de gateway. Clique em **gerenciar configuração de sub-rede** para criar a sub-rede de bastiões do Azure. É altamente recomendável que você use pelo menos uma sub-rede/27 ou maior (/27,/26, etc.). Crie o **AzureBastionSubnet** sem nenhum grupo de segurança de rede, tabelas de rotas ou delegações. Clique em **criar** para criar a sub-rede e prossiga com as próximas configurações.
   * **Endereço IP público**: o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um novo IP público ou use um existente. O endereço IP público deve estar na mesma região que o recurso de bastiões que você está criando.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
1. Na tela validação, clique em **criar**. Aguarde cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

## <a name="next-steps"></a>Passos seguintes

Leia as [perguntas frequentes de bastiões](bastion-faq.md)