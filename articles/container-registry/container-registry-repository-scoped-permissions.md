---
title: Permissões para repositórios no Registo de Contentores de Azure
description: Criar um token com permissões apositórios específicos num registo Premium para puxar ou empurrar imagens, ou realizar outras ações
ms.topic: article
ms.date: 05/27/2020
ms.openlocfilehash: b65b1bf69337cb172a17043490a5d13c7bd7afc2
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381240"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Criar um símbolo com permissões de âmbito de repositório

Este artigo descreve como criar fichas e mapas de âmbito para gerir permissões de âmbito de repositório no seu registo de contentores. Ao criar fichas, um proprietário de registo pode fornecer aos utilizadores ou serviços um acesso limitado e limitado a repositórios para puxar ou empurrar imagens ou realizar outras ações. Um token fornece permissões mais finas do que outras opções de [autenticação](container-registry-authentication.md)de registo , que permite o âmbito de um registo completo. 

Os cenários para a criação de um símbolo incluem:

* Permitir que dispositivos IoT com fichas individuais puxem uma imagem de um repositório
* Fornecer a uma organização externa permissões para um repositório específico 
* Limite o acesso do repositório a diferentes grupos de utilizadores na sua organização. Por exemplo, fornecer acesso de escrita e leitura a desenvolvedores que constroem imagens que visam repositórios específicos, e ler o acesso a equipas que se desdobram desses repositórios.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização, e [algumas limitações aplicam-se](#preview-limitations). As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Não é possível atribuir permissões de âmbito de repositório a uma identidade do Diretório Ativo Azure, como um principal de serviço ou identidade gerida.
* Não é possível criar um mapa de âmbito num registo habilitado para [acesso anónimo .](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)

## <a name="concepts"></a>Conceitos

Para configurar permissões de âmbito de repositório, cria-se um *símbolo* com um mapa de *âmbito* associado. 

* Um **token** juntamente com uma palavra-passe gerada permite ao utilizador autenticar com o registo. Pode definir uma data de validade para uma palavra-passe simbólica ou desativar um token a qualquer momento.  

  Após autenticação com um token, o utilizador ou serviço pode executar uma ou mais *ações* telescópios para um ou mais repositórios.

  |Ação  |Descrição  | Exemplo |
  |---------|---------|--------|
  |`content/delete`    | Remover dados do repositório  | Apagar um repositório ou um manifesto |
  |`content/read`     |  Leia os dados do repositório |  Puxe um artefacto |
  |`content/write`     |  Escreva dados para o repositório     | Use com `content/read` para empurrar um artefacto |
  |`metadata/read`    | Leia metadados do repositório   | Lista de tags ou manifestos |
  |`metadata/write`     |  Escreva metadados para o repositório  | Ativar ou desativar as operações de leitura, escrita ou eliminação |

* Um mapa de **âmbito** agrupe as permissões de repositório que aplica a um token, e pode recandidatar-se a outras fichas. Cada ficha está associada a um único mapa de âmbito. 

   Com um mapa de âmbito:

    * Configurar vários tokens com permissões idênticas a um conjunto de repositórios
    * Atualizar permissões simbólicas quando adicionar ou remover ações de repositório no mapa de âmbito, ou aplicar um mapa de âmbito diferente 

  O Registo de Contentores Azure também fornece vários mapas de âmbito definidos pelo sistema que pode aplicar ao criar fichas. As permissões dos mapas de âmbito definidos pelo sistema aplicam-se a todos os repositórios do seu registo.

A imagem a seguir mostra a relação entre tokens e mapas de âmbito. 

![Fichas de registo e mapas de âmbito](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Pré-requisitos

* **Azure CLI** - Comandos Azure CLI para criar e gerir fichas estão disponíveis na versão 2.0.76 do Azure CLI ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).
* **Docker** - Para autenticar com o registo para puxar ou empurrar imagens, precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para os sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registo de contentores** - Se não tiver um, crie um registo de contentores Premium na sua assinatura Azure ou atualize um registo existente. Por exemplo, utilize o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Criar ficha - CLI

### <a name="create-token-and-specify-repositories"></a>Criar ficha e especificar repositórios

Crie um símbolo utilizando o [token az acr criar][az-acr-token-create] comando. Ao criar um token, pode especificar um ou mais repositórios e ações associadas em cada repositório. Os repositórios ainda não precisam de estar no registo. Para criar um símbolo especificando um mapa de âmbito existente, consulte a [secção seguinte](#create-token-and-specify-scope-map).

O exemplo a seguir cria um símbolo no *miodistria* do registo com as seguintes permissões sobre o `samples/hello-world` repo: `content/write` e `content/read` . Por predefinição, o comando define o estado de token predefinido para `enabled` , mas pode atualizar o estado a qualquer `disabled` momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A saída mostra detalhes sobre o símbolo. Por padrão, são geradas duas palavras-passe. Recomenda-se guardar as palavras-passe num local seguro para usar mais tarde para autenticação. As palavras-passe não podem ser recuperadas novamente, mas as novas podem ser geradas.

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

> [!NOTE]
> Se pretender regenerar senhas simbólicas e definir períodos de validade da palavra-passe, consulte [as palavras-passe do Símbolo Regenerador](#regenerate-token-passwords) mais tarde neste artigo.

A saída inclui detalhes sobre o mapa de âmbito que o comando criou. Você pode usar o mapa de âmbito, aqui `MyToken-scope-map` nomeado, para aplicar as mesmas ações de repositório em outras fichas. Ou, atualizar o mapa de âmbito mais tarde para alterar as permissões dos tokens associados.

### <a name="create-token-and-specify-scope-map"></a>Criar token e especificar mapa de âmbito

Uma forma alternativa de criar um símbolo é especificar um mapa de âmbito existente. Se ainda não tiver um mapa de âmbito, crie primeiro um especificando repositórios e ações associadas. Em seguida, especifique o mapa de âmbito ao criar um símbolo. 

Para criar um mapa de âmbito, utilize o [az acr-map create][az-acr-scope-map-create] command. O seguinte comando cria um mapa de âmbito com as mesmas permissões no `samples/hello-world` repositório usado anteriormente. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Executar [a az acr token criar][az-acr-token-create] para criar um token, especificando o mapa de âmbito *MyScopeMap.* Tal como no exemplo anterior, o comando define o estado de token padrão para `enabled` .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A saída mostra detalhes sobre o símbolo. Por padrão, são geradas duas palavras-passe. Recomenda-se guardar as palavras-passe num local seguro para usar mais tarde para autenticação. As palavras-passe não podem ser recuperadas novamente, mas as novas podem ser geradas.

> [!NOTE]
> Se pretender regenerar senhas simbólicas e definir períodos de validade da palavra-passe, consulte [as palavras-passe do Símbolo Regenerador](#regenerate-token-passwords) mais tarde neste artigo.

## <a name="create-token---portal"></a>Criar ficha - portal

Pode utilizar o portal Azure para criar fichas e mapas de âmbito. Tal como acontece com o `az acr token create` comando CLI, pode aplicar um mapa de âmbito existente ou criar um mapa de âmbito quando criar um símbolo especificando um ou mais repositórios e ações associadas. Os repositórios ainda não precisam de estar no registo. 

O exemplo a seguir cria um símbolo e cria um mapa de âmbito com as seguintes permissões no `samples/hello-world` repositório: `content/write` e `content/read` .

1. No portal, navegue para o registo do seu contentor.
1. Sob **permissões de Repositório** , selecione **Tokens (Preview) > +Add**.

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="Criar ficha no portal":::
1. Insira um nome simbólico.
1. No **mapa scope** , selecione Criar **novo**.
1. Configure o mapa de âmbito:
    1. Introduza um nome e descrição para o mapa de âmbito. 
    1. Sob **repositórios,** insira, `samples/hello-world` e sob **permissões,** selecione  `content/read` e `content/write` . Em seguida, selecione **+Adicionar**.  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="Criar mapa de âmbito no portal":::

    1. Depois de adicionar repositórios e permissões, **selecione Adicionar** para adicionar o mapa de âmbito.
1. Aceite o **estado** de token padrão de **Ativado** e, em seguida, selecione **Criar**.

Após a validação e criação do token, os detalhes simbólicos aparecem no ecrã **de Tokens.**

### <a name="add-token-password"></a>Adicionar senha simbólica

Para utilizar um símbolo criado no portal, tem de gerar uma palavra-passe. Pode gerar uma ou duas palavras-passe e definir uma data de validade para cada uma. 

1. No portal, navegue para o registo do seu contentor.
1. Sob **permissões de Repositório,** selecione **Tokens (Preview)** e selecione um token.
1. Nos detalhes do token, selecione **palavra-passe1** ou **palavra-passe2** e selecione o ícone Gerar.
1. No ecrã da palavra-passe, configurar opcionalmente uma data de validade para a palavra-passe e selecionar **Gerar**. É recomendado para definir uma data de validade.
1. Depois de gerar uma palavra-passe, copie-a e guarde-a para um local seguro. Não é possível recuperar uma palavra-passe gerada depois de fechar o ecrã, mas pode gerar uma nova.

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="Criar palavra-passe simbólica no portal":::

## <a name="authenticate-with-token"></a>Autenticar com ficha

Quando um utilizador ou serviço utiliza um símbolo para autenticar com o registo-alvo, fornece o nome simbólico como nome de utilizador e uma das suas palavras-passe geradas. 

O método de autenticação depende da ação configurada ou das ações associadas ao token.

|Ação  |Como autenticar  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` em Azure CLI<br/><br/>Exemplo: `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>`az acr login` em Azure CLI<br/><br/>Exemplo: `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` em Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` em Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` em Azure CLI |

## <a name="examples-use-token"></a>Exemplos: Use o símbolo

Os exemplos a seguir usam o símbolo criado anteriormente neste artigo para executar operações comuns num repositório: empurrar e puxar imagens, apagar imagens e listar tags de repositório. O token foi criado inicialmente com permissões de pressão `content/write` (e `content/read` ações) no `samples/hello-world` repositório.

### <a name="pull-and-tag-test-images"></a>Imagens de teste de puxar e etiquetar

Para os seguintes exemplos, puxe as `hello-world` imagens e `alpine` imagens do Docker Hub e marque-as para o seu registo e repositório.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag alpine myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Autenticar usando token

Executar `docker login` ou `az acr login` autenticar com o registo para empurrar ou puxar imagens. Forneça o nome simbólico como nome de utilizador e forneça uma das suas palavras-passe. O símbolo deve ter o `Enabled` estatuto.

O exemplo a seguir é formatado para a casca de bash, e fornece os valores usando variáveis ambientais.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve apresentar uma autenticação bem sucedida:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Enviar imagens para o registo

Após o login bem sucedido, tente empurrar as imagens marcadas para o registo. Como o símbolo tem permissões para empurrar imagens para o `samples/hello-world` repositório, o seguinte empurrão tem sucesso:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O token não tem permissões para o `samples/alpine` repo, por isso a seguinte tentativa de impulso falha com um erro semelhante `requested access to the resource is denied` a:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="update-token-permissions"></a>Atualizar permissões simbólicas

Para atualizar as permissões de um token, atualize as permissões no mapa de âmbito associado. O mapa de âmbito atualizado é aplicado imediatamente a todos os tokens associados. 

Por exemplo, atualize `MyToken-scope-map` `content/write` e `content/read` ações sobre o `samples/alpine` repositório e remova a `content/write` ação no `samples/hello-world` repositório.  

Para utilizar o Azure CLI, execute [a atualização do mapa de âmbito az acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

No portal do Azure:

1. Navegue para o seu registo de contentores.
1. Sob **permissões de Repositório,** selecione **mapas de âmbito (pré-visualização)** e selecione o mapa de âmbito para atualizar.
1. Sob **repositórios,** insira, `samples/alpine` e sob **permissões,** selecione `content/read` e `content/write` . Em seguida, selecione **+Adicionar**.
1. Sob **repositórios,** selecione `samples/hello-world` e sob **permissões,** desmarca. `content/write` Em seguida, selecione **Guardar**.

Após a atualização do mapa de âmbito, o seguinte impulso sucede:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Como o mapa de âmbito só tem `content/read` a permissão no `samples/hello-world` repositório, uma tentativa de empurrar para o `samples/hello-world` repo agora falha:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Tirar imagens de ambos os repos tem sucesso, porque o mapa de âmbito fornece `content/read` permissões em ambos os repositórios:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Eliminar imagens

Atualize o mapa de âmbito adicionando a `content/delete` ação ao `alpine` repositório. Esta ação permite a supressão de imagens no repositório, ou a eliminação de todo o repositório.

Para a brevidade, mostramos apenas o comando [de atualização do mapa de âmbito az acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Para atualizar o mapa de âmbito utilizando o portal, consulte a [secção anterior](#update-token-permissions).

Utilize o seguinte [repositório az acr para][az-acr-repository-delete] eliminar o `samples/alpine` repositório. Para apagar imagens ou repositórios, passe o nome e a palavra-passe do símbolo para o comando. O exemplo a seguir utiliza as variáveis ambientais criadas anteriormente no artigo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostrar tags de repo 

Atualize o mapa de âmbito adicionando a `metadata/read` ação ao `hello-world` repositório. Esta ação permite ler manifestos e etiquetar dados no repositório.

Para a brevidade, mostramos apenas o comando [de atualização do mapa de âmbito az acr][az-acr-scope-map-update] para atualizar o mapa de âmbito:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Para atualizar o mapa de âmbito utilizando o portal, consulte a [secção anterior](#update-token-permissions).

Para ler metadados no `samples/hello-world` repositório, execute os [manifestos de show-manifest do repositório az acr][az-acr-repository-show-manifests] ou o comando [az acr repository show-tags.][az-acr-repository-show-tags] 

Para ler metadados, passe o nome e a palavra-passe do token para qualquer um dos comandos. O exemplo a seguir utiliza as variáveis ambientais criadas anteriormente no artigo:

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

### <a name="list-scope-maps"></a>Listar mapas de âmbito

Utilize o comando [da lista de mapas de âmbito az acr,][az-acr-scope-map-list] ou o ecrã de mapas de **âmbito (visualização)** no portal, para listar todos os mapas de âmbito configurados num registo. Por exemplo:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A saída consiste nos três mapas de âmbito definidos pelo sistema e outros mapas de âmbito gerados por si. Os tokens podem ser configurados com qualquer um destes mapas de âmbito.

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostrar detalhes simbólicos

Para visualizar os detalhes de um token, como as datas de validade do seu estado e palavra-passe, executar o comando [de exibição az acr token][az-acr-token-show] ou selecionar o token no ecrã **Tokens (Preview)** no portal. Por exemplo:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Utilize o comando [da lista az acr token,][az-acr-token-list] ou o ecrã **Tokens (Preview)** no portal, para listar todos os tokens configurados num registo. Por exemplo:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>Regenerar senhas simbólicas

Se não gerou uma palavra-passe simbólica, ou se pretender gerar novas palavras-passe, execute o comando [de geração de credenciais az acr.][az-acr-token-credential-generate] 

O exemplo a seguir gera um novo valor para a palavra-passe1 para o token *MyToken,* com um prazo de validade de 30 dias. Armazena a palavra-passe na variável `TOKEN_PWD` ambiente. Este exemplo é formatado para a casca de bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Para utilizar o portal Azure para gerar uma palavra-passe simbólica, consulte os passos em [Create token - portal](#create-token---portal) mais cedo neste artigo.

### <a name="update-token-with-new-scope-map"></a>Atualização token com novo mapa de âmbito

Se pretender atualizar um token com um mapa de âmbito diferente, execute [a atualização az acr token][az-acr-token-update] e especifique o novo mapa de âmbito. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

No portal, no ecrã **Tokens (pré-visualização),** selecione o token e no **mapa Scope** , selecione um mapa de âmbito diferente.

> [!TIP]
> Depois de atualizar um token com um novo mapa de âmbito, poderá querer gerar novas senhas simbólicas. Utilize a [credencial az acr token gerar][az-acr-token-credential-generate] comando ou regenerar uma palavra-passe simbólica no portal Azure.

## <a name="disable-or-delete-token"></a>Desativar ou apagar ficha

Poderá ser necessário desativar temporariamente a utilização das credenciais simbólicas para um utilizador ou serviço. 

Utilizando o Azure CLI, executar o comando [de atualização az acr token][az-acr-token-update] para definir `status` `disabled` o:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

No portal, selecione o token no ecrã **Tokens (Preview)** e selecione **Desativado** em **Estado**.

Para eliminar um símbolo para invalidar permanentemente o acesso por qualquer pessoa que utilize as suas credenciais, executar o comando [az acr apagar.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

No portal, selecione o token no ecrã **Tokens (Preview)** e selecione **Descartar**.

## <a name="next-steps"></a>Próximos passos

* Para gerir mapas de âmbito e fichas, utilize comandos adicionais nos grupos de comando [az acr e][az-acr-scope-map] [az acr token.][az-acr-token]
* Consulte a [visão geral](container-registry-authentication.md) da autenticação para outras opções para autenticar com um registo de contentores Azure, incluindo a utilização de uma identidade do Diretório Ativo Azure, um principal serviço ou uma conta de administração.


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
