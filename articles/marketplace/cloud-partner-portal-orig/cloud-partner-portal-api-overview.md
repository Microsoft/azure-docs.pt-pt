---
title: Cloud Partner Portal Referência API [ Mercado Azure
description: Descrição dos pré-requisitos para utilização e lista de operações de API no mercado.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 296c667876807bdd05a6281de461df76207b5490
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288551"
---
<a name="cloud-partner-portal-api-reference"></a>Referência api do portal de parceiro de nuvem
==================================

O Portal de Parceiros cloud REST APIs permite a recuperação programática e manipulação de cargas de trabalho, ofertas e perfis de editor. As APIs utilizam o controlo de acesso baseado em funções (RBAC) para impor permissões corretas no momento do processamento.

Esta referência fornece os detalhes técnicos para as APIs REST REST Do Portal do Parceiro cloud. As amostras de carga útil neste documento são apenas para referência e estão sujeitas a alterações à medida que é adicionada uma nova funcionalidade.


<a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações
-------------------------------

Antes de utilizar as APIs, deve rever:

- O artigo [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para aprender a adicionar um principal de serviço à sua conta, e obter um sinal de acesso azure Ative Directory (Azure AD) para autenticação. 
- O controlo das duas [moedas.](./cloud-partner-portal-api-concurrency-control.md)
estratégias disponíveis para chamar estas APIs.
- [Considerações](./cloud-partner-portal-api-considerations.md)adicionais da API, tais como versão e manipulação de erros.


<a name="common-tasks"></a>Tarefas comuns
------------
Esta referência detalha as APIs para executar as seguintes tarefas comuns.


### <a name="offers"></a>Ofertas

-   [Recuperar todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
-   [Recuperar uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Obter estado da oferta](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Criar uma oferta](./cloud-partner-portal-api-creating-offer.md)
-   [Publicar uma oferta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operações

-   [Obter operações](./cloud-partner-portal-api-retrieve-operations.md)
-   [Cancelar operações](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicar uma aplicação

-   [Entrar em direto](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

-   [Definir preços para ofertas de máquinas virtuais](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Resolução de problemas

-   [Resolver erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
