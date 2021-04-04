---
title: Autoscaling v1
description: Autoscaling e App Service Environment v1. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021661"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Autoscaling e App Service Environment v1

> [!NOTE]
> Este artigo é sobre o App Service Environment v1.  Existe uma versão mais recente do App Service Environment que é mais fácil de usar e funciona em infraestruturas mais poderosas. Para saber mais sobre a nova versão comece com a [Introdução ao Ambiente de Serviço de Aplicações.](intro.md)
> 

Os ambientes do Serviço de Aplicações Azure *suportam a autoscalagem.* Você pode auto-escalar piscinas individuais de trabalhadores com base em métricas ou horários.

![Opções de autoescala para uma piscina de trabalhadores.][intro]

A autoscalização otimiza a utilização do seu recurso crescendo e diminuindo automaticamente um ambiente de Serviço de Aplicações para se adaptar ao seu orçamento e ou carregar o seu perfil.

## <a name="configure-worker-pool-autoscale"></a>Configurar a autoescala da piscina dos trabalhadores
Pode aceder à funcionalidade de autoescala a partir do **separador Definições** da piscina do trabalhador.

![Separador de configurações da piscina de trabalhadores.][settings-scale]

A partir daí, a interface deve ser bastante familiar, uma vez que é a mesma experiência que se vê quando escala um plano de Serviço de Aplicações. 

![Regulações de escala manual.][scale-manual]

Também pode configurar um perfil de autoescalação.

![Definições de autoescala.][scale-profile]

Os perfis de autoescala são úteis para definir limites na sua escala. Desta forma, pode ter uma experiência de desempenho consistente definindo um valor de escala inferior (1) e uma tampa de gastos previsível, definindo um limite superior (2).

![Regulações de escala no perfil.][scale-profile2]

Depois de definir um perfil, pode adicionar regras de autoescala para escalar o número de casos na piscina do trabalhador dentro dos limites definidos pelo perfil. As regras de autoescala baseiam-se em métricas.

![Regra de escala.][scale-rule]

 Qualquer piscina de trabalhadores ou métricas frontais podem ser usadas para definir regras de autoescala. Estas métricas são as mesmas métricas que pode monitorizar nos gráficos das lâminas de recurso ou definir alertas para.

## <a name="autoscale-example"></a>Exemplo de autoescala
A autoescala de um ambiente de Serviço de Aplicações pode ser melhor ilustrada através de um cenário.

Este artigo explica todas as considerações necessárias quando configurar a autoescala. O artigo acompanha-o através das interações que entram em jogo quando se tem em conta a autoscalagem de ambientes do Serviço de Aplicações que estão hospedados no App Service Environment.

### <a name="scenario-introduction"></a>Introdução do cenário
Frank é um sysadmin para uma empresa que emigrou uma parte das cargas de trabalho que eles conseguem para um ambiente de Serviço de Aplicações.

O ambiente do Serviço de Aplicações está configurado para escala manual da seguinte forma:

* **Extremidades dianteiras:** 3
* **Piscina de trabalhadores 1:** 10
* **Piscina de trabalhadores 2**: 5
* **Piscina de trabalhadores 3**: 5

O grupo de trabalhadores 1 é utilizado para cargas de trabalho de produção, enquanto o grupo de trabalhadores 2 e o grupo de trabalhadores 3 são utilizados para a garantia de qualidade (QA) e cargas de trabalho de desenvolvimento.

Os planos do Serviço de Aplicações para QA e dev estão configurados à escala manual. O plano de serviço de aplicações de produção está definido para auto-escala para lidar com variações na carga e no tráfego.

Frank está muito familiarizado com a aplicação. Sabem que as horas de ponta para a carga são entre as 9:00 e as 18:00 porque esta é uma aplicação de linha de negócios (LOB) que os colaboradores usam enquanto estão no escritório. A utilização cai depois disso quando os utilizadores terminam nesse dia. Fora das horas de ponta, ainda existe alguma carga porque os utilizadores podem aceder remotamente à aplicação utilizando os seus dispositivos móveis ou computadores domésticos. O plano de serviço de aplicações de produção já está configurado para autoescalar com base na utilização do CPU com as seguintes regras:

![Definições específicas para aplicação LOB.][asp-scale]

