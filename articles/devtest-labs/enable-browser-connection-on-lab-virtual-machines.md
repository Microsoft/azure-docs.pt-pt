---
title: Ativar a ligação do navegador nas máquinas virtuais Azure DevTest Labs Microsoft Docs
description: A DevTest Labs integra-se agora com o Azure Bastion, como proprietário do laboratório, podendo aceder a todas as máquinas virtuais de laboratório através de um browser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9b901d36907c8d2bbae866c38bbf1c2554e0f1da
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482943"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Ativar a ligação do navegador em máquinas virtuais de laboratório 

A DevTest Labs integra-se com [o Azure Bastion,](https://docs.microsoft.com/azure/bastion/)que permite ligar-se às suas máquinas virtuais através de um browser. Primeiro, tem de ativar a ligação do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, pode permitir o acesso a todas as máquinas virtuais de laboratório através de um browser. Não precisa de um cliente adicional, agente ou software. O Azure Bastion proporciona conectividade RDP/SSH segura e sem costura às suas máquinas virtuais diretamente no portal Azure sobre o TLS. Quando se conecta através do Azure Bastion, as suas máquinas virtuais não necessitam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)

> [!NOTE]
> Ativar a ligação do navegador em máquinas virtuais de laboratório está em pré-visualização.

Este artigo mostra como ativar a ligação do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Ou implantar um anfitrião de Bastião na rede virtual **(OR)** do seu laboratório existente, ligue o seu laboratório a um VNet configurado por Bastião. 

Para aprender a implantar um anfitrião de Bastião num VNet, consulte [Criar um anfitrião de Bastião Azure (Preview)](../bastion/bastion-create-host-portal.md). Ao criar o anfitrião do Bastião, selecione a rede virtual do laboratório. 

Para aprender a ligar o seu laboratório a um VNet configurado por Bastião, consulte [configurar uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md). Selecione o VNet que tem o anfitrião Bastion implantado e o **AzureBastionSubnet** nele. Aqui estão os passos detalhados: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Da lista de laboratórios, selecione *o seu laboratório.* 

    > [!NOTE]
    > Azure Bastion está neste momento em pré-estreia. Limita-se às seguintes regiões: Eua Ocidental, Leste dos EUA, Europa Ocidental, Centro Sul dos EUA, Austrália Oriental e Japão Leste. Então, crie um laboratório numa destas regiões se o seu laboratório não estiver numa delas. 
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
1. Selecione **redes virtuais**.
1. **Selecione Adicione** na barra de ferramentas. 
1. Selecione o **VNet** que tem o anfitrião Bastion implantado. 
1. Selecione a sub-rede: **AzureBastionSubnet**. 

    ![Subrede](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecione Utilizar na opção **de criação de máquinas virtuais.** 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se tiver um VNet antigo para o laboratório, remova-o selecionando **...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Ativar a ligação do navegador 

Uma vez que tenha um VNet configurado de Bastião dentro do laboratório, como proprietário de laboratório, pode ativar a ligação do navegador em máquinas virtuais de laboratório.

Para ativar a ligação do navegador em máquinas virtuais de laboratório, siga estes passos:

1. No portal Azure, navegue para *o seu laboratório.*
1. Selecione **Configuração e políticas**.
1. Em **Definições**, selecione **Browser connect (Preview)**.

![Ativar a ligação do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte artigo para saber como se conectar aos seus VMs utilizando um browser: [Conecte-se às suas máquinas virtuais através de um browser](connect-virtual-machine-through-browser.md)