---
title: 'Quickstart: Criar um portal NAT - Portal Azure'
titlesuffix: Azure Virtual Network NAT
description: Este quickstart mostra como criar um portal NAT usando o portal Azure
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066393"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Quickstart: Criar um portal NAT utilizando o portal Azure

Este quickstart mostra-lhe como utilizar o serviço AZURE Virtual Network NAT. Você vai criar uma porta de entrada NAT para fornecer conectividade de saída para uma máquina virtual em Azure. 

Se preferir, pode fazer estes passos utilizando o [Azure CLI,](quickstart-create-nat-gateway-cli.md) [Azure PowerShell,](quickstart-create-nat-gateway-powershell.md)ou implementar um [modelo ARM](quickstart-create-nat-gateway-powershell.md) em vez do portal.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Rede virtual e parâmetros

Antes de implementar um VM e poder usar o seu portal NAT, precisamos de criar o grupo de recursos e a rede virtual.

Nesta secção terá de substituir os seguintes parâmetros nos passos com as informações abaixo:

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<>de nome de grupo de recursos**  | myResourceGroupNAT |
| **\<>de nome de rede virtual** | myVNet          |
| **\<>de nome da região**          | E.U.A. Leste 2      |
| **\<>espaço de endereçoI4**   | 192.168.0.0\16          |
| **\<>de nome de subnet**          | mySubnet        |
| **\<>de endereços-endereço de subnet** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Crie um VM para usar o gateway NAT

Vamos agora criar um VM para usar o serviço NAT. Este VM tem um IP público para usar como um IP público de nível de instância para permitir o acesso ao VM. O serviço NAT está consciente e substituirá o destino de Internet padrão na sua subnet. O endereço IP público da VM não será usado para ligações de saída.

1. No lado superior esquerdo do portal, selecione **Criar um recurso** > **Compute** > **Ubuntu Server 18.04 LTS,** ou procurar **ubuntu Server 18.04 LTS** na pesquisa do Marketplace.

2. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**
   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **myResourceGroupNAT**.
   - **Detalhes da** > ocorrência**Nome da máquina virtual**: **Digite myVM**.
   - **Detalhes de instância****Região** > selecionar **Leste US 2**. > 
   - **Conta administrador** > Tipo de**autenticação**: Selecione **Palavra-passe**.
   - **Conta administrador>** Insira o nome de **utilizador,** **palavra-passe**e confirme as informações de **senha.**
   - **Regras de entrada** > nas**portas de entrada As portas de entrada públicas**: Selecione Permitir portas **selecionadas**.
   - **Regras da** > porta de entrada**Selecione portas de entrada**: Selecione **SSH (22)**
   - Selecione o separador **de rede** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**.

3. No separador **de rede** certifique-se de que são selecionados os seguintes:
   - **Rede virtual**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Ip público** > Selecione **Criar novo**.  Na janela **de endereços IP públicos Criar,** digite **o myPublicIPVM** no campo **Nome** e escolha **Standard** para o **SKU**.  Clique em **OK**.
   - **Grupo de segurança da rede NIC**: Selecione **Basic**.
   - **Portas de entrada públicas**: Selecione **Permitir portas selecionadas**.
   - **Selecione portas de entrada**: Confirme se está selecionado **o SSH.**

4. No separador **Gestão,** sob **monitorização,** desembare **os diagnósticos da bota** para **desligar**.

5. Selecione **Rever + criar**. 

6. Reveja as definições e clique em **Criar**.

## <a name="create-the-nat-gateway"></a>Criar o portal NAT

Pode utilizar um ou mais recursos públicos de endereçoip, prefixos IP públicos, ou ambos. Adicionaremos um recurso IP público, prefixo ip público, e um recurso de gateway NAT.

Esta secção detalha como pode criar e configurar os seguintes componentes do serviço NAT utilizando o recurso de gateway NAT:
  - Um pool ip público e um prefixo IP público para usar para fluxos de saída traduzidos pelo recurso de gateway NAT.
  - Mude o tempo de inatividade do padrão de 4 minutos para 10 minutos.

### <a name="create-a-public-ip-address"></a>Crie um endereço IP público

1. No lado superior esquerdo do portal, selecione Criar um**endereço IP público**em**rede** > de **recursos,** > ou procurar endereço **IP público** na pesquisa do Mercado.

