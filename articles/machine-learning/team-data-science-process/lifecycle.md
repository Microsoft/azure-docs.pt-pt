---
title: O ciclo de vida do Processo de Ciência de Dados da Equipa
description: O Team Data Science Process (TDSP) fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados.
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
ms.openlocfilehash: a043a1655950f3ed7688e59352f8a912146e12c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720457"
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do Processo de Ciência de Dados da Equipa

O Team Data Science Process (TDSP) fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve os passos completos que os projetos de sucesso seguem. Se utilizar outro ciclo de vida de ciência de dados, como o Processo Padrão da Indústria Cruzada para a Mineração de Dados [(CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)A Descoberta do Conhecimento em Bases de Dados [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)ou o próprio processo personalizado da sua organização, ainda pode utilizar o TDSP baseado em tarefas. 

Este ciclo de vida destina-se a projetos de ciência de dados que se destinam a ser enviados como parte de aplicações inteligentes. Estas aplicações implementam modelos de machine learning ou inteligência artificial para análiseprevetiva. Projetos exploratórios de ciência de dados e projetos de análise improvisados também podem beneficiar da utilização deste processo. Mas para esses projetos, alguns dos passos aqui descritos podem não ser necessários. 

## <a name="five-lifecycle-stages"></a>Cinco estágios de ciclo de vida

O ciclo de vida da TDSP é composto por cinco grandes etapas que são executadas iterativamente. Estas fases incluem:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Aqui está uma representação visual do ciclo de vida da TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida da TDSP é modelado como uma sequência de passos iterados que fornecem orientação sobre as tarefas necessárias para usar modelos preditivos. Implementa os modelos preditivos no ambiente de produção que pretende utilizar para construir as aplicações inteligentes. O objetivo deste ciclo de vida do processo é continuar a mover um projeto de ciência de dados para um ponto final claro de envolvimento. A ciência dos dados é um exercício de pesquisa e descoberta. A capacidade de comunicar tarefas à sua equipa e aos seus clientes utilizando um conjunto bem definido de artefactos que empregam modelos padronizados ajuda a evitar mal-entendidos. A utilização destes modelos também aumenta a chance de conclusão bem-sucedida de um complexo projeto de ciência de dados.

Para cada etapa, fornecemos as seguintes informações:

   * **Objetivos**: Os objetivos específicos.
   * **Como fazê-lo**: Um esboço das tarefas específicas e orientação sobre como completá-las.
   * **Artefactos**: Os entregas e o suporte para produzi-los.

## <a name="next-steps"></a>Passos seguintes

Fornecemos walkthroughs completos de ponta a ponta que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).
