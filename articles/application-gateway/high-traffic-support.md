---
title: Suporte de volume de tráfego de Gateway de aplicação
description: Este artigo fornece orientações para configurar o Azure Application Gateway em apoio a cenários de volume de tráfego de rede elevado.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: a5f7569fc46d4678ca0c12299e33caa3c78df849
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182925"
---
# <a name="application-gateway-high-traffic-support"></a>Suporte de tráfego elevado para o Gateway de Aplicação

>[!NOTE]
> Este artigo descreve algumas diretrizes sugeridas para ajudá-lo a configurar o seu Gateway de Aplicação para lidar com o tráfego extra para qualquer volume de tráfego elevado que possa ocorrer. Os limiares de alerta são puramente sugestões e de natureza genérica. Os utilizadores podem determinar limiares de alerta com base nas suas expectativas de carga de trabalho e utilização.

Pode utilizar o Gateway de Aplicações com Firewall de Aplicação Web (WAF) para uma forma escalável e segura de gerir o tráfego das suas aplicações web.

É importante que escale o seu Gateway de aplicação de acordo com o seu tráfego e com um pouco de tampão para que esteja preparado para quaisquer picos ou picos de tráfego e minimizando o impacto que pode ter no seu QoS. As seguintes sugestões ajudam-no a configurar o Application Gateway com a WAF para lidar com o tráfego extra.

