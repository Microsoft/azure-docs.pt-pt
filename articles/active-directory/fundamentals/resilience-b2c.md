---
title: Construa resiliência na Gestão de Identidade e Acesso ao Cliente utilizando o Azure AD B2C ; Microsoft Docs
description: Métodos para construir resiliência na Gestão da Identidade e Acesso do Cliente utilizando o Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba7b8ff2555fd7014cf16f66745721c6425ff868
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029528"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Construa resiliência na sua identidade de cliente e gestão de acesso com o Azure Ative Directory B2C

[O Azure Ative Directory (AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) é uma plataforma de Gestão de Identidade e Acesso (CIAM) que foi concebida para o ajudar a lançar com sucesso as suas aplicações críticas de apoio ao cliente. Dispomos de muitas funcionalidades incorporadas para [a resiliência](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) que são projetadas para ajudar a nossa escala de serviço às suas necessidades e melhorar a resiliência face a potenciais situações de paragem. Além disso, ao lançar uma aplicação crítica de missão, é importante considerar vários elementos de design e configuração na sua aplicação, bem como como a aplicação é configurada dentro do Azure AD B2C para garantir que obtém um comportamento resiliente em resposta a cenários de falha ou falha. Neste artigo, vamos discutir algumas das melhores práticas para ajudá-lo a aumentar a resiliência.

Um serviço resiliente é aquele que continua a funcionar apesar das perturbações. Pode ajudar a melhorar a resiliência do seu serviço através de:

- compreender todos os componentes

- eliminação de pontos únicos de falhas

- isolar componentes falhados para limitar o seu impacto

- fornecendo redundância com mecanismos de falha rápida e caminhos de recuperação

Ao desenvolver a sua aplicação, recomendamos que se considere como aumentar a [resiliência da autenticação e autorização nas suas aplicações](resilience-app-development-overview.md) com os componentes de identidade da sua solução. Este artigo procura abordar melhorias para resiliência específicas das aplicações AZure AD B2C. As nossas recomendações são agrupadas pelas funções ciam.

![Imagem mostra componentes CIAM ](media/resilience-b2c/high-level-components.png) Nas secções seguintes, guiaremos-o a construir resiliência nas seguintes áreas:

- [Experiência do utilizador final](resilient-end-user-experience.md): Ative um plano de recuo para o seu fluxo de autenticação e atenuar o impacto potencial de uma rutura do serviço de autenticação AD B2C Azure.

- [Interfaces com processos externos](resilient-external-processes.md): Construa resiliência nas suas aplicações e interfaces recuperando-se de erros.  

- [Melhores práticas do desenvolvedor](resilience-b2c-developer-best-practices.md): Evite fragilidades devido a problemas de política personalizada comuns e melhore o tratamento de erros nas áreas como interações com verificadores de reclamações, aplicações de terceiros e APIs REST.

- [Monitorização e análise](resilience-with-monitoring-alerting.md): Avalie a saúde do seu serviço monitorizando indicadores-chave e detete falhas e perturbações de desempenho através do alerta.

- [Construa resiliência na sua infraestrutura de autenticação](resilience-in-infrastructure.md)

- [Aumentar a resiliência da autenticação e autorização nos seus pedidos](resilience-app-development-overview.md)
