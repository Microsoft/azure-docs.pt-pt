---
title: Monte gitRepo volume para grupo de contentores
description: Aprenda a montar um volume gitRepo para clonar um repositório git em suas instâncias de contentores
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5f361b0bbd4a02f754e47c8321a664af05036601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260713"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Monte um volume gitRepo em Instâncias de Contentores Azure

Aprenda a montar um volume *gitRepo* para clonar um repositório git nas suas instâncias de contentores.

> [!NOTE]
> A montagem de um volume *gitRepo* está atualmente restrita aos contentores Linux. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os contentores do Windows, pode encontrar as diferenças da plataforma atuais na [visão geral.](container-instances-overview.md#linux-and-windows-containers)

## <a name="gitrepo-volume"></a>gitRepo volume

O volume *gitRepo* monta um diretório e clona o repositório git especificado no arranque do contentor. Ao utilizar um volume *gitRepo* nas suas instâncias de contentor, pode evitar adicionar o código para o fazer nas suas aplicações.

Ao montar um volume *gitRepo,* pode definir três propriedades para configurar o volume:

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| `repository` | Sim | A URL completa, incluindo `http://` ou `https://` , do repositório git a ser clonado.|
| `directory` | Não | Diretório no qual o repositório deve ser clonado. O caminho não deve conter ou começar com `..` " "  Se especificar " `.` ", o repositório é clonado no diretório do volume. Caso contrário, o repositório de Git é clonado numa subdiretória do nome próprio dentro do diretório de volume. |
| `revision` | Não | O haxixe compromete-se com a revisão a ser clonado. Se não for especificado, a `HEAD` revisão é clonada. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Monte gitRepo volume: Azure CLI

Para montar um volume gitRepo quando colocar instâncias de contentores com o [Azure CLI,](/cli/azure)forneça o `--gitrepo-url` e os `--gitrepo-mount-path` parâmetros ao recipiente [az crie][az-container-create] comando. Pode especificar opcionalmente o diretório dentro do volume para clonar em ( `--gitrepo-dir` ) e o haxixe de compromisso da revisão a clonar ( `--gitrepo-revision` ).

Este exemplo de comando clona a aplicação de amostra [aci-helloworld][aci-helloworld] da Microsoft `/mnt/aci-helloworld` no caso do recipiente:

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

Para verificar se o volume gitRepo foi montado, lance uma concha no recipiente com um [porta-contentores az][az-container-exec] e enuite o diretório:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Volume Mount gitRepo: Gestor de Recursos

Para montar um volume gitRepo quando colocar instâncias de contentores com um [modelo de Gestor de Recursos Azure,](/azure/templates/microsoft.containerinstance/containergroups)primeiro povoe a matriz na secção do grupo de `volumes` contentores do `properties` modelo. Em seguida, para cada recipiente do grupo de contentores no qual pretende montar o volume *gitRepo,* povoe a `volumeMounts` matriz na secção da `properties` definição do recipiente.

Por exemplo, o seguinte modelo de Gestor de Recursos cria um grupo de contentores composto por um único recipiente. O recipiente clona dois repositórios GitHub especificados pelos blocos de volume *gitRepo.* O segundo volume inclui propriedades adicionais especificando um diretório para clonar, e o haxixe de uma revisão específica para clone.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretório resultante dos dois repos clonados definidos no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de colocação de casos de contentor com um modelo de Gestor de Recursos Azure, consulte [implementar grupos de vários contentores em instâncias de contentores Azure](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autenticação de repo privado Git

Para montar um volume gitRepo para um repositório privado de Git, especifique credenciais no URL do repositório. Tipicamente, as credenciais são sob a forma de um nome de utilizador e um token de acesso pessoal (PAT) que concede acesso alargado ao repositório.

Por exemplo, o parâmetro Azure CLI `--gitrepo-url` para um repositório privado do GitHub apareceria semelhante ao seguinte (onde "gituser" é o nome de utilizador do GitHub, e "abcdef1234fdsa4321abcdef" é o token de acesso pessoal do utilizador):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Para um repositório Azure Repos Git, especifique qualquer nome de utilizador (pode utilizar "azurereposuser" como no exemplo seguinte) em combinação com um PAT válido:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Para obter mais informações sobre fichas de acesso pessoal para GitHub e Azure Repos, consulte o seguinte:

GitHub: [Criar um símbolo de acesso pessoal para a linha de comando][pat-github]

Azure Repos: [Criar fichas de acesso pessoal para autenticar o acesso][pat-repos]

## <a name="next-steps"></a>Passos seguintes

Saiba como montar outros tipos de volume em Instâncias de Contentores Azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume deDir vazio em instâncias de contentores Azure](container-instances-volume-emptydir.md)
* [Monte um volume secreto em Instâncias de Contentores Azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
