---
title: Dimensionamento automático e Ambiente do Serviço de Aplicativo v1 – Azure
description: Dimensionamento automático e Ambiente do Serviço de Aplicativo
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f0c49e1835412b61817ff3571dd3ee1eaa29f21f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070091"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Dimensionamento automático e Ambiente do Serviço de Aplicativo v1

> [!NOTE]
> Este artigo é sobre o Ambiente do Serviço de Aplicativo v1.  Há uma versão mais recente do Ambiente do Serviço de Aplicativo que é mais fácil de usar e é executada em uma infraestrutura mais potente. Para saber mais sobre a nova versão, comece com a [introdução ao ambiente do serviço de aplicativo](intro.md).
> 

Os ambientes de serviçoAzure app dão suporte ao dimensionamento automático. Você pode dimensionar automaticamente os pools de trabalho individuais com base nas métricas ou no agendamento.

![Opções de dimensionamento automático para um pool de trabalho.][intro]

O dimensionamento automático otimiza a utilização de recursos aumentando e reduzindo automaticamente um ambiente do serviço de aplicativo para se ajustar ao seu orçamento e ou carregar o perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar dimensionamento automático do pool de trabalho
Você pode acessar a funcionalidade de dimensionamento automático na guia **configurações** do pool de trabalho.

