---
title: Configure conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente - portal Azure
description: Aprenda a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349715"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Configure um conjunto de escala de máquina virtual com um equilíbrio de carga Azure existente utilizando o portal Azure

Neste artigo, você aprenderá a configurar um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.
- Um equilibrador de carga padrão existente na subscrição onde será implantado o conjunto de escala de máquina virtual.
- Uma Rede Virtual Azure para o conjunto de escala de máquina virtual.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Implementar conjunto de escala de máquina virtual com o equilibrador de carga existente

Nesta secção, você vai criar uma escala de máquina virtual definida no portal Azure com um equilibrador de carga Azure existente.

> [!NOTE]
> Os seguintes passos assumem que uma rede virtual chamada **myVNet** e um equilibrador de carga Azure chamado **myLoadBalancer** foi previamente implementada.

1. No lado superior esquerdo do ecrã, clique em **Criar um** > conjunto de escala de máquina sinérfica**Compute** > **Virtual machine scale set** de recurso ou procurar a escala virtual da **máquina definida** na pesquisa do mercado.

2. Selecione **Criar**.

3. Em Criar um conjunto de escala de **máquina virtual,** introduza ou selecione estas informações no separador **Basics:**

    | Definição                        | Valor                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Detalhes do projeto**            |                                                                                                       |
    | Subscrição                   | selecione a subscrição do Azure                                                                        |
    | Grupo de Recursos                 | Selecione Criar novo, insira **o myResourceGroup,** em seguida, selecione OK ou selecione um grupo de recursos existente. |
    | **Detalhes do conjunto de escala**          |                                                                                                       |
    | Nome do conjunto de dimensionamento de máquinas virtuais | **Insira myVMSS**                                                                                      |
    | Região                         | Selecione **East US 2**                                                                                    |
    | Zona de disponibilidade              | Selecione **Nenhum**                                                                                       |
    | **Detalhes da instância**           |                                                                                                       |
    | Imagem                          | Selecione **Ubuntu Server 18.04 LTS**                                                                    |
    | Instância do Spot Azure            | Selecione **Não**                                                                                         |
    | Tamanho                           | Sair em padrão                                                                                      |
    | **Conta de administrador**      |                                                                                                       |
    | Tipo de autenticação            | Selecione **Palavra-passe**                                                                                   |
    | Nome de utilizador                       | Introduza o seu nome de utilizador administrativo        |
    | Palavra-passe                       | Insira a sua senha de administração    |
    | Confirmar palavra-passe               | Reintroduza a sua senha de administração |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Crie um conjunto de escala de máquina virtual." border="true":::

4. Selecione o separador **Networking.**

5. Introduza ou selecione estas informações no **separador Networking:**

     Definição                           | Valor                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Configuração da rede virtual** |                                                          |
    | Rede virtual                   | Selecione **myVNet** ou a sua rede virtual existente.      |
    | **Balanceamento de carga**                |                                                          |
    | Utilize um equilibrador de carga               | Selecione **Sim**                                           |
    | **Definições de equilíbrio de carga**       |                                                          |
    | Opções de equilíbrio de carga            | Selecione **equilibrador de carga Azure**                           |
    | Selecione um equilibrador de carga            | Selecione **myLoadBalancer** ou o seu equilibrador de carga existente |
    | Selecione uma piscina de backend             | Selecione **myBackendPool** ou a sua piscina de backend existente.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Crie um conjunto de escala de máquina virtual." border="true":::

6. Selecione o separador **Gestão.**

7. No separador **Gestão,** desloque os **diagnósticos da Bota** para **desligar**.

8. Selecione o botão azul **Review + criar.**

9. Reveja as definições e selecione o botão **Criar.**

## <a name="next-steps"></a>Passos seguintes

Neste artigo, implementou um conjunto de escala de máquina virtual com um Equilíbrio de Carga Azure existente.  Para saber mais sobre conjuntos de escala de máquinas virtuais e equilibrador de carga, consulte:

- [O que é o Balanceador de Carga do Azure?](load-balancer-overview.md)
- [O que são os conjuntos de dimensionamento de máquinas virtuais?](../virtual-machine-scale-sets/overview.md)
