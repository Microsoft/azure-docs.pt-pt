---
title: Aquisição e compreensão de dados do Processo de Ciência de Dados da Equipa
description: Os objetivos, tarefas e resultados para a fase de aquisição e compreensão de dados dos seus projetos de ciência de dados
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720508"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Fase de aquisição e compreensão de dados do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e resultados associados à fase de aquisição e compreensão de dados do Processo de Ciência de Dados da Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida da TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Produzir um conjunto de dados limpo e de alta qualidade cuja relação com as variáveis-alvo é compreendida. Localize o conjunto de dados no ambiente de análise apropriado para que esteja pronto para ser modelo.
* Desenvolver uma arquitetura de solução do pipeline de dados que atualiza e pontua os dados regularmente.

## <a name="how-to-do-it"></a>Como fazê-lo
Há três tarefas principais abordadas nesta fase:

   * **Ingerir os dados** no ambiente analítico alvo.
   * **Explore os dados** para determinar se a qualidade dos dados é adequada para responder à pergunta. 
   * **Criar um pipeline de dados** para obter dados novos ou regularmente atualizados.

### <a name="ingest-the-data"></a>Ingestão dos dados
Configurar o processo para mover os dados dos locais de origem para os locais-alvo onde executa operações de análise, como treino e previsões. Para detalhes técnicos e opções sobre como mover os dados com vários serviços de dados do Azure, consulte os [dados de carga em ambientes](ingest-data.md)de armazenamento para análise. 

### <a name="explore-the-data"></a>Explorar os dados
Antes de treinar os seus modelos, precisa de desenvolver uma boa compreensão dos dados. Os conjuntos de dados do mundo real são muitas vezes barulhentos, faltam valores ou têm uma série de outras discrepâncias. Pode utilizar a resumição e visualização de dados para auditar a qualidade dos seus dados e fornecer a informação necessária para processar os dados antes de estar pronto para modelar. Este processo é muitas vezes iterativo.

A TDSP fornece um utilitário automatizado, chamado [IDEAR,](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils)para ajudar a visualizar os dados e a preparar relatórios de resumo de dados. Recomendamos que comece com o IDEAR primeiro para explorar os dados para ajudar a desenvolver a compreensão inicial dos dados interativamente sem codificação. Depois pode escrever código personalizado para a exploração e visualização de dados. Para obter orientações sobre a limpeza dos dados, consulte [Tarefas para preparar dados para uma aprendizagem automática melhorada](prepare-data.md).  

Depois de estar satisfeito com a qualidade dos dados limpos, o próximo passo é compreender melhor os padrões inerentes aos dados. Esta análise de dados ajuda-o a escolher e desenvolver um modelo preditivo adequado para o seu alvo. Procure provas de como os dados estão bem ligados ao alvo. Em seguida, determine se existem dados suficientes para avançar com os próximos passos de modelação. Mais uma vez, este processo é muitas vezes iterativo. Poderá ser necessário encontrar novas fontes de dados com dados mais precisos ou mais relevantes para aumentar o conjunto de dados inicialmente identificado na fase anterior. 

### <a name="set-up-a-data-pipeline"></a>Criar um pipeline de dados
Além da ingestão e limpeza inicial dos dados, normalmente é necessário configurar um processo para marcar novos dados ou atualizar os dados regularmente como parte de um processo de aprendizagem em curso. A pontuação pode ser concluída com um pipeline de dados ou fluxo de trabalho. Os [dados de Move de uma instância do Servidor SQL no local para a Base de Dados Azure SQL com](move-sql-azure-adf.md) artigo da Azure Data Factory dá um exemplo de como configurar um pipeline com a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Nesta fase, desenvolve-se uma arquitetura de soluções do pipeline de dados. Desenvolve-se o oleoduto em paralelo com a próxima fase do projeto de ciência de dados. Dependendo das necessidades do seu negócio e dos constrangimentos dos seus sistemas existentes nos quais esta solução está a ser integrada, o pipeline pode ser uma das seguintes opções: 

   * Baseado em lote
   * Streaming ou tempo real 
   * Um híbrido 

## <a name="artifacts"></a>Artefactos
Seguem-se as entregas nesta fase:

   * [Relatório de qualidade](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md)de dados : Este relatório inclui resumos de dados, as relações entre cada atributo e alvo, classificação variável, e muito mais. A ferramenta [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fornecida como parte do TDSP pode rapidamente gerar este relatório em qualquer conjunto de dados tabular, como um ficheiro CSV ou uma tabela relacional. 
   * Arquitetura de **solução**: A arquitetura de solução pode ser um diagrama ou descrição do seu pipeline de dados que utiliza para executar pontuações ou previsões em novos dados depois de ter construído um modelo. Também contém o pipeline para retreinar o seu modelo com base em novos dados. Guarde o documento no diretório do [Projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quando utilizar o modelo de estrutura de diretório TDSP.
   * **Decisão do checkpoint**: Antes de iniciar a engenharia de funcionalidades completas e a construção de modelos, pode reavaliar o projeto para determinar se o valor esperado é suficiente para continuar a persegui-lo. Pode, por exemplo, estar pronto para prosseguir, precisar de recolher mais dados, ou abandonar o projeto, uma vez que os dados não existem para responder à pergunta.

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 

Por exemplo, como executar passos em TDSPs que usam o Estúdio de Aprendizagem automática Azure, consulte [Utilize o TDSP com aprendizagem automática azure](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
