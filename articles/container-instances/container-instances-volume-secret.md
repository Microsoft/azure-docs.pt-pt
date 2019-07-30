---
title: Montar um volume secreto em instâncias de contêiner do Azure
description: Saiba como montar um volume secreto para armazenar informações confidenciais para acesso por suas instâncias de contêiner
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 2e96ef73c3ff89fd7941fa14a8a1e53e6d4d8593
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325430"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montar um volume secreto em instâncias de contêiner do Azure

Use um volume *secreto* para fornecer informações confidenciais aos contêineres em um grupo de contêineres. O volume *secreto* armazena seus segredos em arquivos dentro do volume, acessíveis pelos contêineres no grupo de contêineres. Ao armazenar segredos em um volume *secreto* , você pode evitar a adição de dados confidenciais, como chaves SSH ou credenciais de banco para o código do aplicativo.

Todos os volumes secretos são apoiados pelo [tmpfs][tmpfs], um sistema de arquivos com suporte de RAM; seu conteúdo nunca é gravado no armazenamento não volátil.

> [!NOTE]
> Os volumes secretos estão atualmente restritos a contêineres do Linux. Saiba como passar variáveis de ambiente seguras para contêineres do Windows e do Linux em [definir variáveis de ambiente](container-instances-environment-variables.md). Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montar volume secreto-CLI do Azure

Para implantar um contêiner com um ou mais segredos usando o CLI do Azure, inclua os `--secrets` parâmetros e `--secrets-mount-path` no comando [AZ container Create][az-container-create] . Este exemplo monta um volume *secreto* que consiste em dois segredos, "mysecret1" e "mysecret2", em `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A saída do seguinte [AZ container exec][az-container-exec] mostra a abertura de um shell no contêiner em execução, listando os arquivos dentro do volume secreto e exibindo seu conteúdo:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montar volume secreto-YAML

Você também pode implantar grupos de contêineres com o CLI do Azure e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação do modelo YAML é o método preferencial ao implantar grupos de contêineres que consistem em vários contêineres.

Quando você implanta com um modelo YAML, os valores secretos devem ser codificados em **Base64** no modelo. No entanto, os valores secretos aparecem em texto não criptografado dentro dos arquivos no contêiner.

O modelo YAML a seguir define um grupo de contêineres com um contêiner que monta um volume `/mnt/secrets`secreto em. O volume secreto tem dois segredos, "mysecret1" e "mysecret2".

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implantar com o modelo YAML, salve o YAML anterior em um arquivo chamado `deploy-aci.yaml`e execute o comando [AZ container Create][az-container-create] com o `--file` parâmetro:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montar volume secreto-Gerenciador de recursos

Além da implantação da CLI e do YAML, você pode implantar um grupo de contêineres usando um modelo do Azure [Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Primeiro, preencha a `volumes` matriz na seção grupo `properties` de contêineres do modelo. Quando você implanta com um modelo do Resource Manager, os valores secretos devem ser codificados em **Base64** no modelo. No entanto, os valores secretos aparecem em texto não criptografado dentro dos arquivos no contêiner.

Em seguida, para cada contêiner no grupo de contêineres no qual você deseja montar o volume *secreto* , preencha a `volumeMounts` matriz na `properties` seção da definição do contêiner.

O modelo do Resource Manager a seguir define um grupo de contêineres com um contêiner que monta um `/mnt/secrets`volume secreto em. O volume secreto tem dois segredos, "mysecret1" e "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implantar com o modelo do Resource Manager, salve o JSON anterior em um arquivo `deploy-aci.json`chamado e execute o comando [AZ Group Deployment Create][az-group-deployment-create] com o `--template-file` parâmetro:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Passos Seguintes

### <a name="volumes"></a>Volumes

Saiba como montar outros tipos de volume em instâncias de contêiner do Azure:

* [Montar uma partilha de ficheiros do Azure em instâncias de contentor do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em instâncias de contentor do Azure](container-instances-volume-emptydir.md)
* [Montar um volume gitRepo em instâncias de contêiner do Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variáveis de ambiente seguras

Outro método para fornecer informações confidenciais aos contêineres (incluindo contêineres do Windows) é por meio do uso de [variáveis de ambiente seguras](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
