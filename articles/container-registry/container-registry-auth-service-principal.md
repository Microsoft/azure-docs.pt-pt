---
title: Autenticar com a entidade de serviço
description: Forneça acesso a imagens em seu registro de contêiner privado usando uma entidade de serviço Azure Active Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455402"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do registro de contêiner do Azure com entidades de serviço

Você pode usar uma entidade de serviço do Azure Active Directory (AD do Azure) para fornecer `docker push` de imagem de contêiner e `pull` acesso ao registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a aplicativos e serviços "sem periféricos".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

As *entidades de serviço* do Azure ad fornecem acesso aos recursos do Azure em sua assinatura. Você pode considerar uma entidade de serviço como uma identidade de usuário para um serviço, em que "serviço" é qualquer aplicativo, serviço ou plataforma que precise acessar os recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas para os recursos que você especificar. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do registro de contêiner do Azure, você pode criar uma entidade de serviço do Azure AD com pull, push e pull ou outras permissões para seu registro particular no Azure. Para obter uma lista completa, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner privado. Crie entidades de serviço diferentes para cada um dos seus aplicativos ou serviços, cada um com direitos de acesso adaptados ao registro. E, como você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar credenciais ou revogar o acesso apenas para a entidade de serviço (e, portanto, o aplicativo) escolhido.

Por exemplo, configure seu aplicativo Web para usar uma entidade de serviço que fornece a ele somente acesso de `pull` de imagem, enquanto o sistema de compilação usa uma entidade de serviço que fornece o acesso `push` e `pull`. Se o desenvolvimento do seu aplicativo mudar de mãos, você poderá girar suas credenciais de entidade de serviço sem afetar o sistema de compilação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer acesso ao registro em **cenários sem periféricos**. Ou seja, qualquer aplicativo, serviço ou script que deva enviar por Push ou extrair imagens de contêiner de maneira automatizada ou autônoma. Por exemplo:

  * *Pull*: implante contêineres de um registro para sistemas de orquestração, incluindo KUBERNETES, DC/so e Docker Swarm. Você também pode efetuar pull de registros de contêiner para serviços do Azure relacionados, como o [AKs (serviço kubernetes do Azure)](../aks/cluster-container-registry-integration.md), [instâncias de contêiner do Azure](container-registry-auth-aci.md), [serviço de aplicativo](../app-service/index.yml), [lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e outros.

  * *Push*: crie imagens de contêiner e envie-as por push para um registro usando soluções de implantação e integração contínuas como Azure pipelines ou Jenkins.

Para acesso individual a um registro, como quando você efetua pull manualmente de uma imagem de contêiner para sua estação de trabalho de desenvolvimento, é recomendável usar sua própria [identidade do Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) em vez de acesso ao registro (por exemplo, com [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para CLI do Azure no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autenticar com a entidade de serviço

Depois que você tiver uma entidade de serviço que concedeu acesso ao registro de contêiner, você pode configurar suas credenciais para acessar serviços e aplicativos "sem periféricos" ou inseri-los usando o comando `docker login`. Utilize os seguintes valores:

* **Nome de usuário** -ID do aplicativo da entidade de serviço (também chamada de *ID do cliente*)
* **Senha** -senha da entidade de serviço (também chamada de *segredo do cliente*)

Cada valor é um GUID do formulário `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Você pode regenerar a senha de uma entidade de serviço executando o comando [AZ ad SP Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Usar credenciais com os serviços do Azure

Você pode usar as credenciais da entidade de serviço de qualquer serviço do Azure que se autentique com um registro de contêiner do Azure.  Use as credenciais de entidade de serviço em vez das credenciais de administrador do registro para uma variedade de cenários.

Por exemplo, use as credenciais para efetuar pull de uma imagem de um registro de contêiner do Azure para [instâncias de contêiner do Azure](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Usar com o logon do Docker

Você pode executar `docker login` usando uma entidade de serviço. No exemplo a seguir, a ID do aplicativo da entidade de serviço é passada na variável de ambiente `$SP_APP_ID`e a senha na variável `$SP_PASSWD`. Para obter as práticas recomendadas para gerenciar as credenciais do Docker, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Depois de conectado, o Docker armazena as credenciais em cache.

### <a name="use-with-certificate"></a>Usar com certificado

Se você tiver adicionado um certificado à sua entidade de serviço, poderá entrar no CLI do Azure com autenticação baseada em certificado e, em seguida, usar o comando [AZ ACR login][az-acr-login] para acessar um registro. Usar um certificado como um segredo em vez de uma senha fornece segurança adicional quando você usa a CLI. 

Um certificado autoassinado pode ser criado quando você [cria uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli). Ou adicione um ou mais certificados a uma entidade de serviço existente. Por exemplo, se você usar um dos scripts neste artigo para criar ou atualizar uma entidade de serviço com direitos para extrair ou enviar imagens por push de um registro, adicione um certificado usando o comando [AZ ad SP Credential rereset][az-ad-sp-credential-reset] .

Para usar a entidade de serviço com o certificado para [entrar no CLI do Azure](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), o certificado deve estar no formato PEM e incluir a chave privada. Se o certificado não estiver no formato necessário, use uma ferramenta como `openssl` para convertê-lo. Ao executar o [AZ login][az-login] para entrar na CLI usando a entidade de serviço, forneça também a ID do aplicativo da entidade de serviço e a ID do locatário do Active Directory. O exemplo a seguir mostra esses valores como variáveis de ambiente:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Em seguida, execute [AZ ACR login][az-acr-login] para autenticar com o registro:

```azurecli
az acr login --name myregistry
```

A CLI usa o token criado quando você executou `az login` para autenticar a sessão com o registro.

## <a name="next-steps"></a>Passos seguintes

* Consulte a [visão geral de autenticação](container-registry-authentication.md) para outros cenários para autenticar com um registro de contêiner do Azure.

* Para obter um exemplo de como usar um cofre de chaves do Azure para armazenar e recuperar as credenciais da entidade de serviço para um registro de contêiner, consulte o tutorial para [criar e implantar uma imagem de contêiner usando tarefas ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
