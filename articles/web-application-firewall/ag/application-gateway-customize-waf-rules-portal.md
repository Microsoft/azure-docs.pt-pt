---
title: Personalize as regras usando o portal - Azure Web Application Firewall
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de Aplicação Web em Gateway de aplicação com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74048368"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalize as regras de Firewall de aplicações web usando o portal Azure

O Azure Application Gateway Web Application Firewall (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Open Web Application Security Project (OWASP) Core Rule set (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos de regras e regras. Para obter mais informações sobre os grupos e regras específicas de regras, consulte [a Lista de grupos e regras de regras do CRS do Firewall de Aplicação Web.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Se o seu gateway de aplicação não estiver a utilizar o nível WAF, a opção de atualizar o gateway de aplicação para o nível WAF aparece no painel direito. 

![Ativar a WAF][fig1]

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

**Para ver grupos de regras e regras**
1. Navegue pelo gateway de aplicações e, em seguida, selecione **firewall de aplicação Web**.  
2. Selecione a sua **Política WAF**.
2. Selecione **Regras Geridas**.

   Esta vista mostra uma tabela na página de todos os grupos de regras fornecidos com o conjunto de regras escolhidos. Todas as caixas de verificação da regra são selecionadas.

## <a name="disable-rule-groups-and-rules"></a>Desativar grupos e regras de regras

> [!IMPORTANT]
> Tenha cuidado ao desativar quaisquer grupos ou regras de regras. Isto pode expô-lo a riscos de segurança aumentados.

Quando está a desativar regras, pode desativar um grupo inteiro de regras ou regras específicas sob um ou mais grupos de regras. 

**Desativar grupos de regras ou regras específicas**

   1. Procure as regras ou grupos de regras que pretende desativar.
   2. Selecione as caixas de verificação para as regras que pretende desativar. 
   3. Selecione a ação no topo da página (ativar/desativar) para as regras selecionadas.
   2. Selecione **Guardar**. 

![Guardar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que a WAF bloqueie o pedido durante o modo de prevenção. No modo de deteção, são registados como exceções.

Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados do formulário)
* O comprimento dos dados do corpo do pedido (sem ficheiros) é maior do que o limite configurado
* O corpo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedeu limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras desativadas, pode aprender a ver os seus registos WAF. Para mais informações, consulte os [diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
