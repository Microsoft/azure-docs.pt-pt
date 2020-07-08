---
title: Opções de autenticação do registo
description: Opções de autenticação para um registo privado de contentores Azure, incluindo a inscrição com uma identidade do Diretório Ativo Azure, utilizando os princípios de serviço e utilizando credenciais de administração opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712042"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com registo de contentores Azure

Existem várias formas de autenticar com um registo de contentores Azure, cada um dos quais é aplicável a um ou mais cenários de utilização do registo.

As formas recomendadas incluem a autenticação de um registo diretamente através de [login individual,](#individual-login-with-azure-ad)ou as suas aplicações e orquestradores de contentores podem executar sem vigilância, ou "sem cabeça", a autenticação através de um diretor de [serviço](#service-principal)Azure Ative (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

As seguintes listas de tabelas disponíveis métodos de autenticação e cenários recomendados. Consulte o conteúdo ligado para mais detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade de anúncio individual](#individual-login-with-azure-ad)                | `az acr login` em Azure CLI                             | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | Sinal de AD deve ser renovado a cada 3 horas     |
| [Diretor de serviços AD](#service-principal)                  | `docker login`<br/><br/>`az acr login`em Azure CLI<br/><br/> Definições de login de registo em APIs ou ferramentas<br/><br/> [Kubernetes puxam segredo](container-registry-auth-kubernetes.md)                                           | Impulso não acompanhado do gasoduto CI/CD<br/><br/> Puxão não acompanhado para a Azure ou serviços externos  | Sim                              | A expiração por defeito da palavra-passe SP é de 1 ano       |                                                           
| [Integrar-se com a AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar registo quando o cluster AKS for criado ou atualizado  | Puxão sem supervisão para o cluster AKS                                                  | Não, só puxe o acesso.             | Disponível apenas com cluster AKS            |
| [Identidade gerida para recursos da Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` em Azure CLI                                       | Impulso não acompanhado do gasoduto Azure CI/CD<br/><br/> Puxão sem supervisão para os serviços da Azure<br/><br/>   | Sim                              | Utilizar apenas a partir de serviços Azure que [suportam identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Utilizador de administração](#admin-account)                            | `docker login`                                          | Push/pull interativo por desenvolvedor ou teste individual                           | Não, sempre puxe e empurre o acesso  | Conta única por registo, não recomendada para vários utilizadores         |
| [Ficha de acesso com âmbito de repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`em Azure CLI   | Impulso/puxão interativo para repositório por desenvolvedor ou testador individual<br/><br/> Impulso/puxar sem supervisão para repositório por sistema individual ou dispositivo externo                  | Sim                              | Não atualmente integrado com identidade AD  |

## <a name="individual-login-with-azure-ad"></a>Login individual com Azure AD

Ao trabalhar diretamente com o seu registo, como puxar imagens para e empurrar imagens de uma estação de trabalho de desenvolvimento, autenticar utilizando o comando [de login az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-login) no [Azure CLI:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

Quando inicia sessão, `az acr login` o CLI utiliza o token criado quando executa [o login az](/cli/azure/reference-index#az-login) para autenticar perfeitamente a sua sessão com o seu registo. Para completar o fluxo de autenticação, o Docker tem de ser instalado e a funcionar no seu ambiente. `az acr login`usa o cliente Docker para definir um token Azure Ative Directory no `docker.config` ficheiro. Uma vez iniciadas desta forma, as suas credenciais estão em cache e `docker` os comandos subsequentes na sessão não requerem um nome de utilizador ou palavra-passe.

> [!TIP]
> Utilize também `az acr login` para autenticar uma identidade individual quando pretende empurrar ou puxar artefactos que não as imagens do Docker para o seu registo, tais como [artefactos OCI](container-registry-oci-artifacts.md).  


Para acesso ao registo, o tousque utilizado `az acr login` é válido por **3 horas,** por isso recomendamos que faça sempre login no registo antes de executar um `docker` comando. Se o seu símbolo expirar, pode refresca-lo usando o `az acr login` comando novamente para reautenticar. 

A utilização `az acr login` com identidades Azure proporciona [acesso baseado em funções](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, poderá querer iniciar sessão num registo com a sua própria identidade individual no Azure AD. Para cenários de cross-service ou para lidar com as necessidades de um grupo de trabalho ou de um fluxo de trabalho de desenvolvimento onde não queira gerir o acesso individual, também pode iniciar sessão com uma [identidade gerida para recursos Azure.](container-registry-authentication-managed-identity.md)

## <a name="service-principal"></a>Service principal (Principal de serviço)

Se atribuir um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao seu registo, a sua aplicação ou serviço pode utilizá-lo para autenticação sem cabeça. Os principais do serviço permitem [o acesso baseado em funções](../role-based-access-control/role-assignments-portal.md) a um registo, e pode atribuir vários princípios de serviço a um registo. Vários princípios de serviço permitem definir diferentes acessos para diferentes aplicações.

As funções disponíveis para um registo de contentores incluem:

* **AcrPull**: puxe

* **AcrPush**: puxe e empurre

* **Proprietário**: puxe, empurre e atribua funções a outros utilizadores

Para obter uma lista completa de funções, consulte [as funções e permissões do Registo do Contentor de Azure](container-registry-roles.md).

Para scripts CLI para criar um principal de serviço para autenticação com um registo de contentores Azure, e mais orientação, consulte [a autenticação do Registo do Contentor de Azure com os principais serviços](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registo de contentores inclui uma conta de utilizador administrada, que é desativada por padrão. Pode ativar o utilizador administrativo e gerir as suas credenciais no portal Azure, ou utilizando o CLI Azure ou outras ferramentas Azure.

> [!IMPORTANT]
> A conta de administração foi concebida para que um único utilizador aceda ao registo, principalmente para fins de teste. Não recomendamos a partilha das credenciais de conta de administração entre vários utilizadores. Todos os utilizadores que autenticam a conta de administração aparecem como um único utilizador com impulso e puxar o acesso ao registo. Alterar ou desativar esta conta desativa o acesso ao registo de todos os utilizadores que utilizam as suas credenciais. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça.
>

A conta de administração é fornecida com duas palavras-passe, ambas podem ser regeneradas. Duas palavras-passe permitem manter a ligação ao registo utilizando uma palavra-passe enquanto regenera a outra. Se a conta de administração estiver ativada, pode passar o nome de utilizador e a palavra-passe para o `docker login` comando quando solicitado para autenticação básica no registo. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/)

Para ativar o utilizador administrativo para um registo existente, pode utilizar o `--admin-enabled` parâmetro do comando de [atualização az acr](/cli/azure/acr?view=azure-cli-latest#az-acr-update) no CLI Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador administrativo no portal Azure navegando no seu registo, selecionando **as teclas de acesso** em **DEFINIÇÕES**e, em seguida, **Ative** o **utilizador Admin**.

![Ativar uI do utilizador de administração no portal Azure][auth-portal-01]

## <a name="next-steps"></a>Próximos passos

* [Empurre a sua primeira imagem usando o Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
