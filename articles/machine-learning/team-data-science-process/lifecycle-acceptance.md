---
title: Fase de aceitação do cliente do ciclo de vida do Processo de Ciência de Dados da Equipa
description: Os objetivos, tarefas e entregas para a fase de aceitação do cliente dos seus projetos de ciência de dados
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720525"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Fase de aceitação do cliente do ciclo de vida do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e entregas associados à fase de aceitação do cliente do Processo de Ciência de Dados da Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implantação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida da TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Objetivo
**Finalizar os resultados**do projeto : Confirme que o oleoduto, o modelo e a sua implantação num ambiente de produção satisfazem os objetivos do cliente.

## <a name="how-to-do-it"></a>Como fazê-lo
Há duas tarefas principais abordadas nesta fase:

   * **Validação do sistema**: Confirme que o modelo e o gasoduto implantados vão ao encontro das necessidades do cliente.
   * **Entrega do projeto**: Entregue o projeto à entidade que vai executar o sistema em produção.

O cliente deve validar que o sistema satisfaz as suas necessidades de negócio e que responde às questões com precisão aceitável para implementar o sistema para produção para uso através da aplicação do seu cliente. Toda a documentação está finalizada e revista. O projeto é entregue à entidade responsável pelas operações. Esta entidade pode ser, por exemplo, uma equipa de TI ou de dados de dados ou um agente do cliente responsável pela execução do sistema em produção. 

## <a name="artifacts"></a>Artefactos
O principal artefacto produzido nesta fase final é o **relatório de saída do projeto para o cliente.** Este relatório técnico contém todos os detalhes do projeto que são úteis para aprender sobre como operar o sistema. A TDSP fornece um modelo de [relatório de saída.](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) Pode usar o modelo como está, ou pode personalizá-lo para necessidades específicas do cliente. 


## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implantação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
