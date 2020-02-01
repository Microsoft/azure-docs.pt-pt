---
title: Migrar bases de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrar uma base de conhecimentos requer a exportação de uma base de conhecimento, importando depois para outra.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902050"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de dados de conhecimento através de importação-exportação

Migrar uma base de conhecimentos requer a exportação de uma base de conhecimento, importando depois para outra.

## <a name="prerequisites"></a>Pré-requisitos

* Criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Criar um novo [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de conhecimento da QnA Maker
1. Inscreva-se no [portal QnA Maker](https://qnamaker.ai).
1. Selecione a base de conhecimento de origem que pretende migrar.

1. Na página **Definições,** selecione Base de **conhecimentos de Exportação** para descarregar um ficheiro .tsv que contenha o conteúdo da sua base de conhecimento de origem - perguntas, respostas, metadados, instruções de seguimento e os nomes de fonte de dados a partir dos quais foram extraídos.

1. Selecione **Criar uma base** de conhecimento a partir do menu superior e, em seguida, criar uma base de conhecimento _vazia._ Está vazio porque quando o criar, não vai adicionar urLs ou ficheiros. Estes são adicionados durante o passo de importação, após a criação.

    Configure a base de conhecimento. Desloque apenas o nome da nova base de conhecimento. São suportados nomes duplicados e carateres especiais são também suportados.

    Não selecione nada do Passo 4 porque esses valores serão substituídos quando importar o ficheiro.

1. No passo 5, selecione **Criar**.

1. Nesta nova base de conhecimentos, abra o separador **Definições** e selecione base de **conhecimentos de Importação**. Isto importa as perguntas, respostas, metadados, instruções de acompanhamento e retém os nomes de origem de dados a partir dos quais foram extraídos.

   > [!div class="mx-imgBorder"]
   > [![base de conhecimentos de importação](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de dados de conhecimento com o painel de teste. Saiba como [testar a sua base de dados de conhecimento](../How-To/test-knowledge-base.md).
1. **Publicar** a base de dados de conhecimento. Saiba como [publicar a sua base de dados de conhecimento](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Utilize o ponto final na sua aplicação ou código bot. Veja aqui como [criar um bot QnA](../Tutorials/create-qna-bot.md).

    ![Valores do QnA Maker](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Neste momento, todo o conhecimento conteúdo - perguntas, respostas e metadados, junto com os nomes dos arquivos de origem e os URLs são importados para a nova base de dados de conhecimento.

## <a name="chat-logs-and-alterations"></a>Registos e alterações de chat
Alterações insensíveis a casos (sinónimos) não são importadas automaticamente. Utilize as [APIs V4](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de conhecimentos.

Não há forma de migrar registos de chat, uma vez que a nova base de conhecimentos utiliza insights de aplicação para armazenar registos de chat.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
