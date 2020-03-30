---
title: Boas práticas para escala automática
description: Padrões de escala automática em Azure para Aplicações Web, conjuntos de escala de máquinas virtuais e serviços de nuvem
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248921"
---
# <a name="best-practices-for-autoscale"></a>Melhores práticas do Dimensionamento Automático
A escala automática Do Monitor Azure aplica-se apenas a conjuntos de escala de [máquinas virtuais, serviços](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [de cloud,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Aplicações Web](https://azure.microsoft.com/services/app-service/web/)e serviços de [Gestão API.](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)

## <a name="autoscale-concepts"></a>Conceitos de escala automática

* Um recurso pode ter apenas *uma* definição de escala automática
* Uma definição de escala automática pode ter um ou mais perfis e cada perfil pode ter uma ou mais regras de escala automática.
* Uma definição de escala automática escala as instâncias horizontalmente, o que é *fora* aumentando as ocorrências e *diminuindo* o número de casos.
  Uma definição de escala automática tem um valor máximo, mínimo e padrão de instâncias.
* Um trabalho de escala automática lê sempre a métrica associada à escala, verificando se ultrapassou o limiar configurado para escala ou escala. Pode ver uma lista de métricas que a escala automática pode escalar através do [Azure Monitor autoscalcificar métricas comuns](autoscale-common-metrics.md).
* Todos os limiares são calculados a um nível de instância. Por exemplo, "escalar por um caso quando a CPU média > 80% quando a contagem de exemplos é 2", significa escala-out quando a média de CPU em todos os casos é superior a 80%.
* Todas as falhas de escala automática estão registadas no Registo de Atividades. Em seguida, pode configurar um alerta de registo de [atividade](./../../azure-monitor/platform/activity-log-alerts.md) para que possa ser notificado via e-mail, SMS ou webhooks sempre que houver uma falha de escala automática.
* Da mesma forma, todas as ações de escala bem sucedida são publicadas no Registo de Atividades. Em seguida, pode configurar um alerta de registo de atividade para que possa ser notificado via e-mail, SMS ou webhooks sempre que houver uma ação de escala automática bem sucedida. Também pode configurar notificações de e-mail ou webhook para ser notificado para ações de escala bem sucedidas através do separador de notificações na definição de escala automática.

## <a name="autoscale-best-practices"></a>Boas práticas de escala automática

Utilize as seguintes boas práticas à medida que utiliza a escala automática.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Verificar se os valores máximo e mínimo são diferentes e têm uma margem adequada entre si

Se tiver uma definição que tenha um mínimo=2, máxima=2 e a contagem de instâncias atual for de 2, não pode ocorrer qualquer ação de escala. Mantenha uma margem adequada entre as contagens máximas e mínimas, que são inclusivas. A escala automática escala sempre entre estes limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>O dimensionamento manual é reposto pelos valores máximo e mínimo do dimensionamento automático

Se atualizar manualmente a contagem de ocorrências para um valor acima ou inferior ao máximo, o motor de escala automática volta automaticamente ao mínimo (se abaixo) ou ao máximo (se acima). Por exemplo, estabelece-se o intervalo entre 3 e 6. Se tiver uma instância de corrida, o motor de escala automática escala três instâncias na sua próxima execução. Da mesma forma, se ajustar manualmente a balança para oito instâncias, na próxima escala de execução a escala automática irá escaloná-la para seis instâncias na sua próxima execução.  A escala manual é temporária, a menos que repor as regras de escala automática também.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Utilize sempre uma combinação de regras de escala e escala que realize um aumento e diminuição
Se utilizar apenas uma parte da combinação, a escala automática só tomará medidas numa única direção (escala para fora ou dentro) até atingir as contagens máximas ou mínimas de instância definidas no perfil. Isto não é ideal, idealmente, você quer que o seu recurso se esforce em momentos de alta utilização para garantir a disponibilidade. Da mesma forma, em momentos de baixo uso você quer que o seu recurso reduza a escala, para que você possa realizar economias de custos.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolher a estatística adequada para a métrica de diagnóstico
Para métricas de diagnóstico, pode escolher entre *média,* *mínima,* *máxima* e *total* como métrica a escala. A estatística mais comum é *a Média.*

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolher cuidadosamente os limiares para todos os tipos de métricas
Recomendamos que escolha minuciosamente diferentes limiares para escala e escala com base em situações práticas.

*Não recomendamos* configurações de escala automática como os exemplos abaixo com os mesmos valores limiares ou similares para fora e em condições:

* Aumentar as instâncias por 1 contagem quando a contagem de fios >= 600
* Diminuir as instâncias por 1 contagem quando a contagem de fios <= 600

Vejamos um exemplo do que pode levar a um comportamento que pode parecer confuso. Considere a seguinte sequência.

1. Assuma que há dois casos para começar e, em seguida, o número médio de fios por instância cresce para 625.
2. A escala automática esescala a adição de uma terceira instância.
3. Em seguida, suponha que a contagem média de fios em instância cai para 575.
4. Antes de reduzir, a escala automática tenta estimar qual será o estado final se escalonar. Por exemplo, 575 x 3 (contagem de exemplos atuais) = 1.725 / 2 (número final de casos quando reduzido) = 862,5 fios. Isto significa que a escala automática teria de voltar a escalar imediatamente mesmo depois de ter escalado, se a contagem média de fios se mantiver a mesma ou mesmo cair apenas uma pequena quantidade. No entanto, se voltasse a escalar, todo o processo se repetiria, levando a um ciclo infinito.
5. Para evitar esta situação (denominada "bater palmas"), a escala automática não diminui de todo. Em vez disso, salta e reavalia a condição novamente da próxima vez que o trabalho do serviço executar. O estado de bater pode confundir muitas pessoas porque a escala automática não parece funcionar quando a contagem média de fios era de 575.

A estimativa durante uma escala destina-se a evitar situações de "bater palmas", em que as ações de escala e escala vão continuamente para trás e para a frente. Tenha em mente este comportamento quando escolher os mesmos limiares para a escala para fora e para dentro.

Recomendamos a escolha de uma margem adequada entre a escala e os limiares. Como exemplo, considere a seguinte melhor combinação de regras.

* Aumentar as ocorrências em 1 contagem quando o CPU% >= 80
* Diminuir os casos em 1 contagem quando o CPU% <= 60

Neste caso  

1. Assuma que há 2 instâncias para começar.
2. Se o CPU% médio entre os casos for para 80, a escala automática aumenta adicionando uma terceira instância.
3. Agora assuma que com o tempo o CPU% cai para 60.
4. A regra da escala automática estima o estado final se for para escalar. Por exemplo, 60 x 3 (contagem de exemplos atuais) = 180 /2 (número final de casos quando reduzidos) = 90. Assim, a escala automática não faz escala, porque teria de voltar a escalar imediatamente. Em vez disso, salta para baixo.
5. Da próxima vez que a escala automática verificar, o CPU continua a cair para 50. Estima-se novamente - 50 x 3 instâncias = 150 / 2 instâncias = 75, que está abaixo do limiar de escala de 80, de modo que escala com sucesso para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações relativas aos valores dos limiares de dimensionamento das métricas especiais
 Para métricas especiais como a métrica de comprimento da fila de armazenamento ou de ônibus de serviço, o limiar é o número médio de mensagens disponíveis por número atual de instâncias. Escolha cuidadosamente o valor limiar para esta métrica.

Vamos ilustrar com um exemplo para garantir que você entenda melhor o comportamento.

* Aumentar as instâncias por 1 contagem quando a contagem de mensagens de fila de armazenamento >= 50
* Diminuir as instâncias por 1 contagem quando a contagem de mensagens de fila de armazenamento <= 10

Considere a seguinte sequência:

1. Há duas caixas de fila de armazenamento.
2. As mensagens continuam a chegar e quando analisa a fila de armazenamento, a contagem total diz 50. Pode supor que a escala automática deve iniciar uma ação de escala. No entanto, note que ainda é 50/2 = 25 mensagens por exemplo. Portanto, a escala-out não ocorre. Para que a primeira escala aconteça, a contagem total de mensagens na fila de armazenamento deve ser de 100.
3. Em seguida, assuma que a contagem total de mensagens chega aos 100.
4. Uma terceira instância de fila de armazenamento é adicionada devido a uma ação de escala.  A próxima ação de escala não acontecerá até que a contagem total de mensagens na fila atinja 150 porque 150/3 = 50.
5. Agora o número de mensagens na fila diminui. Com três casos, a primeira ação de escala-in acontece quando o total de mensagens em todas as filas soma 30 porque 30/3 = 10 mensagens por instância, que é o limiar de escala dentro.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações sobre o dimensionamento quando são configurados vários perfis numa definição de dimensionamento automático
Numa definição de escala automática, pode escolher um perfil predefinido, que é sempre aplicado sem qualquer dependência do horário ou da hora, ou pode escolher um perfil recorrente ou um perfil por um período fixo com uma data e hora.

Quando o serviço de escala automática os processa, verifica sempre a seguinte ordem:

1. Perfil de data fixa
2. Perfil recorrente
3. Perfil padrão ("Sempre")

Se for satisfeita uma condição de perfil, a escala automática não verifica a condição de perfil seguinte abaixo. A escala automática processa apenas um perfil de cada vez. Isto significa que, se pretender incluir também uma condição de processamento a partir de um perfil de nível inferior, deve incluir essas regras também no perfil atual.

Vamos rever usando um exemplo:

A imagem abaixo mostra uma definição de escala automática com um perfil predefinido de instâncias mínimas = 2 e instâncias máximas = 10. Neste exemplo, as regras são configuradas para escalar quando a contagem de mensagens na fila é superior a 10 e escala-in quando a contagem de mensagens na fila é inferior a três. Agora o recurso pode escalar entre dois a dez casos.

Além disso, há um perfil recorrente definido para segunda-feira. É definido para instâncias mínimas = 3 e instâncias máximas = 10. Isto significa que, na segunda-feira, os primeiros controlos à escala automática desta condição, se a contagem de ocorrências for em dois, estão reduzidos ao novo mínimo de três. Enquanto a escala automática continuar a encontrar esta condição de perfil correspondida (segunda-feira), apenas processa as regras de escala/in baseadas em CPU configuradas para este perfil. Neste momento, não verifica o comprimento da fila. No entanto, se também quiser que a condição de duração da fila seja verificada, deve incluir essas regras do perfil padrão também no seu perfil de segunda-feira.

Da mesma forma, quando a escala automática volta ao perfil predefinido, verifica primeiro se as condições mínimas e máximas são satisfeitas. Se o número de instâncias na altura for de 12, ele escala para 10, o máximo permitido para o perfil padrão.

![definições de escala automática](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações sobre o dimensionamento quando são configuradas várias regras num perfil

Há casos em que pode ter de definir várias regras num perfil. As seguintes regras de escala automática são utilizadas pelo motor de escala automática quando são definidas várias regras.

Na *escala para fora,* a escala automática corre se alguma regra for cumprida.
Na *escala,* a escala automática exige que todas as regras sejam cumpridas.

Para ilustrar, assuma que tem as seguintes quatro regras de escala automática:

* Se cpu < 30%, escala por 1
* Se a Memória < 50%, escala por 1
* Se a CPU > 75%, escala-out por 1
* Se a Memória > 75%, escala por 1

Em seguida, ocorre o seguinte:

* Se o CPU for de 76% e a Memória for de 50%, nós escalamos.
* Se o CPU for de 50% e a Memória for de 76%, nós escalamos.

Por outro lado, se o CPU for de 25% e a memória for de 51% de escala automática **não** faz escala. Para entrar em escala, a CPU deve ser de 29% e a Memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecionar sempre uma contagem de instâncias predefinidas segura
A contagem de instâncias padrão é importante porque a escala automática escala o seu serviço para essa contagem quando as métricas não estão disponíveis. Portanto, selecione uma contagem de instâncias padrão que seja segura para as suas cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
A escala automática será postada no Registo de Atividade se ocorrerem alguma das seguintes condições:

* A escala automática emite uma operação à escala.
* O serviço de escala automática completa com sucesso uma ação de escala.
* O serviço de escala automática não toma uma ação de escala.
* As métricas não estão disponíveis para o serviço de escala automática tomar uma decisão de escala.
* As métricas estão disponíveis (recuperação) novamente para tomar uma decisão de escala.

Também pode utilizar um alerta de Registo de Atividades para monitorizar a saúde do motor de escala automática. Aqui estão exemplos para criar um Alerta de Registo de [Atividades para monitorizar todas as operações](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) do motor de escala automática na sua subscrição ou para criar um Alerta de Registo de Atividade para monitorizar todas as operações de [escala automática falhadas na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Além de utilizar alertas de registo de atividade, também pode configurar notificações de e-mail ou webhook para ser notificado para ações de escala bem sucedidas através do separador de notificações na definição de escala automática.

## <a name="next-steps"></a>Passos Seguintes
- [Crie um Alerta de Registo de Atividades para monitorizar todas as operações do motor de escala automática na sua subscrição.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividades para monitorizar todas as operações de escala automática falhadas na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

