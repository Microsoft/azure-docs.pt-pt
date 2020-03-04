---
title: Monte gitRepo volume para grupo de contentores
description: Aprenda a montar um volume gitRepo para clonar um repositório Git nas instâncias do seu recipiente
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252088"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Monte um volume gitRepo em instâncias de contentores azure

Aprenda a montar um volume *gitRepo* para clonar um repositório Git nas instâncias do seu recipiente.

> [!NOTE]
> A montagem de um volume *gitRepo* está atualmente restrita aos recipientes Linux. Enquanto estamos a trabalhar para trazer todas as funcionalidades para os recipientes windows, pode encontrar as diferenças de plataforma atuais na [visão geral](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>volume gitRepo

O volume *gitRepo* monta um diretório e clona o repositório git especificado no arranque do contentor. Ao utilizar um volume *gitRepo* nas instâncias do seu recipiente, pode evitar adicionar o código para o fazer nas suas aplicações.

Quando monta um volume *gitRepo,* pode definir três propriedades para configurar o volume:

| Propriedade | Necessário | Descrição |
| -------- | -------- | ----------- |
| `repository` | Sim | O URL completo, incluindo `http://` ou `https://`, do repositório Git a clonar.|
| `directory` | Não | Diretório no qual o repositório deve ser clonado. O caminho não deve conter ou começar com "`..`".  Se especificar "`.`", o repositório é clonado no diretório do volume. Caso contrário, o repositório Git é clonado num subdiretório do nome dado dentro do volume. |
| `revision` | Não | O compromisso da revisão a ser clonado. Se não especificado, a revisão `HEAD` é clonada. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Volume do Monte GitRepo: Azure CLI

Para montar um volume gitRepo quando implementar instâncias de contentores com o [Azure CLI,](/cli/azure)forneça os parâmetros `--gitrepo-url` e `--gitrepo-mount-path` ao [recipiente az criar][az-container-create] comando. Pode especificar opcionalmente o diretório dentro do volume para clonar em (`--gitrepo-dir`) e o hash de compromisso da revisão a clonar (`--gitrepo-revision`).

Este comando de exemplo clona a aplicação da amostra [Aci-Helloworld][aci-helloworld] da Microsoft para `/mnt/aci-helloworld` na instância do contentor:

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

Para verificar se o volume gitRepo foi montado, lance uma concha no recipiente com o executivo do [contentor az][az-container-exec] e enumere o diretório:

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

Para montar um volume gitRepo quando implementar instâncias de contentores com um modelo de Gestor de [Recursos Azure,](/azure/templates/microsoft.containerinstance/containergroups)primeiro povoe a matriz de `volumes` no grupo de contentores `properties` secção do modelo. Em seguida, para cada recipiente do grupo de contentores em que deseja montar o volume *gitRepo,* povoar a matriz `volumeMounts` na secção `properties` da definição do recipiente.

Por exemplo, o seguinte modelo de Gestor de Recursos cria um grupo de contentores composto por um único recipiente. O recipiente clona dois repositórios GitHub especificados pelos blocos de volume *gitRepo.* O segundo volume inclui propriedades adicionais que especificam um diretório para clonar, e o hash de compromisso de uma revisão específica ao clone.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A estrutura de diretório resultante dos dois repos clonados definidos no modelo anterior é:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver um exemplo de implantação de instâncias de contentores com um modelo de Gestor de Recursos Azure, consulte [a implantação de grupos multi-contentores em instâncias de contentores Azure](container-instances-multi-container-group.md).

## <a name="private-git-repo-authentication"></a>Autenticação privada do repo git

Para montar um volume gitRepo para um repositório git privado, especifique credenciais no URL repositório. Tipicamente, as credenciais são sob a forma de um nome de utilizador e de um token de acesso pessoal (PAT) que concede acesso acessível ao repositório.

Por exemplo, o parâmetro `--gitrepo-url` Azure CLI para um repositório privado gitHub parece similar ao seguinte (quando "gituser" é o nome de utilizador GitHub, e "abcdef1234fdsa4321abcdef" é o sinal de acesso pessoal do utilizador):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Para um repositório Azure Repos Git, especifique qualquer nome de utilizador (pode utilizar "azurereposuser" como no exemplo seguinte) em combinação com um PAT válido:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Para obter mais informações sobre fichas de acesso pessoal para GitHub e Azure Repos, consulte o seguinte:

GitHub: [Criar um sinal de acesso pessoal para a linha de comando][pat-github]

Azure Repos: [Criar fichas de acesso pessoal para autenticar acesso][pat-repos]

## <a name="next-steps"></a>Passos seguintes

Saiba montar outros tipos de volume em instâncias de contentores azure:

* [Montar uma partilha de ficheiros do Azure no Azure Container Instances](container-instances-volume-azure-files.md)
* [Monte um volume dir vazio em instâncias de contentores azure](container-instances-volume-emptydir.md)
* [Monte um volume secreto em instâncias de contentores azure](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
