---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure no CLI do Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689420"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Criar um recurso de instância de contêiner do Azure no CLI do Azure

O YAML abaixo define o recurso de instância de contêiner do Azure. Copie e cole o conteúdo em um novo arquivo, chamado `my-aci.yaml` e substitua os valores comentados pelos seus próprios. Consulte o [formato de modelo][template-format] para YAML válido. Consulte os [repositórios e as imagens do contêiner][repositories-and-images] para obter os nomes de imagem disponíveis e seu repositório correspondente. Para obter mais informações sobre a referência de YAML para instâncias de contêiner, consulte [referência do YAML: instâncias de contêiner do Azure][aci-yaml-ref].

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
> Nem todos os locais têm a mesma disponibilidade de CPU e memória. Consulte a tabela [local e recursos][location-to-resource] para obter a lista de recursos disponíveis para contêineres por local e so.

Vamos contar com o arquivo YAML que criamos para o comando [`az container create`][azure-container-create] . No CLI do Azure, execute o comando `az container create` substituindo o `<resource-group>` pelo seu próprio. Além disso, para proteger valores em uma implantação do YAML, consulte [valores seguros][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

A saída do comando é `Running...` se for válida, depois de algum tempo a saída é alterada para uma cadeia de caracteres JSON que representa o recurso ACI recém-criado. A imagem de contêiner está mais do que provavelmente não está disponível por um tempo, mas o recurso agora está implantado.

> [!TIP]
> Preste muita atenção aos locais de ofertas de serviço cognitiva do Azure de visualização pública, pois o YAML precisará ser ajustado adequadamente para corresponder ao local.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
