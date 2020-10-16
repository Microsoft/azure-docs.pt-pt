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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75932604"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Definições de política para firewall de aplicação web na porta frontal Azure

Uma política de Firewall de Aplicação Web (WAF) permite-lhe controlar o acesso às suas aplicações web através de um conjunto de regras personalizadas e geridas. O nome da política da WAF deve ser único. Receberá um erro de validação se tentar utilizar um nome existente. Existem várias definições de nível de política que se aplicam a todas as regras especificadas para essa política, tal como descrito neste artigo.

## <a name="waf-state"></a>Estado da WAF

Uma política da WAF para porta da frente pode estar num dos dois estados seguintes:
- **Habilitado:** Quando uma política está ativada, a WAF está a inspecionar ativamente os pedidos de entrada e toma as ações correspondentes de acordo com as definições de regras
- **Desativado:** - Quando uma apólice é desativada, a inspeção da WAF é interrompida. Os pedidos de entrada passarão por WAF e serão enviados para back-ends com base no encaminhamento da Porta Frontal.

## <a name="waf-mode"></a>Modo WAF

A política da WAF pode ser configurada para ser executada nos dois modos seguintes:

- **Modo de deteção** Quando executado no modo de deteção, a WAF não toma quaisquer outras ações que não sejam o monitor e regista o pedido e a sua regra WAF correspondida aos registos WAF. Ligue os diagnósticos de registo para porta frontal (quando utilizar o portal, isto pode ser conseguido indo para a secção **de Diagnóstico no** portal Azure).

- **Modo de prevenção** Quando configurado para funcionar em modo de prevenção, a WAF toma a ação especificada se um pedido corresponder a uma regra. Quaisquer pedidos combinados também estão registados nos registos da WAF.

## <a name="waf-response-for-blocked-requests"></a>Resposta da WAF para pedidos bloqueados

Por predefinição, quando a WAF bloqueia um pedido por causa de uma regra compatível, devolve um código de estado 403 com - **O pedido está bloqueado.** Uma cadeia de referência também é devolvida para registar.

Pode definir um código de estado de resposta personalizado e uma mensagem de resposta quando um pedido é bloqueado pela WAF. São suportados os seguintes códigos de estado personalizados:

- 200 OK
- 403 Proibido
- 405 Método não permitido
- 406 Não aceitável
- 429 Pedidos a mais

Código de estado de resposta personalizada e mensagem de resposta é uma definição de nível de política. Uma vez configurado, todos os pedidos bloqueados obtêm o mesmo estado de resposta personalizado e mensagem de resposta.

## <a name="uri-for-redirect-action"></a>URI para ação de redirecionamento

É-lhe exigido que defina um URI para redirecionar os pedidos para se a ação **REDIRECT** for selecionada para qualquer uma das regras contidas numa política da WAF. Este redireccionamento URI precisa de ser um site HTTP(S válido e uma vez configurado, todos os pedidos que correspondam as regras com uma ação "REDIRECT" serão redirecionados para o site especificado.


## <a name="next-steps"></a>Passos seguintes
- Saiba como definir [respostas personalizadas](waf-front-door-configure-custom-response-code.md) da WAF
