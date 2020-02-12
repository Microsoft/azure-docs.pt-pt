---
title: Opções de autenticação do registro
description: Opções de autenticação para um registo privado de contentores Azure, incluindo a assinatura com uma identidade azure Ative Directory, utilizando diretores de serviço, e usando credenciais de administração opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152948"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com um registo de contentores Azure

Há várias maneiras de se autenticar com um registro de contêiner do Azure, sendo que cada um é aplicável a um ou mais cenários de uso do registro.

As formas recomendadas incluem a autenticação a um registo diretamente através de [login individual](#individual-login-with-azure-ad), ou as suas aplicações e orquestradores de contentores podem realizar a autenticação sem vigilância, ou "sem cabeça", utilizando um [diretor](#service-principal)de serviço azure Ative Directory (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

A tabela que se segue lista os métodos de autenticação disponíveis e os cenários recomendados. Consulte o conteúdo ligado para obter mais detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade de anúncio individual](#individual-login-with-azure-ad)                | `az acr login` em  Azure CLI                            | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | O token ad deve ser renovado a cada 3 horas     |
| [  principal de serviço de ad](#service-principal)                 | `docker login`<br/><br/>`az acr login` em Azure CLI<br/><br/> Definições de login de registo em APIs ou ferramentas<br/><br/> [Kubernetes puxam](container-registry-auth-kubernetes.md)     secretos                                       | Impulso não acompanhado do oleoduto CI/CD<br/><br/> Puxão não acompanhado para serviços azure ou externos  | Sim                              | A caducidade da senha do SP é de 1 ano       |                                                           
| [Integrar com a AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar o registo quando o cluster AKS foi criado ou atualizado  | Puxe sem vigilância para o cluster AKS                                                  | Não, só puxar o acesso             | Disponível apenas com cluster AKS            |
| [Identidade gerida para os recursos do Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` em Azure CLI                                       | Impulso não acompanhado do oleoduto Azure CI/CD<br/><br/> Puxão sem supervisão para os serviços do Azure<br/><br/>   | Sim                              | Utilizar apenas a partir de serviços Azure que [suportam identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [  de utilizador de administrador](#admin-account)                           | `docker login`                                          | Push/pull interativo por desenvolvedor ou tester individual                           | Não, puxe sempre e empurre o acesso  | Conta única por registo, não recomendada para vários utilizadores         |
| Acesso com [âmbito de repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` em Azure CLI   | Push/pull interativo para repositório por desenvolvedor ou tester individual<br/><br/> Empurre/puxe sem supervisão para o repositório por sistema individual ou dispositivo externo                  | Sim                              | Não está atualmente integrado com identidade ad  |

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar diretamente com o seu registo, como puxar imagens e empurrar imagens de uma estação de trabalho de desenvolvimento, autenticar utilizando o comando de [login az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) no [Azure CLI:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

Quando inicia sessão com `az acr login`, o CLI utiliza o token criado quando executou o [login az](/cli/azure/reference-index#az-login) para autenticar perfeitamente a sua sessão com o seu registo. Para completar o fluxo de autenticação, o Docker deve ser instalado e em funcionamento no seu ambiente. `az acr login` usa o cliente Docker para definir um token de Diretório Ativo Azure no ficheiro `docker.config`. Uma vez registado desta forma, as suas credenciais são em cache, e os comandos subsequentes `docker` na sua sessão não requerem um nome de utilizador ou senha.

> [!TIP]
> Use também `az acr login` para autenticar uma identidade individual quando quiser empurrar ou puxar artefactos que não sejam imagens do Docker para o seu registo, como [artefactos OCI](container-registry-oci-artifacts.md).  


Para acesso ao registo, o símbolo utilizado pelo `az acr login` é válido por **3 horas,** pelo que recomendamos que faça sempre login no registo antes de executar um comando `docker`. Se o seu símbolo expirar, pode refreá-lo usando o comando `az acr login` novamente para se reatentir. 

A utilização de `az acr login` com identidades Azure proporciona [acesso baseado em papéis.](../role-based-access-control/role-assignments-portal.md) Para alguns cenários, talvez você queira fazer logon em um registro com sua própria identidade individual no Azure AD. Para cenários de cross-service ou para lidar com as necessidades de um grupo de trabalho ou de um fluxo de trabalho de desenvolvimento onde não quer gerir o acesso individual, também pode iniciar sessão com uma [identidade gerida para os recursos Azure.](container-registry-authentication-managed-identity.md)

## <a name="service-principal"></a>Diretor de serviço

Se atribuir um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) ao seu registo, a sua aplicação ou serviço poderá utilizá-lo para autenticação sem cabeça. Os diretores de serviço permitem [o acesso baseado em papéis](../role-based-access-control/role-assignments-portal.md) a um registo, e você pode atribuir vários diretores de serviço a um registo. Várias entidades de serviço permitem que você defina o acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: puxar

* **AcrPush:** puxar e empurrar

* **Proprietário**: puxar, empurrar e atribuir funções a outros utilizadores

Para obter uma lista completa de funções, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores do Azure .

Para que os scripts CLI criem um principal de serviço para autenticação com um registo de contentores Azure, e mais orientação, consulte a autenticação do Registo de [Contentores Azure com os diretores](container-registry-auth-service-principal.md)de serviço .

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que é desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar suas credenciais no portal do Azure ou usando o CLI do Azure ou outras ferramentas do Azure.

> [!IMPORTANT]
> A conta de administração destina-se a um único utilizador a aceder ao registo, principalmente para efeitos de teste. Não recomendamos o compartilhamento das credenciais da conta de administrador entre vários usuários. Todos os usuários que se autenticam com a conta de administrador aparecem como um único usuário com acesso de push e pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam suas credenciais. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça.
>

A conta de administrador é fornecida com duas senhas, que podem ser regeneradas. Duas senhas permitem manter a conexão com o registro usando uma senha enquanto você gera novamente a outra. Se a conta de administração estiver ativada, pode passar o nome de utilizador e qualquer palavra-passe para o comando `docker login` quando solicitado para autenticação básica no registo. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/)

Para ativar o utilizador administrativo de um registo existente, pode utilizar o parâmetro `--admin-enabled` do comando de [atualização az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-update) no Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador administrativo no portal Azure navegando no seu registo, selecionando **teclas** de acesso em **DEFINIÇÕES** **e,** em seguida, ativar sob o **utilizador do Administrador**.

![Habilitar a IU do usuário administrador no portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Passos seguintes

* [Empurre a sua primeira imagem usando o Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
