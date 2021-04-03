---
title: 'FAQ: SRE e | de codificação Microsoft Docs'
titleSuffix: Azure
description: 'FAQ: Compreender a relação entre sRE e codificação'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089076"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Perguntas frequentes: Preciso de saber codificar para me envolver com a SRE?

Quando os indivíduos estão a considerar envolver-se em SRE e as equipas estão a pensar em introduzir práticas SRE, uma questão comum que surge é "Precisas de saber codificar?"

A resposta curta: sim. 

Mas a resposta completa é um pouco mais matizada. Vejamos três locais onde a codificação entra em jogo na engenharia de fiabilidade do local, juntamente com o nível de conhecimentos de codificação necessários para cada um. Esta lista não está completa, mas estes cenários são alguns dos casos de uso mais comuns.

## <a name="scenario-1-removing-toil-through-automation"></a>Cenário 1: Remoção da labuta através da automatização

Os engenheiros de fiabilidade do site e outros que utilizam práticas SRE tentam, sempre que possível, remover a labuta. "Labuta" significa uma coisa específica na SRE. Labuta refere-se ao trabalho de operações sendo feito por um humano que tem certas características. As “tarefas enfadonhas” não possuem um valor compensatório a longo prazo. Não fazem progredir o serviço de forma significativa. São, muitas vezes, repetitivas e extremamente manuais (ainda que possam ser automatizadas). À medida que o serviço ou os sistemas ficam maiores, o número de pedidos desse sistema provavelmente também aumentará de forma proporcional e exigirá ainda mais trabalho manual.

Por exemplo, se um serviço exigir que a equipa SRE reinicie algo todas as semanas, ou que provisa novas contas e espaço de disco manualmente, ou reinicie-o repetidamente à mão -- esta é a carga operacional que está a labuda. Concluir essas ações não melhorou o serviço de forma persistente a longo prazo. Estas ações terão, provavelmente, de ser repetidas várias vezes.

As equipas de SRE odeiam este tipo de tarefas. Trabalham para eliminá-las sempre que possível e apropriado. Esta é uma das áreas em que a automatização entra em cena na SRE. Se estes pedidos puderem ser tratados automaticamente, isso liberta a equipa para trabalhar em coisas mais gratificantes e impactantes.

*Conhecimentos de codificação: a* automatização requer alguma experiência de codificação, mas não tem de requerer competências completas de engenharia de software. Se conseguires escrever pequenos scripts (talvez na concha powerShell ou Bourne) ou mesmo se criares um [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) código com quase nenhum código, esta aplicação ainda pode ajudar a eliminar a labuta.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Cenário 2: Controlo através de APIs/linguagens específicas de domínio (DSLs)/modelos

Embora não seja estritamente necessário para o trabalho de SRE, ser capaz de controlar ambientes através de APIs, DSLs e Modelos (especialmente ambientes de nuvem) permitem que as SREs reforcem o seu trabalho. O provisionamento/desavisionamento de infraestruturas, a monitorização configurada e a integração de vários serviços tornam-se muito mais eficientes através da codificação.

*Conhecimentos de codificação*: tal como o cenário anterior, isto requer alguma experiência de codificação, mas não tem de requerer competências completas de engenharia de software. Além dos scripts e aplicações lógicas mencionadas anteriormente, [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) também podem ser usados com a mínima experiência de codificação.

## <a name="scenario-3-fixing-the-code"></a>Cenário 3: Fixação do código

Os engenheiros de fiabilidade do site procuram melhorar a fiabilidade de um sistema. Este objetivo requer, por vezes, aprofundar o código fonte de um sistema, determinando o problema, e muitas vezes contribuindo com uma correção de volta à base de código. Embora o nível de sofisticação deste trabalho possa variar muito com base na situação, a experiência de codificação é um requisito definitivo nestes casos.

*Conhecimentos de codificação*: é frequentemente necessário conhecimentos completos de engenharia de software neste cenário.


## <a name="next-steps"></a>Passos seguintes

Interessado em aprender mais sobre engenharia de fiabilidade do site e trabalho de baixo código? Consulte o [nosso centro de engenharia de fiabilidade do site,](../index.yml)a documentação do produto acima ligada.
