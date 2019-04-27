---
title: Dimensionamento automático e o ambiente de serviço de aplicações v1 - Azure
description: Dimensionamento automático e o ambiente de serviço de aplicações
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769507"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Dimensionamento automático e o ambiente de serviço de aplicações v1

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Existe uma versão mais recente do ambiente de serviço de aplicação que é mais fácil de usar e é executado numa infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

Ambientes de serviço de aplicações do Azure suportam *dimensionamento automático*. Pode conjuntos de trabalho individuais de dimensionamento automático com base na métrica ou agenda.

![Opções de dimensionamento automático para um conjunto de trabalho.][intro]

Dimensionamento automático otimiza a utilização de recursos por automaticamente crescente e decrescente um ambiente de serviço de aplicações para ajustar o seu orçamento e ou um perfil de carga.

## <a name="configure-worker-pool-autoscale"></a>Configurar o dimensionamento automático do conjunto de trabalho
Pode acessar a funcionalidade de dimensionamento automático do **definições** separador do conjunto de trabalho.

![Separador de definições do conjunto de trabalho.][settings-scale]

A partir daí, a interface deve ser bastante familiar, uma vez que é a mesma experiência que vê quando expandir um plano do serviço de aplicações. 

![Definições de dimensionamento manual.][scale-manual]

Também pode configurar um perfil de dimensionamento automático.

![Definições de dimensionamento automático.][scale-profile]

Perfis de dimensionamento automático são úteis para definir limites no seu dimensionamento. Dessa forma, pode ter um desempenho consistente experiência definindo um valor de limite inferior de dimensionamento (1) e um limite de gastos previsível ao definir um limite superior (2).

![Definições de dimensionamento no perfil.][scale-profile2]

Depois de definir um perfil, pode adicionar regras de dimensionamento automático para aumentar ou reduzir verticalmente o número de instâncias no conjunto de trabalho dentro dos limites definidos pelo perfil. Regras de dimensionamento automático baseiam-se em métricas.

![Regra de dimensionamento.][scale-rule]

 Qualquer conjunto de trabalho ou métricas de front-end podem ser usadas para definir regras de dimensionamento automático. Estas métricas são as mesmas métricas, pode monitorizar os gráficos do painel de recursos ou definir alertas para.

## <a name="autoscale-example"></a>Exemplo de dimensionamento automático
Dimensionamento automático de um ambiente de serviço de aplicações melhor pode ser ilustrado por meio de um cenário do acompanhamento.

Este artigo explica todas as considerações quando configurou o dimensionamento automático. O artigo explica as interações que entram em ação quando considera o dimensionamento automático ambientes de serviço de aplicações que estão alojados num ambiente de serviço de aplicações.

### <a name="scenario-introduction"></a>Introdução do cenário
Frank é um administrador do sistema para uma empresa que tiver migrado uma parte das cargas de trabalho que gerencia o para um ambiente de serviço de aplicações.

O ambiente de serviço de aplicações está configurado para dimensionamento manual da seguinte forma:

* **Front-ends:** 3
* **O conjunto de trabalho 1**: 10
* **Conjunto de trabalho 2**: 5
* **Conjunto de trabalho 3**: 5

O conjunto de trabalho 1 é utilizado para cargas de trabalho de produção, enquanto o conjunto de trabalho 2 e o conjunto de trabalho 3 são utilizadas para controle de qualidade (QA) e cargas de trabalho de desenvolvimento.

Os planos de serviço de aplicações para o controle de qualidade e desenvolvimento são configurados para dimensionamento manual. O plano do serviço de aplicações de produção está definida para dimensionamento automático para lidar com variações na carga e tráfego.

Frank é muito familiarizado com a aplicação. Ele sabe que as horas de pico de carga estão entre as 9:00 e das 18:00, porque esta é uma aplicação (LOB) de linha de negócio que os funcionários utilizam enquanto estiverem no escritório. Utilização ignora depois disso, quando os utilizadores são feitos para esse dia. Fora do horário de pico, ainda há alguns carga porque os utilizadores podem aceder a aplicações remotamente utilizando os respetivos dispositivos móveis ou PCs domésticos. O plano do serviço de aplicações de produção já está configurada para dimensionar automaticamente com base na utilização da CPU com as seguintes regras:

![Definições específicas para a aplicação LOB.][asp-scale]

