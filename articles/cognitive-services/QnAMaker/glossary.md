---
title: Glossário - QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço QnA Maker tem muitos novos termos de machine learning e os termos de processamento, bem como serviços específicos de linguagem natural. Esta lista irão ajudar a compreender os termos.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 832bf85087b706451a6ee58c538d7a163ef18203
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079947"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Glossário do serviço e de base de dados de conhecimento do QnA Maker

## <a name="qna-maker-service"></a>Serviço QnA Maker
Um serviço do QnA Maker é um pré-requisito para começar a utilizar o QnA Maker. Comprar um escalão do QnA Maker configura recursos na sua subscrição do Azure para criar e gerir a sua base de dados de conhecimento. Cada conta de utilizador do QnA Maker pode criar vários serviços do QnA Maker na sua subscrição do Azure.

## <a name="knowledge-base"></a>Base de Dados de Conhecimento
Uma base de dados de conhecimento é o repositório de perguntas e respostas criado, mantido e usado por meio do QnA Maker. Cada camada do QnA Maker pode ser utilizada para várias bases de dados de conhecimento.

## <a name="endpoint"></a>Ponto Final
Um ponto de final de HTTP baseado em REST, o conteúdo de base de dados de conhecimento que pode ser integrado em seu aplicativo, normalmente um chatbot de manutenção. 

## <a name="test-knowledge-base"></a>Base de dados de conhecimento de teste
Uma base de dados de conhecimento tem dois Estados - teste e publicado. A base de dados de conhecimento de teste é a versão que está a ser editado, guardada e testado para precisão e abrangência de respostas. As alterações efetuadas para a base de dados de conhecimento de teste não afetam o utilizador final da sua aplicação/chatbot.

## <a name="published-knowledge-base"></a>Base de dados de conhecimento publicados
Uma base de dados de conhecimento tem dois Estados - teste e a publicada.  A base de dados de conhecimento publicado é a versão utilizada no seu bot/aplicação de chat. A ação de publicação de uma base de dados de conhecimento coloca o conteúdo da base de dados de conhecimento de teste na versão publicada da base de dados de conhecimento. Uma vez que a base de dados de conhecimento publicado é a versão que o aplicativo utiliza através do ponto final, deve ter cuidado para garantir que o conteúdo está correto e bem testados.

## <a name="query"></a>Consulta
Uma consulta de utilizador é a questão que lhe pede o utilizador final ou um testador da base de dados de conhecimento. A consulta é muitas vezes num formato de linguagem natural ou algumas palavras-chave que representam a pergunta.

## <a name="response"></a>Resposta
A resposta é a resposta obtida a partir da base de dados de conhecimento, com base na melhor correspondência para uma consulta de utilizador especificado.

## <a name="confidence-score"></a>Pontuação de Confiança
A pontuação de confiança da resposta é um valor numérico entre 0 e 100, 100 a ser uma correspondência exata de consulta entre a consulta de utilizador e uma pergunta na base de dados de conhecimento, que serviu de resposta é a resposta correta, adequada para uma consulta de utilizador especificado. Respostas são normalmente ordenadas pela pontuação de confiança e com melhor classificação de confiança é servido como a resposta predefinida.
