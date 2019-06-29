---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância (ACI) do contentor do azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455062"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Criar um recurso de instância de contentor do Azure (ACI)

1. Vá para o [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) página do Container Instances.

2. Sobre o **Noções básicas** separador, introduza os seguintes detalhes:

    |Definição|Valor|
    |--|--|
    |Subscrição|Selecione a sua subscrição.|
    |Grupo de recursos|Selecione o grupo de recursos disponíveis ou criar um novo como `cognitive-services`.|
    |Nome do contentor|Introduza um nome como `cognitive-container-instance`. Este nome tem de estar no limites inferiores.|
    |Location|Selecione uma região para a implementação.|
    |Tipo de imagem|`Public`|
    |Nome da imagem|Introduza a localização do contentor de serviços cognitivos. Isso pode ser a mesma localização que utilizou no `docker pull` comando, _por exemplo_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Tipo de SO|`Linux`|
    |Size|Alterar o tamanho para as recomendações sugeridas para o seu contentor específico do serviço cognitivo.:<br>2 núcleos<br>4GB

3. Sobre o **redes** separador, introduza os seguintes detalhes:

    |Definição|Value|
    |--|--|
    |Portas|Defina a porta TCP para `5000`. Expõe o contentor na porta 5000.|

4. Sobre o **avançadas** separador, introduza os seguintes detalhes para passar o contentor [faturas definições necessárias](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) para o recurso ACI:

    |Chave de página Avançadas|Valor de página Avançadas|
    |--|--|
    |`apikey`|Copiado do **chaves** página do recurso de análise de texto. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiado do **descrição geral** página do recurso de análise de texto. Exemplo: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Clique em **rever e criar**
1. Depois de passar a validação, clique em **criar** para concluir o processo de criação
1. Quando o recurso está implementado com êxito, está pronta a utilizar