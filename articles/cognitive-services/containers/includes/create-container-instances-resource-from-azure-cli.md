---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de instância de contentores Azure a partir do Azure CLI.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875140"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Criar um recurso de instância de contentor estoque Azure do Azure CLI

O YAML abaixo define o recurso Azure Container Instance. Copie e cole o conteúdo num `my-aci.yaml` novo ficheiro, nomeado e substitua os valores comentados pelos seus. Consulte o [formato][template-format] do modelo para yAML válido. Consulte os [repositórios e imagens][repositories-and-images] do recipiente para os nomes de imagem disponíveis e o seu repositório correspondente. Para obter mais informações sobre a referência YAML para instâncias de contentores, consulte a [referência YAML: Instâncias de contentores Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
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
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
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
> Nem todos os locais têm a mesma disponibilidade de CPU e Memória. Consulte a tabela de [localização e recursos][location-to-resource] para a listagem de recursos disponíveis para contentores por localização e SISTEMA.

Vamos confiar no ficheiro YAML que [`az container create`][azure-container-create] criámos para o comando. A partir do Azure `az container create` CLI, `<resource-group>` execute o comando substituindo o seu. Adicionalmente, para assegurar valores dentro de uma implementação YAML, consulte [valores seguros][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do `Running...` comando é válida, depois de algum dia a saída muda para uma cadeia JSON que representa o recurso ACI recém-criado. A imagem do recipiente é mais do que provável que não esteja disponível por um tempo, mas o recurso está agora implantado.

> [!TIP]
> Preste muita atenção aos locais de pré-visualização pública do Serviço Cognitivo Azure, uma vez que o YAML terá de ser ajustado em conformidade para corresponder à localização.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
