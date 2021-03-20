---
title: O ciclo de vida do processo de ciência de dados da equipa
description: O Processo de Ciência de Dados de Equipa (TDSP) fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados.
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
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305596"
---
# <a name="the-team-data-science-process-lifecycle"></a>O ciclo de vida do processo de ciência de dados da equipa

O Processo de Ciência de Dados de Equipa (TDSP) fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve os passos completos que os projetos de sucesso seguem. Se utilizar outro ciclo de vida de ciência de dados, como o Processo Padrão da Indústria Cruzada para a Mineração de Dados [(CRISP-DM),](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)a Descoberta do Conhecimento em Bases de Dados [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)ou o processo personalizado da sua própria organização, ainda pode utilizar o TDSP baseado em tarefas. 

Este ciclo de vida destina-se a projetos de ciência de dados que se destinam a ser enviados como parte de aplicações inteligentes. Estas aplicações implementam modelos de aprendizagem automática ou inteligência artificial para análise preditiva. Os projetos exploratórios de ciência de dados e projetos de análise improvisada também podem beneficiar da utilização deste processo. Mas para esses projetos, alguns dos passos aqui descritos podem não ser necessários. 

## <a name="five-lifecycle-stages"></a>Cinco fases do ciclo de vida

O ciclo de vida TDSP é composto por cinco grandes fases que são executadas iterativamente. Estas fases incluem:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Aqui está uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


O ciclo de vida TDSP é modelado como uma sequência de passos iterados que fornecem orientação sobre as tarefas necessárias para usar modelos preditivos. Implementa os modelos preditivos no ambiente de produção que pretende utilizar para construir as aplicações inteligentes. O objetivo deste ciclo de vida do processo é continuar a mover um projeto de ciência de dados para um ponto final claro de envolvimento. A ciência dos dados é um exercício de investigação e descoberta. A capacidade de comunicar tarefas à sua equipa e aos seus clientes utilizando um conjunto bem definido de artefactos que empregam modelos padronizados ajuda a evitar mal-entendidos. A utilização destes modelos também aumenta a possibilidade de ter sucesso na conclusão de um complexo projeto de ciência de dados.

Para cada fase, fornecemos as seguintes informações:

   * **Objetivos**: Os objetivos específicos.
   * **Como fazê-lo**: Um esboço das tarefas específicas e orientação sobre como completá-las.
   * **Artefactos**: Os resultados e o suporte para os produzir.

## <a name="next-steps"></a>Passos seguintes

Fornecemos caminhadas completas de ponta a ponta que demonstram todos os passos no processo para cenários específicos. O artigo [exemplo walkthroughs](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. As caminhadas ilustram como combinar ferramentas de nuvem, ferramentas no local e serviços em um fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com Azure Machine Learning](./index.yml).