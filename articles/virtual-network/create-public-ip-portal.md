---
title: Criar um IP público - Portal Azure
description: Saiba como criar um IP público no portal Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: c1ac3f2fa1ef3f1a24077064ad3ad3f3c30c5f3f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048362"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Criar um endereço IP público utilizando o portal Azure

Este artigo mostra como criar um recurso de endereço IP público utilizando o portal Azure. 

Para obter mais informações sobre recursos este IP público pode ser associado e a diferença entre os SKUs básicos e padrão, consulte [endereços IP públicos](./public-ip-addresses.md). 

Este artigo centra-se nos endereços IPv4. Para obter mais informações sobre endereços IPv6, consulte [o IPv6 para Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-create-public-ip-standard-zones)

Utilize os seguintes passos para criar um endereço IP público redundante de zona padrão chamado **myStandardZRPublicIP**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, insira **o endereço IP público.** Selecione **o endereço IP público** nos resultados da pesquisa.
4. Na página **de endereços IP público,** selecione **Criar**.
5. Na página **de endereço IP público,** ou selecione as seguintes informações: 

    | Definição                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecione IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Nível*                   | Selecione **Regional**         |
    | Name                    | Insira **o myStandardZRPublicIP**          |
    | Atribuição de endereços IP   | Note que esta seleção está bloqueada como "Estática"                                        |
    | Preferência de encaminhamento      | Deixe o padrão da **rede Microsoft**. </br> Para obter mais informações sobre a preferência de encaminhamento, consulte [o que é a preferência de encaminhamento (pré-visualização)?](./routing-preference-overview.md) |
    | Tempo de inatividade (minutos)  | Deixe o padrão de **4**.        |
    | Etiqueta de nome DNS          | Deixe o valor em branco.    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | **Selecione Criar novo,** insira **o myResourceGroup**. </br> Selecione **OK**. |
    | Localização                | Selecione **East US 2**      |
    | Zona de Disponibilidade       | Selecione **Zona-Redundante,** Sem Zona ou escolha zona específica (ver nota abaixo) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Criar endereço IP padrão no portal Azure" border="false":::

> [!NOTE]
> Estas seleções são válidas em regiões com [Zonas de Disponibilidade.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) </br>
Você pode selecionar uma zona específica nestas regiões, embora não seja resiliente a falhas zonais. </br> Para obter mais informações sobre as zonas de disponibilidade, consulte [a visão geral das zonas de disponibilidade.](../availability-zones/az-overview.md)

\* = O nível diz respeito à funcionalidade do [balançador de carga cross-region,](../load-balancer/cross-region-overview.md) atualmente em Pré-visualização.

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Nesta secção, crie um endereço IP público básico chamado **myBasicPublicIP**. 

> [!NOTE]
> Os IPs públicos básicos não suportam zonas de disponibilidade.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, insira **o endereço IP público.** Selecione **o endereço IP público** nos resultados da pesquisa.
4. Na página **de endereços IP público,** selecione **Criar**.
5. Na página **de endereço IP público,** ou selecione as seguintes informações: 

    | Definição                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecione IPv4                 |    
    | SKU                     | Selecione **Basic**         |
    | Name                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereços IP   | Selecione **Estática** (ver nota abaixo)                                     |
    | Tempo de inatividade (minutos)  | Deixe o padrão de **4**.       |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | **Selecione Criar novo,** insira **o myResourceGroup**. </br> Selecione **OK**. |
    | Localização                | Selecione **East US 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Criar endereço IP padrão no portal Azure" border="false":::

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** pode ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais informações sobre os campos individuais acima [indicados, consulte Gerir os endereços IP públicos](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).