---
title: Design de aplicativo iterativo-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422602"
---
# <a name="iterative-app-design-for-luis"></a>Design de aplicativo iterativo para LUIS

Um aplicativo Reconhecimento vocal (LUIS) aprende e executa com mais eficiência com iteração. Aqui está um ciclo de iteração típico:

* criar nova versão
* Edite o esquema do aplicativo LUIS. Isto inclui:
    * tentativas com o exemplo declarações
    * Entidades
    * Funcionalidades
* Treinar, testar e publicar
    * Testar no ponto de extremidade de previsão para o aprendizado ativo
* coletar dados de consultas de ponto de extremidade

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Criando um esquema LUIS

O esquema de um aplicativo define o que o usuário está solicitando (a _intenção_ ou a _intenção_ ) e quais partes da intenção fornecem detalhes (chamados de _entidades_) que são usadas para ajudar a determinar a resposta. 

O esquema do aplicativo deve ser específico para os domínios de aplicativo para determinar palavras e frases relevantes, bem como para determinar a ordenação típica de palavras. 

Exemplos declarações representam entradas de usuário, como fala ou texto reconhecido, que o aplicativo espera no tempo de execução. 

O esquema requer tentativas e _deve ter_ entidades. 

### <a name="example-schema-of-intents"></a>Esquema de exemplo de tentativas

O esquema mais comum é um esquema de intenção organizado com tentativas. Esse tipo de esquema usa LUIS para determinar a intenção de um usuário. 

O tipo de esquema de intenção pode ter entidades se ajudar LUIS a determinar a intenção do usuário. Por exemplo, uma entidade de remessa (como um descritor para uma intenção) ajuda a LUIS a determinar uma intenção de envio. 

### <a name="example-schema-of-entities"></a>Esquema de exemplo de entidades

Um esquema de entidade se concentra em entidades, que são os dados extraídos do usuário declarações. Por exemplo, se um usuário deveria dizer: "Eu gostaria de pedir três pizzas". Há duas entidades que seriam extraídas: _três_ e _pizzas_. Eles são usados para ajudar a atender à intenção, que era fazer um pedido. 

Para um esquema de entidade, a intenção do expressão é menos importante para o aplicativo cliente. 

Um método comum de organizar um esquema de entidade é adicionar todos os declarações de exemplo à intenção de **nenhum** . 

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

Um aplicativo LUIS pode ser projetado para ignorar declarações que não são relevantes para o domínio de um aplicativo atribuindo o expressão à tentativa de **nenhum** .

## <a name="test-and-train-your-app"></a>Testar e treinar seu aplicativo

Depois de ter 15 a 30 exemplos diferentes de declarações em cada tentativa, com as entidades necessárias rotuladas, você precisa testar e [treinar](luis-how-to-train.md) seu aplicativo Luis. 

## <a name="publish-to-a-prediction-endpoint"></a>Publicar em um ponto de extremidade de previsão

O aplicativo LUIS deve ser publicado para que esteja disponível para você nas [regiões de ponto de extremidade de previsão](luis-reference-regions.md)de lista.

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

Você pode testar seu aplicativo LUIS publicado do ponto de extremidade de previsão HTTPS. O teste do ponto de extremidade de previsão permite que o LUIS escolha qualquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Criar uma nova versão para cada ciclo

Cada versão é um instantâneo no momento do aplicativo LUIS. Antes de fazer alterações no aplicativo, crie uma nova versão. É mais fácil voltar para uma versão mais antiga do que tentar remover tentativas e declarações para um estado anterior.

A ID da versão consiste em caracteres, dígitos ou '. ' e não pode ter mais de 10 caracteres.

A versão inicial (0,1) é a versão ativa padrão. 

### <a name="begin-by-cloning-an-existing-version"></a>Comece clonando uma versão existente

Clone uma versão existente para usar como um ponto de partida para cada nova versão. Depois de clonar uma versão, a nova versão torna-se a versão **ativa** . 

### <a name="publishing-slots"></a>Slots de publicação

