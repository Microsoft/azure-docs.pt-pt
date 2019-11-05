---
title: Design de aplicativo iterativo-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS aprende melhor em um ciclo iterativo de alterações de modelo, expressão exemplos, publicação e coleta de dados de consultas de ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487771"
---
# <a name="authoring-cycles-and-versions"></a>Ciclos e versões de criação

Seu aplicativo LUIS aprende melhor em um ciclo iterativo de:

* criar nova versão
* editar esquema de aplicativo
    * tentativas com o exemplo declarações
    * contabilidade
    * elástica
* Trem
* test
* publicar
    * testar no ponto de extremidade de previsão para o aprendizado ativo
* coletar dados de consultas de ponto de extremidade

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Criando um esquema LUIS

A finalidade do esquema de aplicativo é definir o que o usuário está solicitando (a intenção ou a intenção) e quais partes da pergunta fornecem detalhes (entidades) que ajudam a determinar a resposta. 

O esquema de aplicativo precisa ser específico para os domínios de aplicativo para determinar palavras e frases relevantes, bem como ordenação de palavras típica. 

O exemplo declarações representa a entrada de usuário que o aplicativo deve obter em tempo de execução. 

O esquema requer tentativas e _deve ter_ entidades. 

### <a name="example-schema-of-intents"></a>Esquema de exemplo de tentativas

O esquema mais comum é um esquema de intenção organizado com tentativas. Esse tipo de esquema depende do LUIS para determinar a intenção de um usuário. 

Esse tipo de esquema pode ter entidades se ajuda o LUIS a determinar a intenção. Por exemplo, uma entidade de remessa (como um descritor para uma intenção) ajuda a LUIS a determinar uma intenção de envio. 

### <a name="example-schema-of-entities"></a>Esquema de exemplo de entidades

Um esquema de entidade se concentra nas entidades, que são os dados a serem extraídos do declarações. 

A intenção do expressão é menos ou não importante para o aplicativo cliente. 

Um método comum de organizar um esquema de entidade é adicionar todos os declarações de exemplo à intenção de nenhum. 

### <a name="example-of-a-mixed-schema"></a>Exemplo de um esquema misto

O esquema mais potente e maduro é um esquema de intenção com uma gama completa de entidades e recursos. Esse esquema pode começar como um esquema de intenção ou de entidade e aumentar para incluir os conceitos de ambos, uma vez que o aplicativo cliente precisa dessas informações. 

## <a name="add-example-utterances-to-intents"></a>Adicionar declarações de exemplo a intenções

LUIS precisa de alguns exemplos de declarações em cada **tentativa**. O exemplo declarações precisa de uma variação suficiente de opção de palavra e de palavra para poder determinar a qual intenção o expressão se destina. 

> [!CAUTION]
> Não adicione o exemplo declarações em massa. Comece com 15 a 30 exemplos específicos e variados. 

Cada exemplo de expressão precisa ter todos os **dados necessários para extrair** e rotulados com **entidades**. 

|Elemento key|Objetivo|
|--|--|
|Intenção|**Classifique** o declarações do usuário em uma única intenção ou ação. Os exemplos incluem `BookFlight` e `GetWeather`.|
|Entidade|**Extraia** os dados do expressão necessários para concluir a intenção. Os exemplos incluem data e hora de viagem e local.|

Você cria seu aplicativo LUIS para ignorar declarações que não são relevantes para o domínio do seu aplicativo atribuindo o expressão à intenção **None** . 

## <a name="test-and-train-your-app"></a>Testar e treinar seu aplicativo

