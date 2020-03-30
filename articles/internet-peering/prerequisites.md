---
title: Pré-requisitos para configurar o peering com a Microsoft
titleSuffix: Azure
description: Pré-requisitos para configurar o peering com a Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775410"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Pré-requisitos para configurar o peering com a Microsoft

Certifique-se de que os pré-requisitos abaixo são cumpridos antes de solicitar um novo olhar ou converter um legado de observação para o recurso Azure.

## <a name="azure-related-prerequisites"></a>Pré-requisitos relacionados com OIE
* **Conta Microsoft Azure:** Se não tiver uma conta Microsoft Azure, crie uma [conta Microsoft Azure](https://azure.microsoft.com/free). Uma subscrição válida e ativa do Microsoft Azure é necessária para configurar o peering, uma vez que os pares são modelados como recursos dentro das subscrições do Azure. É importante notar que:
    * Os tipos de recursos Azure utilizados para configurar o peering são produtos Azure sempre gratuitos, ou seja, não é cobrado por criar uma conta Azure ou criar uma subscrição ou aceder aos recursos Azure **PeerAsn** e **Peering** para configurar o peering. Isto não deve ser confundido com o acordo de peering para o peering direto entre si e a Microsoft, os termos para os quais são explicitamente discutidos com a nossa equipa de pares. Contacte [a Microsoft para ver](mailto:peering@microsoft.com) se houver alguma dúvida a este respeito.
    * Pode utilizar a mesma subscrição Azure para aceder a outros produtos Azure ou serviços na nuvem que podem ser gratuitos ou pagos. Quando aceder a um produto pago, incorrerá em encargos.
    * Se estiver a criar uma nova conta Azure e/ou subscrição, poderá ser elegível para crédito Azure gratuito durante um período experimental que poderá utilizar para experimentar os serviços da Azure Cloud. Se estiver interessado, visite o [Microsoft Azure](https://azure.microsoft.com/free) para obter mais informações.

* **Peer AsN associado:** Antes de solicitar o peering, primeiro associe o seu ASN e contacte informações à sua subscrição. Siga as instruções no [Associate Peer ASN para a Assinatura Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Outros pré-requisitos
* **Perfil peeringDB:** Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Utilizamos esta informação no nosso sistema de registo para validar os detalhes do par, tais como informações noC, informações técnicas de contacto e sua presença nas instalações de observação, etc.

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique um epeering direto utilizando o portal](howto-direct-portal.md).
* [Converter um peering Direto legado para o recurso do Azure com o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um peering do Exchange legado para o recurso do Azure com o portal](howto-legacy-exchange-portal.md)