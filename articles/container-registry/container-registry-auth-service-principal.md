---
title: Autenticar com o principal de serviço
description: Forneça acesso a imagens no seu registo de contentores privados utilizando um diretor de serviço do Azure Ative Directory.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74455402"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Autenticação do Registo de Contentores Azure com diretores de serviço

Pode utilizar um diretor de serviço azure Ative Directory (Azure AD) para fornecer imagem `docker push` de contentor e `pull` acesso ao seu registo de contentores. Ao utilizar um diretor de serviço, pode fornecer acesso a serviços e aplicações "sem cabeça".

## <a name="what-is-a-service-principal"></a>O que é um principal de serviço?

Os *diretores* de serviço saem do serviço Azure AD para aceder aos recursos do Azure dentro da sua subscrição. Pode pensar num diretor de serviço como uma identidade de utilizador para um serviço, onde "serviço" é qualquer aplicação, serviço ou plataforma que precise de aceder aos recursos. Pode configurar um diretor de serviço com direitos de acesso apenas para os recursos que especifica. Em seguida, configure a sua aplicação ou serviço para utilizar as credenciais do diretor de serviço para aceder a esses recursos.

No contexto do Registo de Contentores Azure, pode criar um diretor de serviço Azure AD com puxão, impulso e puxão, ou outras permissões para o seu registo privado em Azure. Para obter uma lista completa, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores do Azure .

## <a name="why-use-a-service-principal"></a>Por que usar um diretor de serviço?

Ao utilizar um diretor de serviço da Azure AD, pode fornecer acesso ao seu registo de contentores privados. Crie diferentes diretores de serviço para cada uma das suas aplicações ou serviços, cada um com direitos de acesso personalizados ao seu registo. E, como pode evitar partilhar credenciais entre serviços e aplicações, pode rodar credenciais ou revogar o acesso apenas para o diretor de serviço (e, portanto, a aplicação) que escolher.

Por exemplo, configure a sua aplicação web para `pull` utilizar um diretor de serviço que lhe fornece apenas acesso à imagem, enquanto o seu sistema de construção utiliza um diretor de serviço que a fornece tanto `push` como `pull` acesso. Se o desenvolvimento da sua aplicação mudar de mãos, pode rodar as suas credenciais principais de serviço sem afetar o sistema de construção.

## <a name="when-to-use-a-service-principal"></a>Quando utilizar um diretor de serviço

Deve utilizar um diretor de serviço para fornecer acesso ao registo em **cenários sem cabeça**. Ou seja, qualquer aplicação, serviço ou script que deve empurrar ou puxar imagens de contentores de forma automatizada ou de outra forma sem vigilância. Por exemplo:

  * *Pull*: Desloque os recipientes de um registo para sistemas de orquestração, incluindo Kubernetes, DC/OS e Docker Swarm. Também pode retirar dos registos de contentores para serviços relacionados com o Azure [Kubernetes Service (AKS),](../aks/cluster-container-registry-integration.md) [Azure Container Instances,](container-registry-auth-aci.md) [App Service,](../app-service/index.yml) [Batch,](../batch/index.yml) [Service Fabric,](/azure/service-fabric/)entre outros.

  * *Push*: Construa imagens de contentores e empurre-as para um registo utilizando soluções contínuas de integração e implantação como o Azure Pipelines ou o Jenkins.

Para acesso individual a um registo, como quando puxa manualmente uma imagem de contentor para a sua estação de trabalho de desenvolvimento, recomendamos a utilização da sua própria [identidade Azure AD](container-registry-authentication.md#individual-login-with-azure-ad) para acesso ao registo (por exemplo, com [login az acr).][az-acr-login]

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Scripts de exemplo

Pode encontrar os scripts de amostra anteriores para o Azure CLI no GitHub, bem como versões para o Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Autenticar com o diretor de serviço

Uma vez que tenha um diretor de serviço que tenha acesso ao seu registo de contentores, pode configurar as `docker login` suas credenciais para acesso a serviços e aplicações "sem cabeça", ou insira-as usando o comando. Utilize os seguintes valores:

* **Nome do utilizador** - ID de aplicação principal de serviço (também chamado *ID do cliente)*
* **Palavra-passe** - senha principal de serviço (também chamada de segredo de *cliente)*

Cada valor é um `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID da forma. 

> [!TIP]
> Pode regenerar a palavra-passe de um diretor de serviço executando o comando de [credenciais de reset az ad sp.](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
>

### <a name="use-credentials-with-azure-services"></a>Utilize credenciais com serviços Azure

Pode utilizar credenciais principais de serviço de qualquer serviço Azure que autentica com um registo de contentores Azure.  Utilize credenciais principais de serviço em vez das credenciais de administração do registo para uma variedade de cenários.

Por exemplo, utilize as credenciais para retirar uma imagem de um registo de contentores Azure para as instâncias de [contentores de Azure](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Utilização com login de estivador

Pode correr `docker login` com um diretor de serviço. No exemplo seguinte, o ID de aplicação `$SP_APP_ID`principal do serviço é `$SP_PASSWD`passado na variável ambiente , e a palavra-passe na variável . Para obter as melhores práticas para gerir as credenciais do Docker, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/)

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Uma vez registado, Docker caches as credenciais.

### <a name="use-with-certificate"></a>Utilização com certificado

Se tiver adicionado um certificado ao seu diretor de serviço, pode assinar no Azure CLI com autenticação baseada em certificados e, em seguida, utilizar o comando de [login az acr][az-acr-login] para aceder a um registo. A utilização de um certificado como segredo em vez de uma palavra-passe proporciona segurança adicional quando utiliza o CLI. 

Um certificado auto-assinado pode ser criado quando se cria um diretor de [serviço.](/cli/azure/create-an-azure-service-principal-azure-cli) Ou adicionar um ou mais certificados a um diretor de serviço existente. Por exemplo, se utilizar um dos scripts deste artigo para criar ou atualizar um diretor de serviço com direitos de puxar ou empurrar imagens de um registo, adicione um certificado utilizando o comando de [reset credencial az ad sp.][az-ad-sp-credential-reset]

Para utilizar o diretor de serviço com certificado para [assinar no Azure CLI,](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)o certificado deve estar em formato PEM e incluir a chave privada. Se o seu certificado não estiver no formato `openssl` exigido, utilize uma ferramenta como a sua conversão. Quando você executar [az login][az-login] para iniciar sessão no CLI usando o diretor de serviço, também fornecer o ID de aplicação do diretor de serviço e o ID do inquilino do Diretório Ativo. O exemplo que se segue mostra estes valores como variáveis ambientais:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Em seguida, executar [login az acr][az-acr-login] para autenticar com o registo:

```azurecli
az acr login --name myregistry
```

O CLI usa o símbolo `az login` criado quando correu para autenticar a sua sessão com o registo.

## <a name="next-steps"></a>Passos seguintes

* Consulte a visão geral da [autenticação](container-registry-authentication.md) para outros cenários para autenticar com um registo de contentores Azure.

* Para um exemplo de utilização de um cofre chave Azure para armazenar e recuperar as credenciais principais do serviço para um registo de contentores, consulte o tutorial para construir e implementar uma imagem de [contentor utilizando tarefas ACR](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
