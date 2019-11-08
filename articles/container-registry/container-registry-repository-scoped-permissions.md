---
title: Permissões para repositórios no registro de contêiner do Azure
description: Criar um token com permissões com escopo para repositórios específicos em um registro para efetuar pull ou enviar imagens por push
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: 7b9d220ac7e507513458eab6b55276b3aa434739
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742757"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Permissões no escopo do repositório no registro de contêiner do Azure 

O registro de contêiner do Azure dá suporte a várias [Opções de autenticação](container-registry-authentication.md) usando identidades que têm [acesso baseado em função](container-registry-roles.md) a um registro inteiro. No entanto, para determinados cenários, talvez seja necessário fornecer acesso apenas a *repositórios* específicos em um registro. 

Este artigo mostra como criar e usar um token de acesso que tenha permissões para executar ações somente em repositórios específicos em um registro. Com um token de acesso, você pode fornecer aos usuários ou serviços com acesso limitado por tempo e com escopo a repositórios para efetuar pull ou enviar por push as imagens ou executar outras ações. 

Consulte [sobre permissões no escopo do repositório](#about-repository-scoped-permissions), mais adiante neste artigo, para obter informações sobre conceitos e cenários de token.

> [!IMPORTANT]
> Este recurso está atualmente em visualização e algumas [limitações se aplicam](#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="preview-limitations"></a>Limitações de visualização

* Esse recurso só está disponível em um registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).
* Atualmente, não é possível atribuir permissões no escopo do repositório a um objeto Azure Active Directory, como uma entidade de serviço ou uma identidade gerenciada.

## <a name="prerequisites"></a>Pré-requisitos

* **CLI do Azure** -este artigo requer uma instalação local do CLI do Azure (versão 2.0.76 ou posterior). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* **Docker** – para autenticar com o registro, você também precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para os sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro de contêiner com repositórios** – se você não tiver um, crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md). 

  Para fins de teste, [envie por push](container-registry-get-started-docker-cli.md) ou [importe](container-registry-import-images.md) uma ou mais imagens de exemplo para o registro. Os exemplos neste artigo referem-se às seguintes imagens em dois repositórios: `samples/hello-world:v1` e `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Criar um token de acesso

Crie um token usando o comando [AZ ACR token Create][az-acr-token-create] . Ao criar um token, especifique um ou mais repositórios e ações associadas em cada repositório ou especifique um mapa de escopo existente com essas configurações.

### <a name="create-access-token-and-specify-repositories"></a>Criar token de acesso e especificar repositórios

O exemplo a seguir cria um token de acesso com permissões para executar `content/write` e `content/read` ações no repositório `samples/hello-world` e a ação `content/read` no repositório `samples/nginx`. Por padrão, o comando gera duas senhas. 

Este exemplo define o status do token como `enabled` (a configuração padrão), mas você pode atualizar o token a qualquer momento e definir o status como `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

A saída mostra detalhes sobre o token, incluindo senhas geradas e mapa de escopo. É recomendável salvar as senhas em um local seguro para usar mais tarde com `docker login`. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

A saída também mostra que um mapa de escopo é criado automaticamente, chamado `MyToken-scope-map`. Você pode usar o mapa de escopo para aplicar as mesmas ações de repositório a outros tokens. Ou atualize o mapa de escopo posteriormente para alterar as permissões de token.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
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

### <a name="create-a-scope-map-and-associated-token"></a>Criar um mapa de escopo e um token associado

Como alternativa, especifique um mapa de escopo com repositórios e ações associadas ao criar um token. Para criar um mapa de escopo, use o comando [AZ ACR Scope-MAP Create][az-acr-scope-map-create] .

O comando de exemplo a seguir cria um mapa de escopo com as mesmas permissões usadas no exemplo anterior. Ele permite ações de `content/write` e `content/read` no repositório `samples/hello-world` e a ação `content/read` no repositório `samples/nginx`:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

O resultado é semelhante ao seguinte:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Execute [AZ ACR token Create][az-acr-token-create] para criar um token associado ao mapa de escopo *MyScopeMap* . Por padrão, o comando gera duas senhas. Este exemplo define o status do token como `enabled` (a configuração padrão), mas você pode atualizar o token a qualquer momento e definir o status como `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

A saída mostra detalhes sobre o token, incluindo senhas geradas e o mapa de escopo que você aplicou. É recomendável salvar as senhas em um local seguro para usar mais tarde com `docker login`. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

## <a name="generate-passwords-for-token"></a>Gerar senhas para token

Se as senhas tiverem sido criadas quando você criou o token, vá para [autenticar com o registro](#authenticate-using-token).

Se você não tiver uma senha de token ou se quiser gerar novas senhas, execute o comando [AZ ACR token Credential Generate][az-acr-token-credential-generate] .

O exemplo a seguir gera uma nova senha para o token que você criou, com um período de expiração de 30 dias. Ele armazena a senha na variável de ambiente TOKEN_PWD. Este exemplo é formatado para o shell bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Autenticar usando token

Execute `docker login` para autenticar com o registro usando as credenciais do token. Insira o nome do token como o nome de usuário e forneça uma de suas senhas. O exemplo a seguir é formatado para o shell bash e fornece os valores usando variáveis de ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve mostrar a autenticação bem-sucedida:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Verificar o acesso no escopo

Você pode verificar se o token fornece permissões no escopo para os repositórios no registro. Neste exemplo, os seguintes comandos de `docker pull` são concluídos com êxito para efetuar pull de imagens disponíveis no `samples/hello-world` e `samples/nginx` repositórios:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Como o token de exemplo permite a ação `content/write` somente no repositório `samples/hello-world`, `docker push` é bem-sucedida para esse repositório, mas falha para `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Atualizar mapa e token de escopo

