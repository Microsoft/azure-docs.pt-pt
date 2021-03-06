---
title: Autenticar para o Azure Key Vault
description: Saiba como autenticar para a Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7d219b752b894bbce9815911658c804ecb850ea1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753439"
---
# <a name="authenticate-to-azure-key-vault"></a>Autenticar para o Azure Key Vault

O Azure Key Vault permite-lhe armazenar segredos e controlar a sua distribuição num repositório de nuvem centralizado e seguro, o que elimina a necessidade de armazenar credenciais em aplicações. As aplicações só precisam de autenticar com o Key Vault na hora de execução para aceder a esses segredos.

## <a name="app-identity-and-security-principals"></a>Principais de identidade e segurança da app

A autenticação com a Key Vault funciona em conjunto com [o Azure Ative Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)que é responsável pela autenticação da identidade de qualquer **dado diretor de segurança.**

Um principal de segurança é um objeto que representa um utilizador, grupo, serviço ou aplicação que está solicitando acesso aos recursos Azure. O Azure atribui uma **identificação** única a todos os diretores de segurança.

* Um diretor de segurança do **utilizador** identifica um indivíduo que tem um perfil no Diretório Ativo Azure.

* Um diretor de segurança do **grupo** identifica um conjunto de utilizadores criados no Azure Ative Directory. Quaisquer funções ou permissões atribuídas ao grupo são concedidas a todos os utilizadores dentro do grupo.

* Um diretor de **serviço** é um tipo de princípio de segurança que identifica uma aplicação ou serviço, ou seja, um pedaço de código em vez de um utilizador ou grupo. O ID do objeto do principiante de serviço é conhecido como **iD do** seu cliente e age como o seu nome de utilizador. O segredo do cliente do **diretor** do serviço age como a sua senha.

Para aplicações, existem duas formas de obter um principal de serviço:

* Recomendado: ativar uma **identidade gerida** atribuída pelo sistema para a aplicação.

    Com identidade gerida, a Azure gere internamente o principal de serviço da aplicação e autentica automaticamente a aplicação com outros serviços Azure. A identidade gerida está disponível para aplicações implantadas para uma variedade de serviços.

    Para mais informações, consulte a visão geral da [identidade gerida.](../../active-directory/managed-identities-azure-resources/overview.md) Consulte também [os serviços Azure que suportam identidade gerida,](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)que se liga a artigos que descrevem como permitir a identidade gerida para serviços específicos (tais como Serviço de Aplicações, Funções Azure, Máquinas Virtuais, etc.).

* Se não puder utilizar a identidade gerida, em vez **disso, registe** o pedido junto do seu inquilino Azure AD, conforme descrito no [Quickstart: Registe uma aplicação com a plataforma de identidade Azure](../../active-directory/develop/quickstart-register-app.md). O registo também cria um segundo objeto de aplicação que identifica a app em todos os inquilinos.

## <a name="authorize-a-security-principal-to-access-key-vault"></a>Autorizar um diretor de segurança a aceder ao Cofre-Chave

O Cofre-Chave funciona com dois níveis de autorização distintos:

- **As políticas** de acesso controlam se um utilizador, grupo ou diretor de serviço está autorizado a aceder a segredos, chaves e certificados *dentro* de um recurso key vault existente (por vezes referido a operações de "data plane"). As políticas de acesso são normalmente concedidas aos utilizadores, grupos e aplicações.

    Para atribuir políticas de acesso, consulte os seguintes artigos:

    - [Portal do Azure](assign-access-policy-portal.md)
    - [CLI do Azure](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **As permissões de função** controlam se um utilizador, grupo ou chefe de serviço está autorizado a criar, eliminar e gerir de outra forma um recurso Key Vault (por vezes referido como operações de "avião de gestão"). Estas funções são, na maior parte das vezes, concedidas apenas aos administradores.
 
    Para atribuir e gerir funções, consulte os seguintes artigos:

    - [Portal do Azure](../../role-based-access-control/role-assignments-portal.md)
    - [CLI do Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    Para obter informações gerais sobre funções, veja [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)?](../../role-based-access-control/overview.md)


> [!IMPORTANT]
> Para maior segurança, siga sempre o principal do menor privilégio e conceda apenas as políticas e funções de acesso mais específicas que são necessárias. 
    
## <a name="configure-the-key-vault-firewall"></a>Configure a firewall do Cofre de Chaves

Por predefinição, o Key Vault permite o acesso aos recursos através de endereços IP públicos. Para uma maior segurança, também pode restringir o acesso a gamas IP específicas, pontos finais de serviço, redes virtuais ou pontos finais privados.

Para obter mais informações, consulte [o Access Azure Key Vault atrás de uma firewall](./access-behind-firewall.md).


## <a name="the-key-vault-authentication-flow"></a>O fluxo de autenticação do Cofre-Chave

1. Um principal pedido de serviço para autenticar com Azure AD, por exemplo:
    * Um utilizador entra no portal Azure utilizando um nome de utilizador e senha.
    * Uma aplicação invoca uma API Azure REST, apresentando uma identificação do cliente e um certificado de cliente secreto ou cliente.
    * Um recurso Azure, como uma máquina virtual com uma identidade gerida, contacta o ponto final do [Serviço de Metadados de Instância Azure (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) PARA obter um token de acesso.

1. Se a autenticação com Azure AD for bem sucedida, o diretor de serviço recebe um token OAuth.

1. O diretor de serviço faz uma chamada para a API do Key Vault REST através do ponto final do Cofre de Chaves (URI).

1. Key Vault Firewall verifica os seguintes critérios. Se algum critério for cumprido, a chamada é permitida. Caso contrário, a chamada é bloqueada e uma resposta proibida é devolvida.

    * A firewall está desativada e o ponto final público do Key Vault é acessível a partir da internet pública.
    * O chamador é um [serviço de confiança do cofre de chaves,](./overview-vnet-service-endpoints.md#trusted-services)permitindo-lhe contornar a firewall.
    * O chamador está listado na firewall por endereço IP, rede virtual ou ponto final de serviço.
    * O chamador pode chegar ao Key Vault sobre uma ligação de ligação privada configurada.    

1. Se a firewall permitir a chamada, o Key Vault chama a Azure AD para validar o sinal de acesso do diretor de serviço.

1. O Key Vault verifica se o diretor de serviço tem a política de acesso necessária para a operação solicitada. Caso contrário, o Key Vault devolve uma resposta proibida.

1. Key Vault realiza a operação solicitada e devolve o resultado.

O diagrama seguinte ilustra o processo para uma aplicação chamada API do Cofre chave "Get Secret":

![O fluxo de autenticação do cofre da chave Azure](../media/authentication/authentication-flow.png)

> [!NOTE]
> Os clientes key Vault SDK para segredos, certificados e chaves fazem uma chamada adicional para Key Vault sem acesso ao token, o que resulta em 401 resposta para recuperar informações do inquilino. Para mais informações consulte [Autenticação, pedidos e respostas](authentication-requests-and-responses.md)

## <a name="code-examples"></a>Exemplos de código

A tabela a seguir liga-se a diferentes artigos que demonstram como trabalhar com o Key Vault no código de aplicação utilizando as bibliotecas Azure SDK para o idioma em questão. Outras interfaces como o Azure CLI e o portal Azure estão incluídas por conveniência.

| Segredos do Cofre Chave | Chaves do cofre chave | Certificados de cofre chave |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Portal do Azure](../secrets/quick-create-portal.md) | [Portal do Azure](../keys/quick-create-portal.md) | [Portal do Azure](../certificates/quick-create-portal.md) |
| [CLI do Azure](../secrets/quick-create-cli.md) | [CLI do Azure](../keys/quick-create-cli.md) | [CLI do Azure](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [Modelo ARM](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>Passos Seguintes

- [Resolução de problemas da política de acesso ao cofre chave](troubleshooting-access-issues.md)
- [Códigos de erro da API do Cofre de Chaves](rest-error-codes.md)
- [Guia do desenvolvedor do Cofre chave](developers-guide.md)
- [O que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
