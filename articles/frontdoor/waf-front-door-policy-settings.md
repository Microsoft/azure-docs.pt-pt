---
title: Definições de política de firewall de aplicações web com porta da frente do Azure
description: Aprenda a firewall de aplicações web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797468"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Definições de política de firewall de aplicações web com porta da frente do Azure

Uma política de Firewall de aplicações Web (WAF) permite-lhe controlar o acesso às suas aplicações web através de um conjunto de regras personalizadas e gerenciados. O nome da política de WAF tem de ser exclusivo. Irá receber um erro de validação, se tentar usar um nome existente. Existem várias definições ao nível de política que se aplicam a todas as regras especificadas para essa política, conforme descrito neste artigo.

## <a name="waf-state"></a>Estado de WAF

Uma política de WAF para a porta de entrada pode ter um dos seguintes dois Estados:
- **Ativado:** Quando uma política é ativada, o WAF é ativamente a inspecionar os pedidos recebidos e executa as ações correspondentes, de acordo com as definições das regras
- **Desativado:** - quando uma política estiver desativada, a inspeção de WAF está em pausa. Pedidos de entrada irão ignorar a WAF e são enviados para o back-ends com base no encaminhamento de porta de entrada.

## <a name="waf-mode"></a>Modo de WAF

Política de WAF pode ser configurada para ser executado em dois modos seguintes:

- **Modo de deteção** quando executado no modo de deteção, WAF não executar quaisquer ações que não seja o monitor e iniciar sessão o pedido e a regra de WAF correspondente registos WAF. Ativar diagnósticos de Registro em log para a porta de entrada (ao utilizar o portal, isso pode ser conseguido indo para o **diagnóstico** secção no portal do Azure).

- **Modo de prevenção** quando configurado para ser executado no modo de prevenção, WAF utiliza a ação especificada se corresponder a um pedido de uma regra. Todos os pedidos correspondentes também são registados nos registos WAF.

## <a name="waf-response-for-blocked-requests"></a>Resposta de WAF para pedidos bloqueados

Por predefinição, quando a WAF bloquear um pedido devido a uma regra correspondente, ele retorna um código de 403 status com - **o pedido está bloqueado** mensagem. Uma cadeia de caracteres de referência também é devolvida para o registo.

Pode definir um código de estado de resposta personalizada e uma mensagem de resposta quando um pedido está bloqueado pela WAF. São suportados os seguintes códigos de estado personalizado:

- 200    OK
- 403 Proibido
- 405 Método não permitido
- 406 Não aceitável
- 429 demasiados pedidos

Mensagem de código e a resposta do Estado de resposta personalizado é uma definição de política de nível. Assim que estiver configurado, todos os pedidos bloqueados obtém o mesmo Estado de resposta personalizada e uma mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI de ação de redirecionamento

É necessário definir um URI para redirecionar pedidos para se o **REDIRECIONAR** ação está selecionada para qualquer uma das regras contidas numa política de WAF. Este redirecionamento o URI tem de ser um site válido do HTTP (S) e uma vez configurado, todos os pedidos de regras de correspondência com uma ação de "De REDIRECIONAMENTO" serão redirecionadas para o site especificado.


## <a name="next-steps"></a>Passos Seguintes
- Saiba como definir o WAF [respostas personalizadas](waf-front-door-configure-custom-response-code.md)
