---
title: Quickstart - Criar uma escala de máquina virtual definida no portal Azure
description: Começa com as tuas implementações aprendendo a criar rapidamente uma máquina virtual à escala do portal Azure.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 0408341eff483de71d76a24a933ece4cbdc2b947
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100535285"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure

Um conjunto de escala de máquina virtual permite-lhe implantar e gerir um conjunto de máquinas virtuais de escala automática. Pode dimensionar o número de VMs no conjunto de dimensionamento manualmente ou definir regras para dimensionar automaticamente com base na utilização de recursos como CPU, exigência de memória ou tráfego de rede. Em seguida, um balanceador de carga do Azure distribui o tráfego pelas instâncias de VM no conjunto de dimensionamento. Neste início rápido, vai criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

O [equilibrador de carga](../load-balancer/load-balancer-overview.md) Azure distribui o tráfego de entrada entre instâncias de máquinas virtuais saudáveis. 

Em primeiro lugar, crie um Balanceador de Carga Padrão público utilizando o portal. O nome e o endereço IP público que cria são configurados automaticamente como a extremidade frontal do balançador de carga.

1. Na caixa de pesquisa, escreva o **balançador de carga.** No **Mercado** nos resultados da pesquisa, escolha **o equilibrista de carga.**
1. No separador Básicos da página **'Criar balançador** de carga', insira ou selecione as **seguintes** informações:

    | Definição                 | Valor   |
    | ---| ---|
    | Subscrição  | Selecione a sua subscrição.    |    
    | Grupo de recursos | **Selecione Criar novo** e digitar *myVMSSResourceGroup* na caixa de texto.|
    | Name           | *myLoadBalancer*         |
    | Region         | Selecione **East US**.       |
    | Tipo          | Selecione **Público**.       |
    | SKU           | Selecione **Standard**.       |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público  | *myPip*   |
    | Atribuição| Estático |
    | Zona de disponibilidade | Selecione **Zona redundante**. |

1. Quando terminar, selecione **'Rever + criar'** 
1. Depois de passar a validação, **selecione Criar**. 

![Criar um balanceador de carga](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Criar conjunto de dimensionamento da máquina virtual
Pode implementar um conjunto de dimensionamento com uma imagem do Windows Server ou uma imagem do Linux, como RHEL, CentOS, Ubuntu ou SLES.

1. Escala de tipo **definida** na caixa de pesquisa. Nos resultados, no **Marketplace,** selecione **conjuntos de escala de máquina virtual**. Selecione **Criar** na página **de conjuntos de escala de máquina virtual,** que abrirá a página **de conjunto de escala de máquina virtual.** 
1. No **separador Basics,** nos **detalhes do Projeto,** certifique-se de que a subscrição correta é selecionada e selecione *myVMSSResourceGroup* da lista de grupos de recursos. 
1. Digite *o myScaleSet* como o nome do seu conjunto de escala.
1. Na **Região,** selecione uma região próxima da sua área.
1. Selecione uma imagem de mercado para **imagem**. Neste exemplo, escolhemos *o Ubuntu Server 18.04 LTS*.
1. Introduza o seu nome de utilizador pretendido e selecione o tipo de autenticação que preferir.
   - A **Palavra-passe** tem de ter, pelo menos, 12 carateres e cumprir três dos quatro requisitos de complexidade seguintes: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Para obter mais informações, veja [requisitos de nome de utilizador e palavra-passe](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Se selecionar uma imagem de disco de SO Linux, em vez disso, pode escolher **Chave pública SSH**. Forneça apenas a chave pública, como *~/.ssh/id_rsa.pub*. Pode utilizar o Azure Cloud Shell no portal para [criar e utilizar chaves SSH](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    :::image type="content" source="./media/virtual-machine-scale-sets-create-portal/quick-create-scale-set.png" alt-text="Os programas de imagem criam opções para conjuntos de escala no portal Azure.":::

1. Selecione **Seguinte** para mover as outras páginas. 
1. Deixe as predefinições para as páginas **Exemplo** e **Discos.**
1. Na página **'Rede',** em **equilíbrio de carga**, selecione **Sim** para colocar as instâncias definidas na balança atrás de um equilibrador de carga. 
1. Nas **opções de equilíbrio de carga**, selecione o **equilibrador de carga Azure**.
1. Em **Selecione um equilibrador de carga**, selecione o *myLoadBalancer* que criou anteriormente.
1. Para **selecionar uma piscina de backend**, selecione **Criar novo,** digite *myBackendPool* e, em seguida, selecione **Criar**.
1. Quando terminar, selecione **Rever + criar**. 
1. Depois de passar na validação, selecione **Criar** para implementar o conjunto de escala.


## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não for necessário, elimine o grupo de recursos, o conjunto de dimensionamento, a conta do Batch e todos os recursos relacionados. Para tal, selecione o grupo de recursos para o conjunto de escalas e, em seguida, **selecione Delete**.


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, criou um conjunto de dimensionamento básico no Portal do Azure. Para obter mais informações, avance para o tutorial para saber como criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
