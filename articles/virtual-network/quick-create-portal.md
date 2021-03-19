---
title: 'Quickstart: Criar uma rede virtual - Portal Azure'
titleSuffix: Azure Virtual Network
description: Neste arranque rápido, aprenda a criar uma rede virtual utilizando o portal Azure.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606073"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Início Rápido: criar uma rede virtual com o Portal do Azure

Neste arranque rápido, aprende-se a criar uma rede virtual utilizando o portal Azure. Coloca-se duas máquinas virtuais (VMs). Em seguida, você comunica de forma segura entre VMs e conecta-se a VMs a partir da internet. Uma rede virtual é o bloco de construção fundamental para a sua rede privada em Azure. Permite que os recursos da Azure, como os VMs, se comuniquem de forma segura entre si e com a internet.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie um de graça.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **a Rede Virtual.** Selecione **Rede Virtual** nos resultados da pesquisa.

3. Na página **'Rede Virtual',** selecione **Criar**.

4. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Criar novo**.  </br> Insira **o myResourceGroup**. </br> Selecione **OK**. |
    | **Detalhes da instância** |   |
    | Name | Introduza **myVNet**. |
    | Region | Selecione **(EUA) Leste DOS** EUA . |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Criar portal Azure de rede virtual" border="true":::

5. Selecione o separador **endereços IP** ou selecione o botão **Seguinte: Endereços IP** na parte inferior da página.

6. No **espaço de endereço IPv4,** selecione o espaço de endereço existente e altere-o para **10.1.0.0/16**.

7. **Selecione + Adicionar sub-rede,** em seguida, introduza **mySubnet** para **o nome da sub-rede** e **10.1.0.0/24** para **intervalo de endereços sub-rede**.

8. Selecione **Adicionar**.

9. Selecione o separador **Segurança** ou selecione o botão **Seguinte: Segurança** na parte inferior da página.

10. Em **BastionHost**, selecione **Enable**. Insira esta informação:

    | Definição            | Valor                      |
    |--------------------|----------------------------|
    | Nome de bastião | Insira **myBastionHost** |
    | Espaço de endereço AzureBastionSubnet | Insira **10.1.1.0/24** |
    | Endereço IP Público | Selecione **Criar novo**. </br> Para **nome,** insira **myBastionIP**. </br> Selecione **OK**. |

11. Selecione o **separador 'Rever +' ou** selecionar o botão **'Rever +' criar.**

12. Selecione **Criar**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM1** |
    | Region | Selecione **(EUA) Leste dos EUA** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Rede pública de portos de entrada | Selecione **Nenhuma**. |
   
5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.
  
6. Reveja as definições e, em seguida, **selecione Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

1. No lado superior esquerdo do portal, selecione **Criar uma** máquina Virtual  >  **compute de** recurso  >  . 
   
2. Na **Criação de uma máquina virtual,** escreva ou selecione os valores no **separador Básicos:**

    | Definição | Valor                                          |
    |-----------------------|----------------------------------|
    | **Detalhes do projeto** |  |
    | Subscrição | selecione a subscrição do Azure |
    | Grupo de Recursos | Selecione **myResourceGroup** |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM2** |
    | Region | Selecione **(EUA) Leste dos EUA** |
    | Opções de Disponibilidade | Selecione **Não é necessário um despedimento de infraestrutura** |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server 2019** |
    | Instância do Azure Spot | Selecione **Não** |
    | Tamanho | Escolha o tamanho VM ou tome a definição predefinida |
    | **Conta de administrador** |  |
    | Nome de utilizador | Insira um nome de utilizador |
    | Palavra-passe | Introduza uma senha |
    | Confirmar palavra-passe | Reintroduza a palavra-passe |
    | **Regras portuárias de entrada** |    |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |

3. Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.
  
4. No separador Networking, selecione ou introduza:

    | Definição | Valor |
    |-|-|
    | **Interface de rede** |  |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **mySubnet** |
    | IP público | Selecione **Nenhum** |
    | Grupo de segurança de rede NIC | Selecione **Basic**|
    | Rede pública de portos de entrada | Selecione **Nenhuma**. |
   
5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.
  
6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="connect-to-myvm1"></a>Ligue-se ao myVM1

1. Vá ao [portal Azure](https://portal.azure.com) para gerir o seu VM privado. Procure e selecione **máquinas Virtuais.**

2. Escolha o nome da sua máquina virtual privada **myVM1**.

3. Na barra de menus VM, selecione **Connect** e, em seguida, selecione **Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Ligue ao myVM1 com Azure Bastion" border="true":::

4. Na página **'Ligar',** selecione o botão **azul Utilizar bastion.**

5. Na página **Bastion,** insira o nome de utilizador e a palavra-passe que criou para a máquina virtual anteriormente.

6. Selecione **Ligar**.

## <a name="communicate-between-vms"></a>Comunicar entre VMs

1. Na ligação bastonária do **myVM1,** abra o PowerShell.

2. Introduza `ping myvm2`.

    Receberá uma mensagem semelhante a esta saída:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Feche a ligação do bastião ao **myVM1**.

4. Complete os passos em [Connect to myVM1,](#connect-to-myvm1)mas ligue-se ao **myVM2**.

5. Abrir PowerShell no **myVM2,** entrar `ping myvm1` .

    Receberá algo como esta mensagem:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Feche a ligação do bastião ao **myVM2**.

## <a name="clean-up-resources"></a>Limpar os recursos

Neste início rápido, criou uma rede virtual predefinida e duas VMs. 

Ligou-se a um VM da internet e comunicou-se de forma segura entre os dois VMs.

Quando terminar de usar a rede virtual e os VMs, elimine o grupo de recursos e todos os recursos que contém:

1. Procure e selecione **myResourceGroup**.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza **o myResourceGroup** para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as definições de rede virtual, consulte [Criar, alterar ou eliminar uma rede virtual.](manage-virtual-network.md)

Para saber mais sobre tipos de comunicações de rede VM, consulte [o tráfego da rede Filter](tutorial-filter-network-traffic.md).
