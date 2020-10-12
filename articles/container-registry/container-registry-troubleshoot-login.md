---
title: Início de sessão de resolução de problemas para o registo
description: Sintomas, causas e resolução de problemas comuns ao iniciar sessão num registo de contentores Azure
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: d5071a1e759d26ce43d2eb5d9b8215781d813d33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253359"
---
# <a name="troubleshoot-registry-login"></a>Login de registo de resolução de problemas

Este artigo ajuda-o a resolver problemas que poderá encontrar ao iniciar sessão num registo de contentores Azure. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* Incapaz de iniciar sessão no registo `docker login` `az acr login` utilizando, ou ambos
* Não é possível iniciar sessão no registo e recebe erro `unauthorized: authentication required` ou `unauthorized: Application not registered with AAD`
* Não é possível iniciar sessão no registo e recebe erro do Azure CLI `Could not connect to the registry login server`
* Incapaz de empurrar ou puxar imagens e recebes erro do Docker `unauthorized: authentication required`
* Não é possível aceder ao registo do Serviço Azure Kubernetes, Azure DevOps ou outro serviço Azure
* Não consegue aceder ao registo e recebe erro `Error response from daemon: login attempt failed with status: 403 Forbidden` - Consulte [problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md)
* Não pode aceder ou visualizar as definições de registo no portal Azure ou gerir o registo utilizando o CLI Azure

## <a name="causes"></a>Causas

