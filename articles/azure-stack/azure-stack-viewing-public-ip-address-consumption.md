---
title: Ver o consumo de endereços IP públicos no Azure Stack | Documentos da Microsoft
description: Os administradores podem ver o consumo de endereços IP públicos numa região
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2cb1dde60f8d8aa76e0b678347df9be120c39e7c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782110"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Ver o consumo de endereços IP públicos no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um administrador da nuvem, pode exibir:
 - O número de endereços IP públicos que teriam sido alocados aos inquilinos.
 - O número de endereços IP públicos que ainda estão disponíveis para alocação.
 - A percentagem de endereços IP públicos que alocou nessa localização.

O **utilização de conjuntos de IP público** mosaico mostra o número de endereços IP públicos consumidos em conjuntos de endereços IP públicos. Para cada endereço IP, o mosaico mostra a utilização do inquilino VM de IaaS instâncias, os serviços de infraestrutura de recursos de infraestrutura e recursos de endereço IP público que foram criados explicitamente pelos inquilinos.

O objetivo do mosaico é dar uma noção do número de endereços IP públicos utilizados nesta localização de operadores do Azure Stack. O número ajuda os administradores de determinar se estão sendo executados baixas neste recurso.

O **endereços IP públicos** item de menu **recursos de inquilinos** lista apenas os endereços IP públicos que tenham sido *criados explicitamente por inquilinos*. Pode encontrar o item de menu sobre o **fornecedores de recursos**, **rede** painel. O número de **utilizado** endereços IP públicos no **utilização de conjuntos de IP público** mosaico sempre é diferente do (superior) o número no **endereços IP públicos** mosaico em  **Recursos de inquilino**.

## <a name="view-the-public-ip-address-usage-information"></a>Ver as informações de utilização de endereço IP públicas

Para ver o número total de endereços IP públicos que consumiram na região:

1. No portal de administrador do Azure Stack, selecione **todos os serviços**. Em seguida, no **ADMINISTRATION** selecionar categoria **rede**.
1. O **rede** painel apresenta o **utilização de conjuntos de IP público** peça de mosaico no **descrição geral** secção.

![Painel de fornecedor de recursos de rede](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

O **utilizado** número representa o número de endereços IP públicos atribuídos de conjuntos de endereços IP públicos. O **gratuito** conjuntos que ainda não foram atribuídos e ainda estão disponíveis de endereços de número representa o número de IP público de endereços de IP público. O **utilizada** número representa o número de utilizada ou endereços atribuídos como uma percentagem do número total de endereços IP públicos em conjuntos de endereços IP públicos nessa localização.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Ver os endereços IP públicos, que foram criados pelas subscrições de inquilino

Selecione **endereços IP públicos** sob **recursos de inquilino**. Reveja a lista de endereços IP públicos criados explicitamente por subscrições de inquilinos numa região específica.

![Endereços IP públicos do inquilino](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

Pode observar que alguns endereços IP públicos que teriam sido alocados dinamicamente são apresentados na lista. No entanto, um endereço não tem sido associado a elas ainda. Foi criado o recurso de endereço de fornecedor de recursos de rede, mas ainda não no controlador de rede.

O controlador de rede não atribui um endereço para o recurso até que ele liga a uma interface, um cartão de interface de rede (NIC), um balanceador de carga ou a um gateway de rede virtual. Quando associa o endereço IP público a uma interface, o controlador de rede aloca um endereço IP. O endereço aparecerá no **endereço** campo.

## <a name="view-the-public-ip-address-information-summary-table"></a>Ver a pública IP endereço informações tabela de resumo

Em casos diferentes, são atribuídos endereços IP públicos que determinam se o endereço aparecerá numa lista ou de outra.

| **Caso de atribuição de endereço IP público** | **É apresentada no resumo de utilização** | **É apresentado na lista de endereços IP pública para o inquilino** |
| --- | --- | --- |
| Endereço IP público dinâmico ainda não foi atribuído a um NIC ou Balanceador de carga (temporário) |Não |Sim |
| Endereço IP público dinâmico atribuído a um NIC ou Balanceador de carga. |Sim |Sim |
| Endereço IP público estático atribuído a um inquilino NIC ou Balanceador de carga. |Sim |Sim |
| Endereço IP público estático atribuído a um ponto de final de serviço da infraestrutura de recursos de infraestrutura. |Sim |Não |
| Implicitamente, o endereço IP público criado para instâncias de VM de IaaS e utilizado para NAT de saída na rede virtual. Estes são criados em segundo plano sempre que um inquilino cria uma instância VM para que VMs podem enviar informações para a Internet. |Sim |Não |

## <a name="next-steps"></a>Passos Seguintes

[Gerir contas de armazenamento no Azure Stack](azure-stack-manage-storage-accounts.md)