---
title: Monitorize várias séries de tempo numa única regra de alerta métrico
description: Alerta à escala usando uma regra de alerta único para séries de tempo múltiplas
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: 47a2fb9240f6826ed862f7a857264c09b79de9bc
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071090"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Monitorize várias séries de tempo numa única regra de alerta métrico

Uma regra de alerta métrico único pode ser usada para monitorizar uma ou muitas séries de tempo métricas, facilitando a monitorização dos recursos à escala.

## <a name="metric-time-series"></a>Séries de tempo métricas

Uma série de tempo métricas é uma série de medições (ou "valores métricos") capturadas ao longo de um período de tempo. 

Por exemplo:

- A utilização do CPU de uma máquina virtual
- Os bytes de entrada (entrada) para uma conta de armazenamento
- O número de pedidos falhados de uma aplicação web



## <a name="alert-rule-on-a-single-time-series"></a>Regra de alerta numa única série de tempo
Uma regra de alerta monitoriza uma única série de tempo quando satisfaz todas as seguintes condições:
-   A regra monitoriza um único recurso-alvo 
-   Contém uma única condição
-   Avalia uma métrica sem escolher dimensões (assumindo que a métrica suporta dimensões)

Um exemplo de tal regra de alerta (apenas com as propriedades relevantes indicada):
-   Recurso-alvo: *myVM1*
-   Métrica: *Percentagem CPU*
-   Operador: *Maior do que*
-   Limiar: *70*


Para esta regra de alerta, é monitorizada uma única série de tempo métrica:
-   CPU percentual onde *o recurso*='myVM1' > 70%

