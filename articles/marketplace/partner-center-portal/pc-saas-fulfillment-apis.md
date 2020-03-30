---
title: Apis de realização do SaaS Mercado Azure
description: Introduz as versões das APIs de realização que lhe permitem integrar as suas ofertas SaaS com o Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275735"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment APIs

As APIs de cumprimento do SaaS permitem que fornecedores de software independentes (ISVs) integrem as suas aplicações SaaS com o Azure Marketplace. Estas APIs permitem que as aplicações ISV participem em todos os canais habilitados ao comércio: diretos, liderados por parceiros (revendedores) e orientados para o campo.  São um requisito para a listagem de ofertas transacionárias saaS no Mercado Azure.

> [!WARNING]
> A versão atual deste API é a versão 2, que deve ser usada para todas as novas ofertas do SaaS.  A versão 1 da API está depreciada e está a ser mantida para suportar as ofertas existentes.


## <a name="business-model-support"></a>Suporte de modelo de negócio

Esta API suporta as seguintes capacidades de modelo de negócio; É possível:

* Especifique vários planos para uma oferta. Estes planos têm funcionalidades diferentes e podem ter um preço diferente.
* Forneça uma oferta por site ou uma base de modelo de faturação por utilizador.
* Fornecer opções mensais e anuais de faturação (paga antecipadamente).
* Fornecer preços privados a um cliente com base num acordo comercial negociado.


## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, registe a sua aplicação SaaS no [portal Azure,](https://ms.portal.azure.com) conforme explicado no [Registo de Uma Aplicação AD Azure](./pc-saas-registration.md).  Em seguida, utilize a versão mais atual desta interface para desenvolvimento: [SaaS Fulfillment API Version 2](./pc-saas-fulfillment-api-v2.md).
