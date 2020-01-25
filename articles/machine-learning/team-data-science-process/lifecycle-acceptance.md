---
title: Fase de aceitação do cliente do ciclo de vida do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a fase de aceitação do cliente dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720525"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fase de aceitação do cliente do ciclo de vida do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados associados a fase de aceitação do cliente do Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão empresarial**
   2. **Aquisição de dados e compreensão**
   3. **Modelagem**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
**Finalizar os resultados**do projeto : Confirme que o oleoduto, o modelo e a sua implantação num ambiente de produção satisfazem os objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem duas tarefas principais abordadas neste estágio:

   * **Validação do sistema**: Confirme que o modelo implementado e o pipeline atendem às necessidades do cliente.
   * **Transição paraoperações de projeto**: passá o projeto para a entidade que vai ser executado o sistema na produção.

O cliente deve validar que o sistema atenda às suas necessidades de negócios e que responde as perguntas com a precisão aceitável para implantar o sistema para a produção para utilização pela aplicação de seu cliente. Toda a documentação é finalizada e revista. O projeto é entregue inicial para a entidade responsável por operações. Esta entidade pode ser, por exemplo, uma TI ou equipa de ciência de dados do cliente ou com um agente do cliente que é responsável por executar o sistema em produção. 

## <a name="artifacts"></a>Artefactos
O artefacto principal produzido nesse estágio final é o **sair do relatório do projeto do cliente**. Este relatório técnico contém todos os detalhes do projeto que são úteis para saber mais sobre como operar o sistema. TDSP fornece uma [sair do relatório](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) modelo. Pode utilizar o modelo como está, ou pode personalizá-lo para as necessidades do cliente específico. 


## <a name="next-steps"></a>Passos seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão empresarial](lifecycle-business-understanding.md)
   2. [Aquisição de dados e compreensão](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O [instruções passo a passo do exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com links e descrições em miniatura. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
