---
title: Otimize cargas de trabalho do Azure utilizando a pontuação do Advisor
description: Use pontuação do Advisor para tirar o máximo partido de Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056239"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Otimize cargas de trabalho do Azure utilizando a pontuação do Advisor

## <a name="introduction-to-advisor-score"></a>Introdução à pontuação do Advisor

O Azure Advisor fornece recomendações de boas práticas para as suas cargas de trabalho. Estas recomendações são personalizadas e ações para ajudá-lo:
* Melhore a postura das suas cargas de trabalho e otimize as suas implementações Azure
* Proativamente prevenir as principais questões seguindo as melhores práticas
* Avalie as suas cargas de trabalho do Azure contra os cinco pilares do Quadro Bem Architected da [Microsoft Azure](https://docs.microsoft.com/azure/architecture/framework/)

Como uma característica central do Advisor, a **pontuação do Advisor** é concebida para ajudá-lo a alcançar estes objetivos de forma eficaz e eficiente. 

Para tirar o máximo partido do Azure, é crucial entender onde está na sua jornada de otimização de carga de trabalho, quais os serviços/recursos que são bem consumidos e que não são. Além disso, vai querer saber como priorizar as suas ações, com base em recomendações, para maximizar o resultado. Também é importante acompanhar e relatar os progressos que está a fazer nesta jornada de otimização. Com **pontuação Advisor,** você pode facilmente fazer todas estas coisas com a nossa nova experiência de gamificação. Como seu consultor de nuvem personalizado, o Azure Advisor avalia continuamente a sua configuração de telemetria e recursos de uso para verificar as melhores práticas da indústria. O Advisor agrega então as suas descobertas numa única pontuação para que possa ver, num ápice, se está a tomar as medidas necessárias para construir soluções fiáveis, seguras e económicas. A pontuação do Advisor consiste numa pontuação global, que pode ser ainda mais dividida em cinco categorias, uma para cada categoria de Azure Advisor, que representa os cinco pilares do Quadro Bem-Architected. Você pode acompanhar o progresso que faz ao longo do tempo, visualizando a sua pontuação geral e pontuação de categoria com tendência diária, semanal e mensal, e pode definir referências para ajudá-lo a alcançar os seus objetivos. 

 ![Experiência de pontuação de conselheiro](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Como consumir pontuação do Advisor
O Advisor exibe a sua pontuação e desagregação geral do Advisor para as categorias Advisor, em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos avaliados pelo Advisor seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos avaliados pelo Advisor segue as recomendações do Advisor. Utilizando estes grãos de pontuação, pode facilmente alcançar abaixo do fluxo:
* **Pontuação de conselheiro** para ajudá-lo A linha de base como a sua carga de trabalho/subscrições está a fazer com base na pontuação do Advisor. Você também pode ver as tendências históricas para entender qual é a sua tendência.
* **Pontuações da Categoria De Conselheiro** para ajudá-lo a entender quais categorias precisam de mais atenção e ajuda-o com a priorização
* Potencial Aumento de **pontuação** de cada recomendação para ajudá-lo a priorizar as suas ações de remediação para cada categoria

A contribuição de cada recomendação para a sua pontuação é mostrada claramente na página geral do portal Azure. Pode aumentar a sua pontuação adotando as melhores práticas, e pode priorizar as recomendações que têm maior **aumento de pontuação potencial** para fazer o progresso mais rápido com o tempo que tem.  

![Impacto da pontuação do conselheiro](./media/advisor-score-2.png)

Uma vez que a metodologia de pontuação do Advisor aplica um peso extra a recursos mais caros com recomendações de longa data, você pode fazer o maior progresso remediando os recursos com o custo de retalho mais elevado primeiro. Caso alguma das recomendações do Advisor não seja relevante para um recurso individual, pode rejeitar essas recomendações para excluí-las do cálculo da pontuação e enviar feedback ao Advisor para melhorar as suas recomendações. 

## <a name="how-is-advisor-score-calculated"></a>Como é calculada a pontuação do Advisor?
O Advisor exibe as suas pontuações de categoria e a sua pontuação geral do Advisor em percentagens. Uma pontuação de 100% em qualquer categoria significa que todos os seus recursos, *avaliados pelo Advisor,* seguem as melhores práticas que o Advisor recomenda. Do outro lado do espectro, uma pontuação de 0% significa que nenhum dos seus recursos, avaliados pelo Advisor, segue as recomendações do Advisor. 
**Cada uma das cinco categorias tem uma pontuação potencial mais alta de 100.** A sua pontuação geral do Advisor é calculada como uma soma de cada pontuação de categoria aplicável, dividida pela soma da pontuação potencial mais alta de todas as categorias aplicáveis. Para a maioria das subscrições, significa que o Advisor soma a pontuação de cada categoria e divide-se em 500. No entanto, **cada pontuação de categoria só é calculada se utilizar recursos que são avaliados pelo Advisor.**

### <a name="scoring-methodology"></a>Metodologia de pontuação: 
O cálculo da pontuação do Advisor pode ser resumido em quatro etapas:
1. O Advisor calcula o **custo diário de retalho dos recursos impactados,** que são os recursos nas suas subscrições que têm pelo menos uma recomendação no Advisor.
2. O consultor calcula o **custo diário de retalho dos recursos avaliados,** que são os recursos que são monitorizados pelo Advisor, quer tenham ou não recomendações. 
3. Para cada tipo de recomendação, o Advisor calcula a **relação de recursos saudáveis,** que é o custo dos recursos impactados dividido pelo custo dos recursos avaliados.
4. O advisor aplica três pesos adicionais à relação de recursos saudáveis em cada categoria:
* As recomendações com maior impacto são ponderadas mais pesadas do que as que têm menor impacto.
* Os recursos com recomendações de longa data contarão mais contra a sua pontuação.
* Os recursos que dispensar no Advisor são removidos do seu cálculo de pontuação inteiramente. 
    
O Advisor aplica este modelo a um nível de categoria Advisor (Security usa o modelo [Secure Score),](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) dando-nos pontuação Advisor para cada categoria e ainda uma média simples produz a pontuação final do Advisor.


## <a name="advisor-score-faq"></a>Pontuação de assessores FAQ
* **Quantas vezes a minha pontuação é renovada?**
A sua pontuação é atualizada pelo menos uma vez por dia. 
* **Preciso de ver as recomendações do Advisor para conseguir pontos para a minha pontuação?**
Não. A sua pontuação reflete se adota as melhores práticas que o Advisor recomenda, mesmo que nunca veja essas recomendações no Advisor e adote as melhores práticas de forma proativa.  
* **Como calcula o custo diário de venda a retalho de recursos numa subscrição?**
O consultor utiliza as tarifas de pagamento à *medida que vai,* publicada na página de preços Azure.com, que não reflete quaisquer descontos aplicáveis, multiplicados pela quantidade de utilização no último dia em que o recurso foi atribuído. Omitir descontos do cálculo do custo de recurso faz com que a pontuação do Advisor seja comparável entre subscrições, inquilinos e inscrições onde os descontos podem variar. 
* **E se uma recomendação não for relevante?**
Se rejeitar uma recomendação do Advisor, será omitida do cálculo da sua pontuação. Rejeitar recomendações também ajuda o Conselheiro a melhorar a qualidade das recomendações.
* **Porque é que a minha pontuação mudou?** A pontuação pode ser alterada se remediar os recursos impactados adotando as melhores práticas que o Advisor recomenda. Se você ou alguém com permissões na sua subscrição tiver modificado ou criado novos recursos, também poderá ver flutuações na sua pontuação porque a sua pontuação baseia-se numa relação entre os recursos impactados pelo custo em relação ao custo total de todos os recursos.
* **A minha pontuação depende de quanto gasto no Azure?**
Não. A pontuação foi concebida para controlar o tamanho de uma mistura de subscrição e serviço. 
* **A metodologia de pontuação diferencia entre cargas de produção e dev-teste?**
Não, não por enquanto, mas pode rejeitar recomendações sobre recursos individuais se esses recursos forem utilizados para o desenvolvimento e teste e a recomendação não se aplicar.
* **Posso comparar pontuações entre uma subscrição com 100 recursos e uma subscrição com 100.000 recursos?**
A metodologia de pontuação é projetada para controlar o número de recursos numa mistura de subscrição e serviço, para que as subscrições com menos recursos possam ter pontuações mais altas ou mais baixas do que as subscrições com mais recursos. 

## <a name="how-to-access-advisor"></a>Como aceder ao Advisor
A pontuação do conselheiro está em pré-visualização pública no portal Azure. Você tem que ir à secção Advisor e você vai encontrar a pontuação Advisor como o item do 2º menu no navegador esquerdo. 

![Ponto de entrada do Advisor Score](./media/advisor-score-3.png)

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