| **Perfil de dimensionamento automático – dias da semana – plano do serviço de aplicações** | **Perfil de dimensionamento automático – fins de semana – plano do serviço de aplicações** |
| --- | --- |
| **Nome:** Perfil de dia da semana |**Nome:** Perfil de fim de semana |
| **Dimensione por:** Regras de agendamento e desempenho |**Dimensione por:** Regras de agendamento e desempenho |
| **Perfil:** Dias da semana |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 5 a 20 instâncias |**Intervalo de destino:** instâncias de 3 a 10 |
| **Dias:** Segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira |**Dias:** Sábado, Domingo |
| **Hora de início:** 9:00 |**Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (aumentar verticalmente)** |**Regra de dimensionamento automático (aumentar verticalmente)** |
| **recurso:** Produção (ambiente de serviço de aplicações) |**recurso:** Produção (ambiente de serviço de aplicações) |
| **Métrica:** % da CPU |**Métrica:** % da CPU |
| **Operação:** Superior a 60% |**Operação:** Superior a 80% |
| **Duração:** 5 Minutos |**Duração:** 10 Minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Aumentar contagem em 2 |**Ação:** Aumentar contagem em 1 |
| **Repouso (minutos):** 15 |**Repouso (minutos):** 20 |
|  | |
| **Regra de dimensionamento automático (dimensionamento para baixo)** |**Regra de dimensionamento automático (dimensionamento para baixo)** |
| **recurso:** Produção (ambiente de serviço de aplicações) |**recurso:** Produção (ambiente de serviço de aplicações) |
| **Métrica:** % da CPU |**Métrica:** % da CPU |
| **Operação:** Menos de 30% |**Operação:** Menos de 20% |
| **Duração:** 10 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 1 |**Ação:** Diminuir contagem em 1 |
| **Repouso (minutos):** 20 |**Repouso (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação do plano de serviço de aplicações
Planos de serviço de aplicações que estão configurados para dimensionamento automático para fazê-lo no máximo, por hora. Essa taxa pode ser calculada com base nos valores fornecidos na regra de dimensionamento automático.

Compreender e calcular a *taxa de inflação do plano de serviço de aplicações* é importante para dimensionamento automático do ambiente de serviço de aplicações, porque as alterações de dimensionamento para um conjunto de trabalho não são instantâneas.

A taxa de inflação do plano de serviço de aplicações é calculada da seguinte forma:

![Cálculo de taxa de inflação de plano de serviço de aplicações.][ASP-Inflation]

Com base na dimensionamento automático – regra aumentar verticalmente para o perfil de dia da semana do plano do serviço de aplicações de produção:

![Taxa de inflação de plano de serviço de aplicações para dias da semana com base no dimensionamento automático – regra de aumentar verticalmente.][Equation1]

No caso da dimensionamento automático – regra aumentar verticalmente para o perfil de fim de semana de produção do plano do serviço de aplicações, a fórmula deve resolver para:

![Taxa de inflação de plano de serviço de aplicações para fins de semana com base no dimensionamento automático – regra de aumentar verticalmente.][Equation2]

Este valor também pode ser calculado para operações de redução vertical.

Com base na dimensionamento automático – regra de dimensionamento para baixo para o perfil de dia da semana de produção do plano do serviço de aplicações, isso teria o aspeto da seguinte forma:

![Taxa de inflação de plano de serviço de aplicações para dias da semana com base no dimensionamento automático – regra de dimensionamento para baixo.][Equation3]

No caso da dimensionamento automático – regra de dimensionamento para baixo para o perfil de fim de semana de produção do plano do serviço de aplicações, a fórmula deve resolver para:  

![Taxa de inflação de plano de serviço de aplicações para fins de semana com base no dimensionamento automático – regra de dimensionamento para baixo.][Equation4]

O plano do serviço de aplicações de produção pode crescer numa taxa de máxima de oito instâncias por hora durante a semana e quatro instâncias/hora durante o fim de semana. Ele pode liberar instâncias no máximo, quatro instâncias/Hour durante a semana e seis instâncias/hora durante o fim de semana.

Se vários planos de serviço de aplicações estão a ser alojados num conjunto de trabalho, terá de calcular os *taxa total de inflação* como a soma da tarifa inflação todos os planos do serviço de aplicações que estão a ser alojamento nesse agrupamento de trabalho.

![Total de cálculo de taxa de inflação para vários planos de serviço de aplicações alojadas num conjunto de trabalho.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilizar a taxa de inflação do plano de serviço de aplicações para definir regras de dimensionamento automático do conjunto de trabalho
Função de trabalho de agrupamentos que alojam planos de serviço de aplicações que estão configurados para dimensionamento automático tem de ser atribuído uma memória intermédia de capacidade. Permite que a memória intermédia para as operações de dimensionamento automático aumentar e diminuir o plano do serviço de aplicações conforme necessário. O buffer mínimo seria calculada aplicação serviço planear inflação número Total.

Porque as operações de dimensionamento do ambiente de serviço de aplicações demorar algum tempo para aplicar, todas as alterações devem conta ainda mais as alterações de pedido que poderiam acontecer enquanto está em curso uma operação de dimensionamento. Para acomodar esta latência, recomendamos que utilize calculada aplicação serviço planear inflação número Total como o número mínimo de instâncias que são adicionadas para cada operação de dimensionamento automático.

Com essas informações, Frank pode definir o seguinte perfil de dimensionamento automático e as regras:

![Regras de perfil de dimensionamento automático para o exemplo LOB.][Worker-Pool-Scale]

| **Perfil de dimensionamento automático – dias da semana** | **Perfil de dimensionamento automático – fins de semana** |
| --- | --- |
| **Nome:** Perfil de dia da semana |**Nome:** Perfil de fim de semana |
| **Dimensione por:** Regras de agendamento e desempenho |**Dimensione por:** Regras de agendamento e desempenho |
| **Perfil:** Dias da semana |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** instâncias 13 a 25 |**Intervalo de destino:** instâncias de 6 a 15 |
| **Dias:** Segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira |**Dias:** Sábado, Domingo |
| **Hora de início:** 7 DA MANHÃ |**Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de dimensionamento automático (aumentar verticalmente)** |**Regra de dimensionamento automático (aumentar verticalmente)** |
| **recurso:** Conjunto de trabalho 1 |**recurso:** Conjunto de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** Menos de 8 |**Operação:** Menos de 3 |
| **Duração:** 20 minutos |**Duração:** 30 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Aumentar contagem em 8 |**Ação:** Aumentar contagem em 3 |
| **Repouso (minutos):** 180 |**Repouso (minutos):** 180 |
|  | |
| **Regra de dimensionamento automático (dimensionamento para baixo)** |**Regra de dimensionamento automático (dimensionamento para baixo)** |
| **recurso:** Conjunto de trabalho 1 |**recurso:** Conjunto de trabalho 1 |
| **Métrica:** WorkersAvailable |**Métrica:** WorkersAvailable |
| **Operação:** Maior que 8 |**Operação:** Superior a 3 |
| **Duração:** 20 minutos |**Duração:** 15 minutos |
| **Agregação de tempo:** Média |**Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 2 |**Ação:** Diminuir contagem em 3 |
| **Repouso (minutos):** 120 |**Repouso (minutos):** 120 |

O intervalo de destino definido no perfil de é calculado às instâncias mínimo definidas no perfil de para o plano do serviço de aplicações + a memória intermédia.

O intervalo máximo seria a soma de todos os intervalos máximos para todos os planos de serviço de aplicações alojados no conjunto de trabalho.

A aumentar contagem para a regras de dimensionamento deve ser configurada para, pelo menos, 1 X a taxa de inflação de plano ao serviço de aplicação para dimensionamento.

Diminuir contagem pode ser ajustada para algo entre 1/2 X ou 1 X a taxa de inflação de plano ao serviço de aplicação para dimensionamento para baixo.

### <a name="autoscale-for-front-end-pool"></a>Dimensionamento automático para o conjunto de front-end
As regras do dimensionamento automático de front-end são mais simples do que para os conjuntos de trabalho. Essencialmente, deve  
Certifique-se de que a duração a medição e os temporizadores de arrefecimento considerar que as operações de dimensionamento num plano do serviço de aplicações não são instantâneas.

Para este cenário, o Frank sabe que a taxa de erros aumenta depois de front-ends atingir 80% de utilização de CPU e de que define a regra de dimensionamento automático para aumentar as instâncias da seguinte forma:

![Definições de dimensionamento automático para o conjunto de front-end.][Front-End-Scale]

| **Perfil de dimensionamento automático – o front-ends** |
| --- |
| **Nome:** Dimensionamento automático – o front-ends |
| **Dimensione por:** Regras de agendamento e desempenho |
| **Perfil:** todos os dias |
| **Tipo:** Recorrência |
| **Intervalo de destino:** instâncias de 3 a 10 |
| **Dias:** todos os dias |
| **Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |
|  |
| **Regra de dimensionamento automático (aumentar verticalmente)** |
| **recurso:** Conjunto de front-end |
| **Métrica:** % da CPU |
| **Operação:** Superior a 60% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** Média |
| **Ação:** Aumentar contagem em 3 |
| **Repouso (minutos):** 120 |
|  |
| **Regra de dimensionamento automático (dimensionamento para baixo)** |
| **recurso:** Conjunto de trabalho 1 |
| **Métrica:** % da CPU |
| **Operação:** Menos de 30% |
| **Duração:** 20 minutos |
| **Agregação de tempo:** Média |
| **Ação:** Diminuir contagem em 3 |
| **Repouso (minutos):** 120 |

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
