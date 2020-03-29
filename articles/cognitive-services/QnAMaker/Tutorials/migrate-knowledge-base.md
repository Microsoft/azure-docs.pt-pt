---
title: Migrar bases de conhecimento - QnA Maker
description: Migrar uma base de conhecimentos requer a exportação de uma base de conhecimento, importando depois para outra.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258095"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrar uma base de conhecimentos utilizando a importação de exportação

A migração é o processo de criação de uma nova base de conhecimento a partir de uma base de conhecimento existente. Pode fazê-lo por várias razões:

* backup e restaurar processo
* Gasoduto CI/CD
* mover regiões

A migração de uma base de conhecimentos requer a exportação de uma base de conhecimentos existente, importando depois para outra.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Criar um novo [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrar uma base de conhecimento da QnA Maker
1. Inscreva-se no [portal QnA Maker](https://qnamaker.ai).
1. Selecione a base de conhecimento de origem que pretende migrar.

1. Na página **Definições,** selecione Base de **conhecimentos de Exportação** para descarregar um ficheiro .tsv que contenha o conteúdo da sua base de conhecimento de origem - perguntas, respostas, metadados, instruções de seguimento e os nomes de fonte de dados a partir dos quais foram extraídos.

1. Selecione **Criar uma base** de conhecimento a partir do menu superior e, em seguida, criar uma base de conhecimento _vazia._ Está vazio porque quando o criar, não vai adicionar urLs ou ficheiros. Estes são adicionados durante o passo de importação, após a criação.

    Configure a base de conhecimento. Desloque apenas o nome da nova base de conhecimento. Os nomes duplicados são suportados e os caracteres especiais também são suportados.

    Não selecione nada do Passo 4 porque esses valores serão substituídos quando importar o ficheiro.

1. No passo 5, selecione **Criar**.

1. Nesta nova base de conhecimentos, abra o separador **Definições** e selecione base de **conhecimentos de Importação**. Isto importa as perguntas, respostas, metadados, instruções de acompanhamento e retém os nomes de origem de dados a partir dos quais foram extraídos.

   > [!div class="mx-imgBorder"]
   > [![Base de conhecimentos de importação](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Teste** a nova base de conhecimentos utilizando o painel de teste. Aprenda a testar a [sua base de conhecimento.](../How-To/test-knowledge-base.md)

1. **Publique** a base de conhecimento e crie um chat bot. Aprenda a publicar a sua base de [conhecimento.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Migrar programáticamente uma base de conhecimento da QnA Maker

O processo de migração está disponível programáticamente utilizando as seguintes APIs REST:

**Exportar**

* [Descarregue a base de conhecimentos API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Importar**

* [Substitua a API (recarregar por o mesmo ID base de conhecimento)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Criar API (carga com novo ID base de conhecimento)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Registos e alterações de chat
Alterações insensíveis a casos (sinónimos) não são importadas automaticamente. Utilize as [APIs V4](https://go.microsoft.com/fwlink/?linkid=2092179) para mover as alterações na nova base de conhecimentos.

Não há forma de migrar registos de chat, uma vez que a nova base de conhecimentos utiliza insights de aplicação para armazenar registos de chat.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Editar uma base de dados de conhecimento](../How-To/edit-knowledge-base.md)
