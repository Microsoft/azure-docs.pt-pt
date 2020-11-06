---
title: Suporte de volume de tráfego de Gateway de aplicação
description: Este artigo fornece orientações para configurar o Azure Application Gateway em apoio a cenários de volume de tráfego de rede elevado.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 48730d03e9a578fb26b691577fa033e5f7bb4d19
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397489"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte de tráfego elevado para o Gateway de Aplicação

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar o seu Gateway de Aplicação para lidar com o tráfego extra devido ao elevado volume de tráfego que pode ocorrer devido à crise COVID-19.

Pode utilizar o Gateway de Aplicações com Firewall de Aplicação Web (WAF) para uma forma escalável e segura de gerir o tráfego das suas aplicações web.

As seguintes sugestões ajudam-no a configurar o Application Gateway com a WAF para lidar com o tráfego extra.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Utilize o V2 SKU em v1 para as suas capacidades de autoscalagem e benefícios de desempenho
O V2 SKU oferece autoscaling para garantir que o seu Gateway de Aplicação pode aumentar à medida que o tráfego aumenta. Também oferece outros benefícios significativos de desempenho, tais como 5x melhor desempenho de descarregamento TLS, tempos de implementação e atualização mais rápidos, redundância de zona, e muito mais quando comparado com v1. Para mais informações, consulte a nossa [documentação v2.](./application-gateway-autoscaling-zone-redundant.md) 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instância para o máximo possível (125)
 
Assumindo que tem um Gateway de Aplicação v2 SKU, definir a contagem máxima de instância para o valor máximo possível de 125 permite que o Gateway de aplicação se escalone conforme necessário. Isto permite-lhe lidar com o possível aumento de tráfego para as suas aplicações. Só será cobrado para as Unidades de Capacidade (CUs) que utilizar.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Desaça a sua contagem mínima de casos com base no seu uso médio de CU

Assumindo que tem um Gateway de Aplicação v2 SKU, a autoscalagem leva de 6 a 7 minutos para escalar. Com uma contagem mínima mais elevada, o Gateway de Aplicação pode lidar melhor com o seu tráfego quando a carga é aumentada, porque um pico no tráfego não requer uma operação de autoscalagem.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alerta se uma determinada métrica ultrapassar 75% da média de utilização da UC 
Consulte a documentação da [Application Gateway Metrics](./application-gateway-metrics.md#metrics-visualization) para obter uma explicação detalhada das nossas métricas e outras imagens. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: Estabelecer um alerta sobre 75% do uso médio da UC

Este exemplo mostra como usar o portal Azure para configurar um alerta quando 75% do uso médio da UC é atingido. 
1. Navegue para o seu **Gateway de Aplicações**.
2. No painel esquerdo, selecione **Métricas** no **separador Monitor.** 
3. Adicione uma métrica para **unidades de computação corrente médias.** 
![Configuração da métrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se definiu a sua contagem mínima de instância para ser o seu uso médio de CU, vá em frente e desate um alerta quando 75% das suas instâncias mínimas estiverem em uso. Por exemplo, se o seu uso médio for de 10 CUs, coloque um alerta em 7,5 CUs. Isto alerta-o se o uso está a aumentar e dá-lhe tempo para responder. Pode aumentar o mínimo se achar que este tráfego será sustentado para alertá-lo de que o tráfego pode estar a aumentar. 
![Configuração do alerta WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Pode definir o alerta para ocorrer a uma percentagem de utilização de CU inferior ou superior, dependendo da sensibilidade que pretende ser para potenciais picos de tráfego.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurar a WAF com geofiltering e proteção de bots para parar os ataques
Se quiser uma camada extra de segurança à frente da sua aplicação, utilize o Gateway de Aplicação WAF_v2 SKU para capacidades WAF. Pode configurar o V2 SKU para permitir apenas o acesso às suas aplicações a partir de um determinado país/região ou países/regiões. Configura uma regra personalizada da WAF para permitir ou bloquear explicitamente o tráfego com base na geolocalização. Para obter mais informações, consulte [as regras personalizadas de geofilterização](../web-application-firewall/ag/geomatch-custom-rules.md) e [como configurar regras personalizadas no Application Gateway WAF_v2 SKU através da PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Permitir a proteção do bot para bloquear os robôs maus conhecidos. Isto deve reduzir a quantidade de tráfego que chega à sua aplicação. Para obter mais informações, consulte [a proteção do bot com instruções definidas](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ligue os diagnósticos no Application Gateway e NA WAF

Os registos de diagnóstico permitem-lhe visualizar registos de firewall, registos de desempenho e registos de acesso. Pode utilizar estes registos em Azure para gerir e resolver os pedidos de resolução de problemas gateways de aplicações. Para mais informações, consulte a [nossa documentação de diagnóstico.](./application-gateway-diagnostics.md#diagnostic-logging) 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Estabeleça uma política TLS para segurança extra
Certifique-se de que está a utilizar a versão mais recente da política TLS[(AppGwSslPolicy20170401S).](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s) Isto impõe TLS 1.2 e cifras mais fortes. Para obter mais informações, consulte [as versões de política do TLS e as suítes de cifra através do PowerShell](./application-gateway-configure-ssl-policy-powershell.md).