Para atualizar as permissões de token, atualize as permissões no mapa de escopo associado, usando [AZ ACR Scope-MAP Update][az-acr-scope-map-update]. Por exemplo, para atualizar *MyScopeMap* para remover a ação de `content/write` no repositório de `samples/hello-world`:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Se o mapa de escopo estiver associado a mais de um token, o comando atualizará a permissão de todos os tokens associados.

Se você quiser atualizar um token com um mapa de escopo diferente, execute [AZ ACR token Update][az-acr-token-update]. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Depois de atualizar um token ou de um mapa de escopo associado a um token, as alterações de permissão entram em vigor na próxima `docker login` ou outra autenticação usando o token.

Depois de atualizar um token, talvez você queira gerar novas senhas para acessar o registro. Execute a [geração de credencial AZ ACR token][az-acr-token-credential-generate]. Por exemplo:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Sobre permissões no escopo do repositório

### <a name="concepts"></a>Conceitos

Para configurar permissões no escopo do repositório, você cria um *token de acesso* e um *mapa de escopo* associado usando comandos no CLI do Azure.

* Um **token de acesso** é uma credencial usada com uma senha para autenticar com o registro. Há *ações* permitidas no escopo de cada token associadas a um ou mais repositórios. Você pode definir um tempo de expiração para cada token. 

* As **ações** em cada repositório especificado incluem um ou mais dos itens a seguir.

  |Ação  |Descrição  |
  |---------|---------|
  |`content/read`     |  Ler dados do repositório. Por exemplo, receba um artefato.  |
  |`metadata/read`    | Ler metadados do repositório. Por exemplo, listar marcas ou mostrar metadados de manifesto.   |
  |`content/write`     |  Grave dados no repositório. Use com `content/read` para enviar um artefato por push.    |
  |`metadata/write`     |  Grave metadados no repositório. Por exemplo, atualize atributos de manifesto.  |
  |`content/delete`    | Remova os dados do repositório. Por exemplo, exclua um repositório ou um manifesto. |

* Um **mapa de escopo** é um objeto de registro que agrupa permissões de repositório que você aplica a um token ou que pode reaplicar a outros tokens. Se você não aplicar um mapa de escopo ao criar um token, um mapa de escopo será criado automaticamente para você, para salvar as configurações de permissão. 

  Um mapa de escopo ajuda a configurar vários usuários com acesso idêntico a um conjunto de repositórios. O registro de contêiner do Azure também fornece mapas de escopo definidos pelo sistema que você pode aplicar ao criar tokens de acesso.

A imagem a seguir resume a relação entre tokens e mapas de escopo. 

![Mapas e tokens de escopo do registro](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Cenários

Os cenários para usar um token de acesso incluem:

* Fornecer dispositivos IoT com tokens individuais para extrair uma imagem de um repositório
* Fornecer uma organização externa com permissões para um repositório específico 
* Limite o acesso do repositório a grupos de usuários específicos em sua organização. Por exemplo, forneça acesso de gravação e leitura aos desenvolvedores que criam imagens direcionadas a repositórios específicos e acesso de leitura às equipes que implantam esses repositórios.

### <a name="authentication-using-token"></a>Autenticação usando token

Use um nome de token como um nome de usuário e uma de suas senhas associadas para autenticar com o registro de destino. O método de autenticação depende das ações configuradas.

### <a name="contentread-or-contentwrite"></a>conteúdo/leitura ou conteúdo/gravação

Se o token permitir apenas `content/read` ou `content/write` ações, forneça credenciais de token em qualquer um dos seguintes fluxos de autenticação:

* Autenticar com o Docker usando o `docker login`
* Autentique com o registro usando o comando [AZ ACR login][az-acr-login] no CLI do Azure

Após a autenticação, o token permite as ações configuradas no repositório com escopo ou repositórios. Por exemplo, se o token permitir a ação de `content/read` em um repositório, `docker pull` operações serão permitidas em imagens nesse repositório.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadados/leitura, metadados/gravação ou conteúdo/exclusão

Se o token permitir ações de `metadata/read`, `metadata/write`ou `content/delete` em um repositório, as credenciais de token deverão ser fornecidas como parâmetros com os comandos de [repositório AZ ACR][az-acr-repository] relacionados no CLI do Azure.

Por exemplo, se `metadata/read` ações forem permitidas em um repositório, passe as credenciais do token ao executar o comando [AZ ACR Repository show-Tags][az-acr-repository-show-tags] para listar as marcas.

## <a name="next-steps"></a>Passos seguintes

* Para gerenciar mapas de escopo e tokens de acesso, use comandos adicionais nos grupos de comando [AZ ACR Scope-MAP][az-acr-scope-map] e [AZ ACR token][az-acr-token] .
* Consulte a [visão geral de autenticação](container-registry-authentication.md) para ver cenários para autenticar com um registro de contêiner do Azure usando uma conta de administrador ou uma identidade de Azure Active Directory.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
