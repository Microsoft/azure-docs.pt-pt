---
title: Autoscalcificação v1
description: Autoscaling e App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687287"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Autoscaling e App Service Ambiente v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a Introdução ao Ambiente de Serviço de [Aplicações.](intro.md)
> 

Os ambientes do Serviço de Aplicações Azure suportam *a autoscalcificação.* Pode escalar automaticamente piscinas individuais de trabalhadores com base em métricas ou horários.

![Opções de escala automática para uma piscina de trabalhadores.][intro]

A automatização otimiza a utilização do seu recurso, crescendo e diminuindo automaticamente um ambiente de Serviço de Aplicações para se adaptar ao seu orçamento e ou perfil de carga.

## <a name="configure-worker-pool-autoscale"></a>Configure a escala automática da piscina de trabalhadores
Pode aceder à funcionalidade de escala automática a partir do separador **Definições** da piscina do trabalhador.

![Separador de configurações da piscina do trabalhador.][settings-scale]

A partir daí, a interface deve ser bastante familiar, uma vez que é a mesma experiência que se vê quando escala um plano de Serviço de Aplicações. 

![Definições de escala manual.][scale-manual]

Também pode configurar um perfil de escala automática.

![Definições de escala automática.][scale-profile]

Os perfis de escala automática são úteis para definir limites na sua escala. Desta forma, pode ter uma experiência de desempenho consistente, definindo um valor de escala mais baixo (1) e uma tampa de gasto previsível, estabelecendo um limite superior (2).

![Configurações de escala no perfil.][scale-profile2]

Depois de definir um perfil, pode adicionar regras de escala automática para aumentar ou descer o número de instâncias no pool de trabalhadores dentro dos limites definidos pelo perfil. As regras de escala automática baseiam-se em métricas.

![Regra da escala.][scale-rule]

 Qualquer piscina de trabalhadores ou métricas frontais podem ser usadas para definir regras de escala automática. Estas métricas são as mesmas métricas que pode monitorizar nos gráficos da lâmina de recursos ou definir alertas para.

## <a name="autoscale-example"></a>Exemplo de escala automática
A escala automática de um ambiente de Serviço de Aplicações pode ser melhor ilustrada através de um cenário.

Este artigo explica todas as considerações necessárias quando se cria uma escala automática. O artigo acompanha-o através das interações que entram em jogo quando se tem em conta os ambientes do Serviço de Aplicações autoscalcificadas que estão hospedados no App Service Environment.

### <a name="scenario-introduction"></a>Introdução do cenário
Frank é uma sysadmina para uma empresa que emigrou uma parte das cargas de trabalho que conseguem para um ambiente de Serviço de Aplicações.

O ambiente do Serviço de Aplicações está configurado à escala manual da seguinte forma:

* **Extremidades dianteiras:** 3
* **Piscina de trabalhadores 1**: 10
* **Piscina de trabalhadores 2**: 5
* **Piscina de trabalhadores 3**: 5

A piscina 1 dos trabalhadores é utilizada para cargas de trabalho de produção, enquanto a piscina de trabalhadores 2 e a piscina 3 são utilizadas para garantia de qualidade (QA) e cargas de trabalho de desenvolvimento.

Os planos do Serviço de Aplicações para QA e dev estão configurados à escala manual. O plano de serviço de aplicações de produção está definido para a escala automática para lidar com variações na carga e tráfego.

Frank está muito familiarizado com a aplicação. Sabem que as horas de ponta para carga são entre as 9:00 e as 18:00 porque esta é uma aplicação de linha de negócio (LOB) que os funcionários usam enquanto estão no escritório. O uso cai depois disso quando os utilizadores são feitos para esse dia. Fora das horas de ponta, ainda há alguma carga porque os utilizadores podem aceder à aplicação remotamente utilizando os seus dispositivos móveis ou computadores domésticos. O plano de serviço de aplicações de produção já está configurado para escala automática com base no uso do CPU com as seguintes regras:

![Definições específicas para app LOB.][asp-scale]

