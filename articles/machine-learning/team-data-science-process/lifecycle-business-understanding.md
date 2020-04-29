---
title: Compreensão de negócios no Processo de Ciência de Dados de Equipa
description: Os objetivos, tarefas e resultados para a fase de compreensão do negócio dos seus projetos de ciência de dados no Processo de Ciência de Dados da Equipa.
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
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76710331"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>A fase de compreensão do negócio do ciclo de vida do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e resultados associados à fase de compreensão do negócio do Processo de Ciência de Dados da Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais etapas que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida da TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Especifique as variáveis-chave que devem servir como alvos do modelo e cujas métricas relacionadas são usadas determinam o sucesso do projeto.
* Identifique as fontes de dados relevantes a que a empresa tem acesso ou precisa de obter.

## <a name="how-to-do-it"></a>Como fazê-lo
Há duas tarefas principais abordadas nesta fase: 

   * **Definir objetivos**: Trabalhar com o seu cliente e outras partes interessadas para compreender e identificar os problemas de negócio. Formular questões que definam os objetivos de negócio que as técnicas de ciência de dados podem visar.
   * **Identificar fontes**de dados : Encontre os dados relevantes que o ajudem a responder às questões que definem os objetivos do projeto.

### <a name="define-objectives"></a>Definir objetivos
1. Um objetivo central deste passo é identificar as variáveis-chave do negócio que a análise precisa de prever. Referimo-nos a estas variáveis como alvos de *modelos,* e usamos as métricas associadas a elas para determinar o sucesso do projeto. Dois exemplos desses alvos são as previsões de vendas ou a probabilidade de uma encomenda ser fraudulenta.

2. Defina os objetivos do projeto perguntando e refinando questões "afiadas" que sejam relevantes, específicas e inequívocas. A ciência dos dados é um processo que usa nomes e números para responder a tais perguntas. Normalmente, utiliza ciência de dados ou aprendizagem automática para responder a cinco tipos de perguntas:
 
   * Quanto ou quantos? (regressão)
   * Qual categoria? (classificação)
   * Que grupo? (agrupamento)
   * Isto é estranho? (deteção de anomalias)
   * Que opção deve ser tomada? (recomendação)

   Determina qual destas perguntas está a fazer e como responder alcança os seus objetivos de negócio.

3. Defina a equipa do projeto especificando as funções e responsabilidades dos seus membros. Desenvolva um plano de marco de alto nível que itera à medida que descobre mais informações. 

4. Defina as métricas de sucesso. Por exemplo, talvez queira alcançar uma previsão do cliente. Precisa de uma taxa de precisão de "x" por cento até ao final deste projeto de três meses. Com estes dados, pode oferecer promoções ao cliente para reduzir o churn. As métricas devem ser **SMART:** 

   * **S**pecífico 
   * **M**comensurável
   * **Um**cinzeável 
   * **R**elevant 
   * **T**ime-bound 

### <a name="identify-data-sources"></a>Identificar as origens de dados
Identifique fontes de dados que contenham exemplos conhecidos de respostas às suas perguntas agudas. Procure os seguintes dados:

* Dados relevantes para a questão. Possui medidas do alvo e características relacionadas com o alvo?
* Dados que são uma medida precisa do seu alvo modelo e as características de interesse.

Por exemplo, pode descobrir que os sistemas existentes precisam de recolher e registar tipos adicionais de dados para resolver o problema e alcançar os objetivos do projeto. Nesta situação, é melhor procurar fontes de dados externas ou atualizar os seus sistemas para recolher novos dados.

## <a name="artifacts"></a>Artefactos
Aqui estão os resultados nesta fase:

   * [Documento charter](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Um modelo padrão é fornecido na definição de estrutura do projeto TDSP. O documento da carta é um documento vivo. Atualiza o modelo ao longo do projeto à medida que faz novas descobertas e à medida que os requisitos do negócio mudam. A chave é iterar sobre este documento, adicionando mais detalhes, à medida que progride através do processo de descoberta. Mantenha o cliente e outras partes interessadas envolvidos na eificação das alterações e comunique claramente as razões das alterações.  
   * [Fontes](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources)de dados : A secção de fontes de **dados brutos** do relatório de **definições** de dados que é encontrada na pasta de relatório de **dados** do projeto TDSP contém as fontes de dados. Esta secção especifica os locais originais e de destino para os dados brutos. Em fases posteriores, preenche detalhes adicionais como os scripts para mover os dados para o seu ambiente analítico.  
   * [Dicionários](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries)de dados : Este documento fornece descrições dos dados fornecidos pelo cliente. Estas descrições incluem informações sobre o esquema (os tipos de dados e informações sobre as regras de validação, se houver) e os diagramas de relação com a entidade, se disponíveis.

## <a name="next-steps"></a>Passos seguintes

Aqui estão os links para cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passeios completos que demonstram todos os passos no processo para cenários específicos. O artigo de [walkthroughs Exemplo](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. Os passadiços ilustram como combinar cloud, ferramentas no local e serviços em um fluxo de trabalho ou pipeline para criar uma aplicação inteligente. 
