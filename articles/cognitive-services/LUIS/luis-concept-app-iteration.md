---
title: Design de aplicativos iterativos - LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS aprende melhor num ciclo iterativo de mudanças de modelo, exemplos de expressão, publicação e recolha de dados de consultas de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: cc994875e39410ecf01417c4d08248d5bc7d034a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589827"
---
# <a name="iterative-app-design-for-luis"></a>Design de aplicativoiativo para LUIS

Uma aplicação de Compreensão de Línguas (LUIS) aprende e executa de forma mais eficiente com a iteração. Aqui está um ciclo típico de iteração:

* Criar nova versão
* Editar o esquema da aplicação LUIS. Isto inclui:
    * Intenções com pronunciações de exemplo
    * Entidades
    * Funcionalidades
* Treinar, testar e publicar
    * Teste no ponto final da previsão para a aprendizagem ativa
* Recolher dados de consultas de ponto final

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Construção de um esquema LUIS

O esquema de uma aplicação define o que o utilizador está a pedir (a _intenção_ ou _intenção)_ e quais as partes da intenção que fornecem detalhes (entidades chamadas) que são usados para ajudar a determinar a resposta. _entities_

O esquema da aplicação deve ser específico dos domínios da aplicação para determinar palavras e frases relevantes, bem como para determinar a ordenação típica de palavras.

As declarações de exemplo representam as inputs dos utilizadores, como o discurso ou o texto reconhecidos, que a aplicação espera no prazo de execução.

O esquema requer intenções, e _deve ter_ entidades.

### <a name="example-schema-of-intents"></a>Exemplo de esquemas de intenções

O esquema mais comum é um esquema de intenções organizado com intenções. Este tipo de esquema utiliza o LUIS para determinar a intenção do utilizador.

O tipo de esquema de intenção pode ter entidades se ajudar a LUIS a determinar a intenção do utilizador. Por exemplo, uma entidade de transporte marítimo (como uma funcionalidade de _machine learning_ a uma intenção) ajuda a LUIS a determinar uma intenção de envio.

### <a name="example-schema-of-entities"></a>Exemplo de esquema de entidades

Uma entidade de esquema sintetiza-se em entidades, que são os dados que são extraídos das declarações dos utilizadores. Por exemplo, se um utilizador dissesse: "Gostaria de pedir três pizzas." Há duas entidades que seriam extraídas: _três_ e _pizzas._ Estes são usados para ajudar a cumprir a intenção, que era fazer uma ordem.

Para uma entidade schema, a intenção da expressão é menos importante para a aplicação do cliente.

Um método comum de organização de um esquema de entidade é adicionar todas as declarações de exemplo à intenção **de None.**

### <a name="example-of-a-mixed-schema"></a>Exemplo de um esquema misto

O esquema mais poderoso e maduro é um esquema de intenções com uma gama completa de entidades e funcionalidades. Este esquema pode começar como uma intenção ou um esquema de entidade e crescer para incluir conceitos de ambos, uma vez que a aplicação do cliente precisa dessas peças de informação.

## <a name="add-example-utterances-to-intents"></a>Adicione declarações exemplo sintetidades às intenções

Luis precisa de alguns exemplos em cada **intenção.** As declarações exemplo precisam de variação suficiente da escolha da palavra e da ordem de palavras para ser capaz de determinar para que intenção a expressão é destinada.

> [!CAUTION]
> Não adicione pronunciações a granel. Comece com 15 a 30 exemplos específicos e variados.

Cada expressão de exemplo deve ter todos os **dados necessários para extrair** desenhados e rotulados com **entidades**.

|Elemento-chave|Objetivo|
|--|--|
|Intenção|**Classifique** as declarações dos utilizadores numa única intenção, ou ação. Exemplos incluem `BookFlight` e `GetWeather` .|
|Entidade|**Extrair** dados da expressão necessária para completar a intenção. Exemplos incluem data e hora de viagem, e localização.|

Uma aplicação LUIS pode ser projetada para ignorar expressões que não são relevantes para o domínio de uma aplicação atribuindo a expressão à intenção **None.**

## <a name="test-and-train-your-app"></a>Teste e treine a sua app

Depois de ter 15 a 30 expressões diferentes em cada intenção, com as entidades necessárias rotuladas, precisa de testar e [treinar](luis-how-to-train.md) a sua app LUIS.

## <a name="publish-to-a-prediction-endpoint"></a>Publicar para um ponto final de previsão

A aplicação LUIS deve ser publicada de modo a que esteja disponível nas [regiões finais](luis-reference-regions.md)da lista de previsão.

## <a name="test-your-published-app"></a>Teste a sua aplicação publicada

