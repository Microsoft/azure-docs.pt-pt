---
title: Pré-requisitos para configurar o emparelhamento com a Microsoft
titleSuffix: Azure
description: Pré-requisitos para configurar o emparelhamento com a Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775410"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Pré-requisitos para configurar o emparelhamento com a Microsoft

Verifique se os pré-requisitos abaixo foram atendidos antes de solicitar um novo emparelhamento ou converter um emparelhamento herdado no recurso do Azure.

## <a name="azure-related-prerequisites"></a>Pré-requisitos relacionados ao Azure
* **Conta de Microsoft Azure:** Se você não tiver uma conta de Microsoft Azure, crie uma [conta de Microsoft Azure](https://azure.microsoft.com/free). Uma assinatura válida e ativa do Microsoft Azure é necessária para configurar o emparelhamento, pois os emparelhamentos são modelados como recursos nas assinaturas do Azure. É importante observar que:
    * Os tipos de recursos do Azure usados para configurar o emparelhamento são produtos do Azure sempre gratuitos, ou seja, você não é cobrado pela criação de uma conta do Azure ou pela criação de uma assinatura ou acesso aos recursos do Azure **PeerAsn** e **emparelhamento** para configurar o emparelhamento. Isso não deve ser confundido com o contrato de emparelhamento para emparelhamento direto entre você e a Microsoft, os termos que são discutidos explicitamente com nossa equipe de emparelhamento. Entre em contato com o [emparelhamento da Microsoft](mailto:peering@microsoft.com) em caso de dúvidas.
    * Você pode usar a mesma assinatura do Azure para acessar outros produtos do Azure ou serviços de nuvem que podem ser gratuitos ou pagos. Ao acessar um produto pago, você incorrerá em encargos.
    * Se você estiver criando uma nova conta e/ou assinatura do Azure, você poderá estar qualificado para obter o crédito do Azure gratuito durante um período de avaliação que pode ser utilizado para experimentar os serviços de nuvem do Azure. Se estiver interessado, visite [Microsoft Azure conta](https://azure.microsoft.com/free) para obter mais informações.

* **Associar o ASN do par:** Antes de solicitar o emparelhamento, primeiro associe suas informações de ASN e de contato à sua assinatura. Siga as instruções em [associar o ASN do peer à assinatura do Azure](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Outros pré-requisitos
* **Perfil de PeeringDB:** Espera-se que os pares tenham um perfil completo e atualizado em [PeeringDB](https://www.peeringdb.com). Usamos essas informações em nosso sistema de registro para validar os detalhes do par, como informações do NOC, informações de contato técnico e sua presença nos recursos de emparelhamento, etc.

## <a name="next-steps"></a>Passos seguintes

* [Crie ou modifique um emparelhamento direto usando o portal](howto-direct-portal.md).
* [Converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)