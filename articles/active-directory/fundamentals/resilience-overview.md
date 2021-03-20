---
title: Construção de identidade resiliente e gestão de acessos com diretório ativo Azure
description: Um guia para arquitetos, administradores de TI e desenvolvedores sobre a construção de resiliência à rutura dos seus sistemas de identidade.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454347"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Construção de resiliência na gestão de identidade e acesso com o Azure Ative Directory

A gestão de identidades e acessos (IAM) é um quadro de processos, políticas e tecnologias que facilitam a gestão das identidades e daquilo a que acedem. Inclui os muitos componentes que suportam a autenticação e autorização do utilizador e outras contas no seu sistema.

A resiliência do IAM é a capacidade de suportar perturbações nos componentes do sistema e recuperar com o mínimo impacto para o seu negócio, utilizadores, clientes e operações. Reduzir as dependências, a complexidade e os pontos de falha únicos, ao mesmo tempo que garantirá que o tratamento abrangente de erros aumentará a sua resiliência.

A interrupção pode vir de qualquer componente dos seus sistemas IAM. Para construir um sistema IAM resiliente, assuma que as perturbações ocorrerão e planeie para ele. 

Ao planear a resiliência da sua solução IAM, considere os seguintes elementos: 

* As suas aplicações que dependem do seu sistema IAM.

* As infraestruturas públicas que as suas chamadas de autenticação utilizam, incluindo empresas de telecomunicações, fornecedores de serviços de Internet e fornecedores de chaves públicas.

* Os seus fornecedores de identidade na nuvem e no local.

* Outros serviços que dependem do seu IAM e das APIs que os ligam.

* Quaisquer outros componentes no seu sistema.

Seja qual for a fonte, reconhecer e planear as contingências é importante. No entanto, a adição de sistemas de identidade adicionais, e as suas dependências e complexidades resultantes, podem reduzir a sua resiliência em vez de a aumentar.

Para construir mais resiliência nos seus sistemas, reveja os seguintes artigos:

* [Construa resiliência na sua infraestrutura IAM](resilience-in-infrastructure.md)

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas de Gestão de Identidade e Acesso (CIAM)](resilience-b2c.md)
