---
title: Usando Azure Sentinel com firewall de aplicação web Azure
description: Este artigo mostra-lhe como usar O Sentinela Azure com firewall de aplicação web Azure (WAF)
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 6e1d9b8a53eaf69c2294ab42dc0718863e6c1837
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804942"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Usando Azure Sentinel com firewall de aplicação web Azure

A Azure Web Application Firewall (WAF) combinada com a Azure Sentinel pode fornecer gestão de eventos de informação de segurança para recursos WAF. O Azure Sentinel fornece análises de segurança utilizando o Log Analytics, o que lhe permite decompor e visualizar facilmente os seus dados WAF. Utilizando o Azure Sentinel, pode aceder a livros pré-construídos e modificá-los de forma melhor adequada às necessidades da sua organização. O livro pode mostrar análises para WAF na Rede de Entrega de Conteúdos Azure (CDN), WAF na Porta Frontal Azure e WAF no Gateway de aplicações através de várias subscrições e espaços de trabalho.

## <a name="waf-log-analytics-categories"></a>Categorias de análise de registos WAF

As análises de registos WAF são divididas nas seguintes categorias:  

- Todas as ações da WAF tomadas 
- Top 40 bloqueados pedido endereços URI 
- Top 50 despoletadores de eventos,  
- Mensagens ao longo do tempo 
- Detalhes completos da mensagem 
- Ataque eventos por mensagens  
- Eventos de ataque ao longo do tempo 
- Filtro de ID de rastreio 
- Mensagens de ID de rastreio 
- Top 10 endereços IP atacantes 
- Mensagens de ataque de endereços IP 

## <a name="waf-workbook-examples"></a>Exemplos de livros de trabalho da WAF

Os seguintes exemplos do livro da WAF mostram dados da amostra:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="Filtro de ações da WAF":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Top 50 eventos":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Eventos de ataque":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Top 10 endereços IP atacantes":::

## <a name="launch-a-waf-workbook"></a>Lançar um livro da WAF

O livro da WAF funciona para todos os Azure Front Door, Application Gateway e CDN WAFs. Antes de ligar os dados destes recursos, a análise de registo deve ser ativada no seu recurso. 

Para ativar a análise de registos para cada recurso, aceda ao seu recurso Individual Azure Front Door, Application Gateway ou CDN:

1. Selecione **definições de diagnóstico**.
2. **Selecione + Adicione a definição de diagnóstico**. 
3. Na página de definição de Diagnóstico:
   1. Escreva um nome. 
   1. Selecione **Enviar para Registar Análises**. 
   1. Escolha o espaço de trabalho do destino de registo. 
   1. Selecione os tipos de registo que pretende analisar:
      1. Gateway de aplicações: 'ApplicationGatewayAccessLog' e 'ApplicationGatewayFirewallLog'
      1. Azure Front Door: 'FrontDoorAccessLog' e 'FrontDoorFirewallLog'
      1. CDN: 'AzureCdnAccessLog'
   1. Selecione **Guardar**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Definição de diagnóstico":::

4. Na página inicial do Azure, **digite Azure Sentinel** na barra de pesquisa e selecione o recurso **Azure Sentinel.** 
2. Selecione um espaço de trabalho já ativo ou crie um novo espaço de trabalho. 
3. No painel lateral esquerdo sob **configuração** selecione **Os Conectores de Dados**.
4. Procure por **firewall de aplicações web da Microsoft** e selecione firewall de **aplicações web da Microsoft (WAF)**. Selecione Abrir a página do **conector** no canto inferior direito.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Conectores de dados":::

8. Siga as instruções em **Configuração** para cada recurso WAF para o qual pretende ter dados analíticos de registo para se não o tiver feito anteriormente.
6. Uma vez terminado a configuração dos recursos WAF individuais, selecione o **separador passos seguintes.** Selecione um dos livros recomendados. Este livro utilizará todos os dados analíticos de registo que foram ativados anteriormente. Um livro de trabalho da WAF em funcionamento deve agora existir para os seus recursos WAF.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="Livros waf":::


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre a Azure Sentinel](../sentinel/overview.md)
- [Saiba mais sobre os livros de trabalho do Azure Monitor](../azure-monitor/platform/workbooks-overview.md)
