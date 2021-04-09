---
title: Como adicionar detalhes técnicos para a sua oferta de aplicação Azure
description: Saiba como adicionar detalhes técnicos para a sua oferta de aplicação Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370234"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Como adicionar detalhes técnicos para a sua oferta de aplicação Azure

Este artigo descreve como introduzir detalhes técnicos que ajudam o mercado comercial da Microsoft a ligar-se à sua solução. Esta ligação permite-nos oferecer a sua oferta ao cliente se optarem por adquiri-la e geri-la.

Complete esta secção apenas se a sua oferta incluir uma aplicação gerida que emita eventos de medição usando as APIs de [faturação medidos](partner-center-portal/marketplace-metering-service-apis.md) do Marketplace e terá um serviço que será autenticado com um token de segurança AD Azure. Para mais informações, consulte [as estratégias de autenticação do serviço de medição do Marketplace](partner-center-portal/marketplace-metering-service-authentication.md) nas diferentes opções de autenticação.

## <a name="technical-configuration-offer-level"></a>Configuração técnica (nível de oferta)

O separador **de configuração técnica** só se aplica a si se criar uma aplicação gerida que emite eventos de medição utilizando as [APIs de faturação medidos](partner-center-portal/marketplace-metering-service-apis.md)pelo Marketplace . Em caso afirmativo, então complete os seguintes passos. Caso contrário, vá aos [próximos passos.](#next-steps) 

Para obter mais informações sobre estes campos, consulte [Plano uma oferta de aplicação Azure para o mercado comercial.](plan-azure-application-offer.md#technical-configuration)

1. No separador **de configuração técnica,** forneça o ID do inquilino do **Azure Ative e** o ID da **aplicação Azure Ative Directory** usado para validar a ligação entre os nossos dois serviços está por detrás de uma comunicação autenticada.

1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Planview.

## <a name="next-steps"></a>Passos seguintes

- [Como criar planos para a sua oferta de aplicação Azure](create-new-azure-apps-offer-plans.md)
