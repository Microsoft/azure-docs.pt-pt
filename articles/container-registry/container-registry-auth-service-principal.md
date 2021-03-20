---
title: Autenticar com o principal de serviço
description: Fornecer acesso a imagens no seu registo de contentores privados utilizando um diretor de serviço do Azure Ative Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 8d49628576a1c337efaea3e5286fef00e39def17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86259141"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do Registo de Contentores Azure com principais serviços

Pode utilizar um diretor de serviço Azure Ative (Azure AD) para fornecer imagem de contentor `docker push` e acesso ao registo do seu `pull` contentor. Ao utilizar um principal serviço, pode fornecer acesso a serviços e aplicações "sem cabeça".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

Os *principais serviços AZure* AD fornecem acesso aos recursos Azure dentro da sua subscrição. Pode pensar num principal de serviço como uma identidade de utilizador para um serviço, onde "serviço" é qualquer aplicação, serviço ou plataforma que precise de aceder aos recursos. Pode configurar um diretor de serviço com direitos de acesso apenas aos recursos que especifique. Em seguida, configuure a sua aplicação ou serviço para usar as credenciais do diretor de serviço para aceder a esses recursos.

No contexto do Registo de Contentores Azure, pode criar um principal de serviço AZure AD com puxar, empurrar e puxar, ou outras permissões para o seu registo privado em Azure. Para obter uma lista completa, consulte [as funções e permissões do Registo do Contentor de Azure](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Por que usar um diretor de serviço?

Ao utilizar um diretor de serviço Azure AD, pode fornecer acesso ao seu registo de contentores privados. Crie diferentes princípios de serviço para cada uma das suas aplicações ou serviços, cada um com direitos de acesso personalizados ao seu registo. E, como pode evitar a partilha de credenciais entre serviços e aplicações, pode rodar credenciais ou revogar o acesso apenas ao principal do serviço (e, portanto, à aplicação) que escolher.

Por exemplo, configurar a sua aplicação web para usar um principal de serviço que lhe forneça acesso de imagem `pull` apenas, enquanto o seu sistema de construção utiliza um principal de serviço que lhe fornece tanto `push` e `pull` acesso. Se o desenvolvimento da sua aplicação mudar de mãos, pode rodar as suas credenciais principais de serviço sem afetar o sistema de construção.

## <a name="when-to-use-a-service-principal"></a>Quando usar um principal de serviço

Deve utilizar um principal de serviço para fornecer acesso ao registo em **cenários sem cabeça**. Ou seja, qualquer aplicação, serviço ou script que deve empurrar ou puxar imagens de contentores de forma automatizada ou de outra forma sem supervisão. Por exemplo:

  * *Pull*: Desloque os recipientes de um registo para sistemas de orquestração, incluindo Kubernetes, DC/OS e Docker Swarm. Também pode retirar dos registos de contentores para serviços Azure relacionados, tais como [Azure Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch,](../batch/index.yml) [Service Fabric,](../service-fabric/index.yml)entre outros.

  * *Push*: Construa imagens de contentores e empurre-as para um registo utilizando soluções de integração e implantação contínuas como a Azure Pipelines ou Jenkins.

Para o acesso individual a um registo, como quando puxa manualmente uma imagem de contentor para a sua estação de trabalho de desenvolvimento, recomendamos a utilização da sua própria [identidade AZure AD](container-registry-authentication.md#individual-login-with-azure-ad) em vez de para acesso ao registo (por exemplo, com [login az acr).][az-acr-login]

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Scripts de exemplo

Pode encontrar os scripts da amostra anterior para O Azure CLI no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autenticar com o diretor de serviço

Uma vez que tenha um principal de serviço que tenha acesso ao seu registo de contentores, pode configurar as suas credenciais para acesso a serviços e aplicações "sem cabeça" ou inseri-las usando o `docker login` comando. Utilize os seguintes valores:

* **Nome do utilizador** - ID de aplicação principal de serviço (também chamado *ID do cliente)*
* **Palavra-passe** - senha principal do serviço (também chamada *de segredo do cliente)*

Cada valor é um GUID do `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` formulário. 

> [!TIP]
> Pode regenerar a palavra-passe de um diretor de serviço executando o comando [ad sp reset-credentials.](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
>

### <a name="use-credentials-with-azure-services"></a>Use credenciais com serviços Azure

Pode utilizar as principais credenciais de serviço de qualquer serviço Azure que autente com um registo de contentores Azure.  Utilize credenciais principais de serviço em vez das credenciais de administração do registo para uma variedade de cenários.

Por exemplo, utilize as credenciais para retirar uma imagem de um registo de contentores Azure para [instâncias de contentores Azure](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Use com login de estivador

Pode correr `docker login` com um diretor de serviço. No exemplo seguinte, o ID principal de aplicação de serviço é passado na variável `$SP_APP_ID` ambiente, e a palavra-passe na variável `$SP_PASSWD` . Para obter as melhores práticas para gerir as credenciais do Docker, consulte a referência do comando de login do [docker.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Uma vez iniciado, Docker caches as credenciais.

### <a name="use-with-certificate"></a>Uso com certificado

Se adicionou um certificado ao seu principal de serviço, pode inscrever-se no CLI Azure com autenticação baseada em certificados e, em seguida, utilizar o comando [de login az acr][az-acr-login] para aceder a um registo. A utilização de um certificado como segredo em vez de uma palavra-passe proporciona segurança adicional quando utiliza o CLI. 

Um certificado auto-assinado pode ser criado quando [se cria um principal serviço.](/cli/azure/create-an-azure-service-principal-azure-cli) Ou, adicione um ou mais certificados a um diretor de serviço existente. Por exemplo, se utilizar um dos scripts deste artigo para criar ou atualizar um principal de serviço com direitos de puxar ou empurrar imagens de um registo, adicione um certificado usando o comando [de reset de credencial ad sp.][az-ad-sp-credential-reset]

Para utilizar o principal de serviço com certificado para [assinar no Azure CLI,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)o certificado deve estar em formato PEM e incluir a chave privada. Se o seu certificado não estiver no formato exigido, utilize uma ferramenta para `openssl` convertê-lo. Quando fizer [login][az-login] para iniciar sessão no CLI utilizando o principal do serviço, também forneça o ID de aplicação do diretor de serviço e o ID do inquilino do Ative Directory. O exemplo a seguir mostra estes valores como variáveis ambientais:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Em seguida, faça [login az acr][az-acr-login] para autenticar com o registo:

```azurecli
az acr login --name myregistry
```

O CLI utiliza o token criado quando correu `az login` para autenticar a sua sessão com o registo.

## <a name="next-steps"></a>Passos seguintes

* Consulte a [visão geral](container-registry-authentication.md) da autenticação para outros cenários a autenticar com um registo de contentores Azure.

* Para um exemplo de utilização de um cofre-chave Azure para armazenar e recuperar as principais credenciais de serviço para um registo de contentores, consulte o tutorial para [construir e implantar uma imagem de contentor utilizando tarefas ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
