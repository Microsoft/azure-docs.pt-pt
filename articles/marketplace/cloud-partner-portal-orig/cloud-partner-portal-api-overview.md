---
title: Referência da API do Portal de parceiros da cloud | O Azure Marketplace
description: Descrição da pré-requisitos para utilizar e lista de operações de API do marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 9626fdb5dbdf720a65217421565fc559a0cee684
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935465"
---
<a name="cloud-partner-portal-api-reference"></a>Referência de API de Portal de parceiro de cloud
==================================

As APIs de REST de Portal de parceiro de Cloud permitem a obtenção de programática e a manipulação de cargas de trabalho, ofertas e os perfis de publicador. As APIs de utilizam o controlo de acesso baseado em funções (RBAC) para impor permissões corretas no tempo de processamento.

Esta referência disponibiliza os detalhes técnicos para as APIs de REST de Portal de parceiro de Cloud. Os exemplos de payload neste documento são apenas para referência e estão sujeitos a alterações à medida que novas funcionalidades adicionadas.


<a name="prerequisites-and-considerations"></a>Pré-requisitos e considerações
-------------------------------

Antes de utilizar as APIs, deve rever:

- O [pré-requisitos](./cloud-partner-portal-api-prerequisites.md) artigo para saber como adicionar um principal de serviço à sua conta e obter token de acesso um Azure Active Directory (Azure AD), para autenticação. 
- Os dois [controlo de simultaneidade](./cloud-partner-portal-api-concurrency-control.md).
estratégias disponíveis para chamar essas APIs.
- API adicionais [considerações](./cloud-partner-portal-api-considerations.md), como controle de versão e o tratamento de erros.


<a name="common-tasks"></a>Tarefas comuns
------------
Esta referência fornece detalhes sobre as APIs para realizar as seguintes tarefas comuns.


### <a name="offers"></a>Ofertas

-   [Obter todas as ofertas](./cloud-partner-portal-api-retrieve-offers.md)
-   [Obter uma oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md)
-   [Obter estado da oferta](./cloud-partner-portal-api-retrieve-offer-status.md)
-   [Criar uma oferta](./cloud-partner-portal-api-creating-offer.md)
-   [Publicar uma oferta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operações

-   [Obter operações](./cloud-partner-portal-api-retrieve-operations.md)
-   [Cancelar operações](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Publicar uma aplicação

-   [Entrar em direto](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Outras tarefas

-   [Definir preços de ofertas de máquina virtual](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Resolução de problemas

-   [Resolver erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
