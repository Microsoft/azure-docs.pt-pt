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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549089"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Ativar a ligação do navegador em máquinas virtuais de laboratório 

A DevTest Labs integra-se com o [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)que lhe permite ligar-se às suas máquinas virtuais através de um browser. Primeiro é necessário ativar a ligação do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, você pode permitir o acesso a todas as máquinas virtuais de laboratório através de um navegador. Não precisa de um cliente, agente ou peça de software adicional. A Azure Bastion fornece conectividade RDP/SSH segura e sem emenda às suas máquinas virtuais diretamente no portal Azure sobre TLS. Quando se conecta através do Azure Bastion, as suas máquinas virtuais não precisam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)

> [!NOTE]
> Ativar a ligação do navegador em máquinas virtuais de laboratório está em pré-visualização.

Este artigo mostra como ativar a ligação do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Ou implanta um hospedeiro Bastion na rede virtual **(OU)** do seu laboratório existente, ligue o seu laboratório a um VNet configurado por Bastion. 

Para aprender a implantar um anfitrião bastião num VNet, consulte [Create a Azure Bastion host (Preview)](../bastion/bastion-create-host-portal.md). Ao criar o hospedeiro Bastion, selecione a rede virtual do laboratório. 

Para aprender a ligar o seu laboratório a um VNet configurado por Bastion, consulte [a Configure uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md). Selecione o VNet que tem o anfitrião Bastião implantado e o **AzureBastionSubnet** nele. Aqui estão os passos detalhados: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Da lista de laboratórios, *selecione o seu laboratório.* 

    > [!NOTE]
    > O Azure Bastion está atualmente em pré-estreia. Limita-se às seguintes regiões: Oeste dos EUA, Leste dos EUA, Europa Ocidental, Centro-Sul dos EUA, Austrália Leste e Japão Leste. Então, crie um laboratório numa destas regiões se o seu laboratório não estiver numa delas. 
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
1. Selecione **redes Virtuais**.
1. **Selecione Adicionar** a partir da barra de ferramentas. 
1. Selecione o **VNet** que tem o anfitrião Bastião implantado. 
1. Selecione a sub-rede: **AzureBastionSubnet**. 

    ![Subrede](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecione Utilizar na opção de **criação de máquinavirtual.** 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se tiver um VNet antigo para o laboratório, remova-o selecionando ** ...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Ativar a ligação ao navegador 

Uma vez que tenha um Bastião configurado VNet dentro do laboratório, como proprietário de laboratório, você pode ativar a ligação do navegador em máquinas virtuais de laboratório.

Para permitir a ligação do navegador em máquinas virtuais de laboratório, siga estes passos:

1. No portal Azure, navegue para *o seu laboratório.*
1. **Selecione Configuração e políticas**.
1. Em **Definições,** selecione **a ligação do navegador (Pré-visualização)**.

![Ativar a ligação ao navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte artigo para aprender a conectar-se aos seus VMs usando um browser: [Ligue-se às suas máquinas virtuais através](connect-virtual-machine-through-browser.md) de um browser