![Uma regra de alerta numa única série de tempo](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Regra de alerta em séries de tempo múltiplas
Uma regra de alerta monitoriza várias séries de tempo se utilizar pelo menos uma das seguintes características: 
-   Múltiplos recursos
-   Múltiplas condições 
-   Múltiplas dimensões


## <a name="multiple-resources-multi-resource"></a>Múltiplos recursos (multi-recursos)

Uma regra de alerta métrico único pode monitorizar múltiplos recursos, desde que os recursos sejam do mesmo tipo e existam na mesma região do Azure. A utilização deste tipo de regras reduz a complexidade e o número total de regras de alerta que tem de manter. 

Um exemplo de tal regra de alerta:
-   Recurso-alvo: *myVM1, myVM2*
-   Métrica: *Percentagem CPU*
-   Operador: *Maior do que*
-   Limiar: *70*

Para esta regra de alerta, duas séries de tempo métricas estão a ser monitorizadas separadamente:
-   CPU percentual onde *o recurso*='myVM1' > 70%
-   CPU percentual onde *o recurso*='myVM2' > 70%

![Uma regra de alerta de vários recursos](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
Numa regra de alerta multi-recursos, a condição é avaliada **separadamente** para cada um dos recursos (ou mais precisamente, para cada uma das séries de tempo métricas correspondidas a cada recurso). Isto significa que os alertas também são disparados para cada recurso separadamente.

Por exemplo, assuma que definimos a regra de alerta acima para monitorizar para CPU acima de 70%. No período de tempo avaliado (isto é, nos últimos 5 minutos)
-   A *percentagem de CPU* do *myVM1* é superior a 70% 
-   A *percentagem de CPU* do *myVM2* está em 50%

A regra de alerta dispara no *myVM1,* mas não *no myVM2*. Estes alertas desencadeados são independentes. Também podem resolver em momentos diferentes dependendo do comportamento individual de cada uma das máquinas virtuais.

Para obter mais informações sobre as regras de alerta de vários recursos e os tipos de recursos suportados para esta capacidade, consulte [a Monitorização em escala utilizando alertas métricos no Azure Monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> Numa regra de alerta métrico que monitoriza múltiplos recursos, apenas é permitida uma única condição.

## <a name="multiple-conditions-multi-condition"></a>Múltiplas condições (multi-condição)

Uma regra de alerta métrico também pode monitorizar até cinco condições por regra de alerta. 

Por exemplo:

- Recurso-alvo: *myVM1*
- Condição1
  - Métrica: *Percentagem CPU*
  - Operador: *Maior do que*
  - Limiar: *70*
- Condição2
  - Métrica: *Rede no total*
  - Operador: *Maior do que*
  - Limiar: *20 MB*

Para esta regra de alerta, estão a ser monitorizadas duas séries de tempo métricas:

- CPU percentual onde *o recurso*='myVM1' > 70%
- Rede no total onde *o recurso*='myVM1' > 20 MB

![Uma regra de alerta de várias condições](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Um operador 'E' é utilizado entre as condições. A regra de alerta dispara um alerta quando **todas as** condições estão reunidas. O alerta de disparo resolve se pelo menos uma das condições já não estiver reunida. 

> [!NOTE]
> Existem restrições ao utilizar dimensões numa regra de alerta com múltiplas condições. Para obter mais informações, consulte [restrições ao utilizar dimensões numa regra de alerta métrico com múltiplas condições](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Múltiplas dimensões (multi-dimensões)

Uma regra de alerta métrico também pode monitorizar valores de múltipla dimensão de uma métrica. As dimensões de uma métrica são pares de valor-nome que transportam dados adicionais para descrever o valor métrico. Por exemplo, a métrica de **Transações** de uma conta de armazenamento tem uma dimensão chamada **nome API,** descrevendo o nome da API chamada por cada transação (por exemplo, GetBlob, DeleteBlob, PutPage). O uso de dimensões é opcional, mas permite filtrar a métrica e apenas monitorizar séries de tempo específicas, em vez de monitorizar a métrica como um agregado de todos os valores dimensionais reunidos. 

Por exemplo, pode optar por ter um alerta disparado quando o número de transações é elevado em todos os nomes da API (que são os dados agregados), ou ainda decompô-lo em apenas alerta quando o número de transações é elevado para nomes específicos de API. 

Um exemplo de uma regra de alerta que monitoriza várias dimensões é:

- Recurso-alvo: *myStorage1*
- Métrica: *Transações*
- Dimensões
  * Nome API = *GetBlob, DeleteBlob, PutPage*
- Operador: *Maior do que*
- Limiar: *70*

Para esta regra de alerta, três séries de tempo métricas estão a ser monitorizadas:

- Transações em *que o recurso*='myStorage1' e o nome da *API*='GetBlob' > 70
- Transações em *que o recurso*='myStorage1' e o nome da *API*='DeleteBlob' > 70
- Transações em *que o recurso*='myStorage1' e o nome da *API*='PutPage' > 70

![Uma regra de alerta de várias dimensões com valores de uma dimensão](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Uma regra de alerta métrico de várias dimensões também pode monitorizar valores de múltiplas dimensões de **diferentes** dimensões de uma métrica. Neste caso, a regra de alerta monitoriza **separadamente** todas as dimensões combinações de valor dos valores de dimensão selecionados.

Um exemplo deste tipo de regra de alerta:

- Recurso-alvo: *myStorage1*
- Métrica: *Transações*
- Dimensões
  * Nome API = *GetBlob, DeleteBlob, PutPage*
  * Autenticação = *SAS, AccountKey*
- Operador: *Maior do que*
- Limiar: *70*

Para esta regra de alerta, seis séries de tempo métricas estão a ser monitorizadas separadamente:

- Transações em *que o recurso*='myStorage1' e o nome da *API*='GetBlob' e *a Autenticação*='SAS' > 70
- Transações em *que o recurso*='myStorage1' e o nome da *API*='GetBlob' e *a autenticação*='AccountKey' > 70
- Transações em *que o recurso*='myStorage1' e o nome *API*='DeleteBlob' e *a autenticação*='SAS' > 70
- Transações em *que o recurso*='myStorage1' e o nome *API*='DeleteBlob' e *a autenticação*='AccountKey' > 70
- Transações em *que o recurso*='myStorage1' e o nome da *API*='PutPage' e *a autenticação*='SAS' > 70
- Transações em *que o Recurso*='myStorage1' e o Nome *API*='PutPage' e *Autenticação*='AccountKey' > 70

![Uma regra de alerta de várias dimensões com valores de múltiplas dimensões](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Características avançadas de várias dimensões

1.  **Selecionando todas as dimensões atuais e futuras** – Pode optar por monitorizar todos os valores possíveis de uma dimensão, incluindo valores futuros. Tal regra de alerta irá escalar automaticamente para monitorizar todos os valores da dimensão sem que necessite de modificar a regra de alerta sempre que um valor de dimensão for adicionado ou removido.
2.  **Excluindo as dimensões** – Selecionar o operador '≠' (excluir) para um valor de dimensão equivale a selecionar todos os outros valores dessa dimensão, incluindo valores futuros.
3.  **Dimensões novas e personalizadas** – Os valores de dimensão apresentados no portal Azure baseiam-se em dados métricos recolhidos no último dia. Se o valor de dimensão que procura ainda não for emitido, pode adicionar um valor de dimensão personalizada.
4. **Dimensões correspondentes com um prefixo** - Pode optar por monitorizar todos os valores de dimensão que começam com um padrão específico, selecionando o operador 'Começa com' e introduzindo um prefixo personalizado.

![Características avançadas de várias dimensões](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Métrica alerta preços

O preço das regras de alerta métrico está disponível na página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Ao criar uma regra de alerta métrico, a estimativa de preços fornecida baseia-se nas características selecionadas e no número de séries temporizadas monitorizadas, que é determinada a partir da configuração da regra e dos valores métricos atuais. No entanto, a carga mensal baseia-se em avaliações reais das séries-tempo, podendo, portanto, diferir da estimativa original se algumas séries de tempo não tiverem dados para avaliar, ou se a regra de alerta usar funcionalidades que podem torná-la de forma dinâmica.

Por exemplo, uma regra de alerta pode mostrar uma estimativa de preço elevado se alavancar a característica multidimensional, e um grande número de combinações de valores de dimensão são selecionadas, resultando na monitorização de muitas séries de tempo. Mas a taxa real para essa regra de alerta pode ser menor se não todas as séries de tempo resultantes das combinações de valores de dimensão realmente têm dados para avaliar.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Número de séries de tempo monitorizadas por uma única regra de alerta

Para evitar custos excessivos, cada regra de alerta pode monitorizar até 5000 séries de tempo por padrão. Para levantar este limite da sua subscrição, abra um bilhete de apoio.


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a monitorização em escala utilizando alertas métricos e [limiares dinâmicos.](alerts-dynamic-thresholds.md)