Depois de ter 15 a 30 exemplos diferentes de declarações em cada tentativa, com as entidades necessárias rotuladas, você precisa testar e [treinar](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Publicar em um ponto de extremidade de previsão

Certifique-se de publicar seu aplicativo para que ele esteja disponível nas [regiões de ponto de extremidade de previsão](luis-reference-regions.md) de que você precisa. 

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

Você pode testar seu aplicativo LUIS publicado do ponto de extremidade de previsão HTTPS. O teste do ponto de extremidade de previsão permite que o LUIS escolha qualquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Criar uma nova versão para cada ciclo

As versões, em LUIS, são semelhantes às versões na programação tradicional. Cada versão é um instantâneo no momento do aplicativo. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para uma versão mais antiga e tentar remover tentativas e declarações para um estado anterior.

A ID da versão consiste em caracteres, dígitos ou '. ' e não pode ter mais de 10 caracteres.

A versão inicial (0,1) é a versão ativa padrão. 

### <a name="begin-by-cloning-an-existing-version"></a>Comece clonando uma versão existente

Clone uma versão existente para usar como ponto de partida para a nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa** . 

### <a name="publishing-slots"></a>Slots de publicação
Você publica no estágio e nos slots de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isso é útil para verificar as alterações antes da publicação na produção, que está disponível para bots ou outros aplicativos de chamada LUIS. 

Versões treinadas não estão disponíveis automaticamente no ponto de [extremidade](luis-glossary.md#endpoint)do seu aplicativo. Você deve [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que ela esteja disponível no ponto de extremidade do aplicativo. Você pode publicar para **preparo** e **produção**, fornecendo duas versões do aplicativo disponíveis no ponto de extremidade. Se precisar de mais versões do aplicativo disponível em um ponto de extremidade, você deverá exportar a versão e reimportar para um novo aplicativo. O novo aplicativo tem uma ID de aplicativo diferente.

### <a name="import-and-export-a-version"></a>Importar e exportar uma versão
Você pode importar uma versão no nível do aplicativo. Essa versão se torna a versão ativa e usa a ID de versão na propriedade `versionId` do arquivo de aplicativo. Você também pode importar para um aplicativo existente, no nível de versão. A nova versão se torna a versão ativa. 

Você pode exportar uma versão no nível do aplicativo ou da versão. A única diferença é que a versão exportada no nível do aplicativo é a versão ativa no momento no nível de versão, você pode escolher qualquer versão a ser exportada na página de **[configurações](luis-how-to-manage-versions.md)** . 

O arquivo exportado não contém:

* informações aprendidas por computador porque o aplicativo é retreinado após ser importado
* informações do colaborador

Para fazer backup do esquema do aplicativo LUIS, exporte uma versão do portal do LUIS.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Gerenciar alterações de colaborador com versões e aplicativos

O LUIS fornece o conceito de colaboradores de um aplicativo, fornecendo permissões no nível de recursos do Azure. Combine esse conceito com o controle de versão para fornecer colaboração direcionada. 

Use as técnicas a seguir para gerenciar alterações de colaborador em seu aplicativo.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerenciar várias versões dentro do mesmo aplicativo
Comece pela [clonagem](luis-how-to-manage-versions.md#clone-a-version), de uma versão base, para cada autor. 

Cada autor faz alterações em sua própria versão do aplicativo. Quando cada autor estiver satisfeito com o modelo, exporte as novas versões para arquivos JSON.  

Os aplicativos exportados,. JSON ou. Lu, podem ser comparados para alterações. Combine os arquivos para criar um único arquivo da nova versão. Altere a propriedade **VersionId** para significar a nova versão mesclada. Importe essa versão para o aplicativo original. 

Esse método permite que você tenha uma versão ativa, uma versão de estágio e uma versão publicada. Você pode comparar os resultados da versão ativa com uma versão publicada (fase ou produção) no painel de [teste interativo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerenciar várias versões como aplicativos
[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa o aplicativo é o proprietário da versão. Quando terminar de modificar o aplicativo, exporte a versão. 

Os aplicativos exportados são arquivos formatados em JSON, que podem ser comparados com a exportação de base para alterações. Combine os arquivos para criar um único arquivo JSON da nova versão. Altere a propriedade **VersionId** no JSON para significar a nova versão mesclada. Importe essa versão para o aplicativo original.

Saiba mais sobre a criação de contribuições de [colaboradores](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Examinar o ponto de extremidade declarações para iniciar o novo ciclo de criação

Quando terminar de criar um ciclo de criação, você poderá começar novamente. Comece com [revisar ponto de extremidade de previsão declarações](luis-how-to-review-endpoint-utterances.md) Luis marcado com baixa confiança. Verifique essas declarações para a entidade prevista correta e correta e completa extraída. Depois de revisar e aceitar as alterações, a lista de revisão deve estar vazia.  

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre a [colaboração](luis-concept-keys.md).