Pode testar a sua aplicação DELUIS publicada a partir do ponto final de previsão HTTPS. Os testes a partir do ponto final de previsão permitem à LUIS escolher quaisquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Criar uma nova versão para cada ciclo

Cada versão é um instantâneo no tempo da aplicação LUIS. Antes de fazer alterações na aplicação, crie uma nova versão. É mais fácil voltar a uma versão mais antiga do que tentar remover as intenções e as expressões a um estado anterior.

O ID da versão é composto por caracteres, dígitos ou '.' e não pode ter mais de 10 caracteres.

A versão inicial (0.1) é a versão ativa padrão.

### <a name="begin-by-cloning-an-existing-version"></a>Comece por clonar uma versão existente

Clone uma versão existente para usar como ponto de partida para cada nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa.**

### <a name="publishing-slots"></a>Slots de publicação

Pode publicar para as faixas horárias de palco e/ou de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isto é útil para verificar alterações antes de publicar para produção, que está disponível para bots ou outras aplicações de chamada LUIS.

As versões treinadas não estão automaticamente disponíveis no [ponto final](luis-glossary.md#endpoint)da sua aplicação LUIS. Tem de [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que esteja disponível no ponto final da sua aplicação LUIS. Pode publicar na **Staging** and **Production,** dando-lhe duas versões da app disponível no ponto final. Se mais versões da aplicação precisarem de estar disponíveis num ponto final, deverá exportar a versão e reimportá-la para uma nova aplicação. A nova aplicação tem um ID de aplicação diferente.

### <a name="import-and-export-a-version"></a>Importar e exportar uma versão

Uma versão pode ser importada ao nível da aplicação. Esta versão torna-se a versão ativa e utiliza o ID da versão na `versionId` propriedade do ficheiro da aplicação. Também pode importar para uma aplicação existente, ao nível da versão. A nova versão torna-se a versão ativa.

Uma versão também pode ser exportada ao nível da aplicação ou versão. A única diferença é que a versão exportada a nível de aplicações é a versão atualmente ativa enquanto ao nível da versão, pode escolher qualquer versão para exportar na página **[Definições.](luis-how-to-manage-versions.md)**

O ficheiro exportado **não** contém:

* Informação aprendida por máquinas, porque a aplicação é retreinada depois de importada
* Informação dos contribuintes

Para fazer o seu esquema de aplicação LUIS, exporte uma versão do [portal LUIS.](https://www.luis.ai/applications)

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gerir as alterações dos contribuintes com versões e contribuintes

O LUIS utiliza o conceito de contribuintes para uma app, fornecendo permissões ao nível de recursos do Azure. Combine este conceito com a versão para proporcionar colaboração direcionada.

Utilize as seguintes técnicas para gerir alterações de colaboradores na sua aplicação.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerir várias versões dentro da mesma app

Comece por [clonagem a](luis-how-to-manage-versions.md#clone-a-version) partir de uma versão base para cada autor.

Cada autor faz alterações na sua própria versão da app. Quando o autor estiver satisfeito com o modelo, exporte as novas versões para ficheiros JSON.

As aplicações exportadas, ficheiros .json ou .lu, podem ser comparadas para alterações. Combine os ficheiros para criar um único ficheiro da nova versão. Mude a `versionId` propriedade para significar a nova versão fundida. Importe essa versão para a aplicação original.

Este método permite-lhe ter uma versão ativa, uma versão de palco e uma versão publicada. Pode comparar os resultados da versão ativa com uma versão publicada (fase ou produção) no painel de [testes interativo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerir várias versões como apps

[Exportar](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa a app é a proprietária da versão. Quando terminarem de modificar a aplicação, exporte a versão.

As aplicações exportadas são ficheiros formados pela JSON, que podem ser comparados com a exportação base para alterações. Combine os ficheiros para criar um único ficheiro JSON da nova versão. Altere a propriedade **versãoId** no JSON para significar a nova versão fundida. Importe essa versão para a aplicação original.

Saiba mais sobre a autoria de contribuições de [colaboradores.](luis-how-to-collaborate.md)

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Rever as declarações finais para iniciar o novo ciclo iterativo

Quando terminar com um ciclo de iteração, pode repetir o processo. Comece com a revisão das [declarações de ponto final](luis-how-to-review-endpoint-utterances.md) de previsão LUIS marcada com baixa confiança. Verifique estas declarações tanto para a correta intenção prevista como para a entidade correta e completa extraída. Depois de rever e aceitar alterações, a lista de revisão deve estar vazia.

## <a name="next-steps"></a>Passos seguintes

Conheça conceitos sobre [colaboração.](luis-concept-keys.md)
