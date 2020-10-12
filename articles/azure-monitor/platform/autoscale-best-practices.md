---
title: Melhores práticas para autoescala
description: Padrões de escala automática em Azure para aplicações web, conjuntos de escala de máquinas virtuais e serviços na Nuvem
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 414716fbbb36167e52c4f3b98c70ae7696ffea8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327060"
---
# <a name="best-practices-for-autoscale"></a>Melhores práticas do Dimensionamento Automático
A autoescala do Azure Monitor aplica-se apenas a [conjuntos de escalas de máquinas virtuais,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [serviços de nuvem,](https://azure.microsoft.com/services/cloud-services/) [serviço de aplicações - Web Apps](https://azure.microsoft.com/services/app-service/web/)e [serviços de Gestão API.](../../api-management/api-management-key-concepts.md)

## <a name="autoscale-concepts"></a>Conceitos de autoescala

* Um recurso pode ter apenas *uma* definição de autoescalação
* Uma definição de autoescala pode ter um ou mais perfis e cada perfil pode ter uma ou mais regras de autoescala.
* Uma definição de autoescala escalas de instâncias horizontalmente, o que é *fora* aumentando os casos e *diminuindo* o número de casos.
  Uma definição de autoescala tem um valor máximo, mínimo e predefinido de ocorrências.
* Um trabalho de autoescala lê sempre a métrica associada à escala, verificando se ultrapassou o limiar configurado para a escala ou escala. Pode ver uma lista de métricas que a autoescalação pode escalar em [Azure Monitor autoscaling métricas comuns](autoscale-common-metrics.md).
* Todos os limiares são calculados a um nível de instância. Por exemplo, "escala por um caso quando a CPU média > 80% quando a contagem de casos é de 2", significa escala quando a CPU média em todos os casos é superior a 80%.
* Todas as falhas de autoescala estão registadas no Registo de Atividades. Em seguida, pode configurar um [alerta de registo de atividade](./activity-log-alerts.md) para que possa ser notificado por e-mail, SMS ou webhooks sempre que houver uma falha de autoescalação.
* Da mesma forma, todas as ações de escala bem sucedidas são publicadas no Registo de Atividade. Em seguida, pode configurar um alerta de registo de atividade para que possa ser notificado por e-mail, SMS ou webhooks sempre que houver uma ação de autoescala bem sucedida. Também pode configurar notificações de email ou webhook para ser notificado para ações de escala bem sucedidas através do separador de notificações na definição de escala automática.

## <a name="autoscale-best-practices"></a>Melhores práticas de autoescala

Utilize as seguintes boas práticas enquanto utiliza a escala automática.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Verificar se os valores máximo e mínimo são diferentes e têm uma margem adequada entre si

Se tiver uma definição com o mínimo=2 e o máximo=2 e a contagem de instâncias atual for igual a 2, nenhuma ação de dimensionamento poderá ocorrer. Mantenha uma margem adequada entre as contagens de instâncias máxima e mínima, que são inclusivas. O dimensionamento automático dimensiona sempre entre estes limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>O dimensionamento manual é reposto pelos valores máximo e mínimo do dimensionamento automático

Se atualizar manualmente a contagem de ocorrências para um valor acima ou inferior ao máximo, o motor de autoescala automaticamente volta ao mínimo (se abaixo) ou ao máximo (se acima). Por exemplo, define o intervalo entre 3 e 6. Se tiver uma instância de funcionamento, o motor de escala automática escala para três instâncias na sua próxima corrida. Da mesma forma, se definir manualmente a escala para oito instâncias, na escala automática seguinte irá reduzi-la a seis instâncias na sua próxima execução.  A escala manual é temporária, a menos que também repõe as regras de autoescala.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Use sempre uma combinação de regras de escala e escala que realiza um aumento e diminuição
Se utilizar apenas uma parte da combinação, a autoescala só tomará medidas numa única direção (escala para fora, ou dentro) até atingir o máximo, ou as contagens mínimas de instância definidas no perfil. Isto não é o ideal, idealmente pretende que o seu recurso se dimensione em momentos de alta utilização para garantir a disponibilidade. Da mesma forma, em momentos de baixa utilização, pretende que o seu recurso reduza a sua escala, para que possa perceber a poupança de custos.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolher a estatística adequada para a métrica de diagnóstico
Para métricas de diagnóstico, pode escolher entre *Média,* *Mínima,* *Máxima* e *Total* como uma métrica à escala por. A estatística mais comum é *a média.*

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolher cuidadosamente os limiares para todos os tipos de métricas
Recomendamos que escolha cuidadosamente diferentes limiares para escala-out e escala-in com base em situações práticas.

*Não recomendamos* configurações de autoescala como os exemplos abaixo com os mesmos valores limiares ou similares para fora e em condições:

* Aumento das instâncias num número quando a Contagem de Threads >= 600
* Diminuição das instâncias num número quando o Contagem de Threads <= 600

Vamos olhar para um exemplo do que pode levar a um comportamento que pode parecer confuso. Considere a seguinte sequência.

1. Suponha que há dois casos para começar e, em seguida, o número médio de fios por exemplo cresce para 625.
2. A escala automática aumenta adicionando uma terceira instância.
3. Em seguida, assuma que a contagem média de fios em todos os casos cai para 575.
4. Antes de reduzir, a autoescala tenta estimar qual será o estado final se for escalonado. Por exemplo, 575 x 3 (contagem de instâncias correntes) = 1.725 / 2 (número final de casos quando reduzido) = 862,5 fios. Isto significa que a autoescala teria de voltar a escalar imediatamente, mesmo depois de ter escalado, se a contagem média de roscas permanecer a mesma ou mesmo cair apenas uma pequena quantidade. No entanto, se voltasse a aumentar, todo o processo se repetiria, levando a um ciclo infinito.
5. Para evitar esta situação (denominada "flapping"), a autoescala não diminui de todo. Em vez disso, salta e reavalia a condição novamente da próxima vez que o trabalho do serviço for executado. O estado de agitação pode confundir muitas pessoas porque a autoescala não parece funcionar quando a contagem média de fios era de 575.

A estimativa durante uma escala destina-se a evitar situações de "bater palmas", em que as ações de escala e de escala vão continuamente para trás e para a frente. Tenha este comportamento em mente quando escolher os mesmos limiares para a escala e dentro.

Recomendamos a escolha de uma margem adequada entre a escala e os limiares. Como exemplo, considere a seguinte melhor combinação de regras.

* Aumente as ocorrências em 1 conta quando CPU% >= 80
* Diminuir os casos em 1 conta quando CPU% <= 60

Neste caso  

1. Suponha que há 2 instâncias para começar.
2. Se o CPU% médio em todos os casos for para 80, a escala de autoescala adicionando uma terceira instância.
3. Agora assuma que com o tempo o CPU% cai para 60.
4. A regra de escala de auto-escala estima o estado final se for para escalar. Por exemplo, 60 x 3 (contagem de instâncias correntes) = 180 / 2 (número final de casos quando reduzido) = 90. Assim, a autoescala não se escala, pois teria de ser reduzida imediatamente. Em vez disso, salta para baixo.
5. Da próxima vez que verificar automaticamente, o CPU continua a cair para 50. Estima novamente - 50 x 3 instâncias = 150 / 2 instâncias = 75, que está abaixo do limiar de escala de 80, pelo que escala com sucesso para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações relativas aos valores dos limiares de dimensionamento das métricas especiais
 Para métricas especiais como a métrica do comprimento da fila de armazenamento ou de serviço, o limiar é o número médio de mensagens disponíveis por número atual de instâncias. Escolha cuidadosamente o valor limiar desta métrica.

Vamos ilustrá-lo com um exemplo para garantir que compreende melhor o comportamento.

* Aumente as ocorrências por 1 contagem quando a contagem de mensagens de fila de armazenamento >= 50
* Diminuir os casos por 1 contagem quando a contagem de mensagens de fila de armazenamento <= 10

Considere a seguinte sequência:

1. Há duas instâncias de fila de armazenamento.
2. As mensagens continuam a chegar e quando revês a fila de armazenamento, a contagem total é de 50. Pode presumir que a autoescala deve iniciar uma ação de escala. No entanto, note que ainda é 50/2 = 25 mensagens por exemplo. Então, a escala não ocorre. Para que a primeira escala aconteça, a contagem total de mensagens na fila de armazenamento deve ser de 100.
3. Em seguida, assuma que a contagem total de mensagens atinge 100.
4. Uma terceira caixa de fila de armazenamento é adicionada devido a uma ação de escala.  A próxima ação de escala não acontecerá até que a contagem total da mensagem na fila atinja 150 porque 150/3 = 50.
5. Agora o número de mensagens na fila fica menor. Em três instâncias, a primeira ação de escala ocorre quando o total de mensagens em todas as filas soma 30 porque 30/3 = 10 mensagens por exemplo, que é o limiar de escala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações sobre o dimensionamento quando são configurados vários perfis numa definição de dimensionamento automático
Numa definição de escala automática, pode escolher um perfil predefinido, que é sempre aplicado sem qualquer dependência do horário ou hora, ou pode escolher um perfil recorrente ou um perfil por um período fixo com uma data e intervalo de tempo.

Quando o serviço de autoescalação os processa, verifica sempre a seguinte encomenda:

1. Perfil de Data Fixa
2. Perfil recorrente
3. Perfil padrão ("Sempre")

Se uma condição de perfil for satisfeita, a autoescala não verifica a próxima condição de perfil abaixo. A autoescala apenas um perfil de cada vez. Isto significa que se você quiser também incluir uma condição de processamento de um perfil de nível inferior, você deve incluir essas regras também no perfil atual.

Vamos rever usando um exemplo:

A imagem abaixo mostra uma definição de autoescalação com um perfil predefinido de instâncias mínimas = 2 e instâncias máximas = 10. Neste exemplo, as regras são configuradas para escalar quando a contagem de mensagens na fila é maior que 10 e escala quando a contagem de mensagens na fila é inferior a três. Então agora o recurso pode escalar entre dois e dez instâncias.

Além disso, há um perfil recorrente definido para segunda-feira. É definido para instâncias mínimas = 3 e ocorrências máximas = 10. Isto significa que, na segunda-feira, a primeira vez verifica automaticamente esta condição, se a contagem de exemplos for de dois, escala para o novo mínimo de três. Enquanto a autoescala continuar a encontrar esta condição de perfil correspondida (segunda-feira), apenas processa as regras de escala/in configuradas para este perfil com base na CPU. Neste momento, não verifica o comprimento da fila. No entanto, se também quiser que a condição de comprimento da fila seja verificada, deve incluir essas regras do perfil padrão também no seu perfil de segunda-feira.

Da mesma forma, quando a autoescala volta ao perfil predefinido, verifica primeiro se as condições mínimas e máximas são satisfeitas. Se o número de ocorrências no momento for de 12, ele escala para 10, o máximo permitido para o perfil predefinido.

![definições de autoescala](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações sobre o dimensionamento quando são configuradas várias regras num perfil

Há casos em que pode ter de definir várias regras num perfil. As seguintes regras de autoescala são utilizadas pelo motor de autoescala quando são definidas várias regras.

Em *escala,* a escala é automática, se alguma regra for cumprida.
Na *escala,* a escala automática exige que todas as regras sejam cumpridas.

Para ilustrar, assuma que tem as seguintes quatro regras de autoescala:

* Se a CPU < 30%, escala em 1
* Se a memória < 50%, escala em 1
* Se a CPU > 75%, escala em 1
* Se a Memória > 75%, escala por 1

Em seguida, ocorre o seguinte:

* Se o CPU é de 76% e a memória é de 50%, nós escalamos.
* Se o CPU for 50% e a Memória for de 76%, nós escalamos.

Por outro lado, se o CPU for de 25% e a memória for de 51% de autoescala **não** se encaitão. Para a escala, a CPU deve ser de 29% e memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecionar sempre uma contagem de instâncias predefinidas segura
A contagem de casos predefinidos é importante porque a escala automática do seu serviço para essa contagem quando as métricas não estão disponíveis. Portanto, selecione uma contagem de instâncias padrão que seja segura para as suas cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
A autoescala será publicada no Registo de Atividades se ocorrerem alguma das seguintes condições:

* A autoescala uma operação de escala.
* O serviço de autoescala completa com sucesso uma ação em escala.
* O serviço de autoescala não toma uma medida de escala.
* As métricas não estão disponíveis para o serviço de autoescala para tomar uma decisão de escala.
* As métricas estão disponíveis (recuperação) novamente para tomar uma decisão de escala.

Também pode utilizar um alerta de Registo de Atividade para monitorizar a saúde do motor de autoescala. Aqui estão exemplos para [criar um Alerta de Registo de Atividade para monitorizar todas as operações de motor de autoescala na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) ou para [criar um Alerta de Registo de Atividade para monitorizar todas as operações de escala automática falhadas na sua subscrição.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

Além de utilizar alertas de registo de atividade, também pode configurar notificações de email ou webhook para ser notificado para ações de escala bem sucedida através do separador de notificações na definição de escala automática.

## <a name="next-steps"></a>Passos Seguintes
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações do motor de escala automática na sua subscrição.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Crie um Alerta de Registo de Atividade para monitorizar todas as operações falhadas de escala automática na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

