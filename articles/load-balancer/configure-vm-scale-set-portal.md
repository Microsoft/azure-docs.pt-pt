---
title: Configurar a balança virtual da máquina definida com um equilibrador de carga Azure existente - portal Azure
description: Aprenda a configurar uma balança de máquina virtual definida com um Equilibr de Carga Azure existente utilizando o portal Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439522"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configure uma balança de máquina virtual definida com um Equilibrador de Carga Azure existente usando o portal Azure

Neste artigo, você vai aprender a configurar uma balança de máquina virtual definida com um Azure Load Balancer existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um balanceador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar o conjunto de escala de máquina virtual com o equilibrador de carga existente

Nesta secção, irá criar uma balança de máquina virtual definida no portal Azure com um equilibrador de carga Azure existente.

> [!NOTE]
> Os passos seguintes pressupõem que uma rede virtual chamada **myVNet** e um equilibrador de carga Azure chamado **myLoadBalancer** foi previamente implantado.

1. No lado superior esquerdo do ecrã, clique em **Criar um**conjunto de escala de máquina virtual compute de recurso ou procurar a balança de máquina  >  **Compute**  >  **Virtual machine scale set** **virtual definida** na pesquisa do mercado.

2. Selecione **Criar**.

3. Em **Criar um conjunto de escala de máquina virtual,** insira ou selecione esta informação no **separador Básicos:**

    | Definição                        | Valor                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Detalhes do projeto**            |                                                                                                       |
    | Subscrição                   | selecione a subscrição do Azure                                                                        |
    | Grupo de Recursos                 | Selecione Criar novo, insira **o myResourceGroup,** em seguida, selecione OK ou selecione um grupo de recursos existente. |
    | **Detalhes definidos de escala**          |                                                                                                       |
    | Nome do conjunto de dimensionamento de máquinas virtuais | Insira **o myVMSS**                                                                                      |
    | Region                         | Selecione **East US 2**                                                                                    |
    | Zona de disponibilidade              | Selecione **Nenhum**                                                                                       |
    | **Detalhes da instância**           |                                                                                                       |
    | Imagem                          | Selecione **Ubuntu Server 18.04 LTS**                                                                    |
    | Instância do Azure Spot            | Selecione **Não**                                                                                         |
    | Tamanho                           | Sair à revelia                                                                                      |
    | **Conta de administrador**      |                                                                                                       |
    | Tipo de autenticação            | Selecione **palavra-passe**                                                                                   |
    | Nome de utilizador                       | Insira o seu nome de utilizador de administrador        |
    | Palavra-passe                       | Insira a sua senha de administração    |
    | Confirmar palavra-passe               | Reinsenter a sua senha de administração |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="A screenshot mostra o separador de escala de máquina virtual Create Basics." border="true":::

4. Selecione o **separador 'Rede'.**

5. Introduza ou selecione estas informações no **separador Networking:**

     Definição                           | Valor                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configuração de rede virtual** |                                                          |
    | Rede virtual                   | Selecione **myVNet** ou a sua rede virtual existente.      |
    | **Balanceamento de carga**                |                                                          |
    | Use um equilibrador de carga               | Selecione **Sim**                                           |
    | **Definições de equilíbrio de carga**       |                                                          |
    | Opções de equilíbrio de carga            | Selecione **o equilibrador de carga Azure**                           |
    | Selecione um equilibrador de carga            | Selecione **myLoadBalancer** ou o seu balanceador de carga existente |
    | Selecione uma piscina de backend             | Selecione **myBackendPool** ou o pool de backend existente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="A screenshot mostra o separador de escala de máquina virtual Create Basics." border="true":::

6. Selecione o **separador Gestão.**

7. No **separador Gestão,** desata **o diagnóstico de Boot** para **desligar**.

8. Selecione o botão **'Análise azul+' criar.**

9. Reveja as definições e selecione o botão **Criar.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou uma balança de máquina virtual definida com um Balançador de Carga Azure existente.  Para saber mais sobre conjuntos de balanças de máquinas virtuais e balanceador de carga, consulte:

- [O que é o Balanceador de Carga do Azure?](load-balancer-overview.md)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
