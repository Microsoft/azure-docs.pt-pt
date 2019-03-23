---
title: Monte um volume de gitRepo Azure Container Instances
description: Saiba como montar um volume de gitRepo para clonar um repositório de Git para as instâncias de contentor
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/15/2018
ms.author: danlep
ms.openlocfilehash: 70593bffbf30b3a0c0978e56c2af1a856a22f2ec
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369671"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Monte um volume de gitRepo no Azure Container Instances

Saiba como montar uma *gitRepo* volume para clonar um repositório de Git para as instâncias de contentor.

> [!NOTE]
> Montar uma *gitRepo* volume está atualmente restrita para contentores do Linux. Enquanto estamos a trabalhar para colocar todas as funcionalidades de contentores do Windows, pode encontrar as diferenças da plataforma atual em [Quotas e disponibilidade das regiões do Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>gitRepo volume

O *gitRepo* volume monta um diretório e clona o repositório de Git especificado para o mesmo durante o arranque do contentor. Ao utilizar um *gitRepo* volume nas instâncias de contentor, pode evitar adicionar o código para fazê-lo em seus aplicativos.

Quando monta uma *gitRepo* volume, pode definir três propriedades para configurar o volume:

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| `repository` | Sim | O URL completo, incluindo `http://` ou `https://`, do repositório Git para ser clonado.|
| `directory` | Não | Diretório no qual o repositório deve ser clonado. O caminho não deve conter ou começar com "`..`".  Se especificar "`.`", o repositório for clonado no diretório do volume. Caso contrário, o repositório de Git for clonado num subdiretório do nome fornecido dentro do diretório de volume. |
| `revision` | Não | O hash de consolidação de revisão para ser clonado. Se não for especificado, o `HEAD` revisão for clonada. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Volume de montagem gitRepo: CLI do Azure

Para montar um volume de gitRepo ao implementar instâncias de contentor com o [CLI do Azure](/cli/azure), o fornecimento a `--gitrepo-url` e `--gitrepo-mount-path` parâmetros para o [criar contentor de az] [ az-container-create] comando. Opcionalmente, pode especificar o diretório no volume para o clone para (`--gitrepo-dir`) e o hash de consolidação de revisão para ser clonado (`--gitrepo-revision`).

Este comando de exemplo clona da Microsoft [aci-helloworld] [ aci-helloworld] exemplo de aplicação em `/mnt/aci-helloworld` na instância do contentor:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Para verificar se o volume de gitRepo foi montado, iniciar um shell no contentor com [exec de contentor az] [ az-container-exec] e listar no diretório:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Volume de montagem gitRepo: Resource Manager

Para montar um volume de gitRepo ao implementar instâncias de contentor com um [modelo Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), primeiro preencher o `volumes` matriz no grupo de contentores `properties` secção do modelo. Em seguida, para cada contentor no grupo de contentores em que pretende montar o *gitRepo* volume, preencher a `volumeMounts` obsahuje pole o `properties` secção da definição de contentor.

Por exemplo, o modelo do Resource Manager seguinte cria um grupo de contentores que consiste num único contentor. O contentor clona dois repositórios do GitHub especificados pelos *gitRepo* blocos de volume. O segundo volume inclui propriedades adicionais, especificando um diretório para clonar a e o hash de consolidação de uma revisão específica a clonagem.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretórios resultante das duas repos clonados definida no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de implementação de instância de contentor com um modelo Azure Resource Manager, consulte [implementar grupos de vários contentores no Azure Container Instances](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autenticação de repositório de Git privado

Para montar um volume de gitRepo para um repositório de Git privado, especifique as credenciais no URL do repositório. Normalmente, as credenciais estão na forma de um nome de utilizador e um token de acesso pessoal (PAT), que concede o acesso ao repositório de âmbito.

Por exemplo, a CLI do Azure `--gitrepo-url` parâmetro para um repositório privado do GitHub seria ter um aspeto semelhante ao seguinte (em que "gituser" é o nome de utilizador do GitHub, e "abcdef1234fdsa4321abcdef" é o token de acesso pessoal do usuário):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Para um repositório de Git de repositórios do Azure, especifique qualquer nome de utilizador (pode utilizar "azurereposuser" como no exemplo a seguir) em combinação com um TAPINHA válido:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Para obter mais informações sobre os tokens de acesso pessoal GitHub e transparente de repositórios do Azure, consulte o seguinte:

GitHub: [Criar um token de acesso pessoal para a linha de comandos][pat-github]

Repositórios do Azure: [Criar tokens de acesso pessoal para autenticar o acesso][pat-repos]

## <a name="next-steps"></a>Passos Seguintes

Saiba como montar outros tipos de volume no Azure Container Instances:

* [Montar uma partilha de ficheiros do Azure em instâncias de contentor do Azure](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em instâncias de contentor do Azure](container-instances-volume-emptydir.md)
* [Montar um volume secreto em instâncias de contentor do Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
