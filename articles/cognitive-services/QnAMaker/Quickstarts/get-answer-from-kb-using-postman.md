---
title: 'Início rápido: Utilizar o Postman para receber resposta da base de dados de conhecimento - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este início rápido explica como obter uma resposta de sua base de dados de conhecimento com o Postman.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7083285ac81aa8eafee8de49175e40934e5d05b4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253814"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Início rápido: Receber uma resposta da base de dados de conhecimento com o Postman

Este guia de introdução com base no Postman explica como obter uma resposta de sua base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* Mais recente [ **Postman**](https://www.getpostman.com/).
* Tem de ter uma [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e têm um [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicar para obter o ponto final

Quando estiver pronto para gerar uma resposta a uma pergunta da sua base de dados de conhecimento [publicar](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-postman"></a>Utilizar o ponto final de produção com o Postman

Quando é publicado a sua base de dados de conhecimento, o **publicar** página apresenta as definições do pedido HTTP para gerar uma resposta. A vista predefinida mostra as definições necessárias para gerar uma resposta da [Postman](https://www.getpostman.com).

Os números de amarelos na imagem seguinte indicam qual o nome/valor pares para utilizar nos passos seguintes.

[![Publicar os resultados](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Para gerar uma resposta com o Postman, conclua os seguintes passos:

1. Abra o Postman. Se lhe for pedido para escolher um bloco de construção, selecione o **pedir básica** bloco de construção. Definir o **nome do pedido** como `Generate QnA Maker answer`e o **coleção** como `Generate QnA Maker answers`. Se não pretender guardar uma coleção, selecione o **Cancelar** botão.
1. Na área de trabalho, selecione o método HTTP **POST**.

    [![No Postman, definir o método POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Para o URL, concatenar o valor de anfitrião (n. º 2 da imagem) e o valor de Post (n. º 1 da imagem) para criar o URL completo. Um exemplo completo URL é semelhante a: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![No Postman, definir o URL completo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selecione o **cabeçalhos** separador no URL, em seguida, selecione **editar em massa**. 

1. Copie os cabeçalhos (n º 3 e 4 # de imagem) para a área de texto.

    [![No Postman, definir os cabeçalhos](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selecione o **corpo** separador.
1. Selecione o **brutos** formatar e introduza o JSON (n. º 5 da imagem), que representa a pergunta.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![No Postman, definir o valor JSON do corpo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selecione o **enviar** botão.
1. A resposta contém a resposta, juntamente com outras informações que podem ser importantes para a aplicação cliente. 

    [![No Postman, definir o valor JSON do corpo](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Utilizar o ponto final de teste

Se quiser obter uma resposta do ponto de extremidade de teste, acrescentar o URL com o `isTest` body propriedade.

## <a name="next-steps"></a>Passos seguintes

Página de publicação também fornece informações que lhe [gerar uma resposta](get-answer-from-kb-using-curl.md) com cURL. 

> [!div class="nextstepaction"]
> [Metadados de utilização ao gerar uma resposta](../How-to/metadata-generateanswer-usage.md)
