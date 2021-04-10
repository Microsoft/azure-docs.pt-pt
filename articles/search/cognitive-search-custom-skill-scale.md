---
title: Escalar e gerir habilidades personalizadas
titleSuffix: Azure Cognitive Search
description: Aprenda as ferramentas e técnicas para aumentar eficientemente uma habilidade personalizada para obter o máximo rendimento. As habilidades personalizadas invocam modelos ou lógica de IA personalizados que pode adicionar a um pipeline de indexação enriquecido em IA na Azure Cognitive Search.
manager: luisca
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 4fdc222fa20aef6639bf6d5d485f7dcf6b6ca535
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641137"
---
# <a name="efficiently-scale-out-a-custom-skill"></a>Dimensione eficientemente uma habilidade personalizada

As habilidades personalizadas são APIs web que implementam uma interface específica. Uma habilidade personalizada pode ser implementada em qualquer recurso publicamente endereçado. As implementações mais comuns para competências personalizadas são:
* Funções Azure para habilidades lógicas personalizadas
* Azure Webapps para habilidades de IA simples em contentores
* Serviço Azure Kubernetes para habilidades mais complexas ou maiores.

## <a name="prerequisites"></a>Pré-requisitos

+ Reveja a [interface de habilidade personalizada](cognitive-search-custom-skill-interface.md) para uma introdução na interface entrada/saída que uma habilidade personalizada deve implementar.

+ Crie o seu ambiente. Pode começar com [este tutorial de ponta a ponta](/azure/azure-functions/create-first-function-vs-code-python) para configurar a Função Azure sem servidor usando extensões visual Studio Code e Python.

## <a name="skillset-configuration"></a>Configuração de Skillset

Configurar uma habilidade personalizada para maximizar a produção do processo de indexação requer uma compreensão da habilidade, configurações indexantes e como a habilidade se relaciona com cada documento. Por exemplo, o número de vezes que uma habilidade é invocada por documento e a duração esperada por invocação.

### <a name="skill-settings"></a>Definições de habilidades

Na [habilidade personalizada](cognitive-search-custom-skill-web-api.md) definir os seguintes parâmetros.

1. Conjunto `batchSize` da habilidade personalizada para configurar o número de registos enviados para a habilidade numa única invocação da habilidade.

2. Desentei o `degreeOfParallelism` para calibrar o número de pedidos simultâneos que o indexante fará à sua habilidade.

3. Definir `timeout` um valor suficiente para a habilidade responder com uma resposta válida.

