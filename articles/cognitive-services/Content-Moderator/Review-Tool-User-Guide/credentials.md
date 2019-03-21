---
title: Gerir credenciais no Azure Content Moderator - Content Moderator
titlesuffix: Azure Cognitive Services
description: Gerir as credenciais do Content Moderator, que terá de utilizar as APIs.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0da6b6b0fef0f998e20789253b2a65c54121532c
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260014"
---
# <a name="manage-content-moderator-service-credentials"></a>Gerir credenciais de serviço do Content Moderator

As suas credenciais do Content Moderator são criados nas seguintes localizações:

- [O portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [A ferramenta de revisão do Content Moderator](https://contentmoderator.cognitive.microsoft.com/)

Este artigo explica onde encontrá-los e como se relacionam entre si.

## <a name="the-azure-portal"></a>O portal do Azure

No dashboard do portal do Azure, selecione a sua conta do Content Moderator. Sob **gestão de recursos**, selecione **chaves**. Para copiar a chave, selecione o ícone à direita da chave.

![Chaves de moderador de conteúdo no portal do Azure](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Utilizar a conta do Azure com a ferramenta de revisão e reveja a API
Para utilizar a sua chave do Azure com as APIs de revisão, copie o ID de recurso listados no **propriedades** ecrã na captura de ecrã seguinte e introduza-o no ecrã de credenciais a ferramenta de revisão no **IDs do recurso na lista de permissões** campos, conforme mostrado a seguir **ID do recurso** secção. 

> [!NOTE]
> Região do sua assinatura Content Moderator deve corresponder à região da equipa de revisão para que ele reconhece a sua equipa e acessar os dados de equipa. Por exemplo, nas imagens nesta página, o **E.U.A. oeste** região **(4)** contém a subscrição do Azure de moderador conteúdo e a sua equipa de revisão.
>
> Depois de substituir os dois locais na ferramenta de revisão com a chave e o ID de recurso da sua subscrição do Azure, sua **Ocp-Apim-Subscription-Key de versão de avaliação** apresentados nas credenciais ecrã não é mais usado, mas está sempre disponível.
> A chave de avaliação limita máximo de 5000 transações por mês, 1 pedido por segundo (RPS).

![ID de recurso de moderador de conteúdo no portal do Azure](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Utilizar a conta do Azure com os fluxos de trabalho na ferramenta de revisão

Para utilizar a chave do Azure para os fluxos de trabalho disponíveis dentro do Content Moderator, introduza-a na **Ocp-Apim-Subscription-Key** campo na **definições de fluxo de trabalho** secção conforme mostrado na seguinte  **Fluxos de trabalho** secção. Acertar a **'+'** para guardar o seu ID de recurso.

![As credenciais de fluxo de trabalho do moderador na ferramenta de revisão de conteúdo](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>A ferramenta de revisão

A análise ferramenta Dashboard, no **configurações** separador, selecione **credenciais**.

![Credenciais de moderador de conteúdo na ferramenta de revisão](images/credentials-trial-resource-workflow.PNG)

A secção seguinte examina a imagem anterior mais detalhadamente:

### <a name="api"></a>API

As listas de parte primeiro sua **reveja o ponto final de API**, **ID da equipa**e o **Ocp-Apim-Subscription-Key (chave de avaliação do Content Moderator)** gerado como parte da sua equipa de revisão criação. Utilize-os para chamar a todas as APIs de moderador conteúdo, incluindo a API de revisão.

Observe também o identificador de região para o ponto final de API. Por exemplo, **westus** é a região em "https:\//westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0"

![Chave de moderador de conteúdo na ferramenta de revisão](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>ID do Recurso

Este conjunto de campos é abordado na seção anterior, [utilizar a conta do Azure com a ferramenta de revisão e a API de](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api). Este campo é normalmente em branco, a menos que adicione o Id de recurso do Azure para este campo, conforme explicado na secção anterior.

### <a name="workflows"></a>Fluxos de trabalho

Este conjunto de campos é abordado na seção anterior, [utilizar a conta do Azure com os fluxos de trabalho na ferramenta de revisão](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Por predefinição, a ferramenta de revisão utiliza a respetiva chave de avaliação gerado automaticamente para executar os fluxos de trabalho e que é o que aparece para começar. Os outros dois campos permitem com listas de prazo e a imagem nas operações de texto do ecrã e avaliar a imagem, respetivamente.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como utilizar as credenciais do Content Moderator no seu [fluxos de trabalho](workflows.md).
