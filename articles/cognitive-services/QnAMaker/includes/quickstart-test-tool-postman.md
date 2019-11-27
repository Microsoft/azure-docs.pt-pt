---
title: incluir ficheiro
description: incluir ficheiro
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424420"
---
Este guia de início rápido baseado no postmaster orienta você pela obtenção de uma resposta da sua base de dados de conhecimento.

## <a name="prerequisites"></a>Pré-requisitos

* Último [**postmaster**](https://www.getpostman.com/).
* Você deve ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md) e ter uma [base de dados de conhecimento com perguntas e respostas](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publicar para obter ponto de extremidade

Quando você estiver pronto para gerar uma resposta para uma pergunta de sua base de dados de conhecimento, [publique](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) sua base de dados de conhecimento.

## <a name="use-production-endpoint-with-postman"></a>Usar ponto de extremidade de produção com o postmaster

Quando sua base de dados de conhecimento é publicada, a página **publicar** exibe as configurações de solicitação HTTP para gerar uma resposta. A exibição padrão mostra as configurações necessárias para gerar uma resposta do [postmaster](https://www.getpostman.com).

Os números amarelos na imagem a seguir indicam quais pares de nome/valor usar nas etapas a seguir.

[![publicar resultados](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Para gerar uma resposta com o postmaster, conclua as seguintes etapas:

1. Abrir o postmaster. Se você for solicitado a escolher um bloco de construção, selecione o bloco de construção de **solicitação básico** . Defina o **nome da solicitação** como `Generate QnA Maker answer`e a **coleção** como `Generate QnA Maker answers`. Se você não quiser salvar em uma coleção, selecione o botão **Cancelar** .
1. No espaço de trabalho, selecione o método HTTP **post**.

    [![no postmaster, defina o método POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Para a URL, concatene o valor de HOST (#2 da imagem) e o valor de post (#1 da imagem) para criar a URL completa. Uma URL de exemplo completa é semelhante A: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![no postmaster, defina a URL completa](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Selecione a guia **cabeçalhos** na URL e, em seguida, selecione **Editar em massa**. 

1. Copie os cabeçalhos (#3 e #4 da imagem) para a área de texto.

    [![no postmaster, defina os cabeçalhos](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Selecione a guia **corpo** .
1. Selecione o formato **bruto** e insira o JSON (#5 da imagem) que representa a pergunta.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![no postmaster, defina o valor JSON do corpo](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Selecione o botão **Enviar** .
1. A resposta contém a resposta junto com outras informações que podem ser importantes para o aplicativo cliente. 

    [![no postmaster, defina o valor JSON do corpo](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Usar ponto de extremidade de preparo

Se você quiser obter uma resposta do ponto de extremidade de preparo, acrescente a URL com a propriedade de corpo `isTest`.
