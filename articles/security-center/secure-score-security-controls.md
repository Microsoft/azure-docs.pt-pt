---
title: Pontuação de segurança no Centro de Segurança do Azure
description: Descrição da pontuação segura do Azure Security Center e dos seus controlos de segurança
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007686"
---
# <a name="secure-score-in-azure-security-center"></a>Pontuação de segurança no Centro de Segurança do Azure

## <a name="introduction-to-secure-score"></a>Introdução para garantir pontuação

O Centro de Segurança Azure tem dois objetivos principais: 

- para ajudá-lo a entender a sua situação atual de segurança
- para ajudá-lo de forma eficiente e eficaz a melhorar a sua segurança

A característica central no Centro de Segurança que lhe permite atingir esses objetivos é **pontuação segura.**

O Security Center avalia continuamente os seus recursos, subscrições e organização para questões de segurança. Em seguida, agrega todas as conclusões numa única pontuação para que possa dizer, num ápice, a sua situação de segurança atual: quanto maior for a pontuação, menor o nível de risco identificado.

A pontuação segura é mostrada nas páginas do portal Azure como um valor percentual, mas os valores subjacentes também são claramente apresentados:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Pontuação máxima segura, como mostrado no portal":::

Para aumentar a sua segurança, reveja a página de recomendações do Security Center para as ações pendentes necessárias para aumentar a sua pontuação. Cada recomendação inclui instruções para ajudá-lo a remediar a questão específica.

As recomendações são agrupadas nos **controlos de segurança.** Cada controlo é um grupo lógico de recomendações de segurança relacionadas, e reflete as suas superfícies de ataque vulneráveis. A sua pontuação só melhora quando remedia *todas as* recomendações para um único recurso dentro de um controlo. Para ver se a sua organização está a proteger cada superfície de ataque individual, reveja as pontuações para cada controlo de segurança.

