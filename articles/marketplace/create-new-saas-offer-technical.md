---
title: Como adicionar detalhes técnicos para a sua oferta SaaS no Microsoft Partner Center
description: Saiba como fornecer detalhes técnicos para o seu software como uma oferta de serviço (SaaS) para o mercado comercial da Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 28c0be40387f411286230f94c19fa23a80e650af
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746408"
---
# <a name="how-to-add-technical-details-for-your-saas-offer"></a>Como adicionar detalhes técnicos para a sua oferta SaaS

Este artigo descreve como introduzir detalhes técnicos que ajudam o mercado comercial da Microsoft a ligar-se à sua solução. Esta ligação permite-nos oferecer a sua oferta ao cliente se optarem por adquiri-la e geri-la. Para obter mais detalhes sobre estas definições, consulte [informações técnicas](plan-saas-offer.md#technical-information).

> [!NOTE]
> Se optar por processar transações de forma independente, não verá esta opção. Em vez disso, salte para [como vender a sua oferta SaaS.](create-new-saas-offer-marketing.md)

## <a name="technical-configuration"></a>Configuração técnica

No separador **de configuração Técnica,** definirá os detalhes técnicos que o mercado comercial utiliza para comunicar à sua aplicação ou solução SaaS. 

- **URL da página de aterragem** (obrigatório) – Defina o URL do site saaS (por exemplo: `https://contoso.com/signup` ) em que os clientes aterrarão após adquirirem a sua oferta no mercado comercial e desencadeando o processo de configuração a partir da recém-criada subscrição SaaS.

  > [!IMPORTANT]
  > A sua página de aterragem deve estar a funcionar 24 horas por dia, 7 horas por dia. Esta é a única forma de ser notificado sobre novas compras das suas ofertas SaaS efetuadas no mercado comercial, ou pedidos de configuração de uma subscrição ativa de uma oferta.

- **Connection webhook** (obrigatório) – Para todos os eventos assíncronos que a Microsoft precisa de lhe enviar (por exemplo, a subscrição do SaaS foi cancelada), exigimos que forneça um URL webhook de ligação. Vamos chamar este URL para notificá-lo do evento.

  > [!IMPORTANT]
  > O seu webhook deve estar a funcionar 24 horas por dia, 7 dias por semana, uma vez que esta é a única forma de ser notificado sobre as atualizações sobre as subscrições saaS dos seus clientes que são adquiridas através do mercado comercial.

- **ID do inquilino do Azure Ative Directory** (obrigatório) – Para encontrar o ID do inquilino para a sua aplicação Azure Ative Directory (Azure AD), vá à lâmina [de registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App em Azure Ative Directory. Na coluna **'Mostrar' o nome,** selecione a aplicação. Em seguida, procure o número de ID do **Diretório (inquilino)** listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

- **ID de aplicação do Azure Ative Directory** (obrigatório) – Para encontrar o ID da sua [aplicação,](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)aceda à lâmina [de registos](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no Azure Ative Directory. Na coluna **'Mostrar' o nome,** selecione a aplicação. Em seguida, procure o número de ID de aplicação (cliente) listado (por exemplo, `50c464d3-4930-494c-963c-1e951d15360e` ).

**Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Planview.

## <a name="next-steps"></a>Passos seguintes

- [Como criar planos para a sua oferta SaaS.](create-new-saas-offer-plans.md)