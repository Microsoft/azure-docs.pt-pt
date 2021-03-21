---
title: Aumentar a resiliência dos pedidos de autenticação e autorização que desenvolve
titleSuffix: Microsoft identity platform
description: Visão geral da nossa orientação de resiliência para o desenvolvimento de aplicações usando o Azure Ative Directory e a plataforma de identidade microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: d06e851390537bf94b59e656f84bf58fe7216410
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317357"
---
# <a name="increase-resilience-of-authentication-and-authorization-applications-you-develop"></a>Aumentar a resiliência dos pedidos de autenticação e autorização que desenvolve

A Microsoft Identity utiliza a autenticação e a autorização modernas baseadas em símbolos. Isto significa que uma aplicação adquire fichas de um fornecedor de identidade para autenticar o utilizador e autorizar o pedido de chamada de APIs protegidas.

Um token é válido por um certo período de tempo antes que a app tenha de adquirir uma nova. Raramente, uma chamada para recuperar um token pode falhar devido a um problema como falha de rede ou falha de serviço de infraestrutura ou falha de serviço de autenticação. Neste documento, delineamos as medidas que um desenvolvedor pode tomar para aumentar a resiliência nas suas aplicações se ocorrer uma falha de aquisição simbólica.

Estes artigos fornecem orientações sobre o aumento da resiliência em aplicações que utilizam a plataforma de identidade da Microsoft e o Azure Ative Directory. Existe orientação para ambas as aplicações do cliente que funcionam em nome de um utilizador assinado, bem como aplicações daemon que funcionam em seu próprio nome. Contêm as melhores práticas para a utilização de fichas, bem como para chamar recursos.

- [Criar resiliência em aplicações que inscrevam utilizadores](resilience-client-app.md)
- [Criar resiliência em aplicações sem utilizadores](resilience-daemon-app.md)
- [Construa resiliência na sua infraestrutura de gestão de identidade e acessos](resilience-in-infrastructure.md)
- [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)
