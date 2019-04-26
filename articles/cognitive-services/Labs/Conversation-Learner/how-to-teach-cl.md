---
title: Como apresentar com Aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Aprenda a ensinar com Aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322894"
---
# <a name="how-to-teach-with-conversation-learner"></a>Como ensinar com o Conversation Learner 

Este documento explica o que sinaliza Aprendiz de conversação está ciente e descreve como aprende.  

Ensino pode ser decomposto em duas etapas distintas: extração de entidades e seleção de ação.

## <a name="entity-extraction"></a>Extração de entidades

Nos bastidores, o aprendiz de conversação usa [LUIS](https://www.luis.ai) para extração de entidades.  Se estiver familiarizado com os LUIS, o que ocorrer se aplica a extração de entidades no Aprendiz de conversação.

Os modelos de extração de entidades estão atento a *conteúdo* e *contexto* dentro de uma expressão de utilizador.  Por exemplo, se a palavra "Seattle" foi intitulada como uma cidade numa expressão, como "Qual é o clima em Seattle", extração de entidades é capaz de reconhecer os mesmos conteúdos ("Seattle") como uma cidade na outra expressão, como "A população de Seattle", mesmo que o expressões com são muito diferentes.  Por outro lado, se tiver de "Francis" foi reconhecido como um nome de "Programação de uma reunião com Francis", em seguida, um novo nome que não foram visto anteriormente pode ser reconhecido em contexto semelhante, como "Configurar uma reunião com Round Robin".  Aprendizagem infere quando assistir ao conteúdo, o contexto ou ambos, com base nos exemplos de treinamento.

No momento, a extração de entidades apenas está ciente do conteúdo da expressão atual.  Ao contrário de seleção de ação (abaixo), não está ciente do histórico de caixa de diálogo como o folheio de sistema anterior, anterior de utilizador de folheio ou entidades anteriormente reconhecidas.  Como resultado, o comportamento de extração de entidades é "partilhado" em todas as expressões de com.  Por exemplo, se a expressão de usuário "Quero Apple" tem "Apple" identificada como "Frutos" de tipo de entidade numa expressão de utilizador, o modelo de extração de entidades irá esperar que os "Apple" identificada como "Frutos" deve ter sempre esta expressão ("quero Apple").

Se a extração de entidades não está se comportando como esperado, aqui estão as soluções possíveis:

- A primeira coisa a experimentar consiste em adicionar mais exemplos de treinamento – especialmente os exemplos que revelam o contexto de entidade típica (que envolvem palavras) ou exceções
- Considere adicionar uma propriedade de "Entidade esperado" para uma ação, se apropriado.  Consulte o tutorial sobre entidades esperado para obter mais detalhes.
- Embora seja possível adicionar o processamento manual para `EntityExtractionCallback` para extrair entidades com o código, esta é a abordagem recomendada pelo menos, uma vez que ele não se beneficiarão das melhorias no machine learning como o sistema evolui.

## <a name="action-selection"></a>Selecção da acção

Seleção de ação utiliza uma rede neural recorrente, que assume como entrada todas o histórico de conversações.  Assim, seleção de ação é um processo com monitoração de estado para o reconhecimento de expressões de usuário anterior, os valores de entidade e expressões com do sistema.  

Alguns sinais naturalmente preferidas pelo processo de aprendizado.  Em outras palavras, se Aprendiz de conversação pode explicar uma decisão de seleção de ação com mais de "preferenciais" sinais, ele será; Se não for possível, em seguida, ele usará menos "preferenciais" sinais.

Aqui está uma tabela que mostra todos os sinais de aprendiz de conversação e quais são utilizados pela seleção de ação.  Tenha em atenção que a ordem do word em expressões de utilizador é ignorada.

Sinal | Preferência de (1 = das preferências) | Notas
--- | --- | --- 
Ação do sistema, por sua vez anterior | 1 | 
Entidades apresentam, por sua vez atual | 1 | 
Se esta é a primeira vez | 1 |
Correspondência exata de palavras na expressão do utilizador atual | 2 | 
Palavras de significado é semelhante na expressão do utilizador atual | 3 | 
Ações de sistema anterior antes de ativar | 4 |
Entidades apresentam revezando antes de ativar atual | 4 | 
Expressões de utilizador antes de ativar atual | 5 | 

> [!NOTE]
> Seleção de ação não tem o conteúdo das ações de sistema – o texto, conteúdo do cartão, ou o nome da API ou comportamento – apenas a identidade da ação de sistema.  Como resultado, a alterar o conteúdo de uma ação não irá alterar o comportamento do modelo de seleção de ação.
>
> Além disso, que o conteúdo/valores de entidades não servem – apenas sua presença/ausência.

Se a seleção de ação não está se comportando como esperado, aqui estão as soluções possíveis:

- Adicione mais train caixas de diálogo, particularmente caixas de diálogo que ilustram os sinais de seleção de ação deve ser prestando atenção a.  Por exemplo, se a seleção de ação deve preferir um sinal em vez de outro, dê exemplos que mostram o sinal preferencial a ser no mesmo Estado e os outros sinais variados.  Algumas sequências podem demorar um punhado de caixas de diálogo de treinamento para saber mais.
- Adicione "Necessária" e "Disqualifying" entidades às definições de ação.  Este limites que quando ações estão disponíveis e podem ser úteis para regras de negócio express e alguns padrões de senso comum. 

## <a name="updates-to-models"></a>Atualizações para modelos

Sempre que adicionar ou editar uma entidade, ação ou train caixa de diálogo da interface do Usuário, isso gera um pedido para voltar treinar o modelo de extração de entidades e o modelo de seleção de ação.  Este pedido é colocado numa fila e, novamente treinamento é feito de forma assíncrona.  Quando um novo modelo está disponível, é utilizado partir desse ponto em diante para seleção de extração e a ação de entidade.  Este processo de preparação novamente, muitas vezes, demora cerca de 5 segundos, mas pode ser superior se o modelo é complexo, ou se a carga sobre o serviço de treinamento é elevada.

Porque o treinamento é feito de forma assíncrona, é possível que as edições efetuadas não são refletidas imediatamente.  Se a seleção de extração ou ação de entidade não está se comportando como esperado com base nas alterações que foram feitos nos últimos 5 a 10 segundos, isso pode ser a causa.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Limites e valores predefinidos](./cl-values-and-boundaries.md)
