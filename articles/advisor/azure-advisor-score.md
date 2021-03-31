---
title: Otimize as cargas de trabalho do Azure utilizando o Advisor Score
description: Use o Azure Advisor Score para tirar o máximo partido do Azure.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630127"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Otimize as cargas de trabalho do Azure utilizando o Advisor Score

## <a name="introduction-to-advisor-score"></a>Introdução à Pontuação do Conselheiro

O Azure Advisor fornece recomendações de boas práticas para as suas cargas de trabalho. Estas recomendações são personalizadas e ações para ajudá-lo:

* Melhore a postura das suas cargas de trabalho e otimize as suas implementações Azure.
* Proactivamente, previnem as questões de topo seguindo as melhores práticas.
* Avalie as suas cargas de trabalho do Azure contra os cinco pilares do [Quadro de Well-Architected microsoft Azure](/azure/architecture/framework/).

Como uma característica central do Advisor, o Advisor Score pode ajudá-lo a alcançar estes objetivos de forma eficaz e eficiente.

Para tirar o máximo partido do Azure, é crucial perceber onde está na sua jornada de otimização de carga de trabalho. É preciso saber quais os serviços ou recursos que são bem consumidos e quais não são. Além disso, vai querer saber como priorizar as suas ações, com base em recomendações, para maximizar o resultado.

Também é importante acompanhar e relatar os progressos que está a fazer nesta jornada de otimização. Com o Advisor Score, você pode facilmente fazer todas estas coisas com a nova experiência de gamificação.

Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a sua configuração de telemetria e recursos de uso para verificar as melhores práticas da indústria. Em seguida, o conselheiro agrega as suas conclusões numa única pontuação. Com esta pontuação, pode ver se está a tomar as medidas necessárias para construir soluções fiáveis, seguras e económicas.

A pontuação do Advisor consiste numa pontuação global, que pode ser ainda mais dividida em cinco categorias. Uma nota para cada categoria de Conselheiro representa os cinco pilares do Quadro Well-Architected.

