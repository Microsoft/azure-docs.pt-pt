---
title: Criar um IP público - Portal Azure
description: Saiba como criar um IP público no portal Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550240"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Quickstart: Criar um endereço IP público utilizando o portal Azure

Este artigo mostra-lhe como criar um recurso de endereço IP público utilizando o portal Azure. Para obter mais informações sobre quais os recursos a que estes recursos podem estar associados, a diferença entre O SKU Básico e Padrão, e outras informações [relacionadas, consulte endereços IP públicos.](./public-ip-addresses.md)  Para este exemplo, centrar-nos-emos apenas nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-create-public-ip-standard-zones)

Utilize os seguintes passos para criar um endereço IP público redundante de zona padrão chamado **myStandardZRPublicIP**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, *digite o endereço IP público.*
4. Nos resultados da pesquisa, selecione **o endereço IP público.** Em seguida, na página **de endereços IP público,** selecione **Criar**.
5. Na página **de endereço IP público,** insira ou selecione as seguintes informações: 

    | Definição                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecione IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Tier (se mostrado*)                  | Selecione **Regional**         |
    | Name                    | Insira *o myStandardZRPublicIP*          |
    | Atribuição de endereços IP   | Note que isto será bloqueado como "Estática"                                        |
    | Tempo de inatividade (minutos)  | Deixe o valor a 4        |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | Selecione **Criar novo,** insira o myResourceGroup e, em seguida, selecione **OK** |
    | Localização                | Selecione **East US 2**      |
    | Zona de Disponibilidade       | Selecione **Zona-Redundante,** Sem Zona ou escolha zona específica (ver nota abaixo) |

Note que estas são apenas seleções válidas em regiões com [Zonas de Disponibilidade.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)  (Também pode selecionar uma zona específica nestas regiões, embora não seja resiliente à falha zonal.)  Para obter mais informações sobre as zonas de disponibilidade, consulte [a visão geral das zonas de disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview)

\* = O nível diz respeito à funcionalidade [Balancer de Carga cross-region,](../load-balancer/cross-region-overview.md) atualmente em Pré-visualização.

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Utilize os seguintes passos para criar um endereço IP público estático básico chamado **myBasicPublicIP**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, *digite o endereço IP público.*
4. Nos resultados da pesquisa, selecione **o endereço IP público.** Em seguida, na página **de endereços IP público,** selecione **Criar**.
5. Na página **de endereço IP público,** insira ou selecione as seguintes informações: 

    | Definição                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecione IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Name                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereços IP   | Escolha **Estática** (ver nota abaixo)                                     |
    | Tempo de inatividade (minutos)  | Deixe o valor a 4        |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | Selecione **Criar novo,** insira o myResourceGroup e, em seguida, selecione **OK** |
    | Localização                | Selecione **East US 2**      |

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** pode ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre os campos listados acima, consulte [Gerir os endereços IP públicos.](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual](./associate-public-ip-address-vm.md#azure-portal)
- Saiba mais sobre [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).
