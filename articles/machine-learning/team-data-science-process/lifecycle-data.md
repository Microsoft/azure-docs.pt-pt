---
title: Aquisição de dados e a compreensão do processo de ciência de dados de equipa
description: As metas, tarefas e resultados finais para a aquisição de dados e a fase de compreensão dos seus projetos de ciência de dados
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
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720508"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Aquisição de dados e o estágio de compreensão do processo de ciência de dados de equipa

Este artigo descreve os objetivos, tarefas e resultados finais associados à aquisição de dados e a fase de compreensão de Team Data Science Process (TDSP). Este processo fornece um ciclo de vida recomendado que pode utilizar para estruturar os seus projetos de ciência de dados. O ciclo de vida destaca as fases principais projetos normalmente executadas, muitas vezes iterativamente:

   1. **Compreensão de negócios**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Esta é uma representação visual de ciclo de vida do TDSP: 

![Ciclo de vida do TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Produza um conjunto de dados limpo de alta qualidade cuja relação com as variáveis de destino é compreendida. Localize o conjunto de dados no ambiente de análise adequadas para que está pronto para o modelo.
* Desenvolva uma arquitetura de solução do pipeline de dados que é atualizada e pontua os dados regularmente.

## <a name="how-to-do-it"></a>Como fazê-lo
Existem três tarefas principais abordadas neste estágio:

   * **Ingerir os dados** no ambiente analítico alvo.
   * **Explore os dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
   * **Criar um pipeline de dados** para obter dados novos ou regularmente atualizados.

### <a name="ingest-the-data"></a>Incorporar os dados
Configure o processo para mover os dados a partir de localizações de origem para as localizações de destino em que executa operações de análise, como a formação e previsões. Para detalhes técnicos e opções sobre como mover os dados com vários serviços de dados do Azure, consulte os [dados de carga em ambientes](ingest-data.md)de armazenamento para análise. 

### <a name="explore-the-data"></a>Explorar os dados
Antes de treinar seus modelos, tem de desenvolver uma compreensão som dos dados. Conjuntos de dados do mundo real, muitas vezes, são "ruidosas", estão em falta valores ou tem uma série de outras discrepâncias. Pode utilizar o resumo de dados e visualização para a qualidade dos seus dados de auditoria e fornecer as informações necessárias processar os dados antes de ele está pronto para modelagem. Muitas vezes, é um processo iterativo.

A TDSP fornece um utilitário automatizado, chamado [IDEAR,](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)para ajudar a visualizar os dados e a preparar relatórios de resumo de dados. Recomendamos que comece com IDEAR primeiro para explorar os dados para ajudar a desenvolver a compreensão de dados inicial interativamente com nenhuma codificação. Em seguida, pode escrever código personalizado para exploração de dados e visualização. Para obter orientações sobre a limpeza dos dados, consulte [Tarefas para preparar dados para uma aprendizagem automática melhorada](prepare-data.md).  

Depois que estiver satisfeito com a qualidade dos dados limpos, a próxima etapa é compreender melhor os padrões inerentes aos dados. Esta análise de dados ajuda-o a escolher e desenvolver um modelo preditivo adequado para o seu alvo. Vista de olhos para evidências para saber como bem ligado os dados é o destino. Em seguida, determine se há dados suficientes para seguir em frente com os passos seguintes de modelagem. Novamente, esse processo geralmente é iterativo. Poderá ter de encontrar novas origens de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Configure um pipeline de dados
Para além da ingestão inicial e a limpeza dos dados, normalmente, tem de configurar um processo para classificar dados novos ou atualizar os dados regularmente como parte de um processo de aprendizado em curso. A pontuação pode ser concluída com um pipeline de dados ou fluxo de trabalho. Os [dados de Move de uma instância do Servidor SQL no local para a Base de Dados Azure SQL com](move-sql-azure-adf.md) artigo da Azure Data Factory dá um exemplo de como configurar um pipeline com a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Nesta fase, vai desenvolver uma arquitetura de solução do pipeline de dados. Desenvolva o pipeline em paralelo com a próxima fase do projeto de ciência de dados. Dependendo das necessidades do seu negócio e dos constrangimentos dos seus sistemas existentes nos quais esta solução está a ser integrada, o pipeline pode ser uma das seguintes opções: 

   * Com base no batch
   * Transmissão em fluxo ou em tempo real 
   * Um híbrido 

## <a name="artifacts"></a>Artefactos
Seguem-se os resultados finais neste estágio:

   * [Relatório de qualidade](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md)de dados : Este relatório inclui resumos de dados, as relações entre cada atributo e alvo, classificação variável, e muito mais. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fornecida como parte do TDSP pode rapidamente gerar este relatório em qualquer conjunto de dados tabular, como um ficheiro CSV ou uma tabela relacional. 
   * Arquitetura de **solução**: A arquitetura de solução pode ser um diagrama ou descrição do seu pipeline de dados que utiliza para executar pontuações ou previsões em novos dados depois de ter construído um modelo. Também contém o pipeline para voltar a preparar seu modelo com base nos dados de novo. Guarde o documento no diretório do [Projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando utilizar o modelo de estrutura de diretório TDSP.
   * **Decisão do checkpoint**: Antes de iniciar a engenharia de funcionalidades completas e a construção de modelos, pode reavaliar o projeto para determinar se o valor esperado é suficiente para continuar a persegui-lo. Pode, por exemplo, estar pronto para continuar, tem de recolher mais dados, ou abandonar o projeto, como os dados não existem para responder à pergunta.

## <a name="next-steps"></a>Passos Seguintes

Seguem-se ligações para cada etapa do ciclo de vida do TDSP:

   1. [Compreensão de negócios](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. A instruções passo a passo mostram como combinar a cloud, ferramentas no local e serviços num fluxo de trabalho ou um pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