Você pode acompanhar o progresso que faz ao longo do tempo, visualizando a sua pontuação geral e pontuação de categoria com tendências diárias, semanais e mensais. Também pode definir parâmetros de referência para ajudá-lo a atingir os seus objetivos.

 ![Screenshot que mostra a página De Pontuação do Conselheiro.](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Interpretar uma partitura de Conselheiro

O Advisor exibe a sua pontuação geral do Advisor e uma desagregação para as categorias Advisor, em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor segue as recomendações do Advisor. Utilizando estes grãos de pontuação, pode facilmente obter o seguinte fluxo:

* **O Advisor Score** ajuda-o a basear a forma como a sua carga de trabalho ou subscrições estão a fazer com base numa pontuação do Advisor. Você também pode ver as tendências históricas para entender qual é a sua tendência.
* **Pontuação por categoria** para cada recomendação diz-lhe quais as recomendações pendentes que melhoram a sua pontuação mais. Estes valores refletem tanto o peso da recomendação como a facilidade de implementação prevista. Estes fatores ajudam a garantir que você pode obter o máximo valor com o seu tempo. Também te ajudam na priorização.
* **O impacto da pontuação da categoria** para cada recomendação ajuda-o a priorizar as suas ações de reparação para cada categoria.

A contribuição de cada recomendação para a sua pontuação de categoria é mostrada claramente na página **De Pontuação de Conselheiro** no portal Azure. Pode aumentar cada pontuação de categoria pela percentagem listada na coluna **Potencial de aumento de pontuação.** Este valor reflete tanto o peso da recomendação na categoria como a facilidade de implementação prevista para fazer face às tarefas potencialmente mais fáceis. Focar-se nas recomendações com o maior impacto de pontuação irá ajudá-lo a fazer mais progressos com o tempo.

![Screenshot que mostra o impacto da pontuação do Advisor.](./media/advisor-score-2.png)

Se quaisquer recomendações do Advisor não forem relevantes para um recurso individual, pode adiar ou rejeitar essas recomendações. Serão excluídos do cálculo da pontuação com a próxima atualização. O Advisor também usará esta entrada como feedback adicional para melhorar o modelo.

## <a name="how-is-an-advisor-score-calculated"></a>Como é calculada uma pontuação do Advisor?

O Advisor exibe as suas pontuações de categoria e a sua pontuação geral do Advisor em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos, *avaliados pelo Advisor,* seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, segue as recomendações do Advisor.

**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A sua pontuação geral do Advisor é calculada como uma soma de cada pontuação de categoria aplicável, dividida pela soma da pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das subscrições, significa que o Advisor soma a pontuação de cada categoria e divide-se em 500. Mas *cada pontuação de categoria só é calculada se utilizar recursos que são avaliados pelo Advisor*.

### <a name="advisor-score-calculation-example"></a>Exemplo de cálculo de pontuação de conselheiro

* **Pontuação única de subscrição:** Este exemplo é a média simples de todas as pontuações da categoria Advisor para a sua subscrição. Se as pontuações da categoria Advisor forem - **Custo** = 73, **Fiabilidade** = 85, **Excelência Operacional** = 77, e **Desempenho** = 100, a pontuação do Advisor seria (73 + 85 + 77 + 100)/(4x100) = 0,84% ou 84%.
* **Pontuação de várias subscrições:** Quando várias subscrições são selecionadas, as pontuações gerais do Advisor geradas são pontuações de categoria agregadas ponderadas. Aqui, cada pontuação de categoria Advisor é agregada com base em recursos consumidos por subscrições. Depois de o Advisor ter as pontuações de categoria agregadas ponderadas, o Advisor faz um cálculo médio simples para lhe dar uma pontuação global para subscrições.

### <a name="scoring-methodology"></a>Metodologia de pontuação

O cálculo da pontuação do Advisor pode ser resumido em quatro etapas:

1. O consultor calcula o *custo retalhista dos recursos impactados.* Estes recursos são os que estão nas suas subscrições que têm pelo menos uma recomendação no Advisor.
1. O consultor calcula o *custo retalhista dos recursos avaliados.* Estes recursos são os monitorizados pelo Advisor, quer tenham ou não recomendações.
1. Para cada tipo de recomendação, o Advisor calcula a *relação de recursos saudáveis*. Este rácio é o custo retalhista dos recursos impactados divididos pelo custo retalhista dos recursos avaliados.
1. O advisor aplica três pesos adicionais à relação de recursos saudáveis em cada categoria:

   * As recomendações com maior impacto são ponderadas mais pesadas do que as recomendações com menor impacto.
   * Os recursos com recomendações de longa data contarão mais contra a sua pontuação.
   * Os recursos que adia ou dispensa no Advisor são totalmente removidos do seu cálculo de pontuação.

O Advisor aplica este modelo a nível de categoria Advisor para dar uma pontuação advisor para cada categoria. **A** segurança usa um modelo [de pontuação seguro.](../security-center/secure-score-security-controls.md#introduction-to-secure-score) Uma média simples produz a pontuação final do Advisor.

## <a name="advisor-score-faqs"></a>Pontuação de pontuação de aconselhamento FAQs

### <a name="how-often-is-my-score-refreshed"></a>Quantas vezes a minha pontuação é renovada?

A sua pontuação é atualizada pelo menos uma vez por dia.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>Porque é que algumas recomendações têm o valor "-" vazio na coluna de impacto da pontuação da categoria?

O advisor não inclui imediatamente novas recomendações ou recomendações com as recentes alterações no modelo de pontuação. Após um curto período de avaliação, normalmente algumas semanas, estão incluídos na pontuação.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>Porque é que a pontuação de custos é maior para algumas recomendações, mesmo que tenham poupanças potenciais mais baixas?

A sua pontuação **de custos** reflete tanto as suas poupanças potenciais de recursos subutilizados como a facilidade prevista de implementação dessas recomendações. Por exemplo, o peso extra é aplicado a recursos impactados que estão inativos há mais tempo, mesmo que as poupanças potenciais seja menor.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>Por que não tenho uma pontuação para uma ou mais categorias ou assinaturas?

O Advisor gera uma pontuação apenas para as categorias e subscrições que têm recursos que são avaliados pelo Advisor.

### <a name="what-if-a-recommendation-isnt-relevant"></a>E se uma recomendação não for relevante?

Se rejeitar uma recomendação do Advisor, será omitida do cálculo da sua pontuação. Rejeitar recomendações também ajuda o Conselheiro a melhorar a qualidade das recomendações.

### <a name="why-did-my-score-change"></a>Porque é que a minha pontuação mudou?

A pontuação pode ser alterada se remediar os recursos impactados adotando as melhores práticas que o Advisor recomenda. Se você ou alguém com permissões na sua subscrição tiver modificado ou criado novos recursos, também poderá ver flutuações na sua pontuação. A sua pontuação baseia-se numa relação entre os recursos com impacto no custo relativamente ao custo total de todos os recursos.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Como calcula o custo de venda a retalho de recursos numa subscrição?

O advisor usa as tarifas pay-as-you-go publicadas nos [preços Azure](https://azure.microsoft.com/pricing/). Estas tarifas não refletem quaisquer descontos aplicáveis. As taxas são então multiplicadas pela quantidade de utilização no último dia em que o recurso foi atribuído. Omitir descontos do cálculo do custo de recurso faz com que as pontuações do Advisor sejam comparáveis entre subscrições, inquilinos e inscrições onde os descontos podem variar.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>Preciso de ver as recomendações do Advisor para conseguir pontos para a minha pontuação?

N.º A sua pontuação reflete se adota as melhores práticas que o Advisor recomenda, mesmo que adote essas melhores práticas proativamente e nunca veja as suas recomendações no Advisor.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>A metodologia de pontuação diferencia entre cargas de produção e dev-teste?

Não, não por enquanto. Mas pode rejeitar recomendações sobre recursos individuais se esses recursos forem usados para desenvolvimento e teste e as recomendações não se aplicarem.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>Posso comparar pontuações entre uma subscrição com 100 recursos e uma subscrição com 100.000 recursos?

A metodologia de pontuação destina-se a controlar o número de recursos numa mistura de subscrição e serviço. As subscrições com menos recursos podem ter pontuações mais altas ou inferiores às subscrições com mais recursos.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>O que significa quando vejo "Em breve" na coluna de impacto da pontuação?

Esta mensagem significa que a recomendação é nova, e estamos a trabalhar para a levar ao modelo de pontuação do Advisor. Depois de esta nova recomendação ser considerada num cálculo de pontuação, verá o valor de impacto da pontuação para a sua recomendação.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>A minha pontuação depende de quanto gasto no Azure?

N.º A tua pontuação não é necessariamente um reflexo do quanto gastas. Gastos desnecessários resultarão numa pontuação **de custos** mais baixa.

## <a name="access-advisor-score"></a>Pontuação do Conselheiro de Acesso

Advisor Score está em pré-visualização pública no portal Azure. No painel esquerdo, sob a secção **Advisor,** consulte **Advisor Score**.

![Screenshot que mostra o ponto de entrada do Advisor Score.](./media/advisor-score-3.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre recomendações do Advisor, consulte:

* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
