---
title: Otimize cargas de trabalho do Azure utilizando a pontuação do Advisor
description: Use pontuação do Advisor para tirar o máximo partido de Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 29d8480f501a78c1668b52034f439f998419f9d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335624"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Otimize cargas de trabalho do Azure utilizando a pontuação do Advisor

## <a name="introduction-to-advisor-score"></a>Introdução à pontuação do Advisor

O Azure Advisor fornece recomendações de boas práticas para as suas cargas de trabalho. Estas recomendações são personalizadas e ações para ajudá-lo:
* Melhore a postura das suas cargas de trabalho e otimize as suas implementações Azure
* Proativamente prevenir as principais questões seguindo as melhores práticas
* Avalie as suas cargas de trabalho do Azure contra os cinco pilares do [Quadro de Well-Architected microsoft Azure](https://docs.microsoft.com/azure/architecture/framework/)

Como uma característica central do Advisor, a **pontuação do Advisor** é concebida para ajudá-lo a alcançar estes objetivos de forma eficaz e eficiente. 

Para tirar o máximo partido do Azure, é crucial entender onde está na sua jornada de otimização de carga de trabalho, quais os serviços/recursos que são bem consumidos e que não são. Além disso, vai querer saber como priorizar as suas ações, com base em recomendações, para maximizar o resultado. Também é importante acompanhar e relatar os progressos que está a fazer nesta jornada de otimização. Com **pontuação Advisor,** você pode facilmente fazer todas estas coisas com a nossa nova experiência de gamificação. Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a sua configuração de telemetria e recursos de uso para verificar as melhores práticas da indústria. O Advisor agrega então as suas descobertas numa única pontuação para que possa ver, num ápice, se está a tomar as medidas necessárias para construir soluções fiáveis, seguras e económicas. A pontuação do Advisor consiste numa pontuação global, que pode ser ainda mais dividida em cinco categorias, uma para cada categoria de Azure Advisor, que representa os cinco pilares do Quadro Well-Architected. Você pode acompanhar o progresso que faz ao longo do tempo, visualizando a sua pontuação geral e pontuação de categoria com tendência diária, semanal e mensal, e pode definir referências para ajudá-lo a alcançar os seus objetivos. 

 ![Experiência de pontuação de conselheiro](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Como consumir pontuação do Advisor
O Advisor exibe a sua pontuação e desagregação geral do Advisor para as categorias Advisor, em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor segue as recomendações do Advisor. Utilizando estes grãos de pontuação, pode facilmente alcançar abaixo do fluxo:
* **Pontuação de conselheiro** para ajudá-lo a basear como a sua carga de trabalho/subscrições está a fazer com base na pontuação do Advisor. Você também pode ver as tendências históricas para entender qual é a sua tendência.
* **As pontuações** da categoria de conselheiro de cada recomendação dir-lhe-ão quais as recomendações pendentes que melhoram mais a sua pontuação. Estes valores refletem tanto o peso da recomendação como a nossa previsão de facilidade de implementação para garantir que você pode obter o maior valor com o seu tempo e ajuda-o com a priorização
* **Impacto de pontuação** de cada recomendação para ajudá-lo a priorizar as suas ações de reparação para cada categoria

A contribuição de cada recomendação para a sua pontuação de categoria é mostrada claramente na página de pontuação do Advisor no portal Azure. Pode aumentar cada pontuação de categoria pela pontuação percentual listada na coluna de impacto de pontuação da categoria. Este valor reflete tanto o peso da recomendação na categoria como a facilidade de implementação prevista para fazer face aos frutos potencialmente baixos. Focar-se nas recomendações com o maior impacto de pontuação irá ajudá-lo a fazer mais progressos com o tempo.  

![Impacto da pontuação do conselheiro](./media/advisor-score-2.png)

Caso alguma das recomendações do Advisor não seja relevante para um recurso individual, pode adiar ou rejeitar essas recomendações e estas serão excluídas do cálculo da pontuação a partir da próxima atualização. O Advisor também usará esta entrada como feedback adicional para melhorar o modelo.

## <a name="how-is-advisor-score-calculated"></a>Como é calculada a pontuação do Advisor?
O Advisor exibe as suas pontuações de categoria e a sua pontuação geral do Advisor em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos, *avaliados pelo Advisor,* seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, segue as recomendações do Advisor. 
**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A sua pontuação geral do Advisor é calculada como uma soma de cada pontuação de categoria aplicável, dividida pela soma da pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das subscrições, significa que o Advisor soma a pontuação de cada categoria e divide-se em 500. No entanto, **cada pontuação de categoria só é calculada se utilizar recursos que são avaliados pelo Advisor.**

**Exemplo de cálculo de pontuação de conselheiro**
* Pontuação de subscrição única: Esta é a média simples de todas as pontuações da categoria Advisor para a sua subscrição. Se as pontuações da categoria Advisor forem como - Custo = 73, Fiabilidade = 85, Excelência Operacional = 77, Desempenho = 100; a **pontuação do Advisor** seria (73 + 85 + 77 + 100)/(4x100) = 0,84 ou 84%.
* Pontuação de várias subscrições: Quando várias subscrições são selecionadas, a pontuação geral do conselheiro que geramos são pontuações de categoria agregadas ponderadas. Aqui, cada pontuação de categoria Advisor é agregada com base em recursos consumidos por subscrições. Assim que tivermos as pontuações de categoria agregadas ponderadas, fazemos um simples significar dar-lhe uma pontuação global para subscrições. 


### <a name="scoring-methodology"></a>Metodologia de pontuação: 
O cálculo da pontuação do Advisor pode ser resumido em quatro etapas:
1. O Advisor calcula o **custo de retalho dos recursos impactados,** que são os recursos nas suas subscrições que têm pelo menos uma recomendação no Advisor.
2. O consultor calcula o **custo de retalho dos recursos avaliados,** que são os recursos que são monitorizados pelo Advisor, quer tenham ou não recomendações. 
3. Para cada tipo de recomendação, o Advisor calcula a **relação de recursos saudáveis,** que é o custo retalhista dos recursos impactados divididos pelo custo retalhista dos recursos avaliados.
4. O advisor aplica três pesos adicionais à relação de recursos saudáveis em cada categoria:
  * As recomendações com maior impacto são ponderadas mais pesadas do que as que têm menor impacto.
  * Os recursos com recomendações de longa data contarão mais contra a sua pontuação.
  * Os recursos que adia ou dispensa no Advisor são totalmente removidos do seu cálculo de pontuação. 
    
O Advisor aplica este modelo a um nível de categoria Advisor (Security usa o modelo [Secure Score),](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) dando-nos pontuação Advisor para cada categoria e ainda uma média simples produz a pontuação final do Advisor.


## <a name="advisor-score-faq"></a>Pontuação de assessores FAQ
* **Quantas vezes a minha pontuação é renovada?**
A sua pontuação é atualizada pelo menos uma vez por dia. 
* **Porque é que algumas recomendações têm um valor "-" vazio na coluna de impacto da pontuação da categoria?** O advisor não inclui imediatamente novas recomendações ou aquelas com alterações recentes no modelo de pontuação. Após um curto período de avaliação, normalmente algumas semanas, serão incluídos na pontuação. 
* **Porque é que a pontuação de custos é maior para algumas recomendações, mesmo que tenham poupanças potenciais mais baixas?**
A sua pontuação de custos reflete tanto as suas poupanças potenciais de recursos subutilizados como a facilidade prevista de implementação dessas recomendações. Por exemplo, o peso extra é aplicado a recursos impactados que estão inativos há mais tempo, mesmo que as poupanças potenciais seja menor. 
* **Por que não tenho uma pontuação para uma ou mais categorias ou assinaturas?**
O Advisor gerará uma pontuação apenas para as categorias e subscrições que têm recursos que são avaliados pelo Advisor.
* **E se uma recomendação não for relevante?**
Se rejeitar uma recomendação do Advisor, será omitida do cálculo da sua pontuação. Rejeitar recomendações também ajuda o Conselheiro a melhorar a qualidade das recomendações.
* **Porque é que a minha pontuação mudou?** A pontuação pode ser alterada se remediar os recursos impactados adotando as melhores práticas que o Advisor recomenda. Se você ou alguém com permissões na sua subscrição tiver modificado ou criado novos recursos, também poderá ver flutuações na sua pontuação porque a sua pontuação baseia-se numa relação entre os recursos impactados pelo custo em relação ao custo total de todos os recursos.
* **Como calcula o custo de venda a retalho de recursos numa subscrição?**
O consultor utiliza as tarifas de pagamento à medida que vai, publicada na página de preços Azure.com, que não reflete quaisquer descontos aplicáveis, multiplicados pela quantidade de utilização no último dia em que o recurso foi atribuído. Omitir descontos do cálculo do custo de recurso faz com que a pontuação do Advisor seja comparável entre subscrições, inquilinos e inscrições onde os descontos podem variar. 
* **Preciso de ver as recomendações do Advisor para conseguir pontos para a minha pontuação?** N.º A sua pontuação reflete se adota as melhores práticas que o Advisor recomenda, mesmo que adote essas melhores práticas proativamente e nunca veja as suas recomendações no Advisor.
* **A metodologia de pontuação diferencia entre cargas de produção e dev-teste?**
Não, não por enquanto, mas pode rejeitar recomendações sobre recursos individuais se esses recursos forem utilizados para o desenvolvimento e teste e a recomendação não se aplicar.
* **Posso comparar pontuações entre uma subscrição com 100 recursos e uma subscrição com 100.000 recursos?**
A metodologia de pontuação é projetada para controlar o número de recursos numa mistura de subscrição e serviço, para que as subscrições com menos recursos possam ter pontuações mais altas ou mais baixas do que as subscrições com mais recursos. 
* **O que significa quando vejo "Em breve" na coluna de impacto do Score?**
Isto significa que esta é uma nova recomendação e ainda estamos a trabalhar para a introduzir no nosso modelo de pontuação advisor. Uma vez que esta nova recomendação seja considerada no cálculo da pontuação, verá o valor de impacto da pontuação para a sua recomendação.  
* **A minha pontuação depende de quanto gasto no Azure?**
Não, a sua pontuação não é necessariamente um reflexo de quanto gasta, e gastos desnecessários resultarão numa pontuação de custos mais baixa.

## <a name="how-to-access-advisor"></a>Como aceder ao Advisor
A pontuação do conselheiro está em pré-visualização pública no portal Azure. Você tem que ir à secção Advisor e você vai encontrar a pontuação Advisor como o item do 2º menu no navegador esquerdo. 

![Ponto de entrada do Advisor Score](./media/advisor-score-3.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
