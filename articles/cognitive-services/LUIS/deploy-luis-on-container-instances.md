---
title: Colocar o contentor LUIS em instâncias do contentor Azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente LUIS para uma instância do Recipiente Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: edc2ad0f895b8a1bb6448ce1cdf79b1b2ce83951
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95997205"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implementar o recipiente de compreensão linguística (LUIS) para instâncias do contentor Azure

Saiba como implantar o recipiente [Luis](luis-container-howto.md) dos Serviços Cognitivos para [as instâncias do Contentor](../../container-instances/index.yml)Azure . Este procedimento demonstra a criação de um recurso de detetor de anomalias. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

O recipiente LUIS requer um `.gz` ficheiro modelo que é puxado para dentro em tempo de execução. O recipiente deve poder aceder a este ficheiro modelo através de um suporte de volume da instância do Recipiente. Para obter informações sobre a criação de uma partilha de ficheiros Azure, consulte [criar uma partilha de ficheiros](../../storage/files/storage-how-to-create-file-share.md). Tome nota do nome da conta, chave e nome da partilha de ficheiros Azure, pois mais tarde necessitará deles.

### <a name="export-and-upload-packaged-luis-app"></a>App LUIS embalada de exportação e upload

Para fazer o upload do modelo LUIS (app embalado) para a partilha de ficheiros Azure, terá de <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">exportá-lo primeiro <span class="docon docon-navigate-external x-hidden-focus"></span> a partir do portal LUIS. </a> A partir do portal Azure, navegue para a página **geral** do recurso da conta de armazenamento e selecione **ações de ficheiros.** Selecione o nome de partilha de ficheiros que criou recentemente e, em seguida, selecione o botão **Upload.**

> [!div class="mx-imgBorder"]
> ![Upload para partilha de ficheiros](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Faça o upload do ficheiro modelo LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]