---
title: Habilitar conexão do navegador em máquinas virtuais Azure DevTest Labs | Microsoft Docs
description: O DevTest Labs agora se integra à bastiões do Azure, como proprietário do laboratório, você pode habilitar o acesso a todas as máquinas virtuais do laboratório por meio de um navegador.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 2ddc56c60c547bd4ce48d620a83fb79246762bfb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642490"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Habilitar conexão do navegador em máquinas virtuais do laboratório 

O DevTest Labs integra-se à [bastiões do Azure](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais por meio de um navegador. Primeiro, você precisa habilitar a conexão do navegador em máquinas virtuais do laboratório.

Como proprietário de um laboratório, você pode habilitar o acesso a todas as máquinas virtuais do laboratório por meio de um navegador. Você não precisa de um cliente adicional, agente ou parte do software. A bastiões do Azure fornece conectividade RDP/SSH segura e direta para suas máquinas virtuais diretamente no portal do Azure sobre SSL. Quando você se conecta por meio de bastiões do Azure, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](../bastion/bastion-overview.md)

> [!NOTE]
> A habilitação da conexão do navegador em máquinas virtuais do laboratório está em versão prévia.

Este artigo mostra como habilitar a conexão do navegador em máquinas virtuais do laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Implante um host de bastiões na rede virtual do laboratório existente **(ou)** Conecte seu laboratório com uma VNet configurada para bastiões. 

Para saber como implantar um host de bastiões em uma VNet, confira [criar um host de bastiões do Azure (versão prévia)](../bastion/bastion-create-host-portal.md). Ao criar o host de bastiões, selecione a rede virtual do laboratório. 

Para saber como conectar seu laboratório com uma VNet configurada para bastiões, consulte [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md). Selecione a VNet que tem o host de bastiões implantado e o **AzureBastionSubnet** . Estas são as etapas detalhadas: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Na lista de laboratórios, selecione *seu laboratório*. 

    > [!NOTE]
    > A bastiões do Azure está atualmente em visualização. Ele é limitado às seguintes regiões: Oeste dos EUA, leste dos EUA, Europa Ocidental, Sul EUA Central, leste da Austrália e leste do Japão. Portanto, crie um laboratório em uma dessas regiões se o seu laboratório não estiver em um deles. 
1. Selecione **configuração e políticas** na seção **configurações** no menu à esquerda. 
1. Selecione **redes virtuais**.
1. Selecione **Adicionar** na barra de ferramentas. 
1. Selecione a **VNet** que tem o host de bastiões implantado. 
1. Selecione a sub-rede: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecione **usar na opção de criação de máquina virtual** . 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se você tiver uma VNet antiga para o laboratório, remova-a selecionando * *..* .  e **remova**. 

## <a name="enable-browser-connection"></a>Habilitar conexão do navegador 

Depois de ter uma rede virtual configurada para bastiões dentro do laboratório, como um proprietário de laboratório, você pode habilitar o browser Connect em máquinas virtuais do laboratório.

Para habilitar o browser Connect em máquinas virtuais do laboratório, siga estas etapas:

1. No portal do Azure, navegue até *o laboratório*.
1. Selecione **configuração e políticas**.
1. Em **configurações**, selecione **navegador conectar (versão prévia)** .

![Habilitar conexão do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Próximos Passos
Consulte o artigo a seguir para saber como se conectar às suas VMs usando um navegador: [Conectar-se às suas máquinas virtuais por meio de um navegador](connect-virtual-machine-through-browser.md)