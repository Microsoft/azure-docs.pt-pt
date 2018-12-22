---
title: Entidade de Pattern.any
titleSuffix: Azure Cognitive Services
description: Utilize a entidade pattern.any para extrair dados de expressões em que as expressões estão bem formatadas e em que o final dos dados pode ser facilmente confundido com as restantes palavras da expressão.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 58a62a5a882c6883c6fed31a7b95d949247e1bf1
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2018
ms.locfileid: "53752610"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Tutorial: Extrair dados de forma livre com Pattern.any entidade

Neste tutorial, utilize a entidade pattern.any para extrair dados de expressões em que as expressões estão bem formatadas e em que o final dos dados pode ser facilmente confundido com as restantes palavras da expressão. 

**Neste tutorial, ficará a saber como:**

> [!div class="checklist"]
> * Aplicação de exemplo de importação
> * Adicionar expressões de exemplo à entidade existente
> * Criar entidade Pattern.any
> * Criar padrão
> * Preparar
> * Testar o novo padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Usando Pattern.any entidade

A entidade pattern.any permite localizar dados de forma livre em que o texto da entidade dificulta distinguir o final da entidade do resto da expressão. 

Esta aplicação de Recursos Humanos ajuda os colaboradores a encontrar formulários de empresa. 

|Expressão|
|--|
|Onde está **HRF-123456**?|
|Quem é o autor de **HRF-123234**?|
|**HRF-456098** está publicado em francês?|

No entanto, cada formulário tem um nome formatado, utilizado na tabela anterior, bem como um nome amigável, como `Request relocation from employee new to the company 2018 version 5`. 

As expressões na forma amigável têm o seguinte aspeto:

|Expressão|
|--|
|Onde está **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|Quem é o autor de **Pedir transferência do colaborador que é novo na empresa 2018 versão 5**?|
|**Pedir transferência do colaborador que é novo na empresa 2018 versão 5** está publicado em francês?|

O comprimento variado inclui palavras que podem confundir o LUIS sobre onde termina a entidade. Utilizar uma entidade Pattern.any num padrão permite especificar o início e o final do nome do formulário, para que o LUIS extraia corretamente o nome do formulário.

|Exemplo de expressão de modelo|
|--|
|Onde está {FormName}[?]|
|Quem é o autor de {FormName}[?]|
|{FormName} está publicado em francês[?]|

## <a name="import-example-app"></a>Aplicação de exemplo de importação
Continue com a aplicação criada no último tutorial, com o nome **RecursosHumanos**. 

Utilize os passos seguintes:

1.  Transfira e guarde o [ficheiro JSON da aplicação](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

2. Importe o JSON para uma nova aplicação.

3. Na secção **Gerir**, no separador **Versões**, clone a versão e dê-lhe o nome `patt-any`. A clonagem é uma excelente forma de utilizar várias funcionalidades do LUIS sem afetar a versão original. Como o nome da versão é utilizado como parte da rota de URL, o nome não pode conter carateres que não sejam válidos num URL.

## <a name="add-example-utterances"></a>Adicionar expressões de exemplo 
Remova a entidade keyPhrase pré-criada, se for difícil criar e identificar a entidade FormName. 

1. Selecione **Compilar** no painel de navegação superior e, em seguida, selecione **Intenções** no painel de navegação esquerdo.

2. Selecione **FindForm** na lista de intenções.

3. Adicione algumas expressões de exemplo:

    |Expressão de exemplo|
    |--|
    |Onde está o formulário **O que fazer quando um incêndio deflagra no laboratório** e quem precisa de iniciar sessão depois de eu o ler?|
    |Onde está **Pedir transferência do colaborador que é novo na empresa** no servidor?|
    |Quem é o autor de “**Pedidos relacionados com saúde e bem-estar no campus principal**” e qual é a versão mais atual?|
    |Estou à procura do formulário com o nome “**Pedidos de mudança de escritório, incluindo os recursos físicos**”. |

    Sem uma entidade Pattern.any, o LUIS teria dificuldade em compreender onde termina o título do formulário, devido a muitas variações de nomes do formulário.

## <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Só funciona num padrão porque o padrão marca o início e o final da entidade. Se chegar à conclusão que o seu padrão, ao incluir uma entidade Pattern.any, extrai as entidades incorretamente, utilize uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir este problema. 

1. Selecione **Entidades** no painel de navegação esquerdo.

2. Selecione **Criar nova entidade**, introduza o nome `FormName` e selecione **Pattern.any** como o tipo. Selecione **Done** (Concluído). 

    Não é possível identificar a entidade na intenção porque a entidade Pattern.any só é válida num padrão. 

    Se pretender que os dados extraídos incluam outras entidades, como o número ou datetimeV2, terá de criar uma entidade composta que inclua o Pattern.any, bem como o número e o datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que utiliza o Pattern.any

1. Selecione **Padrões** no menu de navegação esquerdo.

2. Selecione a intenção **FindForm**.

3. Introduza as seguintes expressões de modelo, que utilizam a nova entidade:

    |Expressões de modelo|
    |--|
    |Onde está o formulário [“]{FormName}[”] e quem precisa de iniciar sessão depois de eu o ler[?]|
    |Onde está o formulário [“]{FormName}[”] no servidor[?]|
    |Quem é o autor de [“]{FormName}[”] e qual é a versão mais atual[?]|
    |Estou à procura do formulário com o nome [“]{FormName}[”][.]|

    Se quiser ter em conta as variações do formulário, como plicas em vez de aspas ou um ponto final em vez de um ponto de interrogação, crie um novo padrão para cada variação.

4. Se tiver removido a entidade keyPhrase, adicione-a novamente à aplicação. 

## <a name="train-the-luis-app"></a>Preparar a aplicação LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão para extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste. 

2. Introduza a seguinte expressão: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Selecione **Inspecionar** no resultado para ver os resultados da entidade e intenção.

    Em primeiro lugar, é encontrada a entidade `FormName` e depois o padrão, que determina a intenção. Se tiver um resultado de teste em que as entidades não são detetadas e, por conseguinte, o padrão não é encontrado, terá de adicionar mais expressões de exemplo na intenção (não no padrão).

4. Feche o painel de teste, ao selecionar o botão **Testar** no painel de navegação superior.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Este tutorial adicionou expressões de exemplo a uma intenção existente e, em seguida, criou um novo Pattern.any para o nome do formulário. Em seguida, o tutorial criou um padrão para a intenção existente com as novas expressões de exemplo e uma entidade. O teste interativo mostrou que o padrão e a respetiva intenção foram previstos porque a entidade foi encontrada. 

> [!div class="nextstepaction"]
> [Saiba como utilizar funções com um padrão](luis-tutorial-pattern-roles.md)
