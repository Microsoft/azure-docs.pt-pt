---
title: Ativar a ligação ao navegador nas máquinas virtuais do Azure DevTest Labs [ Microsoft Docs
description: A DevTest Labs integra-se agora com o Azure Bastion, como proprietário do laboratório, pode permitir o acesso a todas as máquinas virtuais de laboratório através de um browser.
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
ms.openlocfilehash: 074d512767769fe434cd4a6d4f4e8e6a88f7ed2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80549123"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Ativar a ligação do navegador em máquinas virtuais de laboratório 
A DevTest Labs integra-se com o [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)que lhe permite ligar-se às suas máquinas virtuais através de um browser. Primeiro é necessário ativar a ligação do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, você pode permitir o acesso a todas as máquinas virtuais de laboratório através de um navegador. Não precisa de um cliente, agente ou peça de software adicional. A Azure Bastion fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre TLS. Quando se conecta através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)


Este artigo mostra como ativar a ligação do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Ou implanta um hospedeiro Bastion na rede virtual **(OU)** do seu laboratório existente, conecta o seu laboratório com uma rede virtual configurada por Bastion. 

Para aprender a implantar um anfitrião bastião numa rede virtual, consulte [Create a Azure Bastion host](../bastion/bastion-create-host-portal.md). Ao criar o hospedeiro Bastion, selecione a rede virtual do laboratório. 

Primeiro, é necessário criar uma segunda subrede na rede virtual bastião porque a AzureBastionSubnet não permite a criação de recursos não-Bastião na rede. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Criar uma segunda rede sub-rede na rede virtual bastião
Não se pode criar vMs de laboratório numa subnet azure bastion. Crie outra subrede dentro da rede virtual bastião, como mostra a seguinte imagem:

![Segunda subnet na rede virtual Azure Bastion](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Ativar a criação de VM na sub-rede
Agora, permita a criação de VMs nesta subnet seguindo estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Da lista de laboratórios, *selecione o seu laboratório.* 

    > [!NOTE]
    > O Azure Bastion está agora geralmente disponível nas seguintes regiões: Oeste dos EUA, Leste dos EUA, Europa Ocidental, Centro-Sul dos EUA, Austrália Leste e Japão Leste. Então, crie um laboratório numa destas regiões se o seu laboratório não estiver numa delas. 
    
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
1. Selecione **redes Virtuais**.
1. **Selecione Adicionar** a partir da barra de ferramentas. 
1. Selecione a **rede virtual** que tem o anfitrião Bastião implantado. 
1. Selecione a sub-rede para VMs, não para o **AzureBastionSubnet,** o outro que criou anteriormente. Feche a página e reabra-a se não vir a sub-rede na lista na parte inferior. 

    ![Ativar a criação de VM na sub-rede](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selecione Utilizar na opção de **criação de máquinavirtual.** 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se tiver uma antiga rede virtual para o laboratório, remova-a selecionando ** ...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Ativar a ligação ao navegador 

Uma vez que você tem uma rede virtual configurada Bastion dentro do laboratório, como proprietário de laboratório, você pode ativar a ligação do navegador em máquinas virtuais de laboratório.

Para permitir a ligação do navegador em máquinas virtuais de laboratório, siga estes passos:

1. No portal Azure, navegue para *o seu laboratório.*
1. **Selecione Configuração e políticas**.
1. Em **Definições,** selecione **a ligação do navegador**. Se não vir esta opção, feche a página de políticas de **Configuração** e reabra-a. 

    ![Ativar a ligação ao navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte artigo para aprender a conectar-se aos seus VMs usando um browser: [Ligue-se às suas máquinas virtuais através](connect-virtual-machine-through-browser.md) de um browser
