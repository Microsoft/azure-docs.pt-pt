---
title: Referência de API de Portal do Cloud Partner | Azure Marketplace
description: Descrição de, pré-requisitos para usar e lista de operações de API do Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: b6591e1780d03cbfaff70fbd19ec3dfd274fae79
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819635"
---
<a name="cloud-partner-portal-api-reference"></a>Referência de API de Portal do Cloud Partner
==================================

As APIs REST Portal do Cloud Partner permitem a recuperação programática e a manipulação de cargas de trabalho, ofertas e perfis de Publicador. As APIs usam o RBAC (controle de acesso baseado em função) para impor as permissões corretas no momento do processamento.

Esta referência fornece os detalhes técnicos para as APIs REST do Portal do Cloud Partner. Os exemplos de carga neste documento são apenas para referência e estão sujeitos a alterações à medida que a nova funcionalidade é adicionada.


<a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações
-------------------------------

Antes de usar as APIs, você deve examinar:

- O artigo [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) para saber como adicionar uma entidade de serviço à sua conta e obter um token de acesso Azure Active Directory (AD do Azure) para autenticação. 
- O dois [controles de simultaneidade](./cloud-partner-portal-api-concurrency-control.md).
estratégias disponíveis para chamar essas APIs.
- Considerações adicionais [sobre](./cloud-partner-portal-api-considerations.md)API, como controle de versão e tratamento de erros.


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

### <a name="publish-an-app"></a>Publicar um aplicativo

-   [Entrar em direto](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

-   [Definir preços para ofertas de máquina virtual](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Resolução de problemas

-   [Resolver erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
