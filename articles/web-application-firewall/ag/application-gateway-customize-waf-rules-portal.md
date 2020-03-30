---
title: Personalize regras usando portal - Firewall de aplicação web Azure
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de aplicação web no Gateway de Aplicações com o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048368"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Personalize as regras de firewall de aplicação web usando o portal Azure

O Firewall de aplicação web da Aplicação Gateway (WAF) da Aplicação Azure Application (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Conjunto de Regras de Regra do Núcleo de Segurança de Aplicações Abertas (OWASP) (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos e regras de regras. Para obter mais informações sobre os grupos e regras de regras específicas, consulte [lista de grupos e regras](application-gateway-crs-rulegroups-rules.md)de regras DO FIREWALL de aplicação web .

>[!NOTE]
> Se o gateway da aplicação não estiver a utilizar o nível WAF, a opção de atualizar o portal de aplicação para o nível WAF aparece no painel certo. 

![Ativar waf][fig1]

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

**Ver grupos de regras e regras**
1. Navegue no gateway da aplicação e, em seguida, selecione firewall de **aplicação Web**.  
2. Selecione a sua **Política WAF**.
2. Selecione **Regras Geridas**.

   Esta vista mostra uma tabela na página de todos os grupos de regras fornecidos com o conjunto de regras escolhido. Todas as caixas de verificação da regra são selecionadas.

## <a name="disable-rule-groups-and-rules"></a>Desativar grupos e regras

> [!IMPORTANT]
> Tenha cuidado ao desativar quaisquer grupos ou regras de regras. Isto pode expô-lo a riscos de segurança acrescidos.

Quando se está a desativar as regras, pode desativar todo um grupo de regras ou regras específicas sob um ou mais grupos de regras. 

**Para desativar grupos de regras ou regras específicas**

   1. Procure as regras ou grupos de regras que pretende desativar.
   2. Selecione as caixas de verificação para as regras que pretende desativar. 
   3. Selecione a ação na parte superior da página (ativar/desativar) para as regras selecionadas.
   2. Selecione **Guardar**. 

![Guardar alterações][3]

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista seguinte contém condições que fazem com que o WAF bloqueie o pedido enquanto está no Modo de Prevenção. No Modo de Deteção, são registados como exceções.

Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo de pedidos (sem ficheiros) é maior do que o limite configurado
* O organismo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedida limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras de incapacidade, pode aprender a ver os seus registos WAF. Para mais informações, consulte [diagnósticos de Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)de aplicação .

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png
