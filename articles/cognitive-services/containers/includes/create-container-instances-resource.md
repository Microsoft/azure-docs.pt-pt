---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contentor do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229266"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso de instância de contentor do Azure

1. Vá para o [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) página do Container Instances.

2. Sobre o **Noções básicas** separador, introduza os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscription|Selecione a sua subscrição.|
    |Resource group|Selecione o grupo de recursos disponíveis ou criar um novo como `cognitive-services`.|
    |Nome do contentor|Introduza um nome como `cognitive-container-instance`. O nome tem de estar no limites inferiores.|
    |Location|Selecione uma região para a implementação.|
    |Tipo de imagem|`Public`|
    |Nome da imagem|Introduza a localização do contentor de serviços cognitivos. A localização pode ser igual ao utilizado no `docker pull` comando este problema, consulte a [imagens e repositórios de contentor](../../cognitive-services-container-support.md#container-repositories-and-images) para nomes de imagens disponíveis e o respetivo repositório correspondente.|
    |Tipo de SO|`Linux`|
    |Size|Alterar o tamanho para as recomendações sugeridas para o contentor de serviço cognitivo específico:<br>2 núcleos de CPU<br>4GB

3. Sobre o **redes** separador, introduza os seguintes detalhes:

    |Definição|Value|
    |--|--|
    |Portas|Defina a porta TCP para `5000`. Expõe o contentor na porta 5000.|

4. Sobre o **avançadas** separador, introduza o necessário **variáveis de ambiente** para o contentor de definições do recurso ACI de faturação:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado do **chaves** página do recurso. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado do **descrição geral** página do recurso.|
    |`eula`|`accept`|

1. Clique em **rever e criar**
1. Depois de passar a validação, clique em **criar** para concluir o processo de criação
1. Quando o recurso está implementado com êxito, está pronto