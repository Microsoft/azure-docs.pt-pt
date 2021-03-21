---
title: Pré-requisitos para configurar o peering com a Microsoft
titleSuffix: Azure
description: Pré-requisitos para configurar o peering com a Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586845"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Pré-requisitos para configurar o peering com a Microsoft

Certifique-se de que os pré-requisitos abaixo são cumpridos antes de solicitar um novo espreitamento ou converter um legado que espreita para o recurso Azure.

## <a name="azure-related-prerequisites"></a>Pré-requisitos relacionados com a Azure
* **Conta Microsoft Azure:** Se não tiver uma conta Microsoft Azure, crie uma [conta Microsoft Azure](https://azure.microsoft.com/free). Uma subscrição válida e ativa do Microsoft Azure é necessária para configurar o seu espreitamento, uma vez que os seus pares são modelados como recursos dentro das subscrições do Azure. É importante notar que:
    * Os tipos de recursos Azure utilizados para configurar o espreitamento são produtos Azure sempre gratuitos, ou seja, você não é cobrado para criar uma conta Azure ou criar uma subscrição ou aceder aos recursos Azure **PeerAsn** e **Peering** para configurar o seu esprete. Isto não deve ser confundido com o acordo de espreitar para o olhar direto entre si e a Microsoft, os termos para os quais são explicitamente discutidos com a nossa equipa de observação. Contacte [a Microsoft em](mailto:peering@microsoft.com) caso de dúvida a este respeito.
    * Pode utilizar a mesma subscrição Azure para aceder a outros produtos Azure ou serviços na nuvem que podem ser gratuitos ou pagos. Ao aceder a um produto pago, incorrerá em encargos.
    * Se estiver a criar uma nova conta E/ou subscrição da Azure, poderá ser elegível para crédito Azure gratuito durante um período experimental que poderá utilizar para experimentar os serviços da Azure Cloud. Se estiver interessado, visite a [conta do Microsoft Azure](https://azure.microsoft.com/free) para obter mais informações.

* **Associado Peer ASN:** Antes de solicitar o seu espreitamento, primeiro associe a sua ASN e contacte as informações da sua subscrição. Siga as instruções em [Associado Peer ASN para Azure Subscription](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Outros pré-requisitos
* **Perfil peeringDB:** Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Utilizamos estas informações no nosso sistema de registo para validar os detalhes do par, tais como informações NOC, informações técnicas de contacto e a sua presença nas instalações de observação, etc.

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um espreitamento direto utilizando o portal](howto-direct-portal.md).
* [Converter um peering Direto legado para o recurso do Azure com o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o persto cambial usando o portal](howto-exchange-portal.md)
* [Converter um peering do Exchange legado para o recurso do Azure com o portal](howto-legacy-exchange-portal.md)