Para obter mais informações, consulte [como a sua pontuação segura é calculada](secure-score-security-controls.md#how-your-secure-score-is-calculated) abaixo. 


## <a name="access-your-secure-score"></a>Aceda à sua pontuação segura

Pode encontrar a sua pontuação máxima, bem como a sua pontuação por subscrição, através do portal Azure ou programaticamente como descrito nas seguintes secções:

- [Obtenha a sua pontuação segura a partir do portal](#get-your-secure-score-from-the-portal)
- [Obtenha a sua pontuação segura na API REST](#get-your-secure-score-from-the-rest-api)
- [Obtenha a sua pontuação segura a partir do Azure Resource Graph (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Obtenha a sua pontuação segura a partir do portal

O Centro de Segurança exibe a sua pontuação de forma proeminente no portal: é o primeiro azulejo principal da página geral do Centro de Segurança. Selecionando este azulejo, leva-o à página de pontuação segura dedicada, onde verá a pontuação desabatada por subscrição. Selecione uma única subscrição para ver a lista detalhada de recomendações prioritárias e o impacto potencial que a sua reparação terá na pontuação da subscrição. 

Para recapitular, a sua pontuação segura é mostrada nos seguintes locais nas páginas do portal do Security Center.

- Num azulejo da **visão geral** do Centro de Segurança (painel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="A pontuação segura no painel do Centro de Segurança":::

- Na página de **pontuação Secure** dedicada pode ver a pontuação segura para a sua subscrição e os seus grupos de gestão:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="A pontuação segura para subscrições na página de pontuação segura do Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="A pontuação segura para grupos de gestão na página de pontuação segura do Security Center":::

    > [!NOTE]
    > Quaisquer grupos de gestão para os quais não tenha permissões suficientes, mostrarão a sua pontuação como "Restrito". 

- No topo da página de **Recomendações:**

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="A pontuação segura na página de recomendações do Centro de Segurança":::

### <a name="get-your-secure-score-from-the-rest-api"></a>Obtenha a sua pontuação segura na API REST

Pode aceder à sua pontuação através da pontuação segura API. Os métodos API fornecem a flexibilidade para consultar os dados e construir o seu próprio mecanismo de reporte das suas pontuações seguras ao longo do tempo. Por exemplo, pode utilizar a [API de Pontuações Seguras](/rest/api/securitycenter/securescores) para obter a pontuação de uma subscrição específica. Além disso, pode utilizar a [API de Controlos de Pontuação Segura](/rest/api/securitycenter/securescorecontrols) para listar os controlos de segurança e a pontuação atual das suas subscrições.

![Recuperação de uma única pontuação segura através da API](media/secure-score-security-controls/single-secure-score-via-api.png)

Por exemplo, ferramentas construídas em cima da pontuação segura API, consulte [a área de pontuação segura da nossa comunidade GitHub.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Obtenha a sua pontuação segura a partir do Azure Resource Graph (ARG)

O Azure Resource Graph fornece acesso instantâneo à informação de recursos em todos os seus ambientes em nuvem com capacidades robustas de filtragem, agrupamento e triagem. É uma forma rápida e eficiente de consultar informações através de subscrições Azure programáticas ou dentro do portal Azure. [Saiba mais sobre o Azure Resource Graph](../governance/resource-graph/index.yml).

Para aceder à pontuação segura para várias subscrições com ARG:

1. A partir do portal Azure, abra **o Azure Resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Página de recomendação do Azure Resource Graph Explorer**" :::

1. Insira a sua consulta Kusto (utilizando os exemplos abaixo para obter orientação).

    - Esta consulta devolve o ID de subscrição, a pontuação atual em pontos e em percentagem, e a pontuação máxima para a subscrição. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Esta consulta devolve o estado de todos os controlos de segurança. Para cada controlo, obterá o número de recursos insalubres, a pontuação atual e a pontuação máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selecione **Consulta de execução**.




## <a name="tracking-your-secure-score-over-time"></a>Rastreando a sua pontuação segura ao longo do tempo

Se for um utilizador de Power BI com uma conta Pro, pode utilizar o painel **DeSema** para rastrear a sua pontuação segura ao longo do tempo e investigar quaisquer alterações.

> [!TIP]
> Você pode encontrar este dashboard, bem como outras ferramentas para trabalhar programaticamente com pontuação segura, na área dedicada da comunidade do Centro de Segurança Azure no GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

O painel contém os seguintes dois relatórios para ajudá-lo a analisar o seu estado de segurança:

- **Resumo de Recursos** - fornece dados resumidos sobre a saúde dos seus recursos.
- **Secure Score Summary** - fornece dados resumidos sobre o progresso da sua pontuação. Utilize o gráfico "Obter pontuação segura ao longo do tempo por subscrição" para ver as alterações na pontuação. Se notar uma alteração dramática na sua pontuação, verifique a tabela "alterações detetadas que podem afetar a sua pontuação segura" para eventuais alterações que possam ter causado a alteração. Este quadro apresenta recursos eliminados, recursos recém-utilizados ou recursos que o seu estatuto de segurança mudou para uma das recomendações.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="O painel opcional Secure Score Over Time Power BI para rastrear a sua pontuação segura ao longo do tempo e investigar alterações":::





## <a name="how-your-secure-score-is-calculated"></a>Como a sua pontuação segura é calculada 

A contribuição de cada controlo de segurança para a pontuação máxima segura global é claramente indicada na página de recomendações.

[![A pontuação de segurança reforçada introduz controlos de segurança](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Para obter todos os pontos possíveis para um controlo de segurança, todos os seus recursos devem cumprir todas as recomendações de segurança dentro do controlo de segurança. Por exemplo, o Security Center tem várias recomendações sobre como proteger as suas portas de gestão. Tens de os remediar a todos para fazer a diferença na tua pontuação segura.

Por exemplo, o controlo de segurança chamado "Aplicar atualizações do sistema" tem uma pontuação máxima de seis pontos, que pode ver na ponta da ferramenta sobre o valor potencial de aumento do controlo:

[![O controlo de segurança "Aplicar atualizações do sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

A pontuação máxima para este controlo, Aplicar atualizações do sistema, é sempre 6. Neste exemplo, existem 50 recursos. Assim, dividimos a pontuação máxima por 50, e o resultado é que cada recurso contribui com 0,12 pontos. 

* **Aumento potencial** (0,12 x 8 recursos não saudáveis = 0,96) - Os restantes pontos disponíveis dentro do controlo. Se remediar todas as recomendações neste controlo, a sua pontuação aumentará 2% (neste caso, 0,96 pontos arredondados até 1 ponto). 
* **Pontuação atual** (0,12 x 42 recursos saudáveis = 5,04) - A pontuação atual para este controlo. Cada controlo contribui para a pontuação total. Neste exemplo, o controlo está a contribuir com 5,04 pontos para o total de segurança atual.
* **Pontuação máxima** - O número máximo de pontos que pode ganhar ao completar todas as recomendações dentro de um controlo. A pontuação máxima para um controlo indica o significado relativo desse controlo. Use os valores de pontuação máxima para triagem dos problemas para trabalhar primeiro. 


### <a name="calculations---understanding-your-score"></a>Cálculos - compreender a sua pontuação

|Metric|Fórmula e exemplo|
|-|-|
|**Pontuação atual do controlo de segurança**|<br>![Equação para calcular a pontuação de um controlo de segurança](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Cada controlo de segurança individual contribui para a Pontuação de Segurança. Cada recurso afetado por uma recomendação dentro do controlo, contribui para a pontuação atual do controlo. A pontuação atual para cada controlo é uma medida do estado dos recursos *sob* controlo.<br>![Dicas de ferramentas que mostram os valores utilizados no cálculo da pontuação atual do controlo de segurança](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>Neste exemplo, a pontuação máxima de 6 seria dividida por 78 porque essa é a soma dos recursos saudáveis e insalubres.<br>6 / 78 = 0,0769<br>Multiplicar-se pelo número de recursos saudáveis (4) resulta na pontuação atual:<br>0,0769 * 4 = **0,31**<br><br>|
|**Classificação de segurança**<br>Subscrição individual|<br>![Equação para calcular a pontuação segura de uma subscrição](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Pontuação segura de subscrição única com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>Neste exemplo, existe uma única subscrição com todos os controlos de segurança disponíveis (uma pontuação máxima potencial de 60 pontos). A pontuação mostra 28 pontos de um possível 60 e os restantes 32 pontos refletem-se nos números de "Potencial aumento de pontuação" dos controlos de segurança.<br>![Lista de controlos e o aumento potencial da pontuação](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Classificação de segurança**<br>Múltiplas subscrições|<br>![Equação para calcular a pontuação segura para várias subscrições](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Ao calcular a pontuação combinada para várias subscrições, o Security Center inclui um *peso* para cada subscrição. Os pesos relativos para as suas subscrições são determinados pelo Security Center com base em fatores como o número de recursos.<br>A pontuação atual para cada subscrição é calculada da mesma forma que para uma única subscrição, mas então o peso é aplicado como mostrado na equação.<br>Ao visualizar várias subscrições, a pontuação segura avalia todos os recursos dentro de todas as políticas ativadas e agrupam o seu impacto combinado na pontuação máxima de cada controlo de segurança.<br>![Pontuação segura para várias subscrições com todos os controlos ativados](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>A pontuação combinada **não** é uma média; em vez disso, é a postura avaliada do estado de todos os recursos em todas as subscrições.<br>Também aqui, se for à página de recomendações e somar os pontos potenciais disponíveis, verá que é a diferença entre a pontuação atual (24) e a pontuação máxima disponível (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Que recomendações estão incluídas nos cálculos de pontuação segura?

Só as recomendações incorporadas têm impacto na pontuação segura.

Recomendações assinaladas como **Preview** não estão incluídas nos cálculos da sua pontuação segura. Devem continuar a ser remediados sempre que possível, para que quando o período de pré-visualização terminar contribuam para a sua pontuação.

Um exemplo de uma recomendação de pré-visualização:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendação com a bandeira de pré-visualização":::

## <a name="improve-your-secure-score"></a>Melhorar a sua pontuação segura

Para melhorar a sua pontuação segura, remedia as recomendações de segurança da sua lista de recomendações. Pode remediar manualmente cada recomendação para cada recurso, ou utilizando a **Quick Fix!** opção (quando disponível) aplicar uma remediação para uma recomendação a um grupo de recursos rapidamente. Para obter mais informações, consulte [recomendações corretivas.](security-center-remediate-recommendations.md)

Outra forma de melhorar a sua pontuação e garantir que os seus utilizadores não criam recursos que impactem negativamente a sua pontuação é configurar as opções Depor e Negar nas recomendações relevantes. Saiba mais em [Prevenir configurações erradas com recomendações de Cumprir/Negar](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controlos de segurança e suas recomendações

A tabela abaixo lista os controlos de segurança no Centro de Segurança Azure. Para cada controlo, pode ver o número máximo de pontos que pode adicionar à sua pontuação segura se remediar *todas as* recomendações listadas no controlo, para *todos os* seus recursos. 

O conjunto de recomendações de segurança fornecidas ao Centro de Segurança é adaptado aos recursos disponíveis no ambiente de cada organização. As recomendações podem ser ainda mais [personalizadas, desativando políticas](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) e [isentando recursos específicos de uma recomendação.](exempt-resource.md) 
 
Recomendamos que todas as organizações revejam cuidadosamente as suas iniciativas atribuídas à Política Azure. 

> [!TIP]
> Para mais detalhes sobre a revisão e edição das suas iniciativas, consulte [Trabalhar com políticas de segurança.](tutorial-security-policy.md) 

Embora a iniciativa de segurança padrão do Security Center se baseie nas melhores práticas e padrões da indústria, existem cenários em que as recomendações incorporadas listadas abaixo podem não se adequar completamente à sua organização. Consequentemente, às vezes será necessário ajustar a iniciativa padrão - sem comprometer a segurança - para garantir que está alinhada com as políticas da sua própria organização. padrões da indústria, padrões regulamentares e referências que é obrigado a cumprir.<br><br>
<div class="foo">

<style type="text/css"> .tg {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho da fonte:14px; transbordamento:escondido;estofamento:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;tamanho da fonte:18px; peso de fonte:normal;transbordar:escondido;estofamento:10px 5px;word-break:normal;} .tg-cly1{text-align:left;vertical-align:middle} .tg-lboi{border-color:herdamento;text-align:left;vertical-align:middle: </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Pontuação segura FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se eu abordar apenas três de quatro recomendações num controlo de segurança, a minha pontuação segura mudará?
N.º Não mudará até que remediar todas as recomendações para um único recurso. Para obter a pontuação máxima para um controlo, você deve remediar todas as recomendações, para todos os recursos.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se uma recomendação não for aplicável a mim, e eu a desativar na apólice, o meu controlo de segurança será cumprido e a minha pontuação segura atualizada?
Sim. Recomendamos desativar recomendações quando são inaplicáveis no seu ambiente. Para obter instruções sobre como desativar uma recomendação específica, consulte [as políticas de segurança para desativar](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se um controlo de segurança me oferece zero pontos para a minha pontuação segura, devo ignorá-la?
Em alguns casos, verá uma pontuação máxima de controlo superior a zero, mas o impacto é zero. Quando a pontuação incremental para a fixação de recursos é insignificante, é arredondada para zero. Não ignore estas recomendações, pois ainda trazem melhorias de segurança. A única exceção é o controlo "Melhores Práticas Adicionais". Remediar estas recomendações não aumentará a sua pontuação, mas aumentará a sua segurança geral.

## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a pontuação segura e os controlos de segurança que introduz. Para obter material relacionado, consulte os seguintes artigos:

- [Conheça os diferentes elementos de uma recomendação](security-center-recommendations.md)
- [Saiba como remediar recomendações](security-center-remediate-recommendations.md)
- [Veja as ferramentas baseadas no GitHub para trabalhar programáticamente com pontuação segura](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)