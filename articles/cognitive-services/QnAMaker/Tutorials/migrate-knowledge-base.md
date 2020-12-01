---
title: Bases de conhecimento migram - QnA Maker
description: Migrar uma base de conhecimento requer exportar de uma base de conhecimento, e depois importar para outra.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: c89ab375cb02824a08ff57e6b5278dd9299126ff
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350930"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de conhecimentos utilizando a importação de exportação

A migração é o processo de criação de uma nova base de conhecimento a partir de uma base de conhecimento existente. Pode fazê-lo por várias razões:

* processo de backup e restauro
* Pipeline de CI/CD
* mover regiões

Migrar uma base de conhecimento requer exportar de uma base de conhecimento existente, importando depois para outra.

> [!NOTE]
> Siga as instruções abaixo para migrar a sua base de conhecimento existente para um novo QnA Maker gerido (Preview).

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.
* Configurar um novo [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de conhecimento da QnA Maker
1. Inscreva-se no [portal QnA Maker](https://qnamaker.ai).
1. Selecione a base de conhecimento de origem que pretende migrar.

1. Na página **Definições,** selecione **Base de conhecimentos de exportação** para descarregar um ficheiro .tsv que contenha o conteúdo da sua base de conhecimentos de origem - perguntas, respostas, metadados, pedidos de seguimento e os nomes de origem de dados a partir dos quais foram extraídos. Os IDs QnA que são exportados com as perguntas e respostas podem ser usados para atualizar um par QnA específico usando a [API de atualização](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). O QnA ID para um par QnA específico permanece inalterado em várias operações de exportação.

1. Selecione **Criar uma base** de conhecimento a partir do menu superior e, em seguida, criar uma base de conhecimento _vazia._ Está vazio porque quando o crias, não vais adicionar URLs ou ficheiros. Estes são adicionados durante o passo de importação, após a criação.

    Configurar a base de conhecimento. Desa estalhe o novo nome base de conhecimento apenas. Os nomes duplicados são suportados e os caracteres especiais também são suportados.

    Não selecione nada do Passo 4 porque esses valores serão substituídos quando importar o ficheiro.

1. No passo 5, **selecione Criar**.

1. Nesta nova base de conhecimento, abra o **separador Definições** e selecione **a base de conhecimentos de importação.** Isto importa as perguntas, respostas, metadados, pedidos de seguimento e retém os nomes de origem de dados a partir dos quais foram extraídos. **Os pares QnA criados na nova base de conhecimentos devem ter o mesmo QnA ID presente no ficheiro exportado.** Isto ajuda-o a criar uma réplica exata da base de conhecimento.

   > [!div class="mx-imgBorder"]
   > [![Base de conhecimentos de importação](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de conhecimento utilizando o painel de teste. Saiba como testar a [sua base de conhecimentos.](../How-To/test-knowledge-base.md)

1. **Publique** a base de conhecimento e crie um chat bot. Saiba como publicar a [sua base de conhecimentos.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migrar programáticamente uma base de conhecimento da QnA Maker

O processo de migração está programáticamente disponível utilizando as seguintes APIs REST:

**Exportar**

* [Baixar base de conhecimento API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**Importar**

* [Substituir a API (recarregar com o mesmo ID de base de conhecimento)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [Criar API (carregar com novo ID de base de conhecimento)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Registos de chat e alterações
Alterações insensíveis a casos (sinónimos) não são importadas automaticamente. Utilize as [APIs V4](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) para mover as alterações na nova base de conhecimento.

Não há forma de migrar registos de chat, uma vez que a nova base de conhecimento utiliza Insights de Aplicação para armazenar registos de chat.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)