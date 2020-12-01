---
title: Ativar a ligação do navegador em máquinas virtuais Azure DevTest Labs
description: A DevTest Labs integra-se agora com o Azure Bastion, como proprietário do laboratório, podendo aceder a todas as máquinas virtuais de laboratório através de um browser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6d9d631c79c22f1f713cfc4ee7cdd766a4ad8f06
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341177"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Ativar a ligação do navegador em máquinas virtuais Azure DevTest Labs 
A DevTest Labs integra-se com [o Azure Bastion,](../bastion/index.yml)que permite ligar-se às suas máquinas virtuais através de um browser. Primeiro, tem de ativar a ligação do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, pode permitir o acesso a todas as máquinas virtuais de laboratório através de um browser. Não precisa de um cliente adicional, agente ou software. O Azure Bastion proporciona conectividade RDP/SSH segura e sem costura às suas máquinas virtuais diretamente no portal Azure sobre o TLS. Quando se conecta através do Azure Bastion, as suas máquinas virtuais não necessitam de um endereço IP público. Para mais informações, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)


Este artigo mostra como ativar a ligação do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
- Ou implantar um anfitrião bastonário na rede virtual **(OR)** do seu laboratório existente, ligue o seu laboratório a uma rede virtual configurada pelo Bastião.
Para aprender a implantar um anfitrião bastonário numa rede virtual, consulte  [Criar um hospedeiro Azure Bastion](../bastion/tutorial-create-host-portal.md). Ao criar o anfitrião do Bastião, selecione a rede virtual do laboratório. 
- O utilizador do laboratório precisa de ter um papel **de Leitor** no anfitrião do Bastião e na rede virtual que tem o Bastião configurado. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Criar uma segunda sub-rede na rede virtual De Bastião
Em primeiro lugar, é necessário criar uma segunda sub-rede na rede virtual de Bastião porque o AzureBastionSubnet não permite a criação de recursos não bastonários na mesma. Criar outra sub-rede dentro da rede virtual De Bastião, como mostrado na seguinte imagem:

![Segunda sub-rede na rede virtual Azure Bastion](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Ativar a criação de VM na sub-rede
Agora, possibilite a criação de VMs nesta sub-rede seguindo estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os Serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Da lista de laboratórios, selecione *o seu laboratório.* 

    > [!NOTE]
    > O Azure Bastion está agora geralmente disponível nas seguintes regiões: Eua Ocidental, Leste dos EUA, Europa Ocidental, Centro Sul dos EUA, Austrália Oriental e Japão Leste. Então, crie um laboratório numa destas regiões se o seu laboratório não estiver numa delas. 
    
1. Selecione **Configuração e políticas** na secção **Definições** no menu esquerdo. 
1. Selecione **redes virtuais**.
1. **Selecione Adicione** na barra de ferramentas. 
1. Selecione a **rede virtual** que tem o anfitrião Bastion implantado. 
1. Selecione a sub-rede para VMs, não para **AzureBastionSubnet**, a outra que criou anteriormente. Feche a página e reabra-a se não vir a sub-rede na lista na parte inferior. 

    ![Ativar a criação de VM na sub-rede](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selecione Utilizar na opção **de criação de máquinas virtuais.** 
1. Selecione **Guardar** na barra de ferramentas. 
1. Se tiver uma antiga rede virtual para o laboratório, remova-a selecionando **...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Ativar a ligação do navegador 

Uma vez que tenha uma rede virtual configurada em Bastion dentro do laboratório, como proprietário de laboratório, pode ativar a ligação do navegador em máquinas virtuais de laboratório.

Para ativar a ligação do navegador em máquinas virtuais de laboratório, siga estes passos:

1. No portal Azure, navegue para *o seu laboratório.*
1. Selecione **Configuração e políticas**.
1. Em **Definições**, selecione **Browser connect**. Se não vir esta opção, feche a página de **políticas de Configuração** e reabra-a. 

    ![Ativar a ligação do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte o seguinte artigo para saber como se conectar aos seus VMs utilizando um browser: [Conecte-se às suas máquinas virtuais através de um browser](connect-virtual-machine-through-browser.md)