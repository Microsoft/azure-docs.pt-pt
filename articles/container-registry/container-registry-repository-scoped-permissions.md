---
title: Permissões aos repositórios no Registo de Contentores de Azure
description: Crie um símbolo com permissões revestidas a repositórios específicos num registo para puxar ou empurrar imagens, ou realizar outras ações
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444320"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Crie um símbolo com permissões de repositório

Este artigo descreve como criar tokens e mapas de âmbito para gerir permissões de revisão no seu registo de contentores. Ao criar fichas, um proprietário de registo pode fornecer aos utilizadores ou serviços um acesso limitado de tempo aos repositórios para puxar ou empurrar imagens ou realizar outras ações. Um símbolo fornece permissões mais finas do que outras opções de [autenticação](container-registry-authentication.md)de registo, que âmbito de permissões a todo um registo. 

Os cenários para a criação de um símbolo incluem:

* Permitir que dispositivos IoT com fichas individuais puxem uma imagem de um repositório
* Fornecer a uma organização externa permissões a um repositório específico 
* Limite o acesso ao repositório a diferentes grupos de utilizadores da sua organização. Por exemplo, fornecer acesso de escrita e leitura a desenvolvedores que constroem imagens que visam repositórios específicos, e ler acesso a equipas que se desdobram a partir desses repositórios.

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [aplicam-se algumas limitações.](#preview-limitations) As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Esta funcionalidade só está disponível num registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte o [Registo de Contentores Azure SKUs](container-registry-skus.md).
* Não é possível atribuir permissões de reposição a uma identidade de Diretório Ativo Azure, como um diretor de serviço ou identidade gerida.

## <a name="concepts"></a>Conceitos

Para configurar permissões com requisitório, cria-se um *símbolo* com um mapa de *âmbito*associado . 

* Um **símbolo** juntamente com uma palavra-passe gerada permite ao utilizador autenticar com o registo. Pode definir uma data de validade para uma palavra-passe simbólica ou desativar um token a qualquer momento.  

  Depois de autenticar com um símbolo, o utilizador ou serviço pode realizar uma ou mais *ações* de âmbito de um ou mais repositórios.

  |Ação  |Descrição  | Exemplo |
  |---------|---------|--------|
  |`content/delete`    | Remover dados do repositório  | Eliminar um repositório ou um manifesto |
  |`content/read`     |  Leia os dados do repositório |  Puxe um artefacto |
  |`content/write`     |  Escreva dados para o repositório     | Use `content/read` com para empurrar um artefacto |
  |`metadata/read`    | Ler metadados do repositório   | Listas ou manifestos |
  |`metadata/write`     |  Escreva metadados para o repositório  | Ativar ou desativar a leitura, a escrita ou a eliminação de operações |

* Um mapa de **âmbito** agrupa as permissões de repositório que aplica a um símbolo, e pode reaplicar-se a outras fichas. Cada símbolo está associado a um único mapa de âmbito. 

   Com um mapa de âmbito:

    * Configure várias fichas com permissões idênticas a um conjunto de repositórios
    * Atualizar permissões simbólicas quando adicionar ou remover ações de repositório no mapa de âmbito, ou aplicar um mapa de âmbito diferente 

  O Registo de Contentores Azure também fornece vários mapas de âmbito definidos pelo sistema que você pode aplicar, com permissões fixas em todos os repositórios.

A imagem que se segue mostra a relação entre fichas e mapas de âmbito. 

![Fichas de registo e mapas de âmbito](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Pré-requisitos

* Os comandos **Azure CLI** - Azure CLI para criar e gerir fichas estão disponíveis na versão 2.0.76 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).
* **Docker** - Para autenticar com o registo para puxar ou empurrar imagens, precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para os sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registo de contentores** - Se não tiver um, crie um registo de contentores Premium na sua assinatura Azure ou atualize um registo existente. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Criar ficha - CLI

### <a name="create-token-and-specify-repositories"></a>Criar token e especificar repositórios

Crie um símbolo usando o [az acr token criar][az-acr-token-create] comando. Ao criar um símbolo, pode especificar um ou mais repositórios e ações associadas em cada repositório. Os repositórios ainda não precisam de estar no registo. Para criar um símbolo especificando um mapa de âmbito existente, consulte a secção seguinte.

O exemplo seguinte cria um símbolo no *registo do registo* com `samples/hello-world` as `content/write` seguintes permissões no repo: e `content/read`. Por predefinição, o comando define `enabled`o estado do token `disabled` predefinido para , mas pode atualizar o estado a qualquer momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A saída mostra detalhes sobre o símbolo, incluindo duas senhas geradas. É aconselhável guardar as palavras-passe num local seguro para ser usada mais tarde para autenticação. As palavras-passe não podem ser recuperadas novamente, mas novas podem ser geradas.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

A saída inclui detalhes sobre o mapa de âmbito que o comando criou. Você pode usar o mapa `MyToken-scope-map`de âmbito, aqui chamado, para aplicar as mesmas ações de repositório a outros tokens. Ou, atualizar o mapa de âmbito mais tarde para alterar as permissões dos tokens associados.

### <a name="create-token-and-specify-scope-map"></a>Criar mapa de âmbito simbólico e especificar

Uma forma alternativa de criar um símbolo é especificar um mapa de âmbito existente. Se ainda não tem um mapa de âmbito, crie primeiro um especificando repositórios e ações associadas. Em seguida, especifique o mapa de âmbito ao criar um símbolo. 

Para criar um mapa de âmbito, use o [mapa de alcance az acr criar][az-acr-scope-map-create] comando. O seguinte comando cria um mapa de `samples/hello-world` âmbito com as mesmas permissões no repositório usado anteriormente. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Executar [acri token criar][az-acr-token-create] para criar um símbolo, especificando o mapa de âmbito *MyScopeMap.* Tal como no exemplo anterior, o comando define `enabled`o estado do símbolo padrão para .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A saída mostra detalhes sobre o símbolo, incluindo duas senhas geradas. É aconselhável guardar as palavras-passe num local seguro para ser usada mais tarde para autenticação. As palavras-passe não podem ser recuperadas novamente, mas novas podem ser geradas.

## <a name="create-token---portal"></a>Criar ficha - portal

Pode utilizar o portal Azure para criar tokens e mapas de âmbito. Tal como `az acr token create` acontece com o comando CLI, pode aplicar um mapa de âmbito existente, ou criar um mapa de âmbito quando criar um símbolo, especificando um ou mais repositórios e ações associadas. Os repositórios ainda não precisam de estar no registo. 

O exemplo seguinte cria um símbolo, e cria um mapa `samples/hello-world` de âmbito `content/write` com `content/read`as seguintes permissões no repositório: e .

1. No portal, navegue para o seu registo de contentores.
1. Em **Serviços**, selecione **Tokens (Pré-visualização) > +Adicionar**.
  ![Criar ficha no portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Insira um nome simbólico.
1. No **mapa scope,** selecione **Criar novo**.
1. Configure o mapa de âmbito:
    1. Introduza um nome e descrição para o mapa de âmbito. 
    1. Sob **os Repositórios,** insira, `samples/hello-world`e sob **Permissões,** selecione `content/read` e `content/write`. Em seguida, **selecione +Adicionar**.  
    ![Criar mapa de âmbito no portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Depois de adicionar repositórios e permissões, selecione **Adicionar** para adicionar o mapa de âmbito.
1. Aceite o **estado** de ficha padrão do **Ativado** e, em seguida, selecione **Criar**.

Após a validação e criação do símbolo, os detalhes do token aparecem no ecrã **tokens.**

### <a name="add-token-password"></a>Adicione senha simbólica

Gere uma senha depois de criar um símbolo. Para autenticar com o registo, o símbolo deve ser ativado e ter uma senha válida.

Pode gerar uma ou duas palavras-passe e definir uma data de validade para cada uma. 

1. No portal, navegue para o seu registo de contentores.
1. Em **Serviços,** selecione **Tokens (Pré-visualização)** e selecione um símbolo.
1. Nos detalhes simbólicos, selecione **password1** ou **password2**, e selecione o ícone Generate.
1. No ecrã da palavra-passe, detete opcionalmente uma data de validade para a palavra-passe e selecione **Generate**.
1. Depois de gerar uma senha, copie e guarde-a para um local seguro. Não é possível recuperar uma palavra-passe gerada depois de fechar o ecrã, mas pode gerar uma nova.

    ![Criar senha simbólica no portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Autenticar com símbolo

Quando um utilizador ou serviço utiliza um símbolo para autenticar com o registo-alvo, fornece o nome simbólico como nome de utilizador e uma das suas palavras-passe geradas. O método de autenticação depende da ação ou ações configuradas associadas ao símbolo.

|Ação  |Como autenticar  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`em Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`em Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`em Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`em Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`em Azure CLI |

## <a name="examples-use-token"></a>Exemplos: Use token

Os seguintes exemplos usam o símbolo criado anteriormente neste artigo para realizar operações comuns num repositório: empurrar e puxar imagens, apagar imagens e listar etiquetas de repositório. O símbolo foi criado inicialmente com`content/write` permissões push (e `content/read` ações) no `samples/hello-world` repositório.

### <a name="pull-and-tag-test-images"></a>Puxar e etiquetar imagens de teste

Para os seguintes exemplos, puxe as `hello-world` imagens e imagens `alpine` do Docker Hub e marque-as para o seu registo e repositório.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Autenticar usando símbolo

Executar `docker login` para autenticar com o registo, fornecer o nome simbólico como nome de utilizador e fornecer uma das suas palavras-passe. O símbolo deve `Enabled` ter o estatuto.

O exemplo seguinte é formatado para a concha de bash, e fornece os valores usando variáveis ambientais.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve apresentar autenticação bem sucedida:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Enviar imagens para o registo

Depois de iniciar sessão com sucesso, tente empurrar as imagens marcadas para o registo. Como o símbolo tem permissões `samples/hello-world` para empurrar imagens para o repositório, o seguinte empurrão sucede:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O símbolo não tem permissões `samples/alpine` para o repo, pelo que a seguinte `requested access to the resource is denied`tentativa de empurrar falha com um erro semelhante a:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Alterar permissões de empurrar/puxar

Para atualizar as permissões de um símbolo, atualize as permissões no mapa de âmbito associado. O mapa de âmbito atualizado é aplicado imediatamente a todas as fichas associadas. 

Por exemplo, `MyToken-scope-map` `content/write` atualizar `content/read` e `samples/alpine` ações no repositório `content/write` e `samples/hello-world` remover a ação no repositório.  

Para utilizar o Azure CLI, execute a [atualização do mapa de âmbito acr acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

No portal do Azure:

1. Navegue para o seu registo de contentores.
1. Em **Serviços,** selecione **mapas de âmbito (Pré-visualização)** e selecione o mapa de âmbito para atualizar.
1. Sob **os Repositórios,** insira, `samples/alpine`e sob **Permissões,** selecione `content/read` e `content/write`. Em seguida, **selecione +Adicionar**.
1. Sob **os Repositórios,** selecione `samples/hello-world` e sob **permissões,** desmarque `content/write`. Em seguida, selecione **Guardar**.

Após a atualização do mapa de âmbito, o seguinte impulso sucede:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Como o mapa de `content/read` âmbito `samples/hello-world` só tem a permissão no `samples/hello-world` repositório, uma tentativa de empurrar para o repo agora falha:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Puxar imagens de ambos os repos tem `content/read` sucesso, porque o mapa de âmbito fornece permissões em ambos os repositórios:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Eliminar imagens

Atualize o mapa `content/delete` de âmbito `alpine` adicionando a ação ao repositório. Esta ação permite a supressão de imagens no repositório, ou eliminação de todo o repositório.

Para a brevidade, mostramos apenas o comando de [atualização de mapas de âmbito az acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Para atualizar o mapa de âmbito utilizando o portal, consulte a secção anterior.

Utilize o seguinte [repositório acr acr eliminar][az-acr-repository-delete] o comando para eliminar o `samples/alpine` repositório. Para apagar imagens ou repositórios, o símbolo `docker login`não autentica através de . Em vez disso, passe o nome do token e a senha para o comando. O exemplo seguinte utiliza as variáveis ambientais criadas anteriormente no artigo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostrar etiquetas de repo 

Atualize o mapa `metadata/read` de âmbito `hello-world` adicionando a ação ao repositório. Esta ação permite ler dados manifestos e etiquetas no repositório.

Para a brevidade, mostramos apenas o comando de [atualização de mapas de âmbito az acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Para atualizar o mapa de âmbito utilizando o portal, consulte a secção anterior.

Para ler metadados `samples/hello-world` no repositório, execute os manifestos de [show-manifestos de az acr repositório ou][az-acr-repository-show-manifests] [az acr repositório show-tags][az-acr-repository-show-tags] comando. 

Para ler metadados, o símbolo não autentica `docker login`através de . Em vez disso, passe o nome do token e a palavra-passe para qualquer comando. O exemplo seguinte utiliza as variáveis ambientais criadas anteriormente no artigo:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Resultado do exemplo:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Gerir fichas e mapas de âmbito

### <a name="list-scope-maps"></a>Mapas de âmbito de lista

Utilize o comando da lista de mapas de [âmbito acr acr,][az-acr-scope-map-list] ou o ecrã **de mapas de âmbito (Pré-visualização)** no portal, para listar todos os mapas de âmbito configurados num registo. Por exemplo:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A saída mostra os mapas de âmbito que definiu e vários mapas de âmbito definidos pelo sistema que pode usar para configurar fichas:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostrar detalhes simbólicos

Para ver os detalhes de um token, como o seu estado e datas de validade da palavra-passe, executar o comando de show de [acr acr,][az-acr-token-show] ou selecione o token no ecrã **Tokens (Preview)** no portal. Por exemplo:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Utilize o comando da lista de [token sacr,][az-acr-token-list] ou o ecrã **Tokens (Pré-visualização)** no portal, para listar todos os tokens configurados num registo. Por exemplo:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Gerar senhas para ficha

Se não tiver uma senha simbólica, ou quiser gerar novas palavras-passe, execute o comando de [geração de credibilidade az acr token.][az-acr-token-credential-generate] 

O exemplo seguinte gera um novo valor para a palavra-passe1 para o token *MyToken,* com um prazo de validade de 30 dias. Armazena a palavra-passe na variável `TOKEN_PWD`ambiente. Este exemplo é formatado para a concha da batida.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Para utilizar o portal Azure para gerar uma senha simbólica, consulte os passos em [Create token - portal](#create-token---portal) no início deste artigo.

### <a name="update-token-with-new-scope-map"></a>Token de atualização com novo mapa de âmbito

Se quiser atualizar um token com um mapa de âmbito diferente, faça [atualização token az acr][az-acr-token-update] e especifique o novo mapa de âmbito. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

No portal, no ecrã **Tokens (pré-visualização),** selecione o símbolo e, no **mapa scope,** selecione um mapa de âmbito diferente.

> [!TIP]
> Depois de atualizar um símbolo com um novo mapa de âmbito, talvez queira gerar novas senhas simbólicas. Utilize o comando de [crédito acr token ou][az-acr-token-credential-generate] regenerar uma palavra-passe simbólica no portal Azure.

## <a name="disable-or-delete-token"></a>Desativar ou excluir ficha

Pode ser necessário desativar temporariamente a utilização das credenciais simbólicas para um utilizador ou serviço. 

Utilizando o Azure CLI, execute o comando de `status` [atualização az acr token][az-acr-token-update] para definir o : `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

No portal, selecione o token no ecrã **Tokens (Pré-visualização)** e selecione **Desativado** sob **estado**.

Para eliminar um símbolo para invalidar permanentemente o acesso por qualquer pessoa que utilize as suas credenciais, execute o comando de [exclusão acr acr.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

No portal, selecione o token no ecrã **Tokens (Pré-visualização)** e selecione **'Descartar**' .

## <a name="next-steps"></a>Passos seguintes

* Para gerir mapas de âmbito e fichas, use comandos adicionais no [mapa de âmbito az acr][az-acr-scope-map] e grupos de comando [az acr token.][az-acr-token]
* Consulte a visão geral da [autenticação](container-registry-authentication.md) para outras opções para autenticar com um registo de contentores Azure, incluindo a utilização de uma identidade de Diretório Ativo Azure, um diretor de serviço ou uma conta de administração.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
