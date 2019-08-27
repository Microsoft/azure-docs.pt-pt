---
title: Autenticação do registro de contêiner do Azure com uma entidade de serviço
description: Forneça acesso a imagens em seu registro de contêiner privado usando uma entidade de serviço Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: bee8b801f46c0018e75d58f941470adcc271daf0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032368"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do registro de contêiner do Azure com entidades de serviço

Você pode usar uma entidade de serviço Azure Active Directory (AD do Azure) para fornecer `docker push` a `pull` imagem de contêiner e o acesso ao registro de contêiner. Usando uma entidade de serviço, você pode fornecer acesso a aplicativos e serviços "sem periféricos".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

As *entidades de serviço* do Azure ad fornecem acesso aos recursos do Azure em sua assinatura. Você pode considerar uma entidade de serviço como uma identidade de usuário para um serviço, em que "serviço" é qualquer aplicativo, serviço ou plataforma que precise acessar os recursos. Você pode configurar uma entidade de serviço com direitos de acesso com escopo apenas para os recursos que você especificar. Em seguida, configure seu aplicativo ou serviço para usar as credenciais da entidade de serviço para acessar esses recursos.

No contexto do registro de contêiner do Azure, você pode criar uma entidade de serviço do Azure AD com pull, push e pull ou outras permissões para seu registro particular no Azure. Para obter uma lista completa, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar uma entidade de serviço?

Usando uma entidade de serviço do Azure AD, você pode fornecer acesso com escopo ao seu registro de contêiner privado. Crie entidades de serviço diferentes para cada um dos seus aplicativos ou serviços, cada um com direitos de acesso adaptados ao registro. E, como você pode evitar o compartilhamento de credenciais entre serviços e aplicativos, você pode girar credenciais ou revogar o acesso apenas para a entidade de serviço (e, portanto, o aplicativo) escolhido.

Por exemplo, configure seu aplicativo Web para usar uma entidade de serviço que fornece somente acesso `pull` a imagens, enquanto o sistema de compilação usa uma entidade de serviço que fornece o `push` e `pull` o acesso. Se o desenvolvimento do seu aplicativo mudar de mãos, você poderá girar suas credenciais de princípio de serviço sem afetar o sistema de compilação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para fornecer acesso ao registro em **cenários**sem periféricos. Ou seja, qualquer aplicativo, serviço ou script que deva enviar por Push ou extrair imagens de contêiner de maneira automatizada ou autônoma. Por exemplo:

  * Efetuar *pull*: Implante contêineres de um registro para sistemas de orquestração, incluindo kubernetes, DC/so e Docker Swarm. Você também pode efetuar pull de registros de contêiner para serviços do Azure relacionados, como o [AKs (serviço kubernetes do Azure)](container-registry-auth-aks.md), [instâncias de contêiner do Azure](container-registry-auth-aci.md), [serviço de aplicativo](../app-service/index.yml), [lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e outros.

  * *Enviar por push*: Crie imagens de contêiner e envie-as por push para um registro usando soluções de implantação e integração contínuas como Azure Pipelines ou Jenkins.

Para acesso individual a um registro, como quando você efetua pull manualmente de uma imagem de contêiner para sua estação de trabalho de desenvolvimento, é recomendável usar sua própria [identidade do Azure ad](container-registry-authentication.md#individual-login-with-azure-ad) em vez de acesso ao registro (por exemplo, com [AZ ACR login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Scripts de exemplo

Você pode encontrar os scripts de exemplo anteriores para CLI do Azure no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autenticar com a entidade de serviço

Depois que você tiver uma entidade de serviço que concedeu acesso ao registro de contêiner, você pode configurar suas credenciais para acessar serviços e aplicativos "sem periféricos" ou inseri-los `docker login` usando o comando. Utilize os seguintes valores:

* **Nome de usuário** -ID do aplicativo da entidade de serviço (também chamada de *ID do cliente*)
* **Senha** -senha da entidade de serviço (também chamada de *segredo do cliente*)

Cada valor é um GUID do formulário `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Você pode regenerar a senha de uma entidade de serviço executando o comando [AZ ad SP Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) .
>

### <a name="use-credentials-with-azure-services"></a>Usar credenciais com os serviços do Azure

Você pode usar as credenciais da entidade de serviço de qualquer serviço do Azure que possa autenticar com um registro de contêiner do Azure. Os exemplos incluem:

* [Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure (AKS)](container-registry-auth-aks.md)
* [Autenticar com o registro de contêiner do Azure de ACI (instâncias de contêiner do Azure)](container-registry-auth-aci.md)

### <a name="use-with-docker-login"></a>Usar com o logon do Docker

Você também pode executar `docker login` usando uma entidade de serviço. No exemplo a seguir, a ID do aplicativo da entidade de serviço é passada na `$SP_APP_ID`variável de ambiente e a senha na `$SP_PASSWD`variável. Para obter as práticas recomendadas para gerenciar as credenciais do Docker, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Depois de conectado, o Docker armazena as credenciais em cache.

## <a name="next-steps"></a>Passos seguintes

* Consulte a [visão geral de autenticação](container-registry-authentication.md) para outros cenários para autenticar com um registro de contêiner do Azure.

* Para obter um exemplo de como usar um cofre de chaves do Azure para armazenar e recuperar as credenciais da entidade de serviço para um registro de contêiner, consulte o tutorial para [criar e implantar uma imagem de contêiner usando tarefas ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
