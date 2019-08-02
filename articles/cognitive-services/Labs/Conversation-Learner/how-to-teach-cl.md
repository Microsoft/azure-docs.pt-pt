---
title: Como ensinar com os serviços cognitivas Conversation Learner da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como ensinar com Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: a18d4c31da4ffeefebd4bda9aa441fdfec062be9
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705255"
---
# <a name="how-to-teach-with-conversation-learner"></a>Como ensinar com o Conversation Learner 

Este documento explica quais sinais Conversation Learner estão cientes e descreve como ele aprende.  

O ensino pode ser decomposto em duas etapas distintas: extração de entidade e seleção de ação.

## <a name="entity-extraction"></a>Extração de entidade

Nos bastidores, Conversation Learner usa [Luis](https://www.luis.ai) para extração de entidade.  Se você estiver familiarizado com o LUIS, essa experiência se aplicará à extração de entidades no Conversation Learner.

Os modelos de extração de entidades estão cientes do *conteúdo* e do *contexto* dentro de um expressão de usuário.  Por exemplo, se a palavra "Seattle" tiver sido rotulada como uma cidade em um expressão, como "Qual é o clima em Seattle", a extração de entidades é capaz de reconhecer esse mesmo conteúdo ("Seattle") como uma cidade em outro expressão, como "população de Seattle", mesmo se o declarações são muito diferentes.  Por outro lado, se "Francis" tiver sido reconhecido como um nome em "agendar uma reunião com Francis", um novo nome anteriormente não visto poderá ser reconhecido em um contexto semelhante, como "configurar uma reunião com o Robin".  O aprendizado de máquina infere quando participar do conteúdo, contexto ou ambos, com base em exemplos de treinamento.

No momento, a extração de entidades só está ciente do conteúdo do expressão atual.  Diferentemente da seleção de ação (abaixo), não é ciente do histórico da caixa de diálogo, como as ativações anteriores do sistema, as ativações anteriores do usuário ou as entidades reconhecidas anteriormente.  Como resultado, o comportamento da extração de entidade é "compartilhado" em todos os declarações.  Por exemplo, se o usuário expressão "Eu quero que a Apple" tenha "Apple" rotulado como tipo de entidade "fruta" em um usuário expressão, o modelo de extração de entidades esperará que esse expressão ("Eu quero a Apple") sempre tenha "Apple" rotulado como "fruta".

Se a extração de entidade não estiver se comportando conforme o esperado, aqui estão as possíveis soluções:

- A primeira coisa a ser tentada é adicionar mais exemplos de treinamento, especialmente exemplos que revelam um contexto de entidade típico (palavras adjacentes) ou exceções
- Considere adicionar uma propriedade "entidade esperada" a uma ação, se apropriado.  Consulte o tutorial sobre as entidades esperadas para obter mais detalhes.
- Embora seja possível adicionar processamento manual ao `EntityExtractionCallback` para extrair entidades usando código, essa é a abordagem menos recomendada, pois ela não se beneficiará das melhorias no aprendizado de máquina à medida que seu sistema amadurece.

## <a name="action-selection"></a>Seleção de ação

A seleção de ação usa uma rede neural recorrente, que usa como entrada todo o histórico de conversa.  Portanto, a seleção de ação é um processo com estado que reconhece o declarações de usuário anterior, valores de entidade e declarações de sistema.  

Alguns sinais são naturalmente preferenciais pelo processo de aprendizado.  Em outras palavras, se Conversation Learner puder explicar uma decisão de seleção de ação usando sinais "mais preferenciais", será; Se não puder, ele usará sinais "menos preferenciais".

Aqui está uma tabela mostrando todos os sinais Em Conversation Learner e quais são usados pela seleção de ação.  Observe que a ordem de palavras no usuário declarações é ignorada.

Aviso | Preferência (1 = mais preferencial) | Notas
--- | --- | --- 
Ação do sistema na ativação anterior | 1 | 
Entidades presentes na ativação atual | 1 | 
Se esta é a primeira vez | 1 |
Correspondência exata de palavras no expressão do usuário atual | 2 | 
Palavras de significado semelhante no expressão do usuário atual | 3 | 
Ações do sistema antes da ativação anterior | 4 |
As entidades presentes no são ativadas antes da ativação atual | 4 | 
Declarações do usuário antes da ativação atual | 5 | 

> [!NOTE]
> A seleção de ação não pega o conteúdo das ações do sistema – o texto, o conteúdo do cartão ou o nome da API ou o comportamento, apenas a identidade da ação do sistema.  Como resultado, a alteração do conteúdo de uma ação não alterará o comportamento do modelo de seleção de ação.
>
> Além disso, os conteúdos/valores das entidades não são usados, apenas sua presença/ausência.

Se a seleção da ação não estiver se comportando conforme o esperado, aqui estão as possíveis soluções:

- Adicione mais caixas de diálogo de treinamento, especialmente caixas de diálogo que ilustram a quais sinais a seleção de ação deve prestar atenção.  Por exemplo, se a seleção de ação deve preferir um sinal sobre outro, dê exemplos que mostrem o sinal preferido que está no mesmo estado e os outros sinais variados.  Algumas sequências podem levar algumas caixas de diálogo de treinamento para aprender.
- Adicione entidades "obrigatórias" e "desqualificando" a definições de ação.  Isso limita quando as ações estão disponíveis e pode ser útil para expressar regras de negócio e alguns padrões de senso comuns. 

## <a name="updates-to-models"></a>Atualizações para modelos

Sempre que você adicionar ou editar uma caixa de diálogo de entidade, ação ou treinamento na interface do usuário, isso gerará uma solicitação para treinar novamente o modelo de extração de entidade e o modelo de seleção de ação.  Essa solicitação é colocada em uma fila e o novo treinamento é feito de forma assíncrona.  Quando um novo modelo está disponível, ele é usado desse ponto em diante para extração de entidade e seleção de ação.  Esse processo de readaptação geralmente leva cerca de 5 segundos, mas pode ser mais longo se o modelo for complexo ou se a carga no serviço de treinamento estiver alta.

Como o treinamento é feito de forma assíncrona, é possível que as edições feitas não sejam refletidas imediatamente.  Se a extração de entidade ou a seleção de ação não estiver se comportando conforme o esperado com base nas alterações feitas nos últimos 5-10 segundos, essa pode ser a causa.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Valores e limites padrão](./cl-values-and-boundaries.md)