Você pode publicar no estágio e/ou nos slots de produção. Cada slot pode ter uma versão diferente ou a mesma versão. Isso é útil para verificar as alterações antes de publicar para produção, que está disponível para bots ou outros aplicativos de chamada LUIS. 

Versões treinadas não estão disponíveis automaticamente no ponto de [extremidade](luis-glossary.md#endpoint)do seu aplicativo Luis. Você deve [publicar](luis-how-to-publish-app.md) ou republicar uma versão para que ela esteja disponível no ponto de extremidade do aplicativo Luis. Você pode publicar para **preparo** e **produção**, fornecendo duas versões do aplicativo disponíveis no ponto de extremidade. Se mais versões do aplicativo precisarem estar disponíveis em um ponto de extremidade, você deverá exportar a versão e importá-la novamente para um novo aplicativo. O novo aplicativo tem uma ID de aplicativo diferente.

### <a name="import-and-export-a-version"></a>Importar e exportar uma versão

Uma versão pode ser importada no nível do aplicativo. Essa versão se torna a versão ativa e usa a ID de versão na propriedade `versionId` do arquivo de aplicativo. Você também pode importar para um aplicativo existente, no nível de versão. A nova versão se torna a versão ativa. 

Uma versão pode ser exportada no nível do aplicativo ou da versão também. A única diferença é que a versão exportada no nível do aplicativo é a versão ativa no momento no nível de versão, você pode escolher qualquer versão a ser exportada na página de **[configurações](luis-how-to-manage-versions.md)** . 

O arquivo exportado **não** contém:

* Informações aprendidas por máquina, porque o aplicativo é retreinado depois de ser importado
* informações do colaborador

Para fazer backup do esquema do aplicativo LUIS, exporte uma versão do [portal do Luis](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Gerenciar alterações de colaborador com versões e colaboradores

O LUIS usa o conceito de colaboradores para um aplicativo, fornecendo permissões no nível de recurso do Azure. Combine esse conceito com o controle de versão para fornecer colaboração direcionada. 

Use as técnicas a seguir para gerenciar alterações de colaborador em seu aplicativo.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Gerir facilmente várias versões na mesma aplicação

Comece pela [clonagem](luis-how-to-manage-versions.md#clone-a-version) de uma versão base para cada autor. 

Cada autor faz alterações em sua própria versão do aplicativo. Quando o autor for satisfeito com o modelo, exporte as novas versões para arquivos JSON.  

Os aplicativos exportados,. JSON ou. Lu, podem ser comparados para alterações. Combine os arquivos para criar um único arquivo da nova versão. Altere a propriedade `versionId` para significar a nova versão mesclada. Importe essa versão para a aplicação original. 

Este método permite-lhe ter uma versão de Active Directory, uma versão de fase e uma versão publicada. Você pode comparar os resultados da versão ativa com uma versão publicada (fase ou produção) no painel de [teste interativo](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Gerir facilmente várias versões das aplicações

[Exporte](luis-how-to-manage-versions.md#export-version) a versão base. Cada autor importa a versão. A pessoa que importa a aplicação é o proprietário da versão. Quando são feitas modificar a aplicação, exportar a versão. 

As aplicações exportadas são arquivos de formato JSON, que podem ser comparados com a exportação de base para que as alterações. Combine os ficheiros para criar um único ficheiro JSON da nova versão. Altere a propriedade **VersionId** no JSON para significar a nova versão mesclada. Importe essa versão para a aplicação original.

Saiba mais sobre a criação de contribuições de [colaboradores](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Examinar o ponto de extremidade declarações para iniciar o novo ciclo iterativo

Quando terminar com um ciclo de iteração, você poderá repetir o processo. Comece com [revisar ponto de extremidade de previsão declarações](luis-how-to-review-endpoint-utterances.md) Luis marcado com baixa confiança. Verifique essas declarações para a entidade prevista correta e correta e completa extraída. Depois de revisar e aceitar as alterações, a lista de revisão deve estar vazia.  

## <a name="next-steps"></a>Passos seguintes

Aprenda conceitos sobre a [colaboração](luis-concept-keys.md).
