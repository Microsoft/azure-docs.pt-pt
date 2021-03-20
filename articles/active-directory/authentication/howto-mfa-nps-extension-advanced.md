---
title: Configure a extensão Azure AD MFA NPS - Azure Ative Directory
description: Depois de instalar a extensão NPS, utilize estes passos para uma configuração avançada como listas IP permitidas e substituição UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695261ceae9d64be9395e6de082f97be04292078
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745990"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançada para a extensão NPS para Multi-Factor Authentication

A extensão do Network Policy Server (NPS) estende as suas funcionalidades de autenticação multi-factor Azure AD baseadas na nuvem na sua infraestrutura de instalações. Este artigo assume que já tem a extensão instalada, e agora quer saber como personalizar a extensão para as suas necessidades.

## <a name="alternate-login-id"></a>ID de login alternativo

Uma vez que a extensão NPS se liga tanto aos seus locais como aos diretórios em nuvem, poderá encontrar um problema em que os seus nomes principais de utilizadores no local (UPNs) não correspondam aos nomes na nuvem. Para resolver este problema, utilize IDs de login alternativos. 

Dentro da extensão NPS, pode designar um atributo Ative Directory a ser usado no lugar da UPN para autenticação multi-factor Azure AD. Isto permite-lhe proteger os seus recursos no local com verificação em duas etapas sem modificar as suas UPNs no local. 

Para configurar iDs de login alternativos, vá `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os seguintes valores de registo:

| Nome | Tipo | Valor predefinido | Description |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Vazio | Designe o nome do atributo Ative Directory que pretende utilizar em vez da UPN. Este atributo é utilizado como o atributo AlternateLoginId. Se este valor de registo for definido para um [atributo de Diretório Ativo válido](/windows/win32/adschema/attributes-all) (por exemplo, nome de correio ou visualização), então o valor do atributo é utilizado no lugar da UPN do utilizador para autenticação. Se este valor de registo estiver vazio ou não estiver configurado, então o AlternateLoginId é desativado e o UPN do utilizador é utilizado para a autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | Falso | Utilize esta bandeira para forçar a utilização do Catálogo Global para pesquisas LDAP ao procurar AlternateLoginId. Configure um controlador de domínio como catálogo global, adicione o atributo AlternateLoginId ao Catálogo Global e, em seguida, ative esta bandeira. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado (não vazio), **esta bandeira é aplicada como verdadeira,** independentemente do valor da definição do registo. Neste caso, a extensão NPS requer que o Catálogo Global seja configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Vazio | Forneça uma lista separada de florestas semi-cólon para procurar. Por exemplo, *contoso.com;foobar.com*. . Se este valor de registo for configurado, a extensão NPS pesquisa iterativamente todas as florestas na ordem em que foram listadas, e devolve o primeiro valor AlternativoLoginId bem sucedido. Se este valor de registo não estiver configurado, o lookup AlternateLoginId está confinado ao domínio atual.|

Para resolver problemas com IDs de login alternativos, utilize os passos recomendados para [erros de identificação de login alternativos](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções IP

Se precisar monitorizar a disponibilidade do servidor, como se os equilibradores de carga verificarem quais os servidores que estão a funcionar antes de enviar cargas de trabalho, não pretende que estas verificações sejam bloqueadas por pedidos de verificação. Em vez disso, crie uma lista de endereços IP que sabe que são utilizados por contas de serviço e desative os requisitos de autenticação multi-factor para essa lista.

Para configurar uma lista permitida por IP, vá `HKLM\SOFTWARE\Microsoft\AzureMfa` e configuure o seguinte valor de registo:

| Nome | Tipo | Valor predefinido | Description |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Vazio | Forneça uma lista separada de endereços IP separados do ponto de separo. Inclua os endereços IP das máquinas de origem dos pedidos de serviço, como o servidor NAS/VPN. As gamas e sub-redes IP não são suportadas. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Esta chave de registo não é criada por padrão pelo instalador e aparece um erro no registo AuthZOptCh quando o serviço é reiniciado. Este erro no registo pode ser ignorado, mas se esta chave de registo for criada e deixada vazia se não for necessário, a mensagem de erro não volta.

Quando um pedido é proveniente de um endereço IP que existe na verificação em `IP_WHITELIST` duas etapas é ignorado. A lista de IP é comparada com o endereço IP fornecido no atributo *ratNASIPAddress* do pedido RADIUS. Se um pedido RADIUS entrar sem o atributo ratNASIPAddress, regista-se um aviso: "IP_WHITE_LIST_WARNING::IP Whitelist está a ser ignorado à medida que o IP de origem está em falta no atributo RADIUS request NasIpAddress.

## <a name="next-steps"></a>Passos seguintes

[Resolver mensagens de erro a partir da extensão NPS para autenticação multi-factor Azure AD](howto-mfa-nps-extension-errors.md)