---
title: Quickstart - Criar uma escala de máquina virtual definida no portal Azure
description: Inicie-se com as suas implementações aprendendo a criar rapidamente uma escala virtual de máquinas do portal Azure.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: quickstart
ms.custom: mvc, H1Hack27Feb2017
ms.date: 10/23/2019
ms.author: jushiman
ms.openlocfilehash: 410ae93ea109e5757ccb5b65d353a600574dd595
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010482"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>Início Rápido: Criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure

Um conjunto de dimensionamento de máquinas virtuais permite implementar e gerir um conjunto de máquinas virtuais idênticas e de dimensionamento automático. Pode dimensionar o número de VMs no conjunto de dimensionamento manualmente ou definir regras para dimensionar automaticamente com base na utilização de recursos como CPU, exigência de memória ou tráfego de rede. Em seguida, um balanceador de carga do Azure distribui o tráfego pelas instâncias de VM no conjunto de dimensionamento. Neste início rápido, vai criar um conjunto de dimensionamento de máquinas virtuais no Portal do Azure.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="log-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

O [equilibrador](../load-balancer/load-balancer-overview.md) de carga Azure distribui o tráfego de entrada entre casos de máquinas virtuais saudáveis. 

Em primeiro lugar, crie um Balancer de Carga Padrão público utilizando o portal. O nome e o endereço IP público que cria são automaticamente configurados como a extremidade frontal do equilibrador de carga.

1. Na caixa de pesquisa, digite o **equilibrador**de carga . No **âmbito do Marketplace** nos resultados da pesquisa, escolha o **balanceador de carga**.
1. No separador Basics da página **Criar balanceadores** de carga, introduza ou selecione as **seguintes** informações:

    | Definição                 | Valor   |
    | ---| ---|
    | Subscrição  | Selecione a sua subscrição.    |    
    | Grupo de recursos | Selecione **Criar novo** e digitar *myVMSSResourceGroup* na caixa de texto.|
    | Nome           | *myLoadBalancer*         |
    | Região         | Selecione **East US**.       |
    | Tipo          | Selecione **Public**.       |
    | SKU           | Selecione **Standard**.       |
    | Endereço IP público | Selecione **Criar novo**. |
    | Nome do endereço IP público  | *MyPip*   |
    | Atribuição| Estático |

1. Quando terminar, selecione **Rever + criar** 
1. Depois de passar a validação, selecione **Criar**. 

![Criar um balanceador de carga](./media/virtual-machine-scale-sets-create-portal/load-balancer.png)

## <a name="create-virtual-machine-scale-set"></a>Criar conjunto de dimensionamento da máquina virtual
Pode implementar um conjunto de dimensionamento com uma imagem do Windows Server ou uma imagem do Linux, como RHEL, CentOS, Ubuntu ou SLES.

1. **Tipo Escala definida** na caixa de pesquisa. Nos resultados, no âmbito do **Marketplace,** selecione conjuntos de **escala de máquinavirtual**. A página de conjunto de escala de **máquina virtual Criar** será aberta. 
1. No separador **Noções básicas**, em **Detalhes do projeto**, certifique-se de que está selecionada a subscrição correta e, em seguida, selecione **Criar novo** no grupo de recursos. Digite *myVMSSResourceGroup* para o nome e, em seguida, selecione **OK** . 
1. Digite *myScaleSet* como o nome para o seu conjunto de escala.
1. Na **Região,** selecione uma região que esteja perto de si.
1. Deixe o valor padrão dos **VMs ScaleSet** para **Orchestrator**.
1. Selecione uma imagem de mercado para **Imagem**. Neste exemplo, escolhemos *o Ubuntu Server 18.04 LTS*.
1. Introduza o seu nome de utilizador pretendido e selecione o tipo de autenticação que preferir.
   - A **Palavra-passe** tem de ter, pelo menos, 12 carateres e cumprir três dos quatro requisitos de complexidade seguintes: um caráter em letra minúscula, um caráter em letra maiúscula, um número e um caráter especial. Para obter mais informações, veja [requisitos de nome de utilizador e palavra-passe](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm).
   - Se selecionar uma imagem de disco de SO Linux, em vez disso, pode escolher **Chave pública SSH**. Forneça apenas a chave pública, como *~/.ssh/id_rsa.pub*. Pode utilizar o Azure Cloud Shell no portal para [criar e utilizar chaves SSH](../virtual-machines/linux/mac-create-ssh-keys.md).
   
    ![Criar um conjunto de dimensionamento de máquinas virtuais](./media/virtual-machine-scale-sets-create-portal/quick-create-scaleset.png)

1. Selecione **Next** para mover as outras páginas. 
1. Deixe os predefinições para as páginas **De Exemplo** e **Discos.**
1. Na página **de Networking,** sob **o equilíbrio de carga,** selecione **Sim** para colocar as instâncias de conjunto de escala atrás de um equilibrador de carga. 
1. Nas **opções de equilíbrio de carga,** selecione **o equilíbrio de carga Azure**.
1. Em **Select um balancer**de carga, selecione *myLoadBalancer* que criou anteriormente.
1. Para **selecionar uma piscina de backend,** selecione Criar **novo**, escreva *myBackendPool*e, em seguida, selecione **Criar**.
1. Quando terminar, selecione **Rever + criar**. 
1. Depois de passar a validação, selecione **Criar** para implementar o conjunto de escala.


## <a name="clean-up-resources"></a>Limpar recursos
Quando já não for necessário, elimine o grupo de recursos, o conjunto de dimensionamento, a conta do Batch e todos os recursos relacionados. Para isso, selecione o grupo de recursos para o conjunto de escala e, em seguida, **selecione Eliminar**.


## <a name="next-steps"></a>Passos seguintes
Neste início rápido, criou um conjunto de dimensionamento básico no Portal do Azure. Para obter mais informações, avance para o tutorial para saber como criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure.

> [!div class="nextstepaction"]
> [Criar e gerir conjuntos de dimensionamento de máquinas virtuais do Azure](tutorial-create-and-manage-powershell.md)
