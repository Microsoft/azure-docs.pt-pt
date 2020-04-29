---
title: Suporte de volume de tráfego de gateway de aplicação
description: Este artigo fornece orientações para configurar o Portal de Aplicações Azure em suporte a cenários de volume de tráfego de rede elevados.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: 65f404b52a5fc06d8fa5bb5aad291e57fde8caba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80617204"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte de tráfego elevado para o Gateway de Aplicação

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar o seu Gateway de Aplicação para lidar com o tráfego extra devido ao elevado volume de tráfego que pode ocorrer devido à crise COVID-19.

Pode utilizar o Gateway de Aplicação com firewall de aplicação web (WAF) para uma forma escalável e segura de gerir o tráfego para as suas aplicações web.

As seguintes sugestões ajudam-no a configurar o Application Gateway com a WAF para lidar com o tráfego extra.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Use o v2 SKU sobre v1 para as suas capacidades autoscalcificadas e benefícios de desempenho
O v2 SKU oferece autoscalcificação para garantir que o seu Gateway de Aplicação pode aumentar à medida que o tráfego aumenta. Também oferece outros benefícios significativos de desempenho, tais como 5x melhor desempenho de descarregamento TLS, tempos de implementação e atualização mais rápidos, redundância de zona, e muito mais quando comparado com v1. Para mais informações, consulte a nossa [documentação v2.](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir contagem máxima de instância ao máximo possível (125)
 
Assumindo que tem um Gateway de aplicação v2 SKU, definir a contagem máxima de instância para o valor máximo possível de 125 permite que o Gateway de Aplicação se esforce conforme necessário. Isto permite-lhe lidar com o possível aumento de tráfego nas suas aplicações. Só será cobrado pelas Unidades de Capacidade (CUs) que utiliza.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Desconte a sua contagem mínima de exemplo com base no seu uso médio da UC

Assumindo que tem um Application Gateway v2 SKU, a autoscalcificação leva de 6 a 7 minutos para se esdimensionar. Com uma contagem mínima mais elevada, o Gateway de Aplicação pode lidar melhor com o seu tráfego quando a carga é aumentada, porque um pico de tráfego não requer uma operação de autoscalcificação.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Alerta se uma certa métrica ultrapassar 75% da utilização média da UC 
Consulte a documentação da [Aplicação Gateway Metrics](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) para obter uma explicação detalhada das nossas métricas e outras saídas. 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: Criação de um alerta sobre 75% da utilização média da UC

Este exemplo mostra como usar o portal Azure para configurar um alerta quando 75% da utilização média da UC é atingida. 
1. Navegue para o seu Gateway de **Aplicação.**
2. No painel esquerdo, selecione **Métricas** sob o separador **Monitor.** 
3. Adicione uma métrica para **unidades de computação corrente média**. 
![Configuração da métrica WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Se definiu a sua contagem mínima de instância para ser o seu uso médio de CU, vá em frente e estabeleça um alerta quando 75% dos seus casos mínimos estiverem em uso. Por exemplo, se o seu uso médio for de 10 CUs, coloque um alerta em 7,5 CUs. Isto alerta-o se o uso estiver a aumentar e lhe dá tempo para responder. Pode aumentar o mínimo se achar que este tráfego será sustentado para alertar que o tráfego pode estar a aumentar. 
![Configuração do alerta WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Pode definir o alerta para ocorrer a uma percentagem de utilização de CU inferior ou superior, dependendo da sensibilidade que pretende ser para potenciais picos de tráfego.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Configurar WAF com geofiltração e proteção de bots para parar ataques
Se pretender uma camada extra de segurança em frente à sua aplicação, utilize o Gateway de aplicação WAF_v2 SKU para as capacidades waf. Pode configurar o V2 SKU para permitir apenas o acesso às suas aplicações a partir de um determinado país ou país. Configura uma regra personalizada waf para permitir ou bloquear explicitamente o tráfego com base na geolocalização. Para mais informações, consulte a [geofiltração das regras personalizadas](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) e [como configurar regras personalizadas sobre o Gateway de aplicação WAF_v2 SKU através do PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Ativar a proteção do bot para bloquear bots maus conhecidos. Isto deve reduzir a quantidade de tráfego que chega à sua aplicação. Para mais informações, consulte a proteção do [bot com as instruções configuradas](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ligue os diagnósticos no Application Gateway e NO

Os registos de diagnóstico permitem-lhe visualizar registos de firewall, registos de desempenho e registos de acesso. Pode utilizar estes registos em Azure para gerir e resolver problemas nos Gateways de Aplicação. Para mais informações, consulte a nossa documentação de [diagnóstico.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging) 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Criar uma política de TLS para segurança extra
Certifique-se de que está a utilizar a mais recente versão política do TLS[(AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Isto aplica tLS 1.2 e cifras mais fortes. Para mais informações, consulte a configuração de [versões políticas TLS e suites de cifra através do PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
