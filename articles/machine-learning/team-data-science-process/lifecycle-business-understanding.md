---
title: Compreensão do negócio no processo de ciência de dados de equipe
description: Os objetivos, tarefas e entregas para a fase de compreensão do negócio dos seus projetos de ciência de dados no Processo de Ciência de Dados de Equipa.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76710331"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>A fase de compreensão do negócio do ciclo de vida do Processo de Ciência de Dados da Equipa

Este artigo descreve os objetivos, tarefas e entregas associados à fase de compreensão do negócio do Processo de Ciência de Dados de Equipa (TDSP). Este processo fornece um ciclo de vida recomendado que pode usar para estruturar os seus projetos de ciência de dados. O ciclo de vida descreve as principais fases que os projetos normalmente executam, muitas vezes iterativamente:

   1. **Noções sobre empresas**
   2. **Aquisição e compreensão de dados**
   3. **Modelação**
   4. **Implementação**
   5. **Aceitação do cliente**

Aqui está uma representação visual do ciclo de vida TDSP: 

![Ciclo de vida TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Objetivos
* Especificar as variáveis-chave que devem servir como alvos do modelo e cujas métricas relacionadas são utilizadas determinam o sucesso do projeto.
* Identificar as fontes de dados relevantes a que a empresa tem acesso ou precisa de obter.

## <a name="how-to-do-it"></a>Como fazê-lo
Há duas tarefas principais abordadas nesta fase: 

   * **Definir objetivos**: Trabalhe com o seu cliente e outras partes interessadas para compreender e identificar os problemas de negócio. Formular questões que definam os objetivos de negócio que as técnicas de ciência de dados podem visar.
   * **Identificar fontes de dados**: Encontre os dados relevantes que o ajudam a responder às questões que definem os objetivos do projeto.

### <a name="define-objectives"></a>Definir objetivos
1. Um objetivo central deste passo é identificar as principais variáveis de negócio que a análise precisa de prever. Referimo-nos a estas variáveis como alvos do *modelo,* e utilizamos as métricas associadas a elas para determinar o sucesso do projeto. Dois exemplos desses objetivos são as previsões de vendas ou a probabilidade de uma encomenda ser fraudulenta.

2. Defina os objetivos do projeto pedindo e refinando questões "afiadas" que sejam relevantes, específicas e inequívocas. A ciência dos dados é um processo que usa nomes e números para responder a tais questões. Normalmente, utiliza-se a ciência dos dados ou a aprendizagem automática para responder a cinco tipos de perguntas:
 
   * Quanto ou quantos? (regressão)
   * Qual categoria? (classificação)
   * Que grupo? (agrupamento)
   * Isto é estranho? (deteção de anomalias)
   * Que opção deve ser tomada? (recomendação)

   Determine qual destas perguntas está a fazer e como responder a ela atinge os seus objetivos de negócio.

3. Definir a equipa do projeto especificando as funções e responsabilidades dos seus membros. Desenvolva um plano de marco de alto nível que itere à medida que descobre mais informações. 

4. Defina as métricas de sucesso. Por exemplo, é melhor conseguir uma previsão de churn de clientes. Precisa de uma taxa de precisão de "x" por cento até ao final deste projeto de três meses. Com estes dados, pode oferecer promoções ao cliente para reduzir o impacto. As métricas devem ser **SMART:** 

   * **Pecific S** 
   * **M**easurável
   * **Um**chievable 
   * **R**elevant 
   * **T**ime-bound 

### <a name="identify-data-sources"></a>Identificar as origens de dados
Identifique fontes de dados que contenham exemplos conhecidos de respostas às suas perguntas afiadas. Procure os seguintes dados:

* Dados relevantes para a pergunta. Tem medidas do alvo e características relacionadas com o alvo?
* Dados que são uma medida precisa do seu alvo de modelo e as características de interesse.

Por exemplo, pode descobrir que os sistemas existentes precisam de recolher e registar tipos adicionais de dados para resolver o problema e atingir os objetivos do projeto. Nesta situação, poderá querer procurar fontes de dados externas ou atualizar os seus sistemas para recolher novos dados.

## <a name="artifacts"></a>Artefactos
Aqui estão os resultados nesta fase:

   * [Documento charter](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): Um modelo padrão é fornecido na definição da estrutura do projeto TDSP. O documento charter é um documento vivo. Atualiza o modelo ao longo do projeto à medida que faz novas descobertas e à medida que os requisitos do negócio mudam. A chave é iterar sobre este documento, adicionando mais detalhes, à medida que progride através do processo de descoberta. Mantenha o cliente e outras partes interessadas envolvidas na realização das alterações e comunique claramente as razões das alterações às alterações.  
   * [Fontes de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): A secção de fontes de **dados brutos** do relatório **de definições** de dados encontrado na pasta de **relatório de dados** do projeto TDSP contém as fontes de dados. Esta secção especifica os locais originais e de destino para os dados brutos. Em fases posteriores, preenche detalhes adicionais como os scripts para mover os dados para o seu ambiente analítico.  
   * [Dicionários de dados](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Este documento fornece descrições dos dados fornecidos pelo cliente. Estas descrições incluem informações sobre o esquema (os tipos de dados e informações sobre as regras de validação, se houver) e os diagramas de relação entidade, se disponível.

## <a name="next-steps"></a>Passos seguintes

Aqui estão as ligações a cada passo no ciclo de vida do TDSP:

   1. [Noções sobre empresas](lifecycle-business-understanding.md)
   2. [Aquisição e compreensão de dados](lifecycle-data.md)
   3. [Modelação](lifecycle-modeling.md)
   4. [Implementação](lifecycle-deployment.md)
   5. [Aceitação do cliente](lifecycle-acceptance.md)

Fornecemos passos completos que demonstram todos os passos no processo para cenários específicos. O artigo [exemplo walkthroughs](walkthroughs.md) fornece uma lista dos cenários com links e descrições de miniaturas. As caminhadas ilustram como combinar ferramentas de nuvem, ferramentas no local e serviços em um fluxo de trabalho ou oleoduto para criar uma aplicação inteligente. 
