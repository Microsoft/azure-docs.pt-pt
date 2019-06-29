---
title: Migrar bases de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrar uma base de dados de conhecimento necessita de exportar a partir de uma base de dados de conhecimento, em seguida, importar para outro.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 04ee592122d7c76396f091f8d249518976682004
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446597"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento através de importação-exportação

Migrar uma base de dados de conhecimento necessita de exportar a partir de uma base de dados de conhecimento, em seguida, importar para outro. 

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Configure um novo [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de dados de conhecimento do QnA Maker
1. Inicie sessão no [portal do QnA Maker](https://qnamaker.ai).
1. Selecione a base de dados de conhecimento que pretende migrar.

1. Na **definições** página, selecione **exportar a base de dados de conhecimento** transferir um ficheiro. tsv que contém os conteúdos da sua base de dados de conhecimento - perguntas, obter respostas, metadados, e a origem de dados nomes que eles eram extrair.

1. Selecione **criar uma base de dados de conhecimento** no menu superior, em seguida, criar uma base de dados de conhecimento vazia. 

    ![Origens de dados do conjunto](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Dê o seu serviço um **nome.** São suportados nomes duplicados e carateres especiais são também suportados.

1. Selecione **Criar**.

    ![Criar KB](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Nesta nova base de dados de conhecimento, abra a **configurações** separador e selecione **base de dados de conhecimento de importação**. Importa as perguntas, respostas e metadados e mantém os nomes de origem de dados do qual foram extraídos.

   ![Base de dados de conhecimento de importação](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Teste** a nova base de dados de conhecimento com o painel de teste. Saiba como [testar a sua base de dados de conhecimento](../How-To/test-knowledge-base.md).
1. **Publicar** a base de dados de conhecimento. Saiba como [publicar a sua base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Utilize o ponto de extremidade no código da aplicação ou bot. Veja aqui como [criar um bot QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Neste momento, todo o conhecimento conteúdo - perguntas, respostas e metadados, junto com os nomes dos arquivos de origem e os URLs são importados para a nova base de dados de conhecimento. 

## <a name="chat-logs-and-alterations"></a>Registos de chat ou alterações
Alterações de maiúsculas e minúsculas (sinónimos) não são importadas automaticamente. Utilize o [V4 APIs](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de dados de conhecimento.

Não é possível migrar os logs de bate-papo, uma vez que a nova base de dados de conhecimento utiliza o Application Insights para armazenar registos de chat. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
