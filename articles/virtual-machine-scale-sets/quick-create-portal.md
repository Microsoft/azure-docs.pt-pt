---
title: Início rápido-criar um conjunto de dimensionamento de máquinas virtuais no portal do Azure
description: Comece com suas implantações aprendendo a criar rapidamente uma escala de máquina virtual portal do Azure.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 2acde4efa1dc118498f3c7da29c75e48c0478ac0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543142"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure

Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. Pode dimensionar o número de VMs no conjunto de dimensionamento manualmente ou definir regras para dimensionar automaticamente com base na utilização de recursos como CPU, exigência de memória ou tráfego de rede. Em seguida, um balanceador de carga do Azure distribui o tráfego pelas instâncias de VM no conjunto de dimensionamento. Neste início rápido, vai criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

O Azure [Load Balancer](../load-balancer/load-balancer-overview.md) distribui o tráfego de entrada entre instâncias de máquina virtual íntegras. 

Primeiro, crie um Standard Load Balancer público usando o Portal. O nome e endereço IP público que criou são automaticamente configurados como front-end de Balanceador de carga.

1. Na caixa de pesquisa, digite **balanceador de carga**. Em **Marketplace** nos resultados da pesquisa, escolha **balanceador de carga**.
1. Na guia **noções básicas** da página **criar balanceador de carga** , insira ou selecione as seguintes informações:

    | Definição                 | Valor   |
    | ---| ---|
    | Subscrição  | Selecione a sua subscrição.    |    
    | Grupo de recursos | Selecione **criar novo** e digite *myVMSSResourceGroup* na caixa de texto.|
    | Nome           | *myLoadBalancer*         |
    | Região         | Selecione **E.U.A. Leste**.       |
    | Tipo          | Selecione **Público**.       |
    | SKU           | Selecione **padrão**.       |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público  | *MyPip*   |
    | Atribuição| Estático |

1. Quando terminar, selecione **revisar + criar** 
1. Depois de passar na validação, selecione **criar**. 

![Criar um balanceador de carga](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Criar conjunto de dimensionamento da máquina virtual
Pode implementar um conjunto de dimensionamento com uma imagem do Windows Server ou uma imagem do Linux, como RHEL, CentOS, Ubuntu ou SLES.

1. Tipo de **escala definido** na caixa de pesquisa. Nos resultados, em **Marketplace**, selecione **conjuntos de dimensionamento de máquinas virtuais**. A página **criar um conjunto de dimensionamento de máquinas virtuais** será aberta. 
1. No separador **Noções básicas**, em **Detalhes do projeto**, certifique-se de que está selecionada a subscrição correta e, em seguida, selecione **Criar novo** no grupo de recursos. Digite *myVMSSResourceGroup* para o nome e, em seguida, selecione **OK** . 
1. Digite *Myscalemodeset* como o nome do conjunto de dimensionamento.
1. Em **região**, selecione uma região que esteja perto de sua área.
1. Deixe o valor padrão das **VMs do scaleset** para o **Orchestrator**.
1. Selecione uma imagem do Marketplace para a **imagem**. Neste exemplo, escolhemos o *Ubuntu Server 18, 4 LTS*.
1. Introduza o seu nome de utilizador pretendido e selecione o tipo de autenticação que preferir.
   - A **Palavra-passe** tem de ter, pelo menos, 12 carateres e cumprir três dos quatro requisitos de complexidade seguintes: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Para obter mais informações, veja [requisitos de nome de utilizador e palavra-passe](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Se selecionar uma imagem de disco de SO Linux, em vez disso, pode escolher **Chave pública SSH**. Forneça apenas a chave pública, como *~/.ssh/id_rsa.pub*. Pode utilizar o Azure Cloud Shell no portal para [criar e utilizar chaves SSH](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Criar um conjunto de dimensionamento de máquinas virtuais](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Selecione **Avançar** para mover as outras páginas. 
1. Deixe os padrões para as páginas de **instância** e **discos** .
1. Na página **rede** , em **balanceamento de carga**, selecione **Sim** para colocar as instâncias do conjunto de dimensionamento por trás de um balanceador de carga. 
1. Em **Opções de balanceamento de carga**, selecione **Azure Load Balancer**.
1. Em **selecionar um balanceador de carga**, selecione *myLoadBalancer* que você criou anteriormente.
1. Para **selecionar um pool de back-end**, selecione **criar novo**, digite *myBackendPool*e, em seguida, selecione **criar**.
1. Quando terminar, selecione **revisar + criar**. 
1. Depois de passar na validação, selecione **criar** para implantar o conjunto de dimensionamento.


## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, elimine o grupo de recursos, o conjunto de dimensionamento, a conta do Batch e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos para o conjunto de dimensionamento e, em seguida, selecione **excluir**.


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, criou um conjunto de dimensionamento básico no Portal do Azure. Para obter mais informações, avance para o tutorial para saber como criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
