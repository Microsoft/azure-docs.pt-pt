---
title: Monte volume secreto para grupo de contentores
description: Saiba como montar um volume secreto para armazenar informações confidenciais para acesso pelos seus casos de contentores
ms.topic: article
ms.date: 07/19/2018
ms.openlocfilehash: 913e3d147519bc73c3c57b8da383f9d373f3666d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249939"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Monte um volume secreto em instâncias de contentores azure

Utilize um volume *secreto* para fornecer informações sensíveis aos contentores num grupo de contentores. O volume *secreto* armazena os seus segredos em ficheiros dentro do volume, acessíveis pelos contentores do grupo de contentores. Ao armazenar segredos num volume *secreto,* pode evitar adicionar dados sensíveis, como chaves SSH ou credenciais de base de dados ao seu código de aplicação.

Todos os volumes *secretos* são apoiados por [tmpfs][tmpfs], um sistema de ficheiros apoiado por RAM; os seus conteúdos nunca são escritos para armazenamento não volátil.

> [!NOTE]
> Os volumes *secretos* estão atualmente restritos aos contentores Linux. Aprenda a passar variáveis ambientais seguras para os recipientes Windows e Linux em [variáveis ambiente definidos](container-instances-environment-variables.md). Enquanto estamos a trabalhar para trazer todas as funcionalidades para os recipientes windows, pode encontrar as diferenças de plataforma atuais na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Monte volume secreto - Azure CLI

Para implantar um recipiente com um ou mais segredos `--secrets` utilizando `--secrets-mount-path` o CLI Azure, inclua os e parâmetros no [recipiente az criar][az-container-create] comando. Este exemplo monta um volume *secreto* composto por dois segredos, "mysecret1" e "mysecret2", em: `/mnt/secrets`

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A saída [de exec do contentor az][az-container-exec] az mostra a abertura de uma concha no recipiente de funcionamento, listando os ficheiros dentro do volume secreto e, em seguida, exibindo o seu conteúdo:

```azurecli
az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
```

```output
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

## <a name="mount-secret-volume---yaml"></a>Monte volume secreto - YAML

Também pode implantar grupos de contentores com o Azure CLI e um [modelo YAML](container-instances-multi-container-yaml.md). A implantação por modelo YAML é o método preferido para a implantação de grupos de contentores compostos por vários contentores.

Quando se implanta com um modelo YAML, os valores secretos devem estar **codificados** no modelo. No entanto, os valores secretos aparecem em texto simples dentro dos ficheiros do recipiente.

O seguinte modelo YAML define um grupo de *secret* contentores `/mnt/secrets`com um recipiente que monta um volume secreto em . O volume secreto tem dois segredos, "mysecret1" e "mysecret2".

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

Para implantar com o modelo YAML, guarde o `deploy-aci.yaml`YAML anterior para um ficheiro `--file` chamado e, em seguida, execute o [recipiente az criar][az-container-create] comando com o parâmetro:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Monte volume secreto - Gestor de Recursos

Além da implantação cli e YAML, pode implantar um grupo de contentores utilizando um modelo de Gestor de [Recursos](/azure/templates/microsoft.containerinstance/containergroups)Azure .

Primeiro, povoe a `volumes` matriz na secção do grupo `properties` de contentores do modelo. Quando se implanta com um modelo de Gestor de Recursos, os valores secretos devem ser codificados pelo **Base64** no modelo. No entanto, os valores secretos aparecem em texto simples dentro dos ficheiros do recipiente.

Em seguida, para cada recipiente do grupo de contentores em que `volumeMounts` gostaria `properties` de montar o volume *secreto,* povoe a matriz na secção da definição do recipiente.

O seguinte modelo de Gestor de Recursos define um *secret* grupo `/mnt/secrets`de contentores com um recipiente que monta um volume secreto em . O volume secreto tem dois segredos, "mysecret1" e "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implantar com o modelo de Gestor de Recursos, `deploy-aci.json`guarde o JSON anterior para `--template-file` um ficheiro nomeado, em seguida, execute a implementação do [grupo Az criar][az-group-deployment-create] comando com o parâmetro:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Passos seguintes

### <a name="volumes"></a>Volumes

Saiba montar outros tipos de volume em instâncias de contentores azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume dir vazio em instâncias de contentores azure](container-instances-volume-emptydir.md)
* [Monte um volume gitRepo em instâncias de contentores azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variáveis ambientais seguras

Outro método para fornecer informações sensíveis aos contentores (incluindo recipientes Windows) é através da utilização de [variáveis ambientais seguras](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
