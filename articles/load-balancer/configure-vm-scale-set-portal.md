---
title: Configurar a balança virtual da máquina definida com um equilibrador de carga Azure existente - portal Azure
description: Aprenda a configurar uma balança de máquina virtual definida com um Balançador de Carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 8e55b2a87813da802ec1b00c610b33da4461c008
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809446"
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
    | Região                         | Selecione **East US 2**                                                                                    |
    | Zona de disponibilidade              | Selecione **Nenhum**                                                                                       |
    | **Detalhes da instância**           |                                                                                                       |
    | Imagem                          | Selecione **Ubuntu Server 18.04 LTS**                                                                    |
    | Exemplo de Azure Spot            | Selecione **Não**                                                                                         |
    | Tamanho                           | Sair à revelia                                                                                      |
    | **Conta de administrador**      |                                                                                                       |
    | Tipo de autenticação            | Selecione **palavra-passe**                                                                                   |
    | Nome de utilizador                       | Insira o seu nome de utilizador de administrador        |
    | Palavra-passe                       | Insira a sua senha de administração    |
    | Confirmar palavra-passe               | Reinsenter a sua senha de administração |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Crie conjunto de escala de máquina virtual." border="true":::

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

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Crie conjunto de escala de máquina virtual." border="true":::

6. Selecione o **separador Gestão.**

7. No **separador Gestão,** desata **o diagnóstico de Boot** para **desligar**.

8. Selecione o botão **'Análise azul+' criar.**

9. Reveja as definições e selecione o botão **Criar.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou uma balança de máquina virtual definida com um Balançador de Carga Azure existente.  Para saber mais sobre conjuntos de balanças de máquinas virtuais e balanceador de carga, consulte:

- [What is Azure Load Balancer?](load-balancer-overview.md) (O que é o Balanceador de Carga do Azure?)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
