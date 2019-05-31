---
title: APIs de preenchimento de SaaS | O Azure Marketplace
description: Apresenta as versões do preenchimento de APIs que permitem-lhe integrar o SaaS oferece com o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: e7a01af1eba865b0a088b0fa7226273527abd70e
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257411"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment APIs

> [!IMPORTANT] 
> SaaS oferecem funcionalidade tiver sido migrada para o [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Todos os novos editores tem de utilizar Centro de parceiros para novas ofertas de SaaS de criação e gestão de ofertas existentes.  Os publicadores atuais com ofertas de SaaS estão a ser batchwise migrados do Portal de parceiros da Cloud para o Centro de parceiros.  O Portal de parceiros da Cloud irá apresentar mensagens de estado para indicar quando ofertas existentes específicas foram migradas.
> Para obter mais informações, consulte [crie uma nova oferta SaaS](../../partner-center-portal/create-new-saas-offer.md).

As APIs de preenchimento de SaaS de ativar fornecedores independentes de software (ISVs) para integrar as suas aplicações SaaS com o Azure Marketplace. Essas APIs permitem aplicações ISV participar em todos os canais de comércio ativado: direto, liderada por parceiros (revendedor) e orientado por campo.  Eles são um requisito para a listagem transactable ofertas de SaaS no Azure Marketplace.

> [!WARNING]
> A versão atual desta API é a versão 2, que deve ser utilizado para todos os SaaS novo oferece.  Versão 1 da API foi preterido e está a ser mantido para oferecer suporte a ofertas existentes.


## <a name="business-model-support"></a>Suporte de modelo de negócio

Esta API suporta as seguintes capacidades de modelo de negócios; pode:

* Especifique vários esquemas para uma oferta. Estes planos têm diferentes funcionalidades e podem ser um preço diferente.
* Fornecer uma oferta num por site ou um por utilizador de base do modelo de faturação.
* Fornecer mensal e anual (pago inicialmente) opções de faturação.
* Fornece preços privada para um cliente com base num contrato de negócios negociado.


## <a name="next-steps"></a>Passos Seguintes

Se ainda não o fez, registar a aplicação de SaaS no [portal do Azure](https://ms.portal.azure.com) conforme explicado na [registar uma aplicação do Azure AD](./cpp-saas-registration.md).  Em seguida, utilize a versão mais atual desta interface para desenvolvimento: [Versão de API de preenchimento do SaaS 2](./cpp-saas-fulfillment-api-v2.md).