4. Na `indexer` definição, definido [`batchSize`](/rest/api/searchservice/create-indexer#indexer-parameters) para o número de documentos que devem ser lidos a partir da fonte de dados e enriquecidos simultaneamente.

### <a name="considerations"></a>Considerações

Definir estas variáveis para otimizar o desempenho dos indexantes requer determinar se a sua habilidade funciona melhor com muitos pedidos simultâneos ou menos grandes pedidos. Algumas perguntas a considerar são:

* Qual é a cardinalícia da invocação de habilidades? A habilidade executa uma vez para cada documento, por exemplo, uma habilidade de classificação de documento, ou poderia a habilidade executar várias vezes por documento, uma habilidade de classificação de parágrafo?

* Em média, quantos documentos são lidos da fonte de dados para preencher um pedido de competência baseado no tamanho do lote de habilidades? Idealmente, este deve ser inferior ao tamanho do lote indexante. Com tamanhos de lote superiores a 1, a sua habilidade pode receber registos de vários documentos de origem. Por exemplo, se a contagem de lote de indexante for 5 e a contagem de lotes de habilidades for de 50 e cada documento gerar apenas cinco registos, o indexante terá de preencher um pedido de habilidade personalizado em vários lotes indexantes.

* O número médio de pedidos que um lote indexante pode gerar deve dar-lhe uma configuração ideal para os graus de paralelismo. Se a sua infraestrutura de alojamento não suporta esse nível de concordância, considere reduzir os graus de paralelismo. Como melhor prática, teste a sua configuração com alguns documentos para validar as suas escolhas nos parâmetros.

* Testar com uma amostra menor de documentos, avaliar o tempo de execução da sua habilidade para o tempo total necessário para processar o subconjunto de documentos. O seu indexante passa mais tempo a construir um lote ou à espera de uma resposta da sua habilidade? 

* Considere as implicações a montante do paralelismo. Se a entrada para uma habilidade personalizada é uma saída de uma habilidade anterior, todas as habilidades no skillset são escalonada eficazmente para minimizar a latência?

## <a name="error-handling-in-the-custom-skill"></a>Manipulação de erros na habilidade personalizada

As habilidades personalizadas devem devolver um código de estado de sucesso HTTP 200 quando a habilidade estiver concluída com sucesso. Se um ou mais registos num lote resultarem em erros, considere devolver o código multi-estado 207. A lista de erros ou avisos para o registo deve conter a mensagem apropriada.

Quaisquer itens num lote que erros resultarão na falha do documento correspondente. Se precisar do documento para ter sucesso, devolva um aviso.

Qualquer código de estado sobre 299 é avaliado como um erro e todos os enriquecimentos são falhados resultando num documento falhado. 

### <a name="common-error-messages"></a>Mensagens de erro comuns

* `Could not execute skill because it did not execute within the time limit '00:00:30'. This is likely transient. Please try again later. For custom skills, consider increasing the 'timeout' parameter on your skill in the skillset.` Desaperte o parâmetro de tempo limite na habilidade para permitir uma duração de execução mais longa.

* `Could not execute skill because Web Api skill response is invalid.` Indicativo da habilidade de não devolver uma mensagem no formato de resposta de habilidade personalizada. Isto pode ser o resultado de uma exceção não conseguida na habilidade.

* `Could not execute skill because the Web Api request failed.` Provavelmente causado por erros de autorização ou exceções.

* `Could not execute skill.` Geralmente o resultado da resposta de habilidade sendo mapeado para uma propriedade existente na hierarquia do documento.

## <a name="testing-custom-skills"></a>Testar habilidades personalizadas

Comece por testar a sua habilidade personalizada com um cliente REST API para validar:

* A habilidade implementa a interface de habilidades personalizada para pedidos e respostas

* A habilidade devolve JSON válido com o `application/JSON` tipo MIME

* Devolve um código de estado HTTP válido

Crie uma [sessão de depuração](cognitive-search-debug-session.md) para adicionar a sua habilidade ao skillset e certifique-se de que produz um enriquecimento válido. Embora uma sessão de depurar não lhe permita afinar o desempenho da habilidade, permite-lhe garantir que a habilidade está configurada com valores válidos e devolve os objetos enriquecidos esperados.

## <a name="best-practices"></a>Melhores práticas

* Embora as competências possam aceitar e devolver cargas maiores, considere limitar a resposta a 150 MB ou menos ao devolver o JSON.

* Considere definir o tamanho do lote no indexante e habilidade para garantir que cada lote de fonte de dados gere uma carga útil completa para a sua habilidade.

* Para tarefas de longo prazo, desaprova o tempo limite para um valor suficientemente elevado para garantir que o indexante não se errar quando processa documentos simultaneamente.

* Otimize o tamanho do lote indexante, o tamanho do lote de habilidades e os graus de habilidade do paralelismo para gerar o padrão de carga que a sua habilidade espera, menos pedidos grandes ou muitos pedidos pequenos.

* Monitorize habilidades personalizadas com registos detalhados de falhas, pois pode ter cenários em que pedidos específicos falham consistentemente como resultado da variabilidade dos dados.


## <a name="next-steps"></a>Passos seguintes
Parabéns! A sua habilidade personalizada é agora dimensionada para maximizar a produção no indexante. 

+ [Power Skills: um repositório de habilidades personalizadas](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Adicione uma habilidade personalizada a um oleoduto de enriquecimento de IA](cognitive-search-custom-skill-interface.md)
+ [Adicione uma habilidade de aprendizagem automática Azure](./cognitive-search-aml-skill.md)
+ [Use sessões de depurar para testar alterações](./cognitive-search-debug-session.md)