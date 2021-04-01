---
title: Design de aplicativo iterativo - LUIS
titleSuffix: Azure Cognitive Services
description: O LUIS aprende melhor num ciclo iterativo de mudanças de modelos, exemplos de expressão, publicação e recolha de dados a partir de consultas de ponto final.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 753d214b520affb59722bc29dbabc50c6e5968f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91538726"
---
# <a name="iterative-app-design-for-luis"></a>Design de aplicativo iterativo para LUIS

Uma aplicação de Compreensão de Línguas (LUIS) aprende e executa de forma mais eficiente com a iteração. Aqui está um ciclo típico de iteração:

* Criar nova versão
* Editar o esquema de aplicações LUIS. O que está incluído:
    * Intenções com palavras de exemplo
    * Entidades
    * Funcionalidades
* Treinar, testar e publicar
    * Teste no ponto final de previsão para a aprendizagem ativa
* Recolher dados de consultas de ponto final

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Construção de um esquema LUIS

O esquema de uma aplicação define o que o utilizador está a pedir (a _intenção_ ou _intenção)_ e que partes da intenção fornecem detalhes (chamados _entidades)_ que são usados para ajudar a determinar a resposta.

O esquema da aplicação deve ser específico dos domínios da aplicação para determinar palavras e frases relevantes, bem como para determinar a ordem típica de palavras.

As declarações de exemplo representam entradas de utilizadores, como discurso ou texto reconhecidos, que a app espera em tempo de execução.

O esquema requer intenções, e _deve ter_ entidades.

### <a name="example-schema-of-intents"></a>Exemplo de esquema de intenções

O esquema mais comum é um esquema de intenção organizado com intenções. Este tipo de esquema utiliza o LUIS para determinar a intenção do utilizador.

O tipo de esquema de intenção pode ter entidades se ajudar a LUIS a determinar a intenção do utilizador. Por exemplo, uma entidade de envio (como uma funcionalidade de _machine learning_ a uma intenção) ajuda a LUIS a determinar uma intenção de envio.

### <a name="example-schema-of-entities"></a>Exemplo de esquema de entidades

Um esquema de entidades centra-se nas entidades, que são os dados extraídos das expressões dos utilizadores. Por exemplo, se um utilizador dissesse: "Gostaria de encomendar três pizzas." Há duas entidades que seriam extraídas: _três_ e _pizzas._ Estes são usados para ajudar a cumprir a intenção, que era fazer uma ordem.

Para um esquema de entidade, a intenção da expressão é menos importante para a aplicação do cliente.

Um método comum de organização de um esquema de entidade é adicionar todas as declarações de exemplo à intenção **de Ninguém.**

### <a name="example-of-a-mixed-schema"></a>Exemplo de um esquema misto

O esquema mais poderoso e maduro é um esquema de intenção com uma gama completa de entidades e características. Este esquema pode começar como um esquema de intenção ou entidade e crescer para incluir conceitos de ambos, uma vez que a aplicação do cliente precisa dessas peças de informação.

## <a name="add-example-utterances-to-intents"></a>Adicione palavras de exemplo às intenções

LUIS precisa de algumas palavras de exemplo em cada **intenção.** As declarações de exemplo precisam de variação suficiente da escolha da palavra e da ordem de palavras para ser capaz de determinar para que intenção a expressão é destinada.

> [!CAUTION]
> Não adicione palavras de exemplo a granel. Comece com 15 a 30 exemplos específicos e variados.

Cada exemplo de expressão precisa de ter todos os **dados necessários para extrair** e rotular com **entidades.**

|Elemento chave|Objetivo|
|--|--|
|Intenção|**Classifique as** expressões dos utilizadores numa única intenção ou ação. Exemplos incluem `BookFlight` `GetWeather` e.|
|Entidade|**Extrair** dados da expressão necessária para completar a intenção. Exemplos incluem data e hora de viagem, e localização.|

Uma aplicação LUIS pode ser projetada para ignorar expressões que não são relevantes para o domínio de uma aplicação atribuindo a expressão à intenção **de Zero.**

## <a name="test-and-train-your-app"></a>Teste e treine a sua app

Depois de ter 15 a 30 exemplos diferentes em cada intenção, com as entidades necessárias rotuladas, precisa de testar e [treinar](luis-how-to-train.md) a sua app LUIS.

## <a name="publish-to-a-prediction-endpoint"></a>Publicar para um ponto final de previsão

A aplicação LUIS deve ser publicada para que esteja disponível para si nas [regiões](luis-reference-regions.md)de previsão da lista.

## <a name="test-your-published-app"></a>Teste o seu aplicativo publicado

Pode testar a sua aplicação LUIS publicada a partir do ponto final de previsão HTTPS. O teste a partir do ponto final de previsão permite ao LUIS escolher quaisquer expressões com pouca confiança para [revisão.](luis-how-to-review-endpoint-utterances.md)

## <a name="create-a-new-version-for-each-cycle"></a>Criar uma nova versão para cada ciclo

Cada versão é um instantâneo no tempo da aplicação LUIS. Antes de fazer alterações na aplicação, crie uma nova versão. É mais fácil voltar a uma versão mais antiga do que tentar remover intenções e declarações para um estado anterior.

O ID da versão é composto por caracteres, dígitos ou '.' e não pode ser superior a 10 caracteres.

A versão inicial (0.1) é a versão ativa predefinida.

### <a name="begin-by-cloning-an-existing-version"></a>Comece por clonar uma versão existente

Clone uma versão existente para usar como ponto de partida para cada nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa.**

### <a name="publishing-slots"></a>Slots de publicação

Pode publicar nas faixas horárias de palco e/ou de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isto é útil para verificar alterações antes de publicar a produção, que está disponível para bots ou outras aplicações de chamadas LUIS.

As versões treinadas não estão automaticamente disponíveis no [ponto final](luis-glossary.md#endpoint)da sua aplicação LUIS. Tem de [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que esteja disponível no seu ponto final da aplicação LUIS. Pode publicar para **Staging** and **Production,** dando-lhe duas versões da aplicação disponível no ponto final. Se mais versões da app precisarem de estar disponíveis num ponto final, deverá exportar a versão e reimportá-la para uma nova aplicação. A nova aplicação tem um ID de aplicação diferente.

### <a name="import-a-version"></a>Importar uma versão

Uma versão pode ser **importada** como uma nova:
* App, com um novo ID de aplicação
* Versão de uma app existente

Esta versão torna-se a versão ativa e utiliza o ID da versão na `versionId` propriedade do ficheiro da aplicação.

### <a name="export-a-version"></a>Exportar uma versão

Uma versão pode ser **exportada** a partir do portal LUIS a nível de aplicação ou nível de versão:

* Nível de aplicativo - selecione app na **página de minhas aplicações** e, em seguida, selecione **Exportação**
* Nível de versão - selecione link de aplicativos na página **de minhas aplicações,** selecione **Gerir,** selecione **Versões**

A única diferença é que o nível de aplicação, a versão exportada é a versão atualmente ativa enquanto que, ao nível da versão, pode escolher qualquer versão para exportar na página **[Definições.](luis-how-to-manage-versions.md)**

O ficheiro exportado **não** contém:

* informação de machine-learning, porque a app é retreinada depois de importada
* Informação dos contribuintes

Para fazer o seu esquema de aplicações LUIS, exporte uma versão do [portal LUIS.](https://www.luis.ai/applications)

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gerir as mudanças de colaboradores com versões e contribuintes

A LUIS utiliza o conceito de contribuintes para uma aplicação, fornecendo permissões de nível de recursos Azure. Combine este conceito com a versão para proporcionar colaboração direcionada.

Utilize as seguintes técnicas para gerir as alterações dos colaboradores na sua aplicação.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerir várias versões dentro da mesma app

Comece por [clonagem a](luis-how-to-manage-versions.md#clone-a-version) partir de uma versão base para cada autor.

Cada autor faz alterações na sua própria versão da app. Quando o autor estiver satisfeito com o modelo, exporte as novas versões para ficheiros JSON.

Aplicativos exportados, `.json` ou `.lu` ficheiros, podem ser comparados para alterações. Combine os ficheiros para criar um único ficheiro da nova versão. Altere a `versionId` propriedade para significar a nova versão fundida. Importe essa versão para a aplicação original.

Este método permite-lhe ter uma versão ativa, uma versão de fase e uma versão publicada. Pode comparar os resultados da versão ativa com uma versão publicada (fase ou produção) no [painel de testes interativos](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerir várias versões como apps

[Exportar](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa a app é a proprietária da versão. Quando terminarem de modificar a app, exporte a versão.

As aplicações exportadas são ficheiros com formato JSON, que podem ser comparados com a exportação base para alterações. Combine os ficheiros para criar um único ficheiro JSON da nova versão. Altere a **versão PropriedadeId** no JSON para significar a nova versão fundida. Importe essa versão para a aplicação original.

Saiba mais sobre a autoria de contribuições de [colaboradores.](luis-how-to-collaborate.md)

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Rever as declarações de ponto final para iniciar o novo ciclo iterativo

Quando terminar com um ciclo de iteração, pode repetir o processo. Comece com [a revisão das declarações de ponto final de previsão](luis-how-to-review-endpoint-utterances.md) LUIS marcadas com pouca confiança. Verifique estas declarações para obter a intenção correta e a entidade correta e completa extraída. Depois de rever e aceitar alterações, a lista de revisão deve estar vazia.

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre [colaboração.](luis-how-to-azure-subscription.md)
