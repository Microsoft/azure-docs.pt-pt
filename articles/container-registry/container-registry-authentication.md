---
title: Opções de autenticação do registo
description: Opções de autenticação para um registo privado de contentores Azure, incluindo a assinatura com uma identidade azure Ative Directory, utilizando diretores de serviço, e usando credenciais de administração opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79247049"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com um registo de contentores Azure

Existem várias formas de autenticar com um registo de contentores Azure, cada um dos quais aplicável a um ou mais cenários de utilização do registo.

As formas recomendadas incluem a autenticação a um registo diretamente através de [login individual](#individual-login-with-azure-ad), ou as suas aplicações e orquestradores de contentores podem realizar a autenticação sem vigilância, ou "sem cabeça", utilizando um [diretor](#service-principal)de serviço azure Ative Directory (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

A tabela que se segue lista os métodos de autenticação disponíveis e os cenários recomendados. Consulte o conteúdo ligado para obter mais detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade AD individual](#individual-login-with-azure-ad)                | `az acr login` em Azure CLI                             | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | O token ad deve ser renovado a cada 3 horas     |
| [Diretor de serviço aD](#service-principal)                  | `docker login`<br/><br/>`az acr login`em Azure CLI<br/><br/> Definições de login de registo em APIs ou ferramentas<br/><br/> [Kubernetes puxam segredo](container-registry-auth-kubernetes.md)                                           | Impulso não acompanhado do oleoduto CI/CD<br/><br/> Puxão não acompanhado para serviços azure ou externos  | Sim                              | A caducidade da senha do SP é de 1 ano       |                                                           
| [Integrar com aks](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar o registo quando o cluster AKS foi criado ou atualizado  | Puxe sem vigilância para o cluster AKS                                                  | Não, só puxar o acesso             | Disponível apenas com cluster AKS            |
| [Identidade gerida para recursos Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` em Azure CLI                                       | Impulso não acompanhado do oleoduto Azure CI/CD<br/><br/> Puxão sem supervisão para os serviços do Azure<br/><br/>   | Sim                              | Utilizar apenas a partir de serviços Azure que [suportam identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Utilizador de administrador](#admin-account)                            | `docker login`                                          | Push/pull interativo por desenvolvedor ou tester individual                           | Não, puxe sempre e empurre o acesso  | Conta única por registo, não recomendada para vários utilizadores         |
| [Ficha de acesso com âmbito de repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`em Azure CLI   | Push/pull interativo para repositório por desenvolvedor ou tester individual<br/><br/> Empurre/puxe sem supervisão para o repositório por sistema individual ou dispositivo externo                  | Sim                              | Não está atualmente integrado com identidade ad  |

## <a name="individual-login-with-azure-ad"></a>Login individual com Azure AD

Ao trabalhar diretamente com o seu registo, como puxar imagens e empurrar imagens de uma estação de trabalho de desenvolvimento, autenticar utilizando o comando de [login az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) no [Azure CLI:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

Quando faz login com `az acr login`, o CLI utiliza o token criado quando executou o [login az](/cli/azure/reference-index#az-login) para autenticar perfeitamente a sua sessão com o seu registo. Para completar o fluxo de autenticação, o Docker deve ser instalado e em funcionamento no seu ambiente. `az acr login`usa o cliente Docker para definir um token `docker.config` de Diretório Ativo Azure no ficheiro. Uma vez registado desta forma, as suas credenciais `docker` estão em cache, e os comandos subsequentes na sua sessão não requerem um nome de utilizador ou senha.

> [!TIP]
> Use `az acr login` também para autenticar uma identidade individual quando pretende empurrar ou puxar artefactos para além das imagens do Docker para o seu registo, como [artefactos OCI](container-registry-oci-artifacts.md).  


Para acesso ao registo, o `az acr login` token utilizado é válido por **3 horas,** pelo que recomendamos que faça sempre login no registo antes de executar um `docker` comando. Se o seu símbolo expirar, pode refreá-lo usando o `az acr login` comando novamente para reautenticar. 

A `az acr login` utilização com identidades Azure proporciona [acesso baseado em papéis.](../role-based-access-control/role-assignments-portal.md) Para alguns cenários, pode querer iniciar sessão num registo com a sua própria identidade individual em Azure AD. Para cenários de cross-service ou para lidar com as necessidades de um grupo de trabalho ou de um fluxo de trabalho de desenvolvimento onde não quer gerir o acesso individual, também pode iniciar sessão com uma [identidade gerida para os recursos Azure.](container-registry-authentication-managed-identity.md)

## <a name="service-principal"></a>Service principal (Principal de serviço)

Se atribuir um diretor de [serviço](../active-directory/develop/app-objects-and-service-principals.md) ao seu registo, a sua aplicação ou serviço poderá utilizá-lo para autenticação sem cabeça. Os diretores de serviço permitem [o acesso baseado em papéis](../role-based-access-control/role-assignments-portal.md) a um registo, e você pode atribuir vários diretores de serviço a um registo. Vários diretores de serviço permitem definir diferentes acessos para diferentes aplicações.

As funções disponíveis para um registo de contentores incluem:

* **AcrPull**: puxar

* **AcrPush:** puxar e empurrar

* **Proprietário**: puxar, empurrar e atribuir funções a outros utilizadores

Para obter uma lista completa de funções, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores do Azure .

Para que os scripts CLI criem um principal de serviço para autenticação com um registo de contentores Azure, e mais orientação, consulte a autenticação do Registo de [Contentores Azure com os diretores](container-registry-auth-service-principal.md)de serviço .

## <a name="admin-account"></a>Conta de administrador

Cada registo de contentores inclui uma conta de utilizador de administração, que é desativada por defeito. Pode ativar o utilizador administrativo e gerir as suas credenciais no portal Azure, ou utilizando o Azure CLI ou outras ferramentas Azure.

> [!IMPORTANT]
> A conta de administração destina-se a um único utilizador a aceder ao registo, principalmente para efeitos de teste. Não recomendamos a partilha das credenciais de conta de administração entre vários utilizadores. Todos os utilizadores autenticados com a conta de administração aparecem como um único utilizador com push e puxam o acesso ao registo. Alterar ou desativar esta conta desativa o acesso ao registo de todos os utilizadores que utilizam as suas credenciais. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça.
>

A conta de administração é fornecida com duas palavras-passe, ambas podem ser regeneradas. Duas palavras-passe permitem manter a ligação ao registo utilizando uma palavra-passe enquanto regenera a outra. Se a conta de administração estiver ativada, pode passar `docker login` o nome de utilizador e qualquer palavra-passe para o comando quando solicitada para autenticação básica no registo. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/)

Para ativar o utilizador administrativo para um registo existente, pode utilizar o `--admin-enabled` parâmetro do comando de [atualização az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-update) no Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador administrativo no portal Azure navegando no seu registo, selecionando **teclas** de acesso em **DEFINIÇÕES** **e,** em seguida, ativar sob o **utilizador do Administrador**.

![Ativar o utilizador de administração UI no portal Azure][auth-portal-01]

## <a name="next-steps"></a>Passos seguintes

* [Empurre a sua primeira imagem usando o Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