| **Perfil de autoescala – Weekdays – Plano de Serviço de Aplicações** | **Perfil de autoescalação - Fins de semana – Plano de Serviço de Aplicações** |
| --- | --- |
| **Nome:** Perfil do dia da semana |**Nome:** Perfil de fim de semana |
| **Escala por:** Regras de programação e desempenho |**Escala por:** Regras de programação e desempenho |
| **Perfil:** Dias úteis |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 5 a 20 instâncias |**Intervalo de destino:** 3 a 10 instâncias |
| **Dias:** Segunda, terça, quarta, quinta, sexta |**Dias:** Sábado, domingo |
| **Hora de início:** 9:00 AM |**Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de escala automática (Escala para cima)** |**Regra de escala automática (Escala para cima)** |
| **Recurso:** Produção (Ambiente de Serviço de Aplicações) |**Recurso:** Produção (Ambiente de Serviço de Aplicações) |
| **Métrica:** CPU % |**Métrica:** CPU % |
| **Operação:** Maior que 60% |**Operação:** Maior que 80% |
| **Duração:** 5 Minutos |**Duração:** 10 Minutos |
| **Agregação temporal:** Média |**Agregação temporal:** Média |
| **Ação:** Aumento da contagem de 2 |**Ação:** Aumento da contagem de 1 |
| **Arrefecer (minutos):** 15 |**Arrefecer (minutos):** 20 |
|  | |
| **Regra de escala automática (Escala para baixo)** |**Regra de escala automática (Escala para baixo)** |
| **Recurso:** Produção (Ambiente de Serviço de Aplicações) |**Recurso:** Produção (Ambiente de Serviço de Aplicações) |
| **Métrica:** CPU % |**Métrica:** CPU % |
| **Operação:** Menos de 30% |**Operação:** Menos de 20% |
| **Duração:** 10 minutos |**Duração:** 15 minutos |
| **Agregação temporal:** Média |**Agregação temporal:** Média |
| **Ação:** Diminuição da contagem decrescente por 1 |**Ação:** Diminuição da contagem decrescente por 1 |
| **Arrefecer (minutos):** 20 |**Arrefecer (minutos):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Taxa de inflação do plano de aplicação
Os planos do Serviço de Aplicações que estão configurados para a autoescala fazem-no a uma taxa máxima por hora. Esta taxa pode ser calculada com base nos valores fornecidos na regra de autoescala.

Compreender e calcular a *taxa de inflação* do plano app é importante para o ambiente do Serviço de Aplicações, porque as alterações de escala a um pool de trabalhadores não são instantâneas.

A taxa de insuflação do plano de aplicações é calculada da seguinte forma:

![App Service plan cálculo da taxa de inflação.][ASP-Inflation]

Com base na regra Autoscale – Scale Up para o perfil weekday do plano de Serviço de Aplicações de produção:

![Taxa de inflação do plano app para os dias úteis com base na regra Autoscale – Scale Up.][Equation1]

No caso da regra Autoscale – Scale Up para o perfil de fim de semana do plano de Serviço de Aplicações de produção, a fórmula resolveria:

![Taxa de inflação do plano app para fins de semana com base na regra Autoscale – Scale Up.][Equation2]

Este valor também pode ser calculado para operações de escala.

Com base na regra Autoscale – Scale Down para o perfil weekday do plano de serviço de aplicações de produção, este seria o seguinte:

![Taxa de inflação do plano app para os dias úteis com base na regra Autoscale – Scale Down.][Equation3]

No caso da regra Autoscale – Scale Down para o perfil de fim de semana do plano de Serviço de Aplicações de produção, a fórmula resolveria:  

![Taxa de inflação do plano app para fins de semana com base na regra Autoscale – Scale Down.][Equation4]

O plano de serviço de aplicações de produção pode crescer a uma taxa máxima de oito instâncias/hora durante a semana e quatro instâncias/hora durante o fim de semana. Pode libertar casos a uma taxa máxima de quatro casos/hora durante a semana e seis instâncias/hora durante os fins de semana.

Se vários planos de Serviço de Aplicações estiverem hospedados num pool de trabalhadores, você tem que calcular a *taxa de inflação total* como a soma da taxa de inflação para todos os planos do Serviço de Aplicações que estão hospedados nesse pool de trabalhadores.