Verifique a documentação das [métricas](./application-gateway-metrics.md) para obter a lista completa das métricas oferecidas pela Application Gateway. Consulte [as métricas](./application-gateway-metrics.md#metrics-visualization) do portal Azure e a documentação do [monitor Azure](../azure-monitor/platform/alerts-metric.md) sobre como definir alertas para métricas.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Escala para Gateway de Aplicação v1 SKU (Standard/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Desente a sua contagem de ocorrências com base no seu pico de utilização do CPU
Se estiver a utilizar um gateway V1 SKU, terá a capacidade de configurar o seu Gateway de Aplicação até 32 instâncias para dimensionamento. Verifique a utilização do CPU da sua Aplicação Gateway no último mês para obter quaisquer picos acima de 80%, está disponível como métrica para monitorizar. Recomenda-se que estale a contagem de exemplos de acordo com o seu uso máximo e com um tampão adicional de 10% a 20% para responder a quaisquer picos de tráfego.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Métricas de utilização do CPU V1" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Utilize o V2 SKU em v1 para as suas capacidades de autoscalagem e benefícios de desempenho
O V2 SKU oferece autoscaling para garantir que o seu Gateway de Aplicação pode aumentar à medida que o tráfego aumenta. Também oferece outros benefícios significativos de desempenho, tais como 5x melhor desempenho de descarregamento TLS, tempos de implementação e atualização mais rápidos, redundância de zona, e muito mais quando comparado com v1. Para mais informações, consulte a nossa [documentação v2](./application-gateway-autoscaling-zone-redundant.md) e consulte a nossa [documentação de migração](./migrate-v1-v2.md) v1 v2 para aprender a migrar os seus gateways V1 SKU existentes para v2 SKU. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Autoscalagem para Gateway de Aplicação v2 SKU (Standard_v2/WAF_v2 SKU)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Definir a contagem máxima de instância para o máximo possível (125)
 
Para o Gateway de Aplicação v2 SKU, definir a contagem máxima de instância para o valor máximo possível de 125 permite que o Gateway de aplicação se escalone conforme necessário. Isto permite-lhe lidar com o possível aumento de tráfego para as suas aplicações. Só será cobrado para as Unidades de Capacidade (CUs) que utilizar. 

Certifique-se de verificar o tamanho da sub-rede e a contagem de endereços IP disponíveis na sua sub-rede e desaponho a contagem máxima de instância com base nisso. Se a sua sub-rede não tiver espaço suficiente para acomodar, terá de recriar o seu gateway na mesma sub-rede ou diferente que tem capacidade suficiente. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="Configuração de autoescalagem V2" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Desaça a sua contagem mínima de instância com base na sua utilização média da Unidade computacional

Para o Application Gateway v2 SKU, a autoscalagem leva de seis a sete minutos para escalar e providenciar um conjunto adicional de instâncias prontas para tomar o tráfego. Até lá, se houver pequenos picos no tráfego, as suas instâncias de gateway existentes podem ficar sob stress e isso pode causar uma latência inesperada ou perda de tráfego. 

Recomenda-se que estale a contagem mínima de instância para um nível ideal. Por exemplo, se necessitar de 50 instâncias para lidar com o tráfego na carga máxima, então definir o mínimo de 25 a 30 é uma boa ideia em vez de <10 para que, mesmo quando houver pequenas rajadas de tráfego, o Application Gateway seja capaz de lidar com ele e dar tempo suficiente para que o autoscaling responda e produza efeito.

Verifique a sua métrica da Unidade de Cálculo durante o último mês. A métrica da unidade computacional é uma representação da utilização do CPU do seu gateway e, com base no seu uso máximo dividido por 10, pode definir o número mínimo de instâncias necessárias. Note que 1 instância de gateway de aplicação pode lidar com um mínimo de 10 unidades de computação

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="Métricas da unidade computacional V2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Escala manual para Gateway de Aplicação v2 SKU (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Desaça a sua contagem de ocorrências com base no seu pico de utilização da Unidade computacional 

Ao contrário da autoscalagem, na escala manual, deve definir manualmente o número de casos do seu gateway de aplicação com base nos requisitos de tráfego. Recomenda-se que estale a contagem de exemplos de acordo com o seu uso máximo e com um tampão adicional de 10% a 20% para responder a quaisquer picos de tráfego. Por exemplo, se o seu tráfego necessitar de 50 instâncias no pico, provisões de 55 a 60 instâncias para lidar com picos de tráfego inesperados que possam ocorrer.

Verifique a sua métrica da Unidade de Cálculo durante o último mês. Compute unit metric é uma representação da utilização do CPU do seu gateway e com base no seu uso máximo dividido por 10, você pode definir o número de instâncias necessárias, uma vez que 1 instância de gateway de aplicação pode lidar com um mínimo de 10 unidades de computação

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

Para ser notificado de quaisquer anomalias de tráfego ou utilização, pode configurar alertas em determinadas métricas. Consulte [a documentação das métricas](./application-gateway-metrics.md) para a lista completa das métricas oferecidas pela Application Gateway. Consulte [as métricas](./application-gateway-metrics.md#metrics-visualization) do portal Azure e a documentação do [monitor Azure](../azure-monitor/platform/alerts-metric.md) sobre como definir alertas para métricas.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Alertas para Gateway de Aplicação v1 SKU (Standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Alerta se a utilização média do CPU ultrapassa os 80%

Em condições normais, a utilização da CPU não deve exceder regularmente os 90%, uma vez que isso pode causar latência nos sites alojados por trás do Gateway de Aplicação e perturbar a experiência do cliente. Pode controlar ou melhorar indiretamente a utilização do CPU modificando a configuração do Gateway de aplicação aumentando a contagem de casos ou movendo-se para um tamanho SKU maior ou fazendo ambos. Desateia um alerta se a métrica de utilização do CPU for superior a 80% da média.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Alerta se a contagem de hospedeiros não saudáveis cruza o limiar

Esta métrica indica o número de servidores backend que o gateway da aplicação não é capaz de sondar com sucesso. Isto irá apanhar problemas em que as instâncias de gateway de aplicação não conseguem ligar-se ao backend. Alerta se este número for superior a 20% da capacidade de backend. Por exemplo, se atualmente tiver 30 servidores de backend na sua piscina de backend, desaceia se a contagem de hospedeiros não saudável for superior a 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Alerta se o estado de resposta (4xx, 5xx) cruza o limiar 

Crie alerta quando o estado de resposta do Gateway de Aplicação for de 4xx ou 5xx. Pode haver uma resposta ocasional de 4xx ou 5xx vista devido a problemas transitórios. Deve observar o gateway na produção para determinar o limiar estático ou utilizar o limiar dinâmico para o alerta.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Alerta se pedidos falhados cruzam limiar 

Crie alerta quando os pedidos falhados cruzam o limiar. Deve observar o gateway na produção para determinar o limiar estático ou utilizar o limiar dinâmico para o alerta.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Exemplo: Criação de um alerta para mais de 100 pedidos falhados nos últimos 5 minutos

Este exemplo mostra como utilizar o portal Azure para configurar um alerta quando a contagem de pedidos falhada nos últimos 5 minutos é superior a 100.
1. Navegue para o seu **Gateway de Aplicações**.
2. No painel esquerdo, selecione **Métricas** no **separador Monitor.** 
3. Adicione uma métrica para **pedidos falhados.**
4. Clique em **Nova regra de alerta** e defina a sua condição e ações
5. Clique em **Criar regra de alerta** para criar e ativar o alerta

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 criar alertas" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Alertas para Gateway de Aplicação v2 SKU (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Alerta se a utilização da Unidade computacional cruza 75% do uso médio 

A unidade de cálculo é a medida de utilização computacional do seu Gateway de Aplicação. Verifique a utilização média da unidade de computação no último mês e desemave o alerta se atravessar 75% do mesmo. Por exemplo, se o seu uso médio for de 10 unidades de computação, desateia um alerta em 7,5 CUs. Isto alerta-o se o uso está a aumentar e dá-lhe tempo para responder. Pode aumentar o mínimo se achar que este tráfego será sustentado para alertá-lo de que o tráfego pode estar a aumentar. Siga as sugestões de escalonamento acima para escalar se necessário.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Exemplo: Estabelecer um alerta sobre 75% do uso médio da UC

Este exemplo mostra como usar o portal Azure para configurar um alerta quando 75% do uso médio da UC é atingido. 
1. Navegue para o seu **Gateway de Aplicações**.
2. No painel esquerdo, selecione **Métricas** no **separador Monitor.** 
3. Adicione uma métrica para **unidades de computação corrente médias.** 
4. Se definiu a sua contagem mínima de instância para ser o seu uso médio de CU, vá em frente e desate um alerta quando 75% das suas instâncias mínimas estiverem em uso. Por exemplo, se o seu uso médio for de 10 CUs, coloque um alerta em 7,5 CUs. Isto alerta-o se o uso está a aumentar e dá-lhe tempo para responder. Pode aumentar o mínimo se achar que este tráfego será sustentado para alertá-lo de que o tráfego pode estar a aumentar. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="Alertas da unidade computacional V2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> Pode definir o alerta para ocorrer a uma percentagem de utilização de CU inferior ou superior, dependendo da sensibilidade que pretende ser para potenciais picos de tráfego.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Alerta se a utilização da Unidade de Capacidade cruza 75% do pico de utilização 

As unidades de capacidade representam a utilização global do gateway em termos de produção, cálculo e contagem de ligação. Verifique a utilização máxima da unidade de capacidade no último mês e desemave o alerta se atravessar 75% do mesmo. Por exemplo, se o seu uso máximo for de 100 unidades de capacidade, desateia um alerta em 75 CUs. Siga as duas sugestões acima para escalar, se necessário.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Alerta se a contagem de hospedeiros não saudáveis cruza o limiar 

Esta métrica indica o número de servidores backend que o gateway da aplicação não é capaz de sondar com sucesso. Isto irá apanhar problemas em que as instâncias de gateway de aplicação não conseguem ligar-se ao backend. Alerta se este número for superior a 20% da capacidade de backend. Por exemplo, se atualmente tiver 30 servidores de backend na sua piscina de backend, desaceia se a contagem de hospedeiros não saudável for superior a 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Alerta se o estado de resposta (4xx, 5xx) cruza o limiar 

Crie alerta quando o estado de resposta do Gateway de Aplicação for de 4xx ou 5xx. Pode haver uma resposta ocasional de 4xx ou 5xx vista devido a problemas transitórios. Deve observar o gateway na produção para determinar o limiar estático ou utilizar o limiar dinâmico para o alerta.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Alerta se pedidos falhados cruzam limiar 

Crie alerta quando os pedidos falhados cruzam o limiar. Deve observar o gateway na produção para determinar o limiar estático ou utilizar o limiar dinâmico para o alerta.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Alerta se o tempo de resposta do backend durar passa pelo limiar 

Esta métrica indica o intervalo de tempo entre o início do estabelecimento de uma ligação ao servidor backend e a receção do último byte do corpo de resposta. Crie um alerta se a latência da resposta de backend for mais do que um determinado limiar do habitual. Por exemplo, desempende-o quando a latência da resposta ao backend aumentar mais de 30% em relação ao valor habitual.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Alerta se o tempo total do Gateway de Aplicação cruza o limiar

Este é o intervalo a partir do momento em que o Application Gateway recebe o primeiro byte do pedido HTTP até ao momento em que o último byte de resposta foi enviado ao cliente. Deve criar um alerta se a latência da resposta de backend for mais do que um determinado limiar do habitual. Por exemplo, podem definir isto para ser alertado quando o tempo total aumenta em mais de 30% do valor habitual.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Configurar a WAF com geo filtragem e proteção de bots para parar os ataques
Se quiser uma camada extra de segurança à frente da sua aplicação, utilize o Gateway de Aplicação WAF_v2 SKU para capacidades WAF. Pode configurar o V2 SKU para permitir apenas o acesso às suas aplicações a partir de um determinado país/região ou países/regiões. Configura uma regra personalizada da WAF para permitir ou bloquear explicitamente o tráfego com base na localização geoparal. Para obter mais informações, consulte [as regras personalizadas de filtragem de geo filtragem](../web-application-firewall/ag/geomatch-custom-rules.md) e [como configurar regras personalizadas no Gateway de Aplicações WAF_v2 SKU através do PowerShell](../web-application-firewall/ag/configure-waf-custom-rules.md).

Permitir a proteção do bot para bloquear os robôs maus conhecidos. Isto deve reduzir a quantidade de tráfego que chega à sua aplicação. Para obter mais informações, consulte [a proteção do bot com instruções definidas](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Ligue os diagnósticos no Application Gateway e NA WAF

Os registos de diagnóstico permitem-lhe visualizar registos de firewall, registos de desempenho e registos de acesso. Pode utilizar estes registos em Azure para gerir e resolver os pedidos de resolução de problemas gateways de aplicações. Para mais informações, consulte a [nossa documentação de diagnóstico.](./application-gateway-diagnostics.md#diagnostic-logging) 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Estabeleça uma política TLS para segurança extra
Certifique-se de que está a utilizar a versão mais recente da política TLS[(AppGwSslPolicy20170401S).](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s) Isto impõe TLS 1.2 e cifras mais fortes. Para obter mais informações, consulte [as versões de política do TLS e as suítes de cifra através do PowerShell](./application-gateway-configure-ssl-policy-powershell.md).