---
title: Melhores práticas do dimensionamento automático
description: Padrões de dimensionamento automático no Azure para aplicações Web, conjuntos de dimensionamento de máquinas virtuais e serviços Cloud
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 3700fb90318da3787830f9b6c202436c0e45e2fe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454107"
---
# <a name="best-practices-for-autoscale"></a>Melhores práticas do Dimensionamento Automático
Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços Cloud](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicações - aplicações Web](https://azure.microsoft.com/services/app-service/web/), e [deserviçosdegestãodeAPI](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Conceitos de dimensionamento automático
* Um recurso pode ter apenas *um* definição de dimensionamento automático
* Uma definição de dimensionamento automático pode ter um ou mais perfis e cada perfil podem ter uma ou mais regras de dimensionamento automático.
* Uma definição de dimensionamento automático pode ser dimensionada instâncias horizontalmente, que é *horizontalmente* aumentando as instâncias e *no* ao reduzir o número de instâncias.
  Uma definição de dimensionamento automático tem um máximo, o mínimo e o valor predefinido de instâncias.
* Uma tarefa de dimensionamento automático sempre lê a métrica associada para reduzir horizontalmente para, verificar se ele tem ultrapassou o limiar configurado de escalamento horizontal ou redução horizontal. Pode exibir uma lista de métricas que dimensionamento automático pode dimensionar por na [métricas comuns do dimensionamento automático do Azure Monitor](autoscale-common-metrics.md).
* Todos os limiares são calculados num nível de instância. Por exemplo, "aumentar horizontalmente numa instância quando média da CPU > 80% quando a contagem de instâncias é 2", significa aumentar horizontalmente quando a CPU média de todas as instâncias é superior a 80%.
* Todas as falhas de dimensionamento automático são registadas no registo de atividade. Em seguida, pode configurar uma [alerta de registo de atividade](./../../azure-monitor/platform/activity-log-alerts.md) para que pode ser notificado por e-mail, SMS ou webhooks sempre que houver uma falha de dimensionamento automático.
* Da mesma forma, todas as ações de dimensionamento com êxito são publicadas no registo de atividades. Em seguida, pode configurar um alerta de registo de atividade para que pode ser notificado por e-mail, SMS ou webhooks sempre que há uma ação de dimensionamento automático com êxito. Também pode configurar notificações de e-mail ou webhook para obter notificações de ações de dimensionamento com êxito através do separador notificações sobre a definição de dimensionamento automático.

## <a name="autoscale-best-practices"></a>Melhores práticas de dimensionamento automático
Utilize as seguintes melhores práticas de como utilizar o dimensionamento automático.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Certifique-se de que os valores máximo e mínimos são diferentes e tem uma margem adequada entre eles
Se tiver uma definição que tiver mínimo = 2, máximo = 2 e o atual número de instâncias é 2, nenhuma ação de dimensionamento pode ocorrer. Manter uma margem adequada entre as contagens de instâncias mínima e máxima, o que estão incluídos. Dimensionamento automático pode ser dimensionada sempre entre estes limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Dimensionamento manual é reposto pela dimensionamento automático min e max
Caso atualize manualmente a contagem de instâncias para um valor acima ou abaixo do máximo, o mecanismo de dimensionamento automático é dimensionado automaticamente novamente para o mínimo (se abaixo) ou o máximo (if acima). Por exemplo, defina o intervalo entre 3 e 6. Se tiver uma instância em execução, o mecanismo de dimensionamento automático pode ser dimensionada para três instâncias em sua próxima execução. Da mesma forma, se definir manualmente a escala para oito instâncias, na próxima execução dimensionamento automático irá dimensioná-lo novamente para seis instâncias em sua próxima execução.  Dimensionamento manual é temporário, a menos que repõe as regras de dimensionamento automático também.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Utilize sempre uma combinação de regra de aumento horizontal e a redução horizontal que executa um aumento e diminuição
Se utilizar apenas uma parte da combinação, o dimensionamento automático irá apenas a tomar uma ação numa única direção (out ou em escala), até atingir o máximo ou contagens de instâncias mínima de definidas no perfil. Isso não é o ideal, o ideal é que deseja que o recurso para aumentar verticalmente às vezes de alto uso para garantir a disponibilidade. Da mesma forma, às vezes de baixa utilização que pretende que o recurso para reduzir verticalmente, por isso, pode obter poupança de custos.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolha a estatística apropriada para sua métrica de diagnóstico
Para métricas de diagnóstico, pode escolher entre *médio*, *mínimo*, *máxima* e *Total* como uma métrica para reduzir horizontalmente para. A estatística mais comuns é *média*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolha os limiares com cuidado para todos os tipos de métrica
Recomendamos que escolha cuidadosa das limiares diferentes para aumentar horizontalmente e escala-in com base em situações práticas.

Estamos *não é recomendável* definições de dimensionamento automático, como os exemplos abaixo com os valores de limiar do mesmo ou muito semelhante para terminar e, em condições:

* Aumento de instâncias por 1 contagem quando contagem de threads < = 600
* Instâncias de uma redução até 1 contagem quando contagem de threads > = 600

Vamos examinar um exemplo de como o que pode levar a um comportamento que pode parecer confuso. Considere a seguinte sequência.

1. Suponha que existem duas instâncias para começar e, em seguida, o número médio de threads por instância aumenta para 625.
2. Dimensionamento automático aumenta horizontalmente adição de uma instância de terceiro.
3. Em seguida, partem do princípio de que a contagem de threads médio entre a instância está em 575.
4. Antes de dimensionar para baixo, tentativas de dimensionamento automático para estimar o que o estado final será se ele reduzido horizontalmente. Por exemplo, 575 x 3 (contagem de instâncias atual) = 1,725 / 2 (último número de instâncias quando reduzidos verticalmente) = 862.5 threads. Isso significa que o dimensionamento automático tinha que imediatamente horizontal novamente, mesmo depois foi dimensionado, se a contagem de threads médio permanece o mesmo ou até mesmo recai apenas uma pequena quantidade. No entanto, se ele aumentado verticalmente novamente, todo o processo repetiria, que leva a um loop infinito.
5. Para evitar esta situação (denominada "flapping"), dimensionamento automático não reduzir verticalmente em todos os. Em vez disso, ele ignora e revê a condição novamente na próxima vez em que executa a tarefa do serviço. Isto pode baralhar muitas pessoas, porque o dimensionamento automático não parece funcionar quando a contagem média de threads era 575.

Estimativa durante um dimensionamento-in destina-se para evitar em "oscilação" situações, onde as ações de redução horizontal e Escalamento continuamente então voltar e avançar. Mantenha esse comportamento em mente ao escolher os limiares de mesmo de escalamento horizontal e na.

Recomendamos que escolha uma margem adequada entre o Escalamento horizontal e limiares. Por exemplo, considere a seguinte combinação de regra melhor.

* Aumento de instâncias por 1 contagem quando % da CPU > = 80
* Instâncias de uma redução até 1 contagem quando % de CPU < = 60

Neste caso  

1. Assumir que existem 2 instâncias para começar.
2. Se a % de CPU média em instâncias vai para 80, o dimensionamento automático aumenta horizontalmente a adição de uma instância de terceiro.
3. Agora vamos supor que, ao longo do tempo a % de CPU está em 60.
4. Regra de redução horizontal do dimensionamento automático calcula o estado final, se tiver de ser de redução horizontal. Por exemplo, 60 x 3 (contagem de instâncias atual) = 180 / 2 (último número de instâncias quando reduzidos verticalmente) = 90. Então, dimensionamento automático não escala no porque ele teria que aumentar horizontalmente imediato. Em vez disso, ele ignora a reduzir verticalmente.
5. Verifica a próxima hora de dimensionamento automático, a CPU continua a ser de 50. -Lo novamente - as estimativas de instância de 50 x 3 = 150 / 2 instâncias = 75, que é inferior ao limiar de escalamento horizontal de 80, para que ele reduz horizontalmente com êxito para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações para dimensionar os valores de limiar para métricas especiais
 Para obter métricas especiais, como a métrica de comprimento de armazenamento ou fila do Service Bus, o limite é o número médio de mensagens por número atual de instâncias. Escolha cuidadosamente o valor de limiar para esta métrica.

Vamos ilustrar com um exemplo para se certificar de que compreende o comportamento melhor.

* Aumento de instâncias por 1 contagem quando a contagem de mensagens de fila de armazenamento > = 50
* Diminuir instâncias por 1 contagem quando a contagem de mensagens de fila de armazenamento < = 10

Considere a seguinte sequência:

1. Existem duas instâncias de fila de armazenamento.
2. Mensagens continuo e ao rever a fila de armazenamento, a contagem total lê 50. Pode pressupor que dimensionamento automático deve iniciar uma ação de escalamento horizontal. No entanto, tenha em atenção que é ainda 50/2 = 25 mensagens por instância. Assim, aumentar horizontalmente não ocorre. Para o primeiro aumentar horizontalmente a ocorrer, a contagem total de mensagens na fila de armazenamento deve ser a 100.
3. Em seguida, partem do princípio de que a contagem total de mensagens atinge 100.
4. Uma instância de fila de armazenamento 3 é adicionada devido a uma ação de escalamento horizontal.  A próxima ação de escalamento horizontal não irá ocorrer até a contagem total de mensagens na fila atinge 150 porque 150/3 = 50.
5. Agora o número de mensagens na fila fica menor. Com três instâncias, a primeira ação de redução horizontal acontece quando o total de mensagens em todas as filas adiciona até 30 porque 30/3 = 10 mensagens por instância, o que é o limiar de redução horizontal.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações para dimensionar quando vários perfis são configurados na definição de dimensionamento automático
Na definição de dimensionamento automático, pode escolher um perfil de padrão, o que é sempre aplicado sem qualquer dependência do calendário ou na hora, ou pode escolher um perfil recorrente ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de dimensionamento automático processa-os, ela verifica sempre na seguinte ordem:

1. Perfil de data fixa
2. Perfil recorrente
3. Padrão de perfil ("sempre")

Se for cumprida uma condição de perfil, dimensionamento automático não verifica se a condição de perfil seguinte abaixo dele. Dimensionamento automático só processa um perfil de cada vez. Isso significa que se quiser também incluem uma condição de processamento de um perfil de escalão inferior, tem de incluir essas regras também no perfil de atual.

Vamos examinar isso com um exemplo:

A imagem abaixo mostra uma definição de dimensionamento automático com um perfil predefinido de instâncias mínimas de 2 e máximas de instâncias de = = 10. Neste exemplo, regras estão configuradas para aumentar horizontalmente quando a contagem de mensagens na fila é superior a 10 e escala-in quando a contagem de mensagens na fila é inferior a três. Portanto, agora o recurso pode ser dimensionado entre dois e dez instâncias.

Além disso, existe um conjunto de segunda de perfis de recorrentes. Ele é definido para instâncias mínimas = 3 e máximas de instâncias = 10. Isso significa na segunda-feira, as verificações de dimensionamento automático de iniciantes para esta condição, se a contagem de instâncias é duas, ele é dimensionado para o novo mínimo de três. Desde que o dimensionamento automático continua a encontrar esta condição de perfil correspondentes (segunda-feira), só processa as CPU regras baseadas em escala-out/in configuradas para este perfil. Neste momento, ele não verifica se o comprimento da fila. No entanto, se também pretender que a condição de comprimento da fila a ser verificado, deve incluir essas regras do perfil predefinido também no seu perfil de segunda-feira.

Da mesma forma, quando o dimensionamento automático muda para o perfil predefinido, ele primeiro verifica se estiverem reunidas as condições de mínimas e máxima. Se o número de instâncias no momento é 12, reduz horizontalmente para 10, o máximo permitido para o perfil predefinido.

![Definições de dimensionamento automático](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações para dimensionar quando várias regras estão configuradas num perfil
Há casos em que poderá ter de definir várias regras num perfil. O seguinte conjunto de regras de dimensionamento automático são utilizados pelo uso de serviços quando várias regras estão definidas.

No *aumentar horizontalmente*, dimensionamento automático é executado se forem satisfeita qualquer regra.
No *redução horizontal*, dimensionamento automático requer a todas as regras a ser atendidos.

Para ilustrar, partem do princípio de que tem as seguintes regras de dimensionamento automático de quatro:

* Se CPU < 30%, dimensionamento-in em 1
* Se memória < 50%, dimensionamento-in em 1
* Se CPU > 75%, aumentar horizontalmente em 1
* Se memória > 75%, aumentar horizontalmente em 1

Em seguida, ocorre a seguir:

* Se a CPU está 76% e a memória é de 50%, podemos aumentar horizontalmente.
* Se a CPU tem 50% e a memória é de 76% vamos aumentar horizontalmente.

Por outro lado, se 25% de é de CPU e memória é o dimensionamento automático de 51% faz **não** redução horizontal. Para dimensionamento-in, CPU tem de ser 29% e a memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecione sempre um número de seguro predefinido de instâncias
O número predefinido de instâncias é importante de dimensionamento automático dimensiona o seu serviço para essa contagem quando não estão disponíveis métricas. Por conseguinte, selecione um número predefinido de instâncias que está seguro para cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
Dimensionamento automático irá publicar no registo de atividade se qualquer uma das condições seguintes ocorrem:

* Dimensionamento automático emite uma operação de dimensionamento
* Serviço de dimensionamento automático concluir com êxito uma ação de dimensionamento
* Serviço de dimensionamento automático não consegue efetuar uma ação de dimensionamento.
* As métricas não estão disponíveis para o serviço de dimensionamento automático tomar uma decisão de dimensionamento.
* As métricas estão disponível (recuperação) novamente para tomar uma decisão de dimensionamento.

Também pode utilizar um alerta de registo de atividades para monitorizar o estado de funcionamento do mecanismo de dimensionamento automático. Eis exemplos para [criar um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) ou a [criar um alerta de registo de atividade para monitorizar todos os do dimensionamento automático com falhas no / aumentar horizontalmente operações no seu subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Além de utilizar os alertas de registo de atividade, também pode configurar notificações de e-mail ou webhook para obter notificações de ações de dimensionamento com êxito através do separador notificações sobre a definição de dimensionamento automático.

## <a name="next-steps"></a>Próximos Passos
- [Crie um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta de registo de atividade para monitorizar todos os do dimensionamento automático com falhas no / aumentar horizontalmente operações na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