![Cálculo total da taxa de inflação para vários planos de Serviço de Aplicações hospedados num pool de trabalhadores.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilize a taxa de insuflação do plano de aplicação para definir as regras de autoescala do pool dos trabalhadores
Os grupos de trabalhadores que acolhem planos de Serviço de Aplicações configurados para autoescala precisam de ser alocados a um tampão de capacidade. O tampão permite que as operações de autoescala cresçam e reduzam o plano de Serviço de Aplicações, se necessário. O tampão mínimo seria a taxa de inflação do Plano total de aplicação calculada.

Uma vez que as operações de escala de ambiente do Serviço de Aplicações demoram algum tempo a ser aplicadas, qualquer alteração deve ter em conta novas alterações de procura que possam ocorrer enquanto uma operação de escala está em curso. Para acomodar esta latência, recomendamos que utilize a taxa de inflação do Plano total de aplicação calculada como o número mínimo de casos adicionados para cada operação de autoescala.

Com esta informação, Frank pode definir o seguinte perfil de autoescala e regras:

![Regras de perfil de escala automática para exemplo LOB.][Worker-Pool-Scale]

| **Perfil de autoescala - Dias úteis** | **Perfil de autoescalação - Fins de semana** |
| --- | --- |
| **Nome:** Perfil do dia da semana |**Nome:** Perfil de fim de semana |
| **Escala por:** Regras de programação e desempenho |**Escala por:** Regras de programação e desempenho |
| **Perfil:** Dias úteis |**Perfil:** Fim de semana |
| **Tipo:** Recorrência |**Tipo:** Recorrência |
| **Intervalo de destino:** 13 a 25 instâncias |**Intervalo de destino:** 6 a 15 instâncias |
| **Dias:** Segunda, terça, quarta, quinta, sexta |**Dias:** Sábado, domingo |
| **Hora de início:** 7:00 AM |**Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |**Fuso horário:** UTC-08 |
|  | |
| **Regra de escala automática (Escala para cima)** |**Regra de escala automática (Escala para cima)** |
| **Recurso:** Piscina de trabalhadores 1 |**Recurso:** Piscina de trabalhadores 1 |
| **Métrica:** Trabalhadores disponíveis |**Métrica:** Trabalhadores disponíveis |
| **Operação:** Menos de 8 |**Operação:** Menos de 3 |
| **Duração:** 20 minutos |**Duração:** 30 minutos |
| **Agregação temporal:** Média |**Agregação temporal:** Média |
| **Ação:** Aumento da contagem de 8 |**Ação:** Aumento da contagem de 3 |
| **Arrefecer (minutos):** 180 |**Arrefecer (minutos):** 180 |
|  | |
| **Regra de escala automática (Escala para baixo)** |**Regra de escala automática (Escala para baixo)** |
| **Recurso:** Piscina de trabalhadores 1 |**Recurso:** Piscina de trabalhadores 1 |
| **Métrica:** Trabalhadores disponíveis |**Métrica:** Trabalhadores disponíveis |
| **Operação:** Maior que 8 |**Operação:** Maior que 3 |
| **Duração:** 20 minutos |**Duração:** 15 minutos |
| **Agregação temporal:** Média |**Agregação temporal:** Média |
| **Ação:** Diminuição da contagem de 2 |**Ação:** Diminuição da contagem de 3 |
| **Arrefecer (minutos):** 120 |**Arrefecer (minutos):** 120 |

A gama Target definida no perfil é calculada pelas instâncias mínimas definidas no perfil do plano de Serviço de Aplicação + tampão.

A gama Máxima seria a soma de todas as gamas máximas para todos os planos do Serviço de Aplicações alojados na piscina de trabalhadores.

A contagem de aumento para as regras de escala deve ser definida para pelo menos 1X a taxa de inflação do Plano de Serviço de Aplicação para escala.

A contagem de decréscimos pode ser ajustada para algo entre 1/2X e 1X a Taxa de Inflação do Plano de Serviço de Aplicações para a escala para a escala.

### <a name="autoscale-for-front-end-pool"></a>Autoescala para piscina frontal
As regras para a escala automática frontal são mais simples do que para piscinas de trabalhadores. Em primeiro lugar, deveria.  
certifique-se de que a duração da medição e os temporizadores de arrefecimento consideram que as operações de escala num plano de Serviço de Aplicações não são instantâneas.

Para este cenário, Frank sabe que a taxa de erro aumenta após as extremidades dianteiras atingirem a utilização de 80% do CPU e define a regra de escala automática para aumentar as instâncias da seguinte forma:

![Definições de autoescala para piscina frontal.][Front-End-Scale]

| **Perfil de autoescala - Extremidades frontais** |
| --- |
| **Nome:** Autoscale – Extremidades frontais |
| **Escala por:** Regras de programação e desempenho |
| **Perfil:** Todos os dias |
| **Tipo:** Recorrência |
| **Intervalo de destino:** 3 a 10 instâncias |
| **Dias:** Todos os dias |
| **Hora de início:** 9:00 AM |
| **Fuso horário:** UTC-08 |
|  |
| **Regra de escala automática (Escala para cima)** |
| **Recurso:** Piscina frontal |
| **Métrica:** CPU % |
| **Operação:** Maior que 60% |
| **Duração:** 20 minutos |
| **Agregação temporal:** Média |
| **Ação:** Aumento da contagem de 3 |
| **Arrefecer (minutos):** 120 |
|  |
| **Regra de escala automática (Escala para baixo)** |
| **Recurso:** Piscina de trabalhadores 1 |
| **Métrica:** CPU % |
| **Operação:** Menos de 30% |
| **Duração:** 20 Minutos |
| **Agregação temporal:** Média |
| **Ação:** Diminuição da contagem de 3 |
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
