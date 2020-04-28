---
title: Definições de política para firewall de aplicação web com porta frontal Azure
description: Aprenda firewall de aplicação web (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75932604"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Definições de política para firewall de aplicação web na porta da frente do Azure

Uma política de Firewall de Aplicações Web (WAF) permite controlar o acesso às suas aplicações web através de um conjunto de regras personalizadas e geridas. O nome da política da WAF deve ser único. Receberá um erro de validação se tentar usar um nome existente. Existem múltiplas definições de nível de política que se aplicam a todas as regras especificadas para essa política, tal como descrito neste artigo.

## <a name="waf-state"></a>Estado waf

Uma política waf para porta da frente pode estar em um dos dois seguintes estados:
- **Habilitado:** Quando uma política é ativada, a WAF está a inspecionar ativamente os pedidos de entrada e toma as ações correspondentes de acordo com as definições de regras
- **Deficiente:** - Quando uma apólice é desativada, a inspeção waf é interrompida. Os pedidos de entrada contornarão a WAF e são enviados para back-end com base no encaminhamento da Porta Frontal.

## <a name="waf-mode"></a>Modo WAF

A política waf pode ser configurada para ser executada nos dois modos seguintes:

- **Modo de deteção** Quando executado no modo de deteção, o WAF não toma outras ações que não o monitor e regista o pedido e a sua regra WAF compatível com os registos WAF. Ligue os diagnósticos de registo para a Porta Da Frente (quando utilizar o portal, isto pode ser conseguido indo para a secção **de Diagnósticos** no portal Azure).

- **Modo de prevenção** Quando configurado para funcionar em modo de prevenção, o WAF toma a ação especificada se um pedido corresponder a uma regra. Quaisquer pedidos combinados também são registados nos registos waf.

## <a name="waf-response-for-blocked-requests"></a>Resposta waf para pedidos bloqueados

Por predefinição, quando a WAF bloqueia um pedido por causa de uma regra compatível, devolve um código de estado 403 com - O pedido é uma mensagem **bloqueada.** Uma cadeia de referência também é devolvida para abate.

Pode definir um código de estado de resposta personalizado e mensagem de resposta quando um pedido é bloqueado por WAF. Os seguintes códigos de estado personalizados são suportados:

- 200 OK
- 403 Proibido
- Método 405 não permitido
- 406 Não aceitável
- 429 Pedidos a mais

O código de estado de resposta personalizado e a mensagem de resposta são uma definição de nível de política. Uma vez configurado, todos os pedidos bloqueados recebem o mesmo estado de resposta personalizado e mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI para redirecionar ação

É-lhe exigido que defina um URI para redirecionar pedidos para se a ação **REDIRECT** for selecionada para qualquer uma das regras contidas numa política waf. Este URI redirecionado tem de ser um site HTTP(S) válido e, uma vez configurado, todos os pedidos que correspondam às regras correspondentes com uma ação "REDIRECT" serão redirecionados para o site especificado.


## <a name="next-steps"></a>Passos seguintes
- Saiba como definir [respostas personalizadas](waf-front-door-configure-custom-response-code.md) da WAF
