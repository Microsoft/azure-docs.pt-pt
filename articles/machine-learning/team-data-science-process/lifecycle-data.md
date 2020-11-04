---
title: Aquisição e compreensão de dados da Equipa Data Science Process
description: Os objetivos, tarefas e entregas para a fase de aquisição e compreensão de dados dos seus projetos de ciência de dados
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
ms.openlocfilehash: f7770e929e51ee51b09060e4247c5f92b27d1035
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311878"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Fase de aquisição e compreensão de dados do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e entregas associados à fase de aquisição e compreensão de dados do Processo de Ciência de Dados de Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais fases que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Produzir um conjunto de dados limpo e de alta qualidade cuja relação com as variáveis-alvo é compreendida. Localize o conjunto de dados no ambiente de análise apropriado para que esteja pronto a ser modelar.
* Desenvolver uma arquitetura de solução do pipeline de dados que refresca e pontua os dados regularmente.

## <a name="how-to-do-it"></a>Como fazê-lo
Há três tarefas principais abordadas nesta fase:

   * **Ingerir os dados** no ambiente analítico alvo.
   * **Explore os dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
   * **Crie um pipeline de dados** para obter dados novos ou regularmente atualizados.

### <a name="ingest-the-data"></a>Ingerir os dados
Desloque o processo para mover os dados dos locais de origem para os locais-alvo onde executa operações de análise, como treino e previsões. Para obter detalhes técnicos e opções sobre como mover os dados com vários serviços de dados Azure, consulte [os dados de carga em ambientes de armazenamento para análise](ingest-data.md). 

### <a name="explore-the-data"></a>Explorar os dados
Antes de treinar os seus modelos, precisa de desenvolver uma boa compreensão dos dados. Os conjuntos de dados do mundo real são muitas vezes barulhentos, são valores em falta, ou têm uma série de outras discrepâncias. Pode utilizar a resumo e visualização de dados para auditar a qualidade dos seus dados e fornecer as informações necessárias para processar os dados antes de estar pronto para modelar. Este processo é muitas vezes iterativo.

A TDSP fornece um utilitário automatizado, chamado [IDEAR,](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)para ajudar a visualizar os dados e a preparar relatórios de resumo de dados. Recomendamos que comece com o IDEAR primeiro para explorar os dados para ajudar a desenvolver a compreensão inicial de dados interativamente sem codificação. Em seguida, pode escrever código personalizado para a exploração e visualização de dados. Para obter orientações sobre a limpeza dos [dados, consulte Tarefas para preparar dados para uma aprendizagem melhorada da máquina.](prepare-data.md)  

Depois de estar satisfeito com a qualidade dos dados limpos, o próximo passo é compreender melhor os padrões inerentes aos dados. Esta análise de dados ajuda-o a escolher e desenvolver um modelo preditivo adequado para o seu alvo. Procure provas de como os dados estão bem ligados ao alvo. Em seguida, determine se existem dados suficientes para avançar com os próximos passos de modelação. Mais uma vez, este processo é muitas vezes iterativo. Poderá ser necessário encontrar novas fontes de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Criar um pipeline de dados
Além da ingestão inicial e limpeza dos dados, normalmente é necessário configurar um processo para obter novos dados ou atualizar os dados regularmente como parte de um processo de aprendizagem em curso. A pontuação pode ser completada com um pipeline de dados ou fluxo de trabalho. Os [dados de mudança de uma instância do SQL Server para Azure SQL Database com artigo Azure Data Factory](move-sql-azure-adf.md) dá um exemplo de como configurar um pipeline com a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Nesta fase, desenvolve-se uma arquitetura de solução do pipeline de dados. Desenvolve-se o oleoduto em paralelo com a próxima fase do projeto de ciência de dados. Dependendo das necessidades do seu negócio e dos constrangimentos dos seus sistemas existentes em que esta solução está a ser integrada, o pipeline pode ser uma das seguintes opções: 

   * Baseado em lote
   * Streaming ou em tempo real 
   * Um híbrido 

## <a name="artifacts"></a>Artefactos
Seguem-se os resultados nesta fase:

   * [Relatório de qualidade dos dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Este relatório inclui resumos de dados, as relações entre cada atributo e alvo, classificação variável, e muito mais. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fornecida como parte do TDSP pode gerar rapidamente este relatório em qualquer conjunto de dados tabulares, como um ficheiro CSV ou uma tabela relacional. 
   * **Arquitetura de solução** : A arquitetura da solução pode ser um diagrama ou descrição do seu pipeline de dados que utiliza para executar pontuação ou previsões em novos dados depois de ter construído um modelo. Também contém o pipeline para reconverter o seu modelo com base em novos dados. Guarde o documento no diretório do [Projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando utilizar o modelo de estrutura do diretório TDSP.
   * **Decisão do checkpoint** : Antes de iniciar a engenharia e a construção de modelos completos, pode reavaliar o projeto para determinar se o valor esperado é suficiente para continuar a prossegui-lo. Pode, por exemplo, estar pronto para prosseguir, precisar de recolher mais dados, ou abandonar o projeto, uma vez que os dados não existem para responder à pergunta.

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações a cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passos completos que demonstram todos os passos no processo para cenários específicos. O artigo [exemplo walkthroughs](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. As caminhadas ilustram como combinar ferramentas de nuvem, ferramentas no local e serviços em um fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que utilizam o Azure Machine Learning Studio, consulte [utilizar o TDSP com Azure Machine Learning]().