* Docker não está configurado corretamente no seu ambiente - [solução](#check-docker-configuration)
* O registo não existe ou o nome está incorreto - [solução](#specify-correct-registry-name)
* As credenciais de registo não são válidas - [solução](#confirm-credentials-to-access-registry)
* As credenciais não estão autorizadas para operações push, pull ou Azure Resource Manager - [solução](#confirm-credentials-are-authorized-to-access-registry)
* As credenciais estão caducadas - [solução](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Diagnóstico adicional 

Faça o comando [de check-health az acr](/cli/azure/acr#az-acr-check-health) para obter mais informações sobre a saúde do ambiente de registo e acesso opcional a um registo-alvo. Por exemplo, diagnosticar erros de configuração do Docker ou problemas de login do Azure Ative Directory. 

Consulte [a saúde de um registo de contentores Azure](container-registry-check-health.md) para obter exemplos de comando. Se forem reportados erros, reveja a [referência de erro](container-registry-health-error-reference.md) e as seguintes secções para obter soluções recomendadas.

> [!NOTE]
> Alguns erros de autenticação ou autorização também podem ocorrer se houver configurações de firewall ou rede que impeçam o acesso ao registo. Consulte [problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="check-docker-configuration"></a>Verifique a configuração do Docker

A maioria dos fluxos de autenticação do registo de contentores Azure requerem uma instalação local do Docker para que possa autenticar com o seu registo para operações como empurrar e puxar imagens. Confirme que o cliente e o Daemon (Docker Engine) do Docker estão a funcionar no seu ambiente. Precisa da versão 18.03 ou mais tarde do cliente do Docker.

Links relacionados:

* [Descrição geral da autenticação](container-registry-authentication.md#authentication-options)
* [FaQ do registo de contentores](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Especificar o nome correto do registo

Ao utilizar `docker login` , forneça o nome completo do servidor de login do registo, como *myregistry.azurecr.io*. Certifique-se de que utiliza apenas letras minúsculas. Exemplo:

```console
docker login myregistry.azurecr.io
```

Ao utilizar [o login az acr](/cli/azure/acr#az-acr-login) com uma identidade do Azure Ative Directory, inicie [primeiro o login no CLI Azure](/cli/azure/authenticate-azure-cli)e, em seguida, especifique o nome do recurso Azure do registo. O nome do recurso é o nome fornecido quando o registo foi criado, como *a miogitária* (sem sufixo de domínio). Exemplo:

```azurecli
az acr login --name myregistry
```

Links relacionados:

* [az acr login sucede mas docker falha com erro: não autorizado: autenticação necessária](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Confirmar credenciais de acesso ao registo

Verifique a validade das credenciais que utiliza para o seu cenário, ou foi-lhe fornecida por um proprietário do registo. Algumas questões possíveis:

* Se utilizar um diretor de serviço de Diretório Ativo, certifique-se de que utiliza as credenciais corretas no inquilino ative directory:
  * Nome do utilizador - ID de aplicação principal de serviço (também chamado *ID do cliente)*
  * Palavra-passe - senha principal do serviço (também chamada *de segredo do cliente)*
* Se utilizar um serviço Azure como o Serviço Azure Kubernetes ou o Azure DevOps para aceder ao registo, confirme a configuração do registo do seu serviço.
* Se tiver corrido `az acr login` com a `--expose-token` opção, que permite o login de registo sem utilizar o daemon Docker, certifique-se de que autentica com o nome de utilizador `00000000-0000-0000-0000-000000000000` .

Links relacionados:

* [Descrição geral da autenticação](container-registry-authentication.md#authentication-options)
* [Login individual com Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Faça login com o diretor de serviço](container-registry-auth-service-principal.md)
* [Iniciar sessão com identidade gerida](container-registry-authentication-managed-identity.md)
* [Iniciar sessão com token de âmbito repositório](container-registry-repository-scoped-permissions.md)
* [Faça login com conta de administração](container-registry-authentication.md#admin-account)
* [Códigos de autenticação e erro de autorização Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* referência [de login az acr](/cli/azure/acr#az-acr-login)

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Confirme credenciais são autorizadas a aceder ao registo

Confirme as permissões de registo que estão associadas às credenciais, como o papel do `AcrPull` RBAC para retirar imagens do registo, ou a `AcrPush` função de empurrar imagens. 

O acesso a um registo no portal ou na gestão do registo utilizando o CLI Azure requer pelo menos o `Reader` papel para executar operações do Azure Resource Manager.

Você ou um proprietário do registo devem ter privilégios suficientes na subscrição para adicionar ou remover atribuições de funções.

Links relacionados:

* [Funções e permissões do RBAC - Registo de Contentores Azure](container-registry-roles.md)
* [Iniciar sessão com token de âmbito repositório](container-registry-repository-scoped-permissions.md)
* [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Criar um novo segredo da aplicação](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Códigos de autenticação e autorização Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Verifique se as credenciais não estão caducadas.

As credenciais de Tokens e Ative Directory podem expirar após períodos definidos, impedindo o acesso ao registo. Para permitir o acesso, as credenciais podem ter de ser reiniciadas ou regeneradas.

* Se utilizar uma identidade AD individual, uma identidade gerida ou um principal de serviço para o login de registo, o token AD expira após 3 horas. Faça login de novo no registo.  
* Se utilizar um diretor de serviço AD com um segredo de cliente caducado, um proprietário de subscrição ou administrador de conta precisa de redefinir credenciais ou gerar um novo diretor de serviço.
* Se utilizar um [token com âmbito de repositório,](container-registry-repository-scoped-permissions.md)um proprietário do registo poderá ter de redefinir uma palavra-passe ou gerar um novo token.

Links relacionados:

* [Credenciais principais do serviço de reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Regenerar senhas simbólicas](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Login individual com Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Resolução de problemas avançados

Se a [recolha de registos de recursos](container-registry-diagnostics-audit-logs.md) estiver ativada no registo, reveja o registo De ContainterRegistryLoginEvents. Este registo armazena eventos e estado de autenticação, incluindo a identidade de entrada e endereço IP. Consultar o registo de [falhas de autenticação do registo](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Links relacionados:

* [Registos para avaliação e auditoria de diagnóstico](container-registry-diagnostics-audit-logs.md)
* [FaQ do registo de contentores](container-registry-faq.md)
* [Melhores práticas do Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

Se não resolver o seu problema aqui, consulte as seguintes opções.

* Outros tópicos de resolução de problemas do registo incluem:
  * [Problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md)
  * [Resolver problemas de desempenho de registo](container-registry-troubleshoot-performance.md)
* [Opções de apoio comunitário](https://azure.microsoft.com/support/community/)
* [Perguntas e Respostas da Microsoft](https://docs.microsoft.com/answers/products/)
* [Abra um bilhete de apoio](https://azure.microsoft.com/support/create-ticket/) - com base nas informações que fornece, pode ser executado um diagnóstico rápido para falhas de autenticação no seu registo