| **Perfil de escala automática – Dias úteis – Plano de Serviço de Aplicações** | **Perfil de escala automática – Fins de Semana – Plano de Serviço de Aplicações** |
| --- | --- |
| **Nome:** Perfil do dia da semana |**Nome:** Perfil de fim de semana |
| **Escala por:** Regras de horário e desempenho |**Escala por:** Regras de horário e desempenho |
| **Perfil:** Dias úteis |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 5 a 20 casos |Intervalo de **destino:** 3 a 10 casos |
| **Dias:** Segunda, terça, quarta, quinta, sexta |**Dias:** Sábado, domingo |
| **Hora de início:** 9:00 |**Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra da escala automática (Escala para cima)** |**Regra da escala automática (Escala para cima)** |
| **Recurso:** Produção (App Service Environment) |**Recurso:** Produção (App Service Environment) |
| **Métrica:** CPU % |**Métrica:** CPU % |
| **Operação:** Superior a 60% |**Operação:** Maior que 80% |
| **Duração:** 5 Minutos |**Duração:** 10 Minutos |
| **Agregação do tempo:** Média |**Agregação do tempo:** Média |
| **Ação:** Aumentar a contagem em 2 |**Ação:** Aumentar a contagem em 1 |
| **Arrefecer (minutos):** 15 |**Arrefecer (minutos):** 20 |
|  | |
| **Regra da escala automática (Escala para baixo)** |**Regra da escala automática (Escala para baixo)** |
| **Recurso:** Produção (App Service Environment) |**Recurso:** Produção (App Service Environment) |
| **Métrica:** CPU % |**Métrica:** CPU % |
| **Operação:** Menos de 30% |**Operação:** Menos de 20% |
| **Duração:** 10 minutos |**Duração:** 15 minutos |
| **Agregação do tempo:** Média |**Agregação do tempo:** Média |
| **Ação:** Diminuição contagem por 1 |**Ação:** Diminuição contagem por 1 |
| **Arrefecer (minutos):** 20 |**Arrefecer (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação do plano de aplicativos
Os planos do Serviço de Aplicações que estão configurados para escala automática fazem-no a uma taxa máxima por hora. Esta taxa pode ser calculada com base nos valores fornecidos na regra da escala automática.

Compreender e calcular a taxa de inflação do plano de serviço de *aplicações* é importante para a escala automática do ambiente do App Service porque as alterações de escala para um grupo de trabalhadores não são instantâneas.

A taxa de inflação do plano de serviço de aplicações é calculada da seguinte forma:

![Cálculo da taxa de inflação do plano de aplicações.][ASP-Inflation]

Com base na regra Autoscale – Scale Up para o perfil do Weekday do plano de serviço de aplicações de produção:

![Taxa de inflação do plano de aplicativos para os dias úteis com base na regra Autoscale – Scale Up.][Equation1]

No caso da regra Autoscale – Scale Up para o perfil de fim de semana do plano de serviço de aplicações de produção, a fórmula resolveria:

![App Service planeia taxa de inflação para fins de semana com base na regra Autoscale – Scale Up.][Equation2]

Este valor também pode ser calculado para operações de redução de escala.

Com base na regra Autoscale – Scale Down para o perfil do Weekday do plano de serviço de aplicações de produção, isto seria o seguinte:

![Taxa de inflação do plano de aplicativos para os dias úteis com base na regra Autoscale – Scale Down.][Equation3]

No caso da regra Autoscale – Scale Down para o perfil de fim de semana do plano de serviço de aplicações de produção, a fórmula resolveria:  

![App Service planeia taxa de inflação para fins de semana com base na regra Autoscale – Scale Down.][Equation4]

O plano de serviço de aplicações de produção pode crescer a uma taxa máxima de oito instâncias/hora durante a semana e quatro instâncias/hora durante o fim de semana. Pode libertar instâncias a uma taxa máxima de quatro instâncias/hora durante a semana e seis instâncias/hora durante os fins de semana.

Se vários planos de Serviço de Aplicações estão a ser hospedados num grupo de trabalhadores, você tem que calcular a *taxa de inflação total* como a soma da taxa de inflação para todos os planos do Serviço de Aplicações que estão hospedando naquele pool de trabalhadores.

![Cálculo total da taxa de inflação para vários planos de Serviço de Aplicações hospedados num grupo de trabalhadores.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilize a taxa de inflação do plano de serviço de aplicações para definir as regras de escala automática do conjunto de trabalhadores
Os pools de trabalhadores que acolhem planos de Serviço de Aplicações configurados à escala automática precisam de ser atribuídos um tampão de capacidade. O tampão permite que as operações de escala automática cresçam e reduzam o plano de Serviço de Aplicações conforme necessário. O tampão mínimo seria a taxa de inflação calculada do Plano total de aplicações.

Uma vez que as operações de escala ambiental do Serviço de Aplicações demoram algum tempo a ser aplicadas, qualquer alteração deve ter em conta novas alterações de procura que possam ocorrer enquanto uma operação de escala está em curso. Para acomodar esta latência, recomendamos que utilize a taxa de inflação do Plano total de serviços de aplicações calculada como o número mínimo de instâncias adicionadas para cada operação de escala automática.

Com esta informação, Frank pode definir o seguinte perfil e regras de escala automática:

![Regras de perfil de escala automática para o exemplo LOB.][Worker-Pool-Scale]

| **Perfil de escala automática – Dias úteis** | **Perfil de escala automática - Fins de semana** |
| --- | --- |
| **Nome:** Perfil do dia da semana |**Nome:** Perfil de fim de semana |
| **Escala por:** Regras de horário e desempenho |**Escala por:** Regras de horário e desempenho |
| **Perfil:** Dias úteis |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 13 a 25 casos |**Intervalo de destino:** 6 a 15 casos |
| **Dias:** Segunda, terça, quarta, quinta, sexta |**Dias:** Sábado, domingo |
| **Hora de início:** 7:00 AM |**Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra da escala automática (Escala para cima)** |**Regra da escala automática (Escala para cima)** |
| **Recurso:** Piscina operária 1 |**Recurso:** Piscina operária 1 |
| **Métrica:** TrabalhadoresDisponíveis |**Métrica:** TrabalhadoresDisponíveis |
| **Operação:** Menos de 8 |**Operação:** Menos de 3 |
| **Duração:** 20 minutos |**Duração:** 30 minutos |
| **Agregação do tempo:** Média |**Agregação do tempo:** Média |
| **Ação:** Aumentar a contagem em 8 |**Ação:** Aumentar a contagem em 3 |
| **Arrefecer (minutos):** 180 |**Arrefecer (minutos):** 180 |
|  | |
| **Regra da escala automática (Escala para baixo)** |**Regra da escala automática (Escala para baixo)** |
| **Recurso:** Piscina operária 1 |**Recurso:** Piscina operária 1 |
| **Métrica:** TrabalhadoresDisponíveis |**Métrica:** TrabalhadoresDisponíveis |
| **Operação:** Maior que 8 |**Operação:** Maior que 3 |
| **Duração:** 20 minutos |**Duração:** 15 minutos |
| **Agregação do tempo:** Média |**Agregação do tempo:** Média |
| **Ação:** Diminuição contagem por 2 |**Ação:** Diminuição contagem de 3 |
| **Arrefecer (minutos):** 120 |**Arrefecer (minutos):** 120 |

A gama Target definida no perfil é calculada pelas instâncias mínimas definidas no perfil para o plano de Serviço de Aplicações + tampão.

A gama Máxima seria a soma de todas as gamas máximas para todos os planos de Serviço de Aplicações hospedados na piscina de trabalhadores.

A contagem de aumento para as regras de escala deve ser definida para pelo menos 1X a taxa de inflação do plano de serviço sinuoso do plano de aplicação para aumentar.

A contagem de crescentes pode ser ajustada a algo entre 1/2X ou 1X a Taxa de Inflação do Plano de Serviço de Aplicações para a redução da escala.

### <a name="autoscale-for-front-end-pool"></a>Escala automática para piscina frontal
As regras para a escala automática frontal são mais simples do que para as piscinas dos trabalhadores. Principalmente, deve  
Certifique-se de que a duração da medição e os tempos de arrefecimento consideram que as operações de escala num plano de Serviço de Aplicações não são instantâneas.

Para este cenário, Frank sabe que a taxa de erro aumenta após as extremidades dianteiras atingirem 80% de utilização de CPU e define a regra de escala automática para aumentar as instâncias da seguinte forma:

![Definições de escala automática para piscina frontal.][Front-End-Scale]

| **Perfil de escala automática – Extremidades dianteiras** |
| --- |
| **Nome:** Autoescala – Extremidades dianteiras |
| **Escala por:** Regras de horário e desempenho |
| **Perfil:** Todos os dias |
| **Tipo:** Recorrência |
| Intervalo de **destino:** 3 a 10 casos |
| **Dias:** Todos os dias |
| **Hora de início:** 9:00 |
| **Fuso horário:** UTC-08 |
|  |
| **Regra da escala automática (Escala para cima)** |
| **Recurso:** Piscina frontal |
| **Métrica:** CPU % |
| **Operação:** Superior a 60% |
| **Duração:** 20 minutos |
| **Agregação do tempo:** Média |
| **Ação:** Aumentar a contagem em 3 |
| **Arrefecer (minutos):** 120 |
|  |
| **Regra da escala automática (Escala para baixo)** |
| **Recurso:** Piscina operária 1 |
| **Métrica:** CPU % |
| **Operação:** Menos de 30% |
| **Duração:** 20 Minutos |
| **Agregação do tempo:** Média |
| **Ação:** Diminuição contagem de 3 |
| **Arrefecer (minutos):** 120 |

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
