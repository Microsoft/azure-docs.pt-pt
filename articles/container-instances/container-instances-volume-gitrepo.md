---
title: Montar o volume gitRepo para o grupo de contêineres
description: Saiba como montar um volume gitRepo para clonar um repositório git em suas instâncias de contêiner
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 708fca185227292e7cdf33952bde6f42b3d4951f
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533221"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montar um volume gitRepo em instâncias de contêiner do Azure

Saiba como montar um volume *gitRepo* para clonar um repositório git em suas instâncias de contêiner.

> [!NOTE]
> A montagem de um volume *gitRepo* está atualmente restrita a contêineres do Linux. Enquanto estamos trabalhando para trazer todos os recursos para contêineres do Windows, você pode encontrar as diferenças da plataforma atual na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>volume gitRepo

O volume *gitRepo* monta um diretório e clona o repositório git especificado nele na inicialização do contêiner. Usando um volume *gitRepo* em suas instâncias de contêiner, você pode evitar adicionar o código para fazer isso em seus aplicativos.

Ao montar um volume *gitRepo* , você pode definir três propriedades para configurar o volume:

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| `repository` | Sim | A URL completa, incluindo `http://` ou `https://`, do repositório git a ser clonado.|
| `directory` | Não | Diretório no qual o repositório deve ser clonado. O caminho não deve conter ou começar com "`..`".  Se você especificar "`.`", o repositório será clonado no diretório do volume. Caso contrário, o repositório git será clonado em um subdiretório do nome fornecido dentro do diretório do volume. |
| `revision` | Não | O hash de confirmação da revisão a ser clonada. Se não for especificado, a revisão de `HEAD` será clonada. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montar o volume gitRepo: CLI do Azure

Para montar um volume gitRepo ao implantar instâncias de contêiner com o [CLI do Azure](/cli/azure), forneça os parâmetros `--gitrepo-url` e `--gitrepo-mount-path` ao comando [AZ container Create][az-container-create] . Opcionalmente, você pode especificar o diretório dentro do volume para clonar (`--gitrepo-dir`) e o hash de confirmação da revisão a ser clonada (`--gitrepo-revision`).

Este exemplo de comando clona o aplicativo de exemplo Microsoft [ACI-HelloWorld][aci-helloworld] em `/mnt/aci-helloworld` na instância de contêiner:

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

Para verificar se o volume gitRepo foi montado, inicie um shell no contêiner com [AZ container exec][az-container-exec] e liste o diretório:

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montar o volume gitRepo: Gerenciador de recursos

Para montar um volume gitRepo ao implantar instâncias de contêiner com um [modelo Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), primeiro preencha a matriz `volumes` na seção `properties` do grupo de contêineres do modelo. Em seguida, para cada contêiner no grupo de contêineres no qual você deseja montar o volume *gitRepo* , preencha a matriz de `volumeMounts` na seção `properties` da definição do contêiner.

Por exemplo, o modelo do Resource Manager a seguir cria um grupo de contêineres que consiste em um único contêiner. O contêiner clona dois repositórios GitHub especificados pelos blocos de volume *gitRepo* . O segundo volume inclui propriedades adicionais especificando um diretório para clonar e o hash de confirmação de uma revisão específica para clonar.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretório resultante dos dois repositórios clonados definidos no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de implantação de instância de contêiner com um modelo de Azure Resource Manager, consulte [implantar grupos de vários contêineres em instâncias de contêiner do Azure](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autenticação de repositório git particular

Para montar um volume gitRepo para um repositório git privado, especifique as credenciais na URL do repositório. Normalmente, as credenciais estão na forma de um nome de usuário e um PAT (token de acesso pessoal) que concede acesso de escopo ao repositório.

Por exemplo, o parâmetro CLI do Azure `--gitrepo-url` para um repositório GitHub privado seria semelhante ao seguinte (em que "gituser" é o nome de usuário do GitHub, e "abcdef1234fdsa4321abcdef" é o token de acesso pessoal do usuário):

```azurecli
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Para um repositório Azure Repos git, especifique qualquer nome de usuário (você pode usar "azurereposuser" como no exemplo a seguir) em combinação com uma PAT válida:

```azurecli
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Para obter mais informações sobre tokens de acesso pessoal para GitHub e Azure Repos, consulte o seguinte:

GitHub: [criando um token de acesso pessoal para a linha de comando][pat-github]

Azure Repos: [criar tokens de acesso pessoal para autenticar o acesso][pat-repos]

## <a name="next-steps"></a>Passos seguintes

Saiba como montar outros tipos de volume em instâncias de contêiner do Azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Montar um volume emptyDir em instâncias de contêiner do Azure](container-instances-volume-emptydir.md)
* [Montar um volume secreto em instâncias de contêiner do Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
