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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970795"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Habilitar conexão do navegador em máquinas virtuais do laboratório 
O DevTest Labs integra-se à [bastiões do Azure](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais por meio de um navegador. Primeiro, você precisa habilitar a conexão do navegador em máquinas virtuais do laboratório.

Como proprietário de um laboratório, você pode habilitar o acesso a todas as máquinas virtuais do laboratório por meio de um navegador. Você não precisa de um cliente adicional, agente ou parte do software. O Azure Bastion oferece conectividade RDP/SSH segura e totalmente integrada às suas máquinas virtuais diretamente no portal do Azure por SSL. Quando você se conecta por meio de bastiões do Azure, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](../bastion/bastion-overview.md)


Este artigo mostra como habilitar a conexão do navegador em máquinas virtuais do laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Implante um host bastião na rede virtual do laboratório existente **(ou)** Conecte seu laboratório com uma rede virtual configurada para bastiões. 

Para saber como implantar um host de bastiões em uma rede virtual, consulte [criar um host de bastiões do Azure](../bastion/bastion-create-host-portal.md). Ao criar o host de bastiões, selecione a rede virtual do laboratório. 

Primeiro, você precisa criar uma segunda sub-rede na rede virtual de bastiões porque o AzureBastionSubnet não permite a criação de recursos de não-bastiões nele. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Criar uma segunda sub rede na rede virtual de bastiões
Não é possível criar VMs de laboratório em uma sub-rede de bastiões do Azure. Crie outra sub-rede dentro da rede virtual de bastiões, conforme mostrado na imagem a seguir:

![Segunda sub-rede na rede virtual de bastiões do Azure](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Habilitar a criação de VM na sub-rede
Agora, habilite a criação de VMs nesta sub-rede seguindo estas etapas: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **todos os serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Na lista de laboratórios, selecione *seu laboratório*. 

    > [!NOTE]
    > A bastiões do Azure agora está disponível para o público em geral nas seguintes regiões: oeste dos EUA, leste dos EUA, Europa Ocidental, Sul EUA Central, leste da Austrália e leste do Japão. Portanto, crie um laboratório em uma dessas regiões se o seu laboratório não estiver em um deles. 
    
1. Selecione **configuração e políticas** na seção **configurações** no menu à esquerda. 
1. Selecione **redes virtuais**.
1. Selecione **Adicionar** na barra de ferramentas. 
1. Selecione a **rede virtual** que tem o host de bastiões implantado. 
1. Selecione a sub-rede para VMs, não o **AzureBastionSubnet**, o outro que você criou anteriormente. Feche a página e reabra-a se você não vir a sub-rede na lista na parte inferior. 

    ![Habilitar a criação de VM na sub-rede](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selecione **usar na opção de criação de máquina virtual** . 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se você tiver uma rede virtual antiga para o laboratório, remova-a selecionando * *..* .  e **remova**. 

## <a name="enable-browser-connection"></a>Habilitar conexão do navegador 

Depois de ter uma rede virtual configurada para bastiões dentro do laboratório, como um proprietário de laboratório, você pode habilitar o browser Connect em máquinas virtuais de laboratório.

Para habilitar o browser Connect em máquinas virtuais do laboratório, siga estas etapas:

1. No portal do Azure, navegue até *o laboratório*.
1. Selecione **configuração e políticas**.
1. Em **configurações**, selecione **navegador conectar**. Se você não vir essa opção, feche a página **políticas de configuração** e abra-a novamente. 

    ![Habilitar conexão do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Próximos Passos
Consulte o artigo a seguir para saber como se conectar às suas VMs usando um navegador: [conectar-se às suas máquinas virtuais por meio de um navegador](connect-virtual-machine-through-browser.md)