![Guia Configurações do pool de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar, pois é a mesma experiência que você vê ao dimensionar um plano do serviço de aplicativo. 

![Configurações de escala manual.][scale-manual]

Você também pode configurar um perfil de dimensionamento automático.

![Configurações de autoescala.][scale-profile]

Os perfis de dimensionamento automático são úteis para definir limites em sua escala. Dessa forma, você pode ter uma experiência de desempenho consistente definindo um valor de escala de limite inferior (1) e um limite de gastos previsível definindo um limite superior (2).

![Configurações de escala no perfil.][scale-profile2]

Depois de definir um perfil, você pode adicionar regras de dimensionamento automático para escalar ou reduzir verticalmente o número de instâncias no pool de trabalho dentro dos limites definidos pelo perfil. As regras de dimensionamento automático são baseadas em métricas.

![Regra de dimensionamento.][scale-rule]

 Qualquer métrica de pool de trabalho ou de front-end pode ser usada para definir regras de dimensionamento automático. Essas métricas são as mesmas métricas que você pode monitorar nos grafos de folha de recursos ou definir alertas para o.

## <a name="autoscale-example"></a>Exemplo de dimensionamento automático
A autoescala de um ambiente do serviço de aplicativo pode ser melhor ilustrada ao percorrer um cenário.

Este artigo explica todas as considerações necessárias ao configurar o dimensionamento automático. O artigo orienta você pelas interações que entram em jogo quando você fatorar em ambientes de serviço de aplicativo de dimensionamento automático hospedados em Ambiente do Serviço de Aplicativo.

### <a name="scenario-introduction"></a>Introdução ao cenário
Frank é um sysadmin para uma empresa que migrou uma parte das cargas de trabalho que eles gerenciam para um ambiente do serviço de aplicativo.

O ambiente do serviço de aplicativo é configurado para escala manual da seguinte maneira:

* **Front-ends:** 3
* **Pool de trabalho 1**: 10
* **Pool de trabalho 2**: 5
* **Pool de trabalho 3**: 5

O pool de trabalhadores 1 é usado para cargas de trabalho de produção, enquanto o pool de trabalho 2 e o pool de trabalho 3 são usados para o controle de qualidade (p e r) e cargas de trabalhos de desenvolvimento.

Os planos do serviço de aplicativo para QA e dev são configurados para dimensionamento manual. O plano do serviço de aplicativo de produção é definido como dimensionamento automático para lidar com variações na carga e no tráfego.

Frank está muito familiarizado com o aplicativo. Eles sabem que o horário de pico para carga está entre 9:00 e 6:00, pois esse é um aplicativo de linha de negócios (LOB) que os funcionários usam enquanto estão no escritório. O uso cai depois disso, quando os usuários são feitos naquele dia. Fora do horário de pico, ainda há alguma carga, pois os usuários podem acessar o aplicativo remotamente usando seus dispositivos móveis ou computadores domésticos. O plano do serviço de aplicativo de produção já está configurado para dimensionamento automático com base no uso da CPU com as seguintes regras:

![Configurações específicas para o aplicativo LOB.][asp-scale]

| **Perfil de dimensionamento automático – dias da semana – plano do serviço de aplicativo** | **Perfil de dimensionamento automático – fins de semana – plano do serviço de aplicativo** |
| --- | --- |
| **Nomes** Perfil de dia da semana |**Nomes** Perfil de fim de semana |
| **Dimensionar por:** Regras de agendamento e desempenho |**Dimensionar por:** Regras de agendamento e desempenho |
| **Criar** Dias da semana |**Criar** Fim |
| **Escreva** Periodicidade |**Escreva** Periodicidade |
| **Intervalo de destino:** 5 a 20 instâncias |**Intervalo de destino:** 3 a 10 instâncias |
| **Dias** Segunda-feira, terça-feira, quarta-feira, sexta-feira |**Dias** Sábado, domingo |
| **Hora de início:** 9:00 AM |**Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (escalar verticalmente)** |**Regra de dimensionamento automático (escalar verticalmente)** |
| **Kit** Produção (Ambiente do Serviço de Aplicativo) |**Kit** Produção (Ambiente do Serviço de Aplicativo) |
| **Medidas** CPUS |**Medidas** CPUS |
| **Operacional** Maior que 60% |**Operacional** Maior que 80% |
| **Permanência** 5 Minutos |**Permanência** 10 Minutos |
| **Agregação de tempo:** Average |**Agregação de tempo:** Average |
| **Action** Aumentar contagem em 2 |**Action** Aumentar contagem em 1 |
| **Resfriamento (minutos):** 15 |**Resfriamento (minutos):** 20 |
|  | |
| **Regra de dimensionamento automático (reduzir verticalmente)** |**Regra de dimensionamento automático (reduzir verticalmente)** |
| **Kit** Produção (Ambiente do Serviço de Aplicativo) |**Kit** Produção (Ambiente do Serviço de Aplicativo) |
| **Medidas** CPUS |**Medidas** CPUS |
| **Operacional** Menos de 30% |**Operacional** Menos de 20% |
| **Permanência** 10 minutos |**Permanência** 15 minutos |
| **Agregação de tempo:** Average |**Agregação de tempo:** Average |
| **Action** Diminuir contagem em 1 |**Action** Diminuir contagem em 1 |
| **Resfriamento (minutos):** 20 |**Resfriamento (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação do plano do serviço de aplicativo
Os planos do serviço de aplicativo que são configurados para dimensionamento automático fazem isso com uma taxa máxima por hora. Essa taxa pode ser calculada com base nos valores fornecidos na regra de dimensionamento automático.

Entender e calcular a *taxa de inflação do plano do serviço de aplicativo* é importante para o dimensionamento automático do ambiente do serviço de aplicativo porque as alterações de escala para um pool de trabalho não são instantâneas

A taxa de inflação do plano do serviço de aplicativo é calculada da seguinte maneira:

![Cálculo da taxa de inflação do plano do serviço de aplicativo.][ASP-Inflation]

Com base na regra de dimensionamento automático – escalar verticalmente para o perfil de dia da semana do plano do serviço de aplicativo de produção:

![Taxa de inflação do plano do serviço de aplicativo para dias da semana com base na regra de dimensionamento automático – escalar verticalmente.][Equation1]

No caso da regra de dimensionamento automático – escalar verticalmente para o perfil de fim de semana do plano do serviço de aplicativo de produção, a fórmula seria resolvida para:

![Taxa de inflação do plano do serviço de aplicativo para fins de semana com base na regra de dimensionamento automático – escalar verticalmente.][Equation2]

Esse valor também pode ser calculado para operações de redução.

Com base na regra de dimensionamento automático – reduzir verticalmente para o perfil de dia da semana do plano do serviço de aplicativo de produção, isso seria semelhante ao seguinte:

![Taxa de inflação do plano do serviço de aplicativo para dias da semana com base na regra de dimensionamento automático – reduzir verticalmente.][Equation3]

No caso da regra de dimensionamento automático – reduzir verticalmente para o perfil de fim de semana do plano do serviço de aplicativo de produção, a fórmula resolveria para:  

![Taxa de inflação do plano do serviço de aplicativo para fins de semana com base na regra de dimensionamento automático – reduzir verticalmente.][Equation4]

O plano do serviço de aplicativo de produção pode crescer em uma taxa máxima de oito instâncias/hora durante a semana e quatro instâncias/hora durante o fim de semana. Ele pode liberar instâncias a uma taxa máxima de quatro instâncias/hora durante a semana e seis instâncias/hora durante os fins de semana.

Se vários planos do serviço de aplicativo estiverem sendo hospedados em um pool de trabalho, será necessário calcular a *taxa de inflação total* como a soma da taxa de inflação para todos os planos do serviço de aplicativo que estão sendo hospedados nesse pool de trabalho.

![Cálculo da taxa de inflação total para vários planos do serviço de aplicativo hospedados em um pool de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Usar a taxa de inflação do plano do serviço de aplicativo para definir regras de autoescala do pool de trabalho
Os pools de trabalho que hospedam planos do serviço de aplicativo que são configurados para dimensionamento automático precisam ser alocados em um buffer de capacidade. O buffer permite que as operações de dimensionamento automático aumentem e diminuam o plano do serviço de aplicativo conforme necessário. O buffer mínimo seria a taxa de inflação do plano do serviço de aplicativo total calculado.

Como as operações de escala do ambiente do serviço de aplicativo levam algum tempo para serem aplicadas, qualquer alteração deve levar em conta outras alterações de demanda que poderiam ocorrer enquanto uma operação de escala está em andamento. Para acomodar essa latência, recomendamos que você use a taxa de inflação do plano do serviço de aplicativo total calculada como o número mínimo de instâncias que são adicionadas para cada operação de dimensionamento automático.

Com essas informações, Frank pode definir o seguinte perfil e regras de dimensionamento automático:

![Regras de perfil de dimensionamento automático para exemplo de LOB.][Worker-Pool-Scale]

| **Perfil de dimensionamento automático – dias da semana** | **Perfil de dimensionamento automático – fins de semana** |
| --- | --- |
| **Nomes** Perfil de dia da semana |**Nomes** Perfil de fim de semana |
| **Dimensionar por:** Regras de agendamento e desempenho |**Dimensionar por:** Regras de agendamento e desempenho |
| **Criar** Dias da semana |**Criar** Fim |
| **Escreva** Periodicidade |**Escreva** Periodicidade |
| **Intervalo de destino:** 13 a 25 instâncias |**Intervalo de destino:** 6 a 15 instâncias |
| **Dias** Segunda-feira, terça-feira, quarta-feira, sexta-feira |**Dias** Sábado, domingo |
| **Hora de início:** 7:00 AM |**Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (escalar verticalmente)** |**Regra de dimensionamento automático (escalar verticalmente)** |
| **Kit** Pool de trabalho 1 |**Kit** Pool de trabalho 1 |
| **Medidas** WorkersAvailable |**Medidas** WorkersAvailable |
| **Operacional** Menos de 8 |**Operacional** Menos de 3 |
| **Permanência** 20 minutos |**Permanência** 30 minutos |
| **Agregação de tempo:** Average |**Agregação de tempo:** Average |
| **Action** Aumentar contagem em 8 |**Action** Aumentar contagem em 3 |
| **Resfriamento (minutos):** 180 |**Resfriamento (minutos):** 180 |
|  | |
| **Regra de dimensionamento automático (reduzir verticalmente)** |**Regra de dimensionamento automático (reduzir verticalmente)** |
| **Kit** Pool de trabalho 1 |**Kit** Pool de trabalho 1 |
| **Medidas** WorkersAvailable |**Medidas** WorkersAvailable |
| **Operacional** Maior que 8 |**Operacional** Maior que 3 |
| **Permanência** 20 minutos |**Permanência** 15 minutos |
| **Agregação de tempo:** Average |**Agregação de tempo:** Average |
| **Action** Diminuir contagem por 2 |**Action** Diminuir contagem em 3 |
| **Resfriamento (minutos):** 120 |**Resfriamento (minutos):** 120 |

O intervalo de destino definido no perfil é calculado pelas instâncias mínimas definidas no perfil para o plano do serviço de aplicativo + buffer.

O intervalo máximo seria a soma de todos os intervalos máximos para todos os planos do serviço de aplicativo hospedados no pool de trabalho.

A contagem de aumento das regras de escala vertical deve ser definida para pelo menos 1X a taxa de inflação do plano do serviço de aplicativo para escalar verticalmente.

A contagem de diminuição pode ser ajustada para algo entre 1/2X ou 1X a taxa de inflação do plano do serviço de aplicativo para reduzir verticalmente.

### <a name="autoscale-for-front-end-pool"></a>Dimensionamento automático para pool de front-end
As regras para o dimensionamento automático de front-end são mais simples do que para pools de trabalho. Principalmente, você deve  
Verifique se a duração da medida e os temporizadores cooldown consideram que as operações de escala em um plano do serviço de aplicativo não são instantâneas.

Para esse cenário, Frank sabe que a taxa de erro aumenta depois que front-ends atingem 80% de utilização da CPU e define a regra de dimensionamento automático para aumentar as instâncias da seguinte maneira:

![Configurações de AutoEscala para o pool de front-end.][Front-End-Scale]

| **Perfil de dimensionamento automático – front-ends** |
| --- |
| **Nomes** Dimensionamento automático – front-ends |
| **Dimensionar por:** Regras de agendamento e desempenho |
| **Criar** Todos os dias |
| **Escreva** Periodicidade |
| **Intervalo de destino:** 3 a 10 instâncias |
| **Dias** Todos os dias |
| **Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |
|  |
| **Regra de dimensionamento automático (escalar verticalmente)** |
| **Kit** Pool de front-end |
| **Medidas** CPUS |
| **Operacional** Maior que 60% |
| **Permanência** 20 minutos |
| **Agregação de tempo:** Average |
| **Action** Aumentar contagem em 3 |
| **Resfriamento (minutos):** 120 |
|  |
| **Regra de dimensionamento automático (reduzir verticalmente)** |
| **Kit** Pool de trabalho 1 |
| **Medidas** CPUS |
| **Operacional** Menos de 30% |
| **Permanência** 20 minutos |
| **Agregação de tempo:** Average |
| **Action** Diminuir contagem em 3 |
| **Resfriamento (minutos):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
