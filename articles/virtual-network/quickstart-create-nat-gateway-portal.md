---
title: 'Tutorial: Criar uma porta de entrada NAT - Portal Azure'
titlesuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um gateway NAT usando o portal Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 98989a38a0c1a7f57fc8eba70e064e5d6f2cce8f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671535"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Criar uma porta de entrada NAT utilizando o portal Azure

Este tutorial mostra-lhe como utilizar o serviço Azure Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

Se preferir, pode fazer estes passos utilizando o [Azure CLI](quickstart-create-nat-gateway-cli.md), [Azure PowerShell,](quickstart-create-nat-gateway-powershell.md)ou implementar um [Modelo ARM](quickstart-create-nat-gateway-powershell.md) em vez do portal.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Antes de implementar um VM e poder utilizar o seu gateway NAT, precisamos de criar o grupo de recursos e a rede virtual.

Nesta secção, deverá substituir os seguintes parâmetros nos passos pelas informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | E.U.A. Leste 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Criar um VM para usar o gateway NAT

Vamos agora criar um VM para usar o serviço NAT. Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM. O serviço NAT está consciente da direção do fluxo e substituirá o destino de Internet predefinido na sua sub-rede. O endereço IP público do VM não será utilizado para ligações de saída.

1. No lado superior esquerdo do portal, selecione **Criar um recurso**  >  **Compute**  >  **Ubuntu Server 18.04 LTS**, ou procurar **Ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: **Digite myVM**.
   - **Detalhes da**  >  instância **Região** > selecione **East US 2**.
   - Conta de **administrador**  >  **Tipo de autenticação**: Selecione **Palavra-passe**.
   - **Conta de administrador** > Introduza o **nome de utilizador,** **palavra-passe** e confirme as informações **de senha.**
   - **Regras portuárias**  >  de entrada **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Regras portuárias**  >  de entrada **Selecione portas de entrada**: Selecione **SSH (22)**
   - Selecione o **separador 'Rede'** ou selecione **Seguinte: Discos** e, em seguida, **seguinte: Networking**.

3. No **separador Networking** certifique-se de que estão selecionados:
   - **Rede virtual**: **myVnet**
   - **Sub-rede**: **mySubnet**
   - **IP público** > Selecione **Criar novo**.  Na janela **de endereço IP público,** digite **o myPublicIPVM** no campo **Nome** e escolha **Standard** para o **SKU**.  Clique em **OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Selecione portas de entrada**: Confirme que **o SSH** está selecionado.

4. No separador **Gestão,** em **Monitorização,** desemote **os diagnósticos boot** para **desligar**.

5. Selecione **Rever + criar**. 

6. Reveja as definições e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o portal NAT

Você pode usar um ou mais recursos de endereço IP público, prefixos IP públicos, ou ambos. Adicionaremos um recurso IP público, prefixo IP público e um recurso de gateway NAT.

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool IP público e prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Altere o tempo de inatividade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No lado superior esquerdo do portal, selecione **Criar um** endereço IP público em rede de recursos ou procurar endereço  >    >   **IP público** na pesquisa do Marketplace.

2. Para **criar o endereço IP público,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Standard**.
    | Name | Insira **o myPublicIP**. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **myResourceGroupNAT**. |
    | Localização | Selecione **E.U.A. Leste 2**.|

3. Deixe o resto das predefinições e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione **Criar um**  >  prefixo IP público em rede **de** recursos ou  >  procurar **prefixo IP público** na pesquisa do Marketplace. 

2. Na **Criação de um prefixo IP público,** escreva ou selecione os seguintes valores no separador **Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**>
   - **Detalhes da**  >  instância **Nome**: **Digite myPublicIPprefix**.
   - **Detalhes da**  >  instância **Região**: Selecione **Leste US 2**.
   - **Detalhes da**  >  instância **Tamanho do prefixo**: Selecione **/31 (2 endereços)**

3. Deixe o resto as predefinições e selecione **Review + create**.

4. Reveja as definições e, em seguida, **selecione Criar**.
   

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

1. No lado superior esquerdo do portal, selecione **Criar um** gateway NAT  >  **de rede de**  >  recursos, ou procurar por **gateway NAT** na pesquisa do Marketplace.

2. No **Portal de Tradução de Endereços de Rede (NAT),** escreva ou selecione os seguintes valores no **separador Básicos:**
   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupNAT**.
   - **Detalhes da**  >  instância **Nome do gateway NAT**: **Digite myNATgateway**.
   - **Detalhes da**  >  instância **Região**: Selecione **Leste US 2**.
   - **Detalhes da**  >  instância **Intervalo de tempo inativo (minutos)**: Tipo **10**.
   - Selecione o **separador IP público** ou selecione **Seguinte: IP público**.

3. No **separador IP público,** escreva ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIP**.
   - **Prefixos IP públicos**: Selecione **myPublicIPprefix**.
   - Selecione o separador **Sub-rede** ou selecione **Seguinte: Sub-rede**.

4. No separador **Sub-rede,** escreva ou selecione os seguintes valores:
   - **Rede Virtual**: Selecione **myResourceGroupNAT**  >  **myVnet**.
   - **Nome da sub-rede**: Selecione a caixa ao lado **do mySubnet**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, **selecione Criar**.

## <a name="discover-the-ip-address-of-the-vm"></a>Descubra o endereço IP do VM

1. No lado esquerdo do portal, selecione **grupos de recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVM**.
4. Em **Visão Geral,** copie o valor **do endereço IP público** e cole-o em bloco de notas para que possa utilizá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo para aceder ao VM.

## <a name="sign-in-to-vm"></a>Inscreva-se em VM

Abra uma [Azure Cloud Shell](https://shell.azure.com) no seu navegador. Utilize o endereço IP recuperado no passo anterior para SSH para a máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Está agora pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos, o gateway NAT e todos os recursos relacionados. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway NAT e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma porta DE NAT e um VM para usá-lo. 

Reveja as métricas no Azure Monitor para ver o seu serviço NAT a funcionar. Diagnosticar problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é abordado adicionando recursos adicionais de endereços IP públicos ou recursos prefixos IP públicos ou ambos.


- Saiba mais sobre [a Rede Virtual Azure NAT](./nat-overview.md)
- Saiba mais sobre [o recurso nat gateway.](./nat-gateway-resource.md)
- Quickstart para implantar [recurso de gateway NAT utilizando O Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart para implantar [recurso de gateway NAT utilizando Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart para implantar [recurso de gateway NAT utilizando o portal Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

