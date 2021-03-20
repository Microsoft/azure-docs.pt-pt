---
title: Monte o volume secreto para o grupo de contentores
description: Saiba como montar um volume secreto para armazenar informações sensíveis para acesso através de instâncias de contentores
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: ea82ba5994feaf102d4622eada284df431e004d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169566"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Monte um volume secreto em Instâncias de Contentores Azure

Utilize um volume *secreto* para fornecer informações sensíveis aos recipientes de um grupo de contentores. O volume *secreto* armazena os seus segredos em ficheiros dentro do volume, acessível pelos contentores do grupo de contentores. Ao armazenar segredos num volume *secreto,* pode evitar adicionar dados sensíveis como chaves SSH ou credenciais de base de dados ao seu código de aplicação.

* Uma vez implantados com segredos num grupo de contentores, um volume secreto é *apenas lido*.
* Todos os volumes secretos são apoiados por [tmpfs][tmpfs], um sistema de ficheiros apoiado pela RAM; o seu conteúdo nunca é escrito para armazenamento não volátil.

> [!NOTE]
> Os volumes *secretos* estão atualmente restritos aos contentores Linux. Saiba como passar variáveis ambientais seguras para os recipientes Windows e Linux em [variáveis ambiente definidos](container-instances-environment-variables.md). Enquanto estamos a trabalhar para trazer todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças da plataforma atuais na [visão geral.](container-instances-overview.md#linux-and-windows-containers)

## <a name="mount-secret-volume---azure-cli"></a>Monte volume secreto - Azure CLI

Para implantar um recipiente com um ou mais segredos utilizando o CLI Azure, inclua os `--secrets` parâmetros e `--secrets-mount-path` parâmetros no [contentor az criar][az-container-create] comando. Este exemplo monta um volume *secreto* composto por dois ficheiros contendo segredos, "mysecret1" e "mysecret2", `/mnt/secrets` em:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A seguinte saída [do executivo do contentor az][az-container-exec] mostra a abertura de uma concha no recipiente de funcionamento, listando os ficheiros dentro do volume secreto e, em seguida, mostrando o seu conteúdo:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Monte volume secreto - YAML

Também pode implantar grupos de contentores com o CLI Azure e um [modelo YAML.](container-instances-multi-container-yaml.md) A implantação pelo modelo YAML é o método preferido ao implantar grupos de contentores constituídos por vários contentores.

Quando implementar com um modelo YAML, os valores secretos devem estar codificados na **Base64** no modelo. No entanto, os valores secretos aparecem em texto simples dentro dos ficheiros do recipiente.

O modelo YAML que se segue define um grupo de contentores com um recipiente que monta um volume *secreto* em `/mnt/secrets` . O volume secreto tem dois ficheiros que contêm segredos, "mysecret1" e "mysecret2".

```yaml
apiVersion: '2019-12-01'
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

Para implementar com o modelo YAML, guarde o YAML anterior para um ficheiro `deploy-aci.yaml` nomeado, em seguida, execute o [contentor az criar][az-container-create] comando com o `--file` parâmetro:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Monte volume secreto - Gestor de Recursos

Além da implantação de CLI e YAML, pode implantar um grupo de contentores utilizando um [modelo de Gestor de Recursos](/azure/templates/microsoft.containerinstance/containergroups)Azure .

Primeiro, povoe a `volumes` matriz na secção do grupo de contentores do `properties` gabarito. Quando implementar com um modelo de Gestor de Recursos, os valores secretos devem estar codificados na **Base64** no modelo. No entanto, os valores secretos aparecem em texto simples dentro dos ficheiros do recipiente.

Em seguida, para cada recipiente no grupo de contentores no qual pretende montar o volume *secreto,* povoe a `volumeMounts` matriz na secção da `properties` definição do recipiente.

O modelo seguinte do Gestor de Recursos define um grupo de contentores com um recipiente que monta um volume *secreto* em `/mnt/secrets` . O volume secreto tem dois segredos, "mysecret1" e "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implementar com o modelo de Gestor de Recursos, guarde o JSON anterior para um ficheiro `deploy-aci.json` nomeado, em seguida, execute o [grupo de implementação az criar][az-deployment-group-create] comando com o `--template-file` parâmetro:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Passos seguintes

### <a name="volumes"></a>Volumes

Saiba como montar outros tipos de volume em Instâncias de Contentores Azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume deDir vazio em instâncias de contentores Azure](container-instances-volume-emptydir.md)
* [Monte um volume gitRepo em Instâncias de Contentores Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variáveis ambientais seguras

Outro método para fornecer informações sensíveis aos contentores (incluindo recipientes windows) é através da utilização de [variáveis ambientais seguras](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
