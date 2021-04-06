---
title: Interfaces resilientes com processos externos utilizando | Azure AD B2C Microsoft Docs
description: Métodos para construir interfaces resilientes com processos externos
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
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724412"
---
# <a name="resilient-interfaces-with-external-processes"></a>Interfaces resilientes com processos externos

Neste artigo, fornecemos-lhe orientações sobre como planear e implementar as APIs RESTFul na jornada do utilizador e tornar a sua aplicação mais resistente às falhas da API.

![Imagem mostra interfaces com componentes de processo externos](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Garantir a correta colocação das APIs

As políticas de enquadramento de identidade (IEF) permitem-lhe chamar um sistema externo utilizando um [perfil técnico restful da API](../../active-directory-b2c/restful-technical-profile.md). Os sistemas externos não são controlados pelo ambiente de execução do IEF e são um potencial ponto de falha.

### <a name="how-to-manage-external-systems-using-apis"></a>Como gerir sistemas externos utilizando APIs

- Ao ligar para uma interface para aceder a determinados dados, verifique se os dados vão impulsionar a decisão de autenticação. Avaliar se a informação é essencial para a funcionalidade principal da aplicação. Por exemplo, um e-commerce vs. uma funcionalidade secundária, como uma administração. Se a informação não for necessária para a autenticação e apenas necessária para cenários secundários, considere mover a chamada para a lógica da aplicação.

- Se os dados necessários para a autenticação forem relativamente estáticos e pequenos, e não tão nenhum outro motivo comercial para serem externos do diretório, então considere tê-lo no diretório.

- Remova as chamadas API do caminho pré-autenticado sempre que possível. Se não conseguir, deve colocar proteções rigorosas para ataques de Negação de Serviço (DoS) e Denegação de Serviço Distribuído (DDoS) em frente às suas APIs. Os atacantes podem carregar a página de inscrição e tentar inundar a sua API com ataques DoS e aleijar a sua aplicação. Por exemplo, usando o CAPTCHA no seu sinal, o fluxo de inscrição pode ajudar.

- Utilize [conectores API do fluxo de utilizador de inscrição incorporado](../../active-directory-b2c/api-connectors-overview.md) sempre que possível para se integrar com APIs web, quer depois de iniciar sessão com um fornecedor de identidade ou antes de criar o utilizador. Uma vez que os fluxos de utilizador já estão amplamente testados, é provável que não tenha de realizar testes funcionais, de desempenho ou de escala ao nível do utilizador. Ainda precisa de testar as suas aplicações para funcionalidade, desempenho e escala.

- Os [perfis técnicos da](../../active-directory-b2c/restful-technical-profile.md) AD RESTFUL da AD API não fornecem qualquer comportamento de caching. Em vez disso, o perfil de API RESTFUL implementa uma lógica de retídica e um tempo limite que é incorporado na política.

- Para as APIs que precisam de escrever dados, faça fila para ter tais tarefas executadas por um trabalhador de fundo. Serviços como [as filas Azure](../../storage/queues/storage-queues-introduction.md) podem ser usados. Isto fará com que a API regresse de forma eficiente, aumentando o desempenho da execução da política.  

## <a name="api-error-handling"></a>Tratamento de erros da API

Como as APIs vivem fora do sistema Azure AD B2C, é necessário ter um manuseamento adequado de erros dentro do perfil técnico. Certifique-se de que o utilizador final é informado adequadamente e que a aplicação pode lidar com a falha graciosamente.

### <a name="how-to-gracefully-handle-api-errors"></a>Como lidar graciosamente com erros da API

- Uma API pode falhar por várias razões, tornar a sua aplicação resiliente a tais falhas. [Devolva uma mensagem de erro HTTP 4XX](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) se a API não conseguir completar o pedido. Na política Azure AD B2C, tente gerir graciosamente a indisponibilidade da API e talvez tornar uma experiência reduzida.

- [Lidar com erros transitórios graciosamente](../../active-directory-b2c/restful-technical-profile.md#error-handling). O perfil DE API RESTFul permite-lhe configurar mensagens de erro para [vários disjuntores](/azure/architecture/patterns/circuit-breaker).

- Monitorize e utilize proactivamente a Integração Contínua/Entrega Contínua (CICD), rode as credenciais de acesso da API, tais como palavras-passe e certificados utilizados pelo [motor de perfil técnico.](../../active-directory-b2c/restful-technical-profile.md)

## <a name="api-management---best-practices"></a>Gestão da API - boas práticas

Enquanto implementa as APIs REST e configura o perfil técnico RESTful, seguindo as melhores práticas recomendadas irá ajudá-lo a não cometer erros comuns e as coisas serem negligenciadas.

### <a name="how-to-manage-apis"></a>Como gerir as APIs

- A API Management (APIM) publica, gere e analisa as suas APIs. A APIM também lida com a autenticação para fornecer acesso seguro a serviços de backend e microserviços. Utilize uma porta de entrada API para escalar as implementações da API, o tiaching e o equilíbrio de carga.

- Recomendação é obter o símbolo certo no início da viagem do utilizador em vez de ligar várias vezes para cada API e [garantir um API AZURE APIM](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga).

## <a name="next-steps"></a>Passos seguintes

- [Recursos de resiliência para desenvolvedores Azure AD B2C](resilience-b2c.md)
  - [Experiência resiliente do utilizador final](resilient-end-user-experience.md)
  - [Resiliência através das melhores práticas do desenvolvedor](resilience-b2c-developer-best-practices.md)
  - [Resiliência através da monitorização e análise](resilience-with-monitoring-alerting.md)
- [Construa resiliência na sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência da autenticação e autorização nos seus pedidos](resilience-app-development-overview.md)