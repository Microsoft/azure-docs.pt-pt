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
ms.openlocfilehash: 49a89ee90d28c9c7a3f59424b773ee0f221381e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89302727"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Quickstart: Criar um endereço IP público utilizando o portal Azure

Este artigo mostra-lhe como criar um recurso de endereço IP público utilizando o portal Azure. Para obter mais informações sobre quais os recursos a que estes recursos podem estar associados, a diferença entre O SKU Básico e Padrão, e outras informações [relacionadas, consulte endereços IP públicos.](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)  Para este exemplo, centrar-nos-emos apenas nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para Azure VNet](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**SKU Padrão - Utilização de zonas**](#tab/option-create-public-ip-standard-zones)

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
    | Nome                    | Insira *o myStandardZRPublicIP*          |
    | Atribuição de endereços IP   | Note que isto será bloqueado como "Estática"                                        |
    | Tempo de inatividade (minutos)  | Deixe o valor a 4        |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | Selecione **Criar novo,** insira o myResourceGroup e, em seguida, selecione **OK** |
    | Localização                | Selecione **East US 2**      |
    | Zona de Disponibilidade       | Selecione **Zona-Redundante** ou escolha zona específica (ver nota abaixo) |

Note que estas são apenas seleções válidas em regiões com [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)  (Também pode selecionar uma zona específica nestas regiões, embora não seja resiliente à falha zonal.)

# <a name="standard-sku---no-zones"></a>[**SKU Padrão - Sem zonas**](#tab/option-create-public-ip-standard)

Utilize os seguintes passos para criar um endereço IP público padrão como um recurso não zonal chamado **myStandardPublicIP**.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Criar um recurso**. 
3. Na caixa de pesquisa, *digite o endereço IP público.*
4. Nos resultados da pesquisa, selecione **o endereço IP público.** Em seguida, na página **de endereços IP público,** selecione **Criar**.
5. Na página **de endereço IP público,** insira ou selecione as seguintes informações: 

    | Definição                 | Valor                       |
    | ---                     | ---                         |
    | Versão IP              | Selecione IPv4                 |    
    | SKU                     | Selecione **Standard**         |
    | Nome                    | Insira *o myStandardPublicIP*          |
    | Atribuição de endereços IP   | Note que isto será bloqueado como "Estática"                                        |
    | Tempo de inatividade (minutos)  | Deixe o valor a 4        |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | Selecione **Criar novo,** insira o myResourceGroup e, em seguida, selecione **OK** |
    | Localização                | Selecione **East US 2**      |
    | Zona de Disponibilidade       | Selecione **No Zone** (e ver nota abaixo) |

Esta seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [Zonas de Disponibilidade.](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones)

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
    | Nome                    | Insira *myBasicPublicIP*          |
    | Atribuição de endereços IP   | Escolha **Estática** (ver nota abaixo)                                     |
    | Tempo de inatividade (minutos)  | Deixe o valor a 4        |
    | Etiqueta de nome DNS          | Deixe o valor em branco    |
    | Subscrição            | Selecione a sua subscrição.   |
    | Grupo de recursos          | Selecione **Criar novo,** insira o myResourceGroup e, em seguida, selecione **OK** |
    | Localização                | Selecione **East US 2**      |

Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** pode ser selecionada.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre os campos listados acima, consulte [Gerir os endereços IP públicos.](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address)

## <a name="next-steps"></a>Passos seguintes
- Associar um [endereço IP público a uma Máquina Virtual](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Saiba mais sobre [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) em Azure.
- Saiba mais sobre todas as [definições de endereços IP públicos](virtual-network-public-ip-address.md#create-a-public-ip-address).