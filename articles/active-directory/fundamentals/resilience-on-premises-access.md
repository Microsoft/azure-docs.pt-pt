---
title: Criar resiliência no acesso à aplicação com Aplicação Proxy
description: Um guia para arquitetos e administradores de TI sobre a utilização do Application Proxy para acesso resiliente a aplicações no local
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
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724665"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Criar resiliência no acesso à aplicação com Aplicação Proxy

Application Proxy é uma funcionalidade do Azure AD que permite aos utilizadores acederem a aplicações web no local a partir de um cliente remoto. O Application Proxy inclui tanto o serviço Application Proxy na nuvem, como os conectores Application Proxy, que funcionam num servidor no local. 

Os utilizadores acedem aos recursos no local através de um URL publicado via Application Proxy. São redirecionados para o sinal AZure AD na página. O serviço de Procuração de Aplicações em Azure AD envia então um símbolo para o conector Proxy de aplicação na rede corporativa, que passa o token para o Diretório Ativo no local O utilizador autenticado pode então aceder ao recurso no local. No diagrama abaixo, [os conectores](../manage-apps/application-proxy-connectors.md) são indicados num [grupo de conector](../manage-apps/application-proxy-connector-groups.md).

> [!IMPORTANT]
> Ao publicar as suas aplicações através do Application Proxy, tem de implementar [o planeamento de capacidade e o despedimento adequado para os conectores Application Proxy](../manage-apps/application-proxy-connectors.md#capacity-planning).

![Diagrama de arquitetura da Aplicação y](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Como implemento o Application Proxy?

Para implementar o acesso remoto com a Azure AD Application Proxy, consulte os seguintes recursos.

* [Planear uma implementação do Proxy de Aplicações](../manage-apps/application-proxy-deployment-plan.md)

* [Alta disponibilidade e carga equilibrando as melhores práticas](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Configurar servidores de procuração](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Desenhe uma estratégia resiliente de controlo de acessos](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Passos seguintes
Recursos de resiliência para administradores e arquitetos
 
* [Construir resiliência com gestão credencial](resilience-in-credentials.md)

* [Construir resiliência com estados de dispositivo](resilience-with-device-states.md)

* [Construa resiliência utilizando a Avaliação contínua de Acesso (CAE)](resilience-with-continuous-access-evaluation.md)

* [Construir resiliência na autenticação externa do utilizador](resilience-b2b-authentication.md)

* [Construa resiliência na sua autenticação híbrida](resilience-in-hybrid.md)

Recursos de resiliência para desenvolvedores

* [Construa resiliência do IAM nas suas aplicações](resilience-app-development-overview.md)

* [Construa resiliência nos seus sistemas CIAM](resilience-b2c.md)