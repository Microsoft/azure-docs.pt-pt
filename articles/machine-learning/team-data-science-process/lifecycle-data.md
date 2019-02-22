---
title: Aquisição de dados e a compreensão do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a aquisição de dados e a fase de compreensão dos seus projetos de ciência de dados
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d65704f7037eb4152b4f91d4cbe68ceaad2cea48
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650229"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Aquisição de dados e o estágio de compreensão do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados finais associados à aquisição de dados e a fase de compreensão de Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão empresarial**
   2. **Aquisição de dados e compreensão**
   3. **Modelagem**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Produza um conjunto de dados limpo de alta qualidade cuja relação com as variáveis de destino é compreendida. Localize o conjunto de dados no ambiente de análise adequadas para que está pronto para o modelo.
* Desenvolva uma arquitetura de solução do pipeline de dados que é atualizada e pontua os dados regularmente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais abordadas neste estágio:

   * **Incorporar os dados** para o ambiente de análise de destino.
   * **Explorar os dados** para determinar se a qualidade de dados é adequada para responder à pergunta. 
   * **Configure um pipeline de dados** Pontuar novos ou regularmente de dados atualizados.

### <a name="ingest-the-data"></a>Incorporar os dados
Configure o processo para mover os dados a partir de localizações de origem para as localizações de destino em que executa operações de análise, como a formação e previsões. Para obter detalhes técnicos e opções mover os dados com vários serviços de dados do Azure, consulte [carregar dados para ambientes de armazenamento para análise](ingest-data.md). 

### <a name="explore-the-data"></a>Explorar os dados
Antes de treinar seus modelos, tem de desenvolver uma compreensão som dos dados. Conjuntos de dados do mundo real, muitas vezes, são "ruidosas", estão em falta valores ou tem uma série de outras discrepâncias. Pode utilizar o resumo de dados e visualização para a qualidade dos seus dados de auditoria e fornecer as informações necessárias processar os dados antes de ele está pronto para modelagem. Muitas vezes, é um processo iterativo.

TDSP fornece um utilitário automatizado, chamado [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), para ajudar a visualizar os dados e preparar os relatórios de resumo de dados. Recomendamos que comece com IDEAR primeiro para explorar os dados para ajudar a desenvolver a compreensão de dados inicial interativamente com nenhuma codificação. Em seguida, pode escrever código personalizado para exploração de dados e visualização. Para obter orientações sobre a limpeza de dados, consulte [tarefas para preparar dados para avançada do machine learning](prepare-data.md).  

Depois que estiver satisfeito com a qualidade dos dados limpos, a próxima etapa é compreender melhor os padrões inerentes aos dados. Isso ajuda a escolher e desenvolver um modelo preditivo apropriado para seu destino. Vista de olhos para evidências para saber como bem ligado os dados é o destino. Em seguida, determine se há dados suficientes para seguir em frente com os passos seguintes de modelagem. Novamente, esse processo geralmente é iterativo. Poderá ter de encontrar novas origens de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Configure um pipeline de dados
Para além da ingestão inicial e a limpeza dos dados, normalmente, tem de configurar um processo para classificar dados novos ou atualizar os dados regularmente como parte de um processo de aprendizado em curso. Pode fazê-lo ao configurar um pipeline de dados ou o fluxo de trabalho. O [mover dados de uma instância do SQL Server no local para a base de dados do Azure SQL com o Azure Data Factory](move-sql-azure-adf.md) artigo apresenta um exemplo de como configurar um pipeline com [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Nesta fase, vai desenvolver uma arquitetura de solução do pipeline de dados. Desenvolva o pipeline em paralelo com a próxima fase do projeto de ciência de dados. Dependendo das necessidades da sua empresa e as restrições de seus sistemas existentes no qual está a ser integrada esta solução, o pipeline pode ser um dos seguintes: 

   * Com base no batch
   * Transmissão em fluxo ou em tempo real 
   * Um híbrido 

## <a name="artifacts"></a>Artefactos
Seguem-se os resultados finais neste estágio:

   * [Relatório de qualidade de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Este relatório inclui resumos de dados, as relações entre cada atributo e de destino, classificação de variável e muito mais. O [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) ferramenta fornecida como parte do TDSP rapidamente pode gerar este relatório em qualquer conjunto de dados em tabela, como um ficheiro CSV ou uma tabela relacional. 
   * **Arquitetura da solução**: A arquitetura da solução pode ser um diagrama ou a descrição do seu pipeline de dados que utiliza para executar a classificação ou as previsões de indisponibilidade de novos dados depois de criar um modelo. Também contém o pipeline para voltar a preparar seu modelo com base nos dados de novo. Store o documento no [projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) diretório quando utilizar o modelo de estrutura de diretório do TDSP.
   * **Decisão de ponto de verificação**: Antes de iniciar a engenharia de funcionalidades completo e criação de modelo, pode reavaliar o projeto para determinar se o valor esperado é suficiente para continuar a pursing-lo. Pode, por exemplo, estar pronto para continuar, tem de recolher mais dados, ou abandonar o projeto, como os dados não existem para responder à pergunta.

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão empresarial](lifecycle-business-understanding.md)
   2. [Aquisição de dados e compreensão](lifecycle-data.md)
   3. [Modelagem](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos orientações passo a passo de completa-a-ponto, que demonstram todas as etapas do processo para cenários específicos. O [instruções passo a passo do exemplo](walkthroughs.md) artigo fornece uma lista dos cenários com links e descrições em miniatura. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Para obter exemplos de como executar os passos no TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
