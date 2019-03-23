---
title: Monte um volume secreto no Azure Container Instances
description: Saiba como montar um volume secreto para armazenar informações confidenciais para o acesso pelas instâncias de contentor
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 3c1c83bb0c3e46a7eaab519050d9c556e2cc1a7a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372289"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Monte um volume secreto no Azure Container Instances

Utilize um *segredo* volume para fornecer informações confidenciais para os contentores num grupo de contentor. O *segredo* volume armazena seus segredos em ficheiros no volume, acessível para os contentores no grupo de contentores. Ao armazenar segredos num *segredo* volume, pode evitar a adição de dados confidenciais, como chaves SSH ou as credenciais da base de dados para o código da aplicação.

Todos os *segredo* volumes são apoiados por [tmpfs][tmpfs], um sistema de ficheiros com cópia de segurança em RAM; seu conteúdo nunca é escrito no armazenamento de não-volátil.

> [!NOTE]
> *Segredo* volumes estão atualmente restritos para contentores do Linux. Saiba como passar variáveis de ambiente seguro para contentores do Windows e Linux no [definir variáveis de ambiente](container-instances-environment-variables.md). Enquanto estamos a trabalhar para colocar todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças de plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Montar o volume secreto - CLI do Azure

Para implementar um contentor com um ou mais segredos com a CLI do Azure, inclua o `--secrets` e `--secrets-mount-path` parâmetros nos [criar o contentor de az] [ az-container-create] comando. Neste exemplo monta um *segredo* volume formada por dois segredos, "mysecret1" e "mysecret2," em `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

O seguinte procedimento [exec de contentor az] [ az-container-exec] saída mostra a abertura de um shell no contentor em execução, listando os ficheiros no volume SECRETO, em seguida, exibir seu conteúdo:

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

## <a name="mount-secret-volume---yaml"></a>Montar o volume secreto - YAML

Também pode implementar grupos de contentores com a CLI do Azure e um [modelo YAML](container-instances-multi-container-yaml.md). Implantando com o modelo YAML é o método preferencial quando implementar grupos de contentores que consiste de vários contentores.

Ao implementar com um modelo YAML, os valores secretos tem de ser **codificada em Base64** no modelo. No entanto, os valores secretos são apresentados em texto não criptografado dentro os ficheiros no contentor.

O modelo YAML seguinte define um grupo de contentores com um contentor que monta um *segredo* volume em `/mnt/secrets`. O volume secreto tem dois segredos, "mysecret1" e "mysecret2."

```yaml
apiVersion: '2018-06-01'
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

Para implementar com o modelo YAML, guardar o YAML anterior num ficheiro denominado `deploy-aci.yaml`, em seguida, execute o [criar contentor de az] [ az-container-create] comando com o `--file` parâmetro:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montar o volume secreto - Resource Manager

Além de uma implementação da CLI e YAML, pode implementar um grupo de contentor através do Azure [modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Em primeiro lugar, preencher a `volumes` matriz no grupo de contentores `properties` secção do modelo. Ao implementar com um modelo do Resource Manager, os valores secretos tem de ser **codificada em Base64** no modelo. No entanto, os valores secretos são apresentados em texto não criptografado dentro os ficheiros no contentor.

Em seguida, para cada contentor no grupo de contentores em que pretende montar o *segredo* volume, preencher a `volumeMounts` obsahuje pole o `properties` secção da definição de contentor.

O modelo do Resource Manager seguinte define um grupo de contentores com um contentor que monta um *segredo* volume em `/mnt/secrets`. O volume secreto tem dois segredos, "mysecret1" e "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implementar com o modelo do Resource Manager, guardar o JSON anterior num ficheiro denominado `deploy-aci.json`, em seguida, execute o [criar a implementação do grupo az] [ az-group-deployment-create] comando com o `--template-file` parâmetro:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Passos Seguintes

### <a name="volumes"></a>Volumes

Saiba como montar outros tipos de volume no Azure Container Instances:

* [Montar uma partilha de ficheiros do Azure em instâncias de contentor do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em instâncias de contentor do Azure](container-instances-volume-emptydir.md)
* [Monte um volume de gitRepo no Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variáveis de ambiente seguro

Outro método para fornecer informações confidenciais para contentores (incluindo contentores do Windows) é através da utilização de [variáveis de ambiente de proteger](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