2. No **Endereço IP do Público,** insira ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | Versão IP | Selecione **IPv4**.
    | SKU | Selecione **Standard**.
    | Nome | Insira **o meu PublicIP**. |
    | Subscrição | Selecione a sua subscrição.|
    | Grupo de recursos | Selecione **myResourceGroupNAT**. |
    | Localização | Selecione **E.U.A. Leste 2**.|

3. Deixe o resto dos predefinições e selecione **Criar**.

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

1. No lado superior esquerdo do portal, selecione Criar um**prefixo IP público**em**rede** > de **recursos,** > ou procurar **prefixo IP público** na pesquisa do Mercado. 

2. Em **Criar um prefixo IP público,** digite ou selecione os **seguintes valores** no separador Basics:
   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **myResourceGroupNAT**>
   - **Detalhes da** > instância**Nome**: Digite **myPublicIPprefix**.
   - **Detalhes da** > instância**Região**: Selecione **East US 2**.
   - **Detalhes da** > **prefixação:** Selecione **/31 (2 endereços)**

3. Deixe o resto os defeitos e selecione **Rever + criar**.

4. Reveja as definições e, em seguida, selecione **Criar**.
   

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway NAT

1. No lado superior esquerdo do portal, selecione Criar um**portal NAT**de**rede** > de **recursos,** > ou procurar gateway **NAT** na pesquisa do Mercado.

2. Na Criação de gateway de tradução de **endereços de rede (NAT),** escreva ou selecione os **seguintes valores** no separador Basics:
   - **Subscription** > **Grupo de recursos**de subscrição : Selecione **myResourceGroupNAT**.
   - **Detalhes da** > instância**nome gateway NAT**: Digite **myNATgateway**.
   - **Detalhes da** > instância**Região**: Selecione **East US 2**.
   - **Pormenores da** > ocorrência **(minutos)**: Tipo **10**.
   - Selecione o **separador IP público** ou selecione **Seguinte: IP público**.

3. No **separador IP público,** escreva ou selecione os seguintes valores:
   - **Endereços IP públicos**: Selecione **myPublicIP**.
   - **Prefixos IP públicos**: Selecione **myPublicIPprefix**.
   - Selecione o separador **Subnet** ou selecione **Seguinte: Subnet**.

4. No separador **Subnet,** escreva ou selecione os seguintes valores:
   - **Rede Virtual**: Selecione **myResourceGroupNAT** > **myVnet**.
   - **Nome da sub-rede**: Selecione a caixa ao lado da **minha Subnet**.

5. Selecione **Rever + criar**.

6. Reveja as definições e, em seguida, selecione **Criar**.

## <a name="discover-the-ip-address-of-the-vm"></a>Descubra o endereço IP do VM

1. No lado esquerdo do portal, selecione **Grupos de Recursos**.
2. Selecione **myResourceGroupNAT**.
3. Selecione **myVM**.
4. Em **resumo,** copie o valor do **endereço IP público** e cole no bloco de notas para que possa usá-lo para aceder ao VM.

>[!IMPORTANT]
>Copie o endereço IP público e, em seguida, cole-o num bloco de notas para que possa usá-lo para aceder ao VM.

## <a name="sign-in-to-vm"></a>Inscreva-se na VM

Abra uma [Nuvem Azure](https://shell.azure.com) Shell no seu navegador. Utilize o endereço IP recuperado no passo anterior para sSH para a máquina virtual.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o portal NAT e todos os recursos conexos. Selecione o grupo de recursos **myResourceGroupNAT** que contém o gateway NAT e, em seguida, **selecione Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou um portal NAT e um VM para usá-lo. 

Reveja as métricas no Monitor Azure para ver o seu serviço NAT a funcionar. Diagnosticar problemas como a exaustão de recursos das portas SNAT disponíveis.  O esgotamento dos recursos das portas SNAT é abordado adicionando recursos de endereços IP públicos adicionais ou recursos públicos de prefixo IP ou ambos.


- Conheça a [Rede Virtual Azure NAT](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway NAT.](./nat-gateway-resource.md)
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Arranque rápido para a implementação de [recurso de gateway NAT utilizando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Arranque rápido para a implementação do [recurso de gateway NAT utilizando](./quickstart-create-nat-gateway-portal.md)o portal Azure .
> [!div class="nextstepaction"]

