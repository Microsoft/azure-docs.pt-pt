---
title: APIs de preenchimento de SaaS - o Azure Marketplace | Documentos da Microsoft
description: Apresenta as versões do preenchimento de APIs que permitem-lhe integrar o SaaS oferece com o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101358"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment APIs

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
