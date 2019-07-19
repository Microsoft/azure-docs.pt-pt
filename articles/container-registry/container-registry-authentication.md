---
title: Autenticar com um registro de contêiner do Azure
description: Opções de autenticação para um registro de contêiner do Azure, incluindo a entrada com uma identidade de Azure Active Directory, o uso de entidades de serviço e o uso de credenciais de administrador opcionais.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 82fe80e098ee95c09c4a1400068ab813910e0e1a
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309840"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Autenticar com um registro de contêiner privado do Docker

Há várias maneiras de se autenticar com um registro de contêiner do Azure, sendo que cada um é aplicável a um ou mais cenários de uso do registro.

Você pode fazer logon em um registro diretamente por meio de [logon individual](#individual-login-with-azure-ad)ou seus aplicativos e orquestradores de contêiner podem executar a autenticação autônoma ou "sem periféricos" usando uma [entidade de serviço](#service-principal)Azure Active Directory (AD do Azure).

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar diretamente com o registro, como extrair imagens e enviar por push imagens de uma estação de trabalho de desenvolvimento, autentique usando o comando [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) no [CLI do Azure](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Quando você faz logon com `az acr login`o, a CLI usa o token criado quando você executou [AZ login](/cli/azure/reference-index#az-login) para autenticar a sessão diretamente com o registro. Depois de fazer logon dessa forma, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes em sua sessão não exigem um nome de usuário ou senha. 

Para acesso ao registro, o token usado `az acr login` pelo é válido por 1 hora, portanto, é recomendável que você sempre faça logon no registro antes `docker` de executar um comando. Se o token expirar, você poderá atualizá-lo usando `az acr login` o comando novamente para autenticar novamente. 

Usar `az acr login` com identidades do Azure fornece [acesso baseado em função](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, talvez você queira fazer logon em um registro com sua própria identidade individual no Azure AD. Para cenários de serviço cruzado ou para lidar com as necessidades de um grupo de trabalho em que você não deseja gerenciar o acesso individual, você também pode fazer logon com uma [identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Entidade de serviço

Se você atribuir uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao registro, seu aplicativo ou serviço poderá usá-la para autenticação sem periféricos. As entidades de serviço permitem o [acesso baseado em função](../role-based-access-control/role-assignments-portal.md) a um registro e você pode atribuir várias entidades de serviço a um registro. Várias entidades de serviço permitem que você defina o acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: pull

* **AcrPush**: efetuar pull e enviar por push

* **Proprietário**: efetuar pull, enviar por push e atribuir funções a outros usuários

Para obter uma lista completa de funções, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md).

Para scripts da CLI para criar uma ID do aplicativo principal de serviço e uma senha para autenticação com um registro de contêiner do Azure ou para usar uma entidade de serviço existente, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md).

As entidades de serviço habilitam a conectividade sem periféricos a um registro em cenários de pull e Push, como o seguinte:

  * Efetuar *pull*: Implante contêineres de um registro para sistemas de orquestração, incluindo kubernetes, DC/so e Docker Swarm. Você também pode efetuar pull de registros de contêiner para serviços do Azure relacionados, como o [serviço kubernetes do Azure](container-registry-auth-aks.md), [instâncias de contêiner do Azure](container-registry-auth-aci.md), [serviço de aplicativo](../app-service/index.yml), [lote](../batch/index.yml), [Service Fabric](/azure/service-fabric/)e outros.

  * *Enviar por push*: Crie imagens de contêiner e envie-as por push para um registro usando soluções de implantação e integração contínuas como Azure Pipelines ou Jenkins.

Você também pode fazer logon diretamente com uma entidade de serviço. Quando você executar o comando a seguir, forneça de forma interativa a appID (nome de usuário) e a senha da entidade de serviço quando solicitado. Para obter as práticas recomendadas para gerenciar as credenciais de logon, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Depois de conectado, o Docker armazena em cache as credenciais, de modo que você não precisa se lembrar da ID do aplicativo.

> [!TIP]
> Você pode regenerar a senha de uma entidade de serviço executando o comando [AZ ad SP Reset-Credentials](/cli/azure/ad/sp?view=azure-cli-latest) .
>

## <a name="admin-account"></a>Conta do administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que é desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar suas credenciais no portal do Azure ou usando o CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta de administrador é projetada para que um único usuário acesse o registro, principalmente para fins de teste. Não recomendamos o compartilhamento das credenciais da conta de administrador com vários usuários. Todos os usuários que se autenticam com a conta de administrador aparecem como um único usuário com acesso de push e pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam suas credenciais. A identidade individual é recomendada para usuários e entidades de serviço para cenários sem periféricos.
>

A conta de administrador é fornecida com duas senhas, que podem ser regeneradas. Duas senhas permitem manter a conexão com o registro usando uma senha enquanto você gera novamente a outra. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e `docker login` a senha para o comando quando a autenticação básica for solicitada ao registro. Por exemplo:

```
docker login myregistry.azurecr.io 
```


Para habilitar o usuário administrador para um registro existente, você pode usar o `--admin-enabled` parâmetro do comando [AZ ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) no CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Você pode habilitar o usuário administrador no portal do Azure navegando no registro, selecionando **chaves de acesso** em **configurações**e, em seguida, **habilitar** em **usuário administrador**.

![Habilitar a IU do usuário administrador no portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Passos seguintes

* [Envie por push sua primeira imagem usando o CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
