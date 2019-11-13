---
title: Práticas recomendadas para dimensionamento automático
description: Padrões de dimensionamento automático no Azure para aplicativos Web, conjuntos de dimensionamento de máquinas virtuais e serviços de nuvem
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 604cf0564039a542ec117612bcbf74601388c0f7
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007612"
---
# <a name="best-practices-for-autoscale"></a>Melhores práticas do Dimensionamento Automático
Azure Monitor dimensionamento automático se aplica somente aos [conjuntos de dimensionamento de máquinas virtuais](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços de nuvem](https://azure.microsoft.com/services/cloud-services/), [serviço de aplicativo-aplicativos Web](https://azure.microsoft.com/services/app-service/web/)e [serviços de gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Conceitos de dimensionamento automático
* Um recurso pode ter apenas *uma* configuração de dimensionamento automático
* Uma configuração de dimensionamento automático pode ter um ou mais perfis e cada perfil pode ter uma ou mais regras de dimensionamento automático.
* Uma configuração de dimensionamento automático dimensiona as instâncias horizontalmente, que estão *fora* aumentando as instâncias e *no* diminuindo o número de instâncias.
  Uma configuração de dimensionamento automático tem um valor máximo, mínimo e padrão de instâncias.
* Um trabalho de dimensionamento automático sempre lê a métrica associada para ser dimensionada, verificando se ela ultrapassou o limite configurado para expansão ou redução horizontal. Você pode exibir uma lista de métricas que o dimensionamento automático pode dimensionar em [Azure monitor métricas comuns de autoescala](autoscale-common-metrics.md).
* Todos os limites são calculados em um nível de instância. Por exemplo, "escalar horizontalmente por uma instância quando a CPU média > 80% quando a contagem de instâncias for 2", significa escalar horizontalmente quando a média da CPU em todas as instâncias for maior que 80%.
* Todas as falhas de dimensionamento automático são registradas no log de atividades. Em seguida, você pode configurar um [alerta do log de atividades](./../../azure-monitor/platform/activity-log-alerts.md) para que possa ser notificado por email, SMS ou webhook sempre que houver uma falha de dimensionamento automático.
* Da mesma forma, todas as ações de dimensionamento bem-sucedidas são postadas no log de atividades. Você pode configurar um alerta do log de atividades para que você possa ser notificado por email, SMS ou webhook sempre que houver uma ação de dimensionamento automático bem-sucedida. Você também pode configurar notificações por email ou webhook para ser notificado para ações de dimensionamento bem-sucedidas por meio da guia notificações na configuração de dimensionamento automático.

## <a name="autoscale-best-practices"></a>Práticas recomendadas de dimensionamento automático
Use as práticas recomendadas a seguir ao usar o dimensionamento automático.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Verifique se os valores mínimo e máximo são diferentes e têm uma margem adequada entre eles
Se você tiver uma configuração que tenha mínimo = 2, máximo = 2 e a contagem de instâncias atual for 2, nenhuma ação de escala poderá ocorrer. Mantenha uma margem adequada entre as contagens de instância máxima e mínima, que são inclusivas. O dimensionamento automático sempre é dimensionado entre esses limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>O dimensionamento manual é redefinido por autoescala mín e máx.
Se você atualizar manualmente a contagem de instâncias para um valor acima ou abaixo do máximo, o mecanismo de dimensionamento automático será dimensionado automaticamente de volta para o mínimo (se abaixo) ou o máximo (se acima). Por exemplo, você define o intervalo entre 3 e 6. Se você tiver uma instância em execução, o mecanismo de dimensionamento automático dimensionará para três instâncias na próxima execução. Da mesma forma, se você definir manualmente a escala para oito instâncias, na próxima execução, o dimensionamento automático o dimensionará de volta para seis instâncias na próxima execução.  O dimensionamento manual é temporário, a menos que você redefina as regras de dimensionamento automático também.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Sempre use uma combinação de regras de redução e redução que executa um aumento e uma diminuição
Se você usar apenas uma parte da combinação, o dimensionamento automático só executará uma ação em uma única direção (scale out ou in) até atingir o máximo ou as contagens de instâncias mínimas de definidas no perfil. Isso não é o ideal, idealmente você deseja que seu recurso seja dimensionado às vezes de alto uso para garantir a disponibilidade. Da mesma forma, às vezes de baixo uso, você deseja que o recurso seja reduzido para baixo, para que possa perceber a economia de custos.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolha a estatística apropriada para sua métrica de diagnóstico
Para métricas de diagnóstico, você pode escolher entre *média*, *mínimo*, *máximo* e *total* como uma métrica para reduzir. A estatística mais comum é a *média*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolha os limites cuidadosamente para todos os tipos de métrica
Recomendamos escolher cuidadosamente limites diferentes para escalar horizontalmente e reduzir horizontalmente com base em situações práticas.

*Não recomendamos* configurações de dimensionamento automático como os exemplos abaixo com os mesmos valores de limite de saída ou muito semelhantes para condições de out e in:

* Aumentar as instâncias por contagem de 1 quando a contagem de threads > = 600
* Diminuir as instâncias por contagem de 1 quando a contagem de threads < = 600

Vejamos um exemplo do que pode levar a um comportamento que pode parecer confuso. Considere a sequência a seguir.

1. Suponha que haja duas instâncias para começar e, em seguida, o número médio de threads por instância aumenta para 625.
2. Dimensionamento automático escala horizontalmente adicionando uma terceira instância.
3. Em seguida, suponha que a contagem média de threads na instância cai para 575.
4. Antes de reduzir verticalmente, o dimensionamento automático tenta estimar qual será o estado final se ele for dimensionado. Por exemplo, 575 x 3 (contagem de instância atual) = 1.725/2 (número final de instâncias quando reduzido verticalmente) = 862,5 threads. Isso significa que o dimensionamento automático precisaria ser redimensionado imediatamente, mesmo depois de ser dimensionado em, se a contagem média de threads permanecer igual ou até mesmo cair apenas uma pequena quantidade. No entanto, se ele for aumentado novamente, todo o processo se repetiria, levando a um loop infinito.
5. Para evitar essa situação (com o termo "oscilação"), o dimensionamento automático não reduz horizontalmente. Em vez disso, ele ignora e reavalia a condição novamente na próxima vez que o trabalho do serviço for executado. Isso pode confundir muitas pessoas porque o dimensionamento automático pareceria não funcionar quando a contagem média de threads era 575.

A estimativa durante uma redução é destinada a evitar situações de "oscilação", nas quais as ações de reduzir e reduzir continuamente voltam e saem. Tenha esse comportamento em mente ao escolher os mesmos limites de expansão e em.

É recomendável escolher uma margem adequada entre a expansão e os limites. Como exemplo, considere a seguinte combinação de regra melhor.

* Aumentar as instâncias em 1 contagem quando a CPU% > = 80
* Diminuir as instâncias em 1 contagem quando a CPU% < = 60

Nesse caso  

1. Suponha que haja duas instâncias para começar.
2. Se a média de% de CPU entre instâncias for para 80, o dimensionamento automático escalará a adição de uma terceira instância.
3. Agora suponha que, ao longo do tempo, o percentual de CPU cai para 60.
4. A regra de redução horizontal do dimensionamento automático estima o estado final se ele for para reduzir horizontalmente. Por exemplo, 60 x 3 (contagem de instância atual) = 180/2 (número final de instâncias quando reduzido verticalmente) = 90. Portanto, o dimensionamento automático não reduz horizontalmente porque teria que escalar horizontalmente novamente de imediato. Em vez disso, ele ignora a redução vertical.
5. Na próxima vez em que o dimensionamento automático for verificado, a CPU continuará a cair para o 50. Ele estima novamente-50 x 3 instância = 150/2 instâncias = 75, que está abaixo do limite de expansão de 80, para que ele seja dimensionado com êxito para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações para dimensionar valores de limite para métricas especiais
 Para métricas especiais como a métrica de comprimento de fila do barramento de serviço ou armazenamento, o limite é o número médio de mensagens disponíveis por número atual de instâncias. Escolha cuidadosamente o valor de limite para essa métrica.

Vamos ilustrar isso com um exemplo para garantir que você compreenda melhor o comportamento.

* Aumentar as instâncias em 1 contagem quando a contagem de mensagens da fila de armazenamento > = 50
* Diminuir as instâncias em 1 contagem quando a contagem de mensagens da fila de armazenamento < = 10

Considere a seguinte sequência:

1. Há duas instâncias de fila de armazenamento.
2. As mensagens continuam chegando e quando você revisa a fila de armazenamento, a contagem total lê 50. Você pode assumir que o dimensionamento automático deve iniciar uma ação de expansão. No entanto, observe que ainda é 50/2 = 25 mensagens por instância. Portanto, a expansão não ocorre. Para que a primeira escala horizontal ocorra, a contagem total de mensagens na fila de armazenamento deve ser 100.
3. Em seguida, suponha que a contagem total de mensagens chegue a 100.
4. Uma terceira instância de fila de armazenamento é adicionada devido a uma ação de expansão.  A próxima ação de escala horizontal não ocorrerá até que a contagem total de mensagens na fila atinja 150 porque 150/3 = 50.
5. Agora, o número de mensagens na fila é menor. Com três instâncias, a primeira ação de scale-in ocorre quando o total de mensagens em todas as filas é somado a 30 porque 30/3 = 10 mensagens por instância, que é o limite de redução.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações sobre o dimensionamento quando vários perfis são configurados em uma configuração de dimensionamento automático
Em uma configuração de dimensionamento automático, você pode escolher um perfil padrão, que sempre é aplicado sem nenhuma dependência de agenda ou hora, ou você pode escolher um perfil recorrente ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de dimensionamento automático os processa, ele sempre verifica na seguinte ordem:

1. Perfil de data fixa
2. Perfil recorrente
3. Perfil padrão ("Always")

Se uma condição de perfil for atendida, o dimensionamento automático não verificará a próxima condição de perfil abaixo dela. O dimensionamento automático processa apenas um perfil de cada vez. Isso significa que, se você quiser incluir também uma condição de processamento de um perfil de camada inferior, deverá incluir essas regras também no perfil atual.

Vamos examinar isso usando um exemplo:

A imagem abaixo mostra uma configuração de dimensionamento automático com um perfil padrão de instâncias mínimas = 2 e máximo de instâncias = 10. Neste exemplo, as regras são configuradas para escalar horizontalmente quando a contagem de mensagens na fila for maior que 10 e reduzir quando a contagem de mensagens na fila for menor que três. Agora, o recurso pode ser dimensionado entre duas e dez instâncias.

Além disso, há um perfil recorrente definido para segunda-feira. Ele é definido para instâncias mínimas = 3 e máximo de instâncias = 10. Isso significa que na segunda-feira, a primeira verificação de dimensionamento automático para essa condição, se a contagem de instâncias for duas, ela será dimensionada para o novo mínimo de três. Desde que o dimensionamento automático continue a localizar essa condição de perfil correspondente (segunda-feira), ele só processa as regras de redução/saída baseadas em CPU configuradas para esse perfil. Neste momento, ele não verifica o comprimento da fila. No entanto, se você também quiser que a condição de comprimento da fila seja marcada, você deverá incluir essas regras do perfil padrão também em seu perfil de segunda-feira.

Da mesma forma, quando o dimensionamento automático volta para o perfil padrão, ele verifica primeiro se as condições mínimas e máximas foram atendidas. Se o número de instâncias no momento for 12, ele será dimensionado para 10, o máximo permitido para o perfil padrão.

![Configurações de autoescala](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações sobre o dimensionamento quando várias regras são configuradas em um perfil
Há casos em que você pode precisar definir várias regras em um perfil. O conjunto de regras de dimensionamento automático a seguir é usado pelos serviços quando várias regras são definidas.

Em *scale out*, o dimensionamento automático é executado se alguma regra for atendida.
Na *redução horizontal*, o dimensionamento automático exige que todas as regras sejam atendidas.

Para ilustrar, suponha que você tenha as quatro seguintes regras de dimensionamento automático:

* Se a CPU < 30%, reduza horizontalmente em 1
* Se a memória < 50%, reduza a escala em 1
* Se a CPU > 75%, escale horizontalmente em 1
* Se a memória > 75%, escale horizontalmente em 1

Em seguida, ocorre o seguinte:

* Se a CPU for 76% e a memória for 50%, vamos expandir.
* Se a CPU for 50% e a memória for 76%, escalaremos horizontalmente.

Por outro lado, se a CPU for de 25% e a memória for de 51%, o dimensionamento automático **não** reduzirá horizontalmente. Para reduzir, a CPU deve ser 29% e a memória de 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Sempre selecionar uma contagem de instâncias padrão segura
A contagem de instâncias padrão é importante escala automática de seu serviço para essa contagem quando as métricas não estão disponíveis. Portanto, selecione uma contagem de instâncias padrão que seja segura para suas cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
O dimensionamento automático enviará ao log de atividades se qualquer uma das seguintes condições ocorrer:

* O dimensionamento automático emite uma operação de dimensionamento
* O serviço de dimensionamento automático conclui com êxito uma ação de escala
* O serviço de dimensionamento automático não realiza uma ação de dimensionamento.
* As métricas não estão disponíveis para o serviço de dimensionamento automático fazer uma decisão de escala.
* As métricas estão disponíveis (recuperação) novamente para tomar uma decisão de escala.

Você também pode usar um alerta do log de atividades para monitorar a integridade do mecanismo de dimensionamento automático. Aqui estão exemplos para [criar um alerta do log de atividades para monitorar todas as operações do mecanismo de dimensionamento automático em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) ou para [criar um alerta do log de atividades para monitorar todas as operações de escalabilidade horizontal com falha em/escalar horizontalmente em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Além de usar alertas do log de atividades, você também pode configurar notificações por email ou webhook para ser notificado para ações de dimensionamento bem-sucedidas por meio da guia notificações na configuração de dimensionamento automático.

## <a name="next-steps"></a>Passos Seguintes
- [Crie um alerta do log de atividades para monitorar todas as operações do mecanismo de dimensionamento automático em sua assinatura.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta do log de atividades para monitorar todas as operações com falha na escala de dimensionamento/expansão em sua assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

