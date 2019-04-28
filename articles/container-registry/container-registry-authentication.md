---
title: Autenticar com um Azure container registry
description: Opções de autenticação para um Azure container registry, incluindo o início de sessão com uma identidade do Azure Active Directory, utilizando principais de serviço e com credenciais de administrador opcional.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9682b9b832a8fd7374cb84e8cc6faad69df15945
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333880"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um registo privado de contentor do Docker

Existem várias formas de autenticar com um Azure container registry, cada um dos quais é aplicável a um ou mais cenários de utilização do registo.

Pode iniciar sessão para um registo diretamente via [início de sessão individual](#individual-login-with-azure-ad), ou de seus aplicativos e orquestradores de contentor podem efetuar a autenticação automática, ou "sem interface", com um Azure Active Directory (Azure AD) [ principal de serviço](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Início de sessão individual com o Azure AD

Ao trabalhar com o registo diretamente, como extrair imagens e enviar imagens a partir de uma estação de trabalho de desenvolvimento, autenticar com o [início de sessão az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) comando no [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando iniciar sessão com `az acr login`, a CLI utiliza o token criado quando tiver executado [início de sessão az](/cli/azure/reference-index#az-login) para autenticar facilmente a sua sessão com o registo. Uma vez que já iniciou sessão desse modo, as suas credenciais estão em cache e subsequentes `docker` comandos na sua sessão não necessitam de um nome de utilizador ou palavra-passe. 

Para obter acesso de registo, o token utilizado pelo `az acr login` é válido durante uma hora, pelo que recomendamos que sempre iniciar sessão registo antes de executar um `docker` comando. Se o seu token expira, pode atualizá-la utilizando o `az acr login` comando novamente para o autenticar. 

Usando `az acr login` com identidades do Azure fornece [acesso baseado em funções](../role-based-access-control/role-assignments-portal.md). Para alguns cenários talvez queira iniciar sessão para um registo com a sua própria identidade individual no Azure AD. Para cenários de serviço cruzado, ou para processar as necessidades de um grupo de trabalho em que não pretende gerir o acesso individual, também pode iniciar sessão com uma [identidade de recursos do Azure gerida](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Principal de serviço

Se atribuir um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) para o seu registo, a aplicação ou serviço pode utilizá-lo para a autenticação sem periféricos. Principais de serviço permitem [acesso baseado em funções](../role-based-access-control/role-assignments-portal.md) para um registo, e pode atribuir múltiplos principais de serviço para um registo. Vários principais de serviço permitem-lhe definir acesso diferentes para diferentes aplicações.

As funções disponíveis para um registo de contentor incluem:

* **AcrPull**: pull

* **AcrPush**: enviar e receber

* **Proprietário**: extrair, push e atribuir funções a outros utilizadores

Para obter uma lista completa de funções, consulte [permissões e funções do Azure Container Registry](container-registry-roles.md).

Para scripts da CLI para criar um ID de aplicação do principal de serviço e a palavra-passe para autenticar com um registo de contentor do Azure ou para utilizar um principal de serviço existente, consulte [autenticação do Azure Container Registry com principais de serviço](container-registry-auth-service-principal.md).

Principais de serviço permitem a conectividade sem interface para um registo em cenários de push e pull semelhante ao seguinte:

  * *Extrair*: Implemente contentores a partir de um registo a sistemas de orquestração, incluindo o Kubernetes, DC/OS e Docker Swarm. Pode também tirar de registos de contentores para serviços relacionados do Azure, tal como [do Azure Kubernetes Service](container-registry-auth-aks.md), [Azure Container Instances](container-registry-auth-aci.md), [serviço de aplicações](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)entre outros.

  * *Push*: Criar imagens de contentor e emiti-las para um registo com soluções de integração e implementação contínuas, como os Pipelines do Azure ou o Jenkins.

Também pode iniciar sessão diretamente com um principal de serviço. Quando executar o seguinte comando, forneça interativamente o appID principal de serviço (nome de utilizador) e a palavra-passe quando lhe for pedido. Para melhores práticas gerir as credenciais de início de sessão, consulte a [início de sessão do docker](https://docs.docker.com/engine/reference/commandline/login/) referência do comando:

```
docker login myregistry.azurecr.io
```

Depois de iniciar sessão, Docker coloca em cache as credenciais, pelo que não precisa se lembrar a ID da aplicação.

> [!TIP]
> Pode voltar a gerar a palavra-passe de um principal de serviço ao executar o [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest) comando.
>

## <a name="admin-account"></a>Conta de admin

Cada registo de contentor inclui uma conta de utilizador de administrador, o que está desativada por predefinição. Pode permitir que o utilizador de administrador e gerir as respetivas credenciais no portal do Azure ou ao utilizar a CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta de administrador foi concebida para um único utilizador aceder ao registo, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta de administrador com vários utilizadores. Todos os utilizadores a autenticação com a conta de administrador são apresentados como um único utilizador com acesso de push e pull para o registo. Alterar ou desativar esta conta Desabilita o acesso de registo para todos os utilizadores que utilizam as suas credenciais. Identidade individual é recomendada para utilizadores e os principais de serviço para cenários sem periféricos.
>

A conta de administrador é fornecido com duas palavras-passe, que podem ser regeneradas. Duas palavras-passe permitem-lhe manter a ligação ao registo, utilizando uma palavra-passe enquanto volta a gerar a outra. Se a conta de administrador estiver ativada, pode passar o nome de utilizador e a palavra-passe para o `docker login` quando lhe for pedido para a autenticação básica para o registo de comando. Por exemplo:

```
docker login myregistry.azurecr.io 
```


Para permitir que o utilizador de administrador num registo existente, pode utilizar o `--admin-enabled` parâmetro do [atualização do az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-update) comando na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador de administrador no portal do Azure ao navegar até seu registro, selecionando **chaves de acesso** em **definições**, em seguida, **ativar** em **administrador utilizador**.

![Ative o utilizador de administrador da interface do Usuário no portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Passos Seguintes

* [Enviar a sua primeira imagem com a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
