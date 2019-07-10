---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contentor do Azure a partir da CLI do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711738"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Criar um recurso de instância de contentor do Azure a partir da CLI do Azure

YAML abaixo define o recurso de instância de contentor do Azure. Copie e cole o conteúdo num novo arquivo, com o nome `my-aci.yaml` e substitua os valores de comentado com os seus próprios. Consulte o [formato de modelo] [modelo-formant] para YAML válido. Consulte a [imagens e repositórios de contentor][repositories-and-images] para nomes de imagens disponíveis e o respetivo repositório correspondente.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nem todas as localizações têm a mesmo disponibilidade de CPU e memória. Consulte a [recursos e localização][location-to-resource] tabela para a listagem de recursos disponíveis para contentores por localização e o sistema operacional.

Vamos utilizar o ficheiro YAML criadas para o [ `az container create` ][azure-container-create] comando. Na CLI do Azure, execute o `az container create` comando substituindo o `<resource-group>` com os seus próprios. Além disso, para proteger a valores dentro de um YAML implementação consultar [secure valores][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do comando é `Running...` se válidos, após algum tempo a saída é alterado para uma cadeia de caracteres do JSON que representa o recurso ACI recém-criado. A imagem de contentor é mais do que provável que não está disponível há algum tempo, mas o recurso agora está implementado.

> [!TIP]
> Preste muita atenção para as localizações de ofertas de serviços cognitivos do Azure de pré-visualização pública, conforme o YAML será necessário para ser ajustado em conformidade, de acordo com a localização.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values