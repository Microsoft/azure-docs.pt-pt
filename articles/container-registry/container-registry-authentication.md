---
title: Opções de autenticação do registo
description: Opções de autenticação para um registo privado de contentores Azure, incluindo a inscrição com uma identidade do Diretório Ativo Azure, utilizando os princípios de serviço e utilizando credenciais de administração opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5315c11e0f1e2c859384e3783ae4be5d709adb42
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148565"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com registo de contentores Azure

Existem várias formas de autenticar com um registo de contentores Azure, cada um dos quais é aplicável a um ou mais cenários de utilização do registo.

As formas recomendadas incluem a autenticação de um registo diretamente através de [login individual,](#individual-login-with-azure-ad)ou as suas aplicações e orquestradores de contentores podem executar sem vigilância, ou "sem cabeça", a autenticação através de um diretor de [serviço](#service-principal)Azure Ative (Azure AD).

## <a name="authentication-options"></a>Opções de autenticação

As seguintes listas de tabelas disponíveis métodos de autenticação e cenários típicos. Consulte o conteúdo ligado para mais detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | Controlo de acesso baseado em funções do Azure (RBAC do Azure)                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade de anúncio individual](#individual-login-with-azure-ad)                | `az acr login` em Azure CLI                             | Push/pull interativo por desenvolvedores, testadores                                    | Sim                              | Sinal de AD deve ser renovado a cada 3 horas     |
| [Diretor de serviços AD](#service-principal)                  | `docker login`<br/><br/>`az acr login` em Azure CLI<br/><br/> Definições de login de registo em APIs ou ferramentas<br/><br/> [Kubernetes puxam segredo](container-registry-auth-kubernetes.md)                                           | Impulso não acompanhado do gasoduto CI/CD<br/><br/> Puxão não acompanhado para a Azure ou serviços externos  | Sim                              | A expiração por defeito da palavra-passe SP é de 1 ano       |                                                           
| [Integrar-se com a AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar registo quando o cluster AKS for criado ou atualizado  | Puxão sem supervisão para o cluster AKS                                                  | Não, só puxe o acesso.             | Disponível apenas com cluster AKS            |
| [Identidade gerida para recursos da Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` em Azure CLI                                       | Impulso não acompanhado do gasoduto Azure CI/CD<br/><br/> Puxão sem supervisão para os serviços da Azure<br/><br/>   | Sim                              | Utilizar apenas a partir de serviços Azure que [suportam identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Utilizador de administração](#admin-account)                            | `docker login`                                          | Push/pull interativo por desenvolvedor ou teste individual<br/><br/>Implantação do portal de imagem do registo para O Serviço de Aplicações Azure ou Instâncias de Contentores Azure                      | Não, sempre puxe e empurre o acesso  | Conta única por registo, não recomendada para vários utilizadores         |
| [Ficha de acesso com âmbito de repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` em Azure CLI   | Impulso/puxão interativo para repositório por desenvolvedor ou testador individual<br/><br/> Impulso/puxar sem supervisão para repositório por sistema individual ou dispositivo externo                  | Sim                              | Não atualmente integrado com identidade AD  |

## <a name="individual-login-with-azure-ad"></a>Login individual com Azure AD

Ao trabalhar diretamente com o seu registo, como puxar imagens para e empurrar imagens de uma estação de trabalho de desenvolvimento para um registo que criou, autente utilizando a sua identidade Azure individual. Inicie sessão no [Azure CLI](/cli/azure/install-azure-cli) com [login az](/cli/azure/reference-index#az-login), e, em seguida, executar o comando [de login az acr:](/cli/azure/acr#az-acr-login)

```azurecli
az login
az acr login --name <acrName>
```

Quando inicia sessão, `az acr login` o CLI utiliza o token criado quando executa `az login` para autenticar perfeitamente a sua sessão com o seu registo. Para completar o fluxo de autenticação, o Daemon Docker CLI e Docker deve ser instalado e em funcionamento no seu ambiente. `az acr login` usa o cliente Docker para definir um token Azure Ative Directory no `docker.config` ficheiro. Uma vez iniciadas desta forma, as suas credenciais estão em cache e `docker` os comandos subsequentes na sessão não requerem um nome de utilizador ou palavra-passe.

> [!TIP]
> Utilize também `az acr login` para autenticar uma identidade individual quando pretende empurrar ou puxar artefactos que não as imagens do Docker para o seu registo, tais como [artefactos OCI](container-registry-oci-artifacts.md).  

Para acesso ao registo, o tousque utilizado `az acr login` é válido por **3 horas,** por isso recomendamos que faça sempre login no registo antes de executar um `docker` comando. Se o seu símbolo expirar, pode refresca-lo usando o `az acr login` comando novamente para reautenticar. 

A utilização `az acr login` com identidades Azure fornece [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, poderá querer iniciar sessão num registo com a sua própria identidade individual em Azure AD, ou configurar outros utilizadores do Azure com [funções e permissões específicas do Azure.](container-registry-roles.md) Para cenários de cross-service ou para lidar com as necessidades de um grupo de trabalho ou de um fluxo de trabalho de desenvolvimento onde não queira gerir o acesso individual, também pode iniciar sessão com uma [identidade gerida para recursos Azure.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>login az acr com --expor-token

Em alguns casos, talvez precises de autenticar `az acr login` quando o daemon do Docker não estiver a funcionar no teu ambiente. Por exemplo, podes precisar de executar `az acr login` um script em Azure Cloud Shell, que fornece o Docker CLI mas não gere o daemon do Docker.

Para este cenário, corra `az acr login` primeiro com o `--expose-token` parâmetro. Esta opção expõe um token de acesso em vez de iniciar sessão através do Docker CLI.

```azurecli
az acr login --name <acrName> --expose-token
```

A saída exibe o token de acesso, abreviado aqui:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 

Em seguida, `docker login` corra, passando `00000000-0000-0000-0000-000000000000` como nome de utilizador e usando o token de acesso como senha:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password eyJhbGciOiJSUzI1NiIs[...]24V7wA
```

## <a name="service-principal"></a>Service principal (Principal de serviço)

Se atribuir um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao seu registo, a sua aplicação ou serviço pode utilizá-lo para autenticação sem cabeça. Os principais de serviço permitem o [controlo de acesso baseado em funções (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) a um registo, e pode atribuir vários princípios de serviço a um registo. Vários princípios de serviço permitem definir diferentes acessos para diferentes aplicações.

As funções disponíveis para um registo de contentores incluem:

* **AcrPull**: puxe

* **AcrPush**: puxe e empurre

* **Proprietário**: puxe, empurre e atribua funções a outros utilizadores

Para obter uma lista completa de funções, consulte [as funções e permissões do Registo do Contentor de Azure](container-registry-roles.md).

Para scripts CLI para criar um principal de serviço para autenticação com um registo de contentores Azure, e mais orientação, consulte [a autenticação do Registo do Contentor de Azure com os principais serviços](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registo de contentores inclui uma conta de utilizador administrada, que é desativada por padrão. Pode ativar o utilizador administrativo e gerir as suas credenciais no portal Azure, ou utilizando o CLI Azure ou outras ferramentas Azure. A conta de administração tem permissões completas para o registo.

A conta de administração é atualmente necessária para que alguns cenários implementem uma imagem de um registo de contentores para certos serviços da Azure. Por exemplo, a conta de administração é necessária quando coloca uma imagem de contentor no portal a partir de um registo diretamente para [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) ou [Azure Web Apps for Containers](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> A conta de administração foi concebida para que um único utilizador aceda ao registo, principalmente para fins de teste. Não recomendamos a partilha das credenciais de conta de administração entre vários utilizadores. Todos os utilizadores que autenticam a conta de administração aparecem como um único utilizador com impulso e puxar o acesso ao registo. Alterar ou desativar esta conta desativa o acesso ao registo de todos os utilizadores que utilizam as suas credenciais. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça.
>

A conta de administração é fornecida com duas palavras-passe, ambas podem ser regeneradas. Duas palavras-passe permitem manter a ligação ao registo utilizando uma palavra-passe enquanto regenera a outra. Se a conta de administração estiver ativada, pode passar o nome de utilizador e a palavra-passe para o `docker login` comando quando solicitado para autenticação básica no registo. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as melhores práticas para gerir as credenciais de login, consulte a referência do comando de login do [estivador.](https://docs.docker.com/engine/reference/commandline/login/)

Para ativar o utilizador administrativo para um registo existente, pode utilizar o `--admin-enabled` parâmetro do comando de [atualização az acr](/cli/azure/acr#az-acr-update) no CLI Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Pode ativar o utilizador administrativo no portal Azure navegando no seu registo, selecionando **as teclas de acesso** em **DEFINIÇÕES**e, em seguida, **Ative** o **utilizador Admin**.

![Ativar uI do utilizador de administração no portal Azure][auth-portal-01]

## <a name="next-steps"></a>Passos seguintes

* [Empurre a sua primeira imagem usando o Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
