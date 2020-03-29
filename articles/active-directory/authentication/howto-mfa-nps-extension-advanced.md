---
title: Configure a extensão Azure MFA NPS - Diretório Ativo Azure
description: Depois de instalar a extensão NPS, utilize estes passos para configuração avançada como a whitelisting IP e a substituição UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ea5b4f52fc161cb8359ef56e76e0607459d6280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848362"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançada para a extensão NPS para Multi-Factor Authentication

A extensão do Servidor de Política de Rede (NPS) estende as suas funcionalidades de autenticação azure multi-factor baseadas na nuvem para a sua infraestrutura no local. Este artigo assume que já tem a extensão instalada, e agora quer saber como personalizar a extensão para as suas necessidades. 

## <a name="alternate-login-id"></a>Id de login alternativo

Uma vez que a extensão nps se liga tanto aos seus diretórios no local como às direções em nuvem, poderá encontrar um problema em que os nomes principais dos utilizadores no local (UPNs) não correspondem aos nomes na nuvem. Para resolver este problema, utilize iDs de login alternativos. 

Dentro da extensão NPS, pode designar um atributo de Diretório Ativo a ser utilizado no lugar da UPN para autenticação multi-factor Azure. Isto permite-lhe proteger os seus recursos no local com verificação em duas etapas sem modificar as suas UPNs no local. 

Para configurar iDs de login alternativos, vá e `HKLM\SOFTWARE\Microsoft\AzureMfa` edite os seguintes valores de registo:

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Vazio | Designe o nome de Atributo de Diretório Ativo que pretende utilizar em vez da UPN. Este atributo é usado como atributo AlternateLoginId. Se este valor de registo for definido para um [atributo de Diretório Ativo válido](https://msdn.microsoft.com/library/ms675090.aspx) (por exemplo, mail ou displayName), então o valor do atributo é usado no lugar da UPN do utilizador para autenticação. Se este valor de registo estiver vazio ou não estiver configurado, o AlternateLoginId está desativado e a UPN do utilizador é utilizada para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | Falso | Utilize esta bandeira para forçar o uso do Catálogo Global para pesquisas LDAP ao procurar o AlternateLoginId. Configure um controlador de domínio como catálogo global, adicione o atributo AlternateLoginId ao Catálogo Global e, em seguida, ative esta bandeira. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado (não vazio), **esta bandeira é aplicada como verdadeira,** independentemente do valor da definição de registo. Neste caso, a extensão NPS exige que o Catálogo Global seja configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Vazio | Forneça uma lista separada de florestas semi-cólon para procurar. Por exemplo, *contoso.com;foobar.com*. Se este valor de registo estiver configurado, a extensão NPS procura iterativamente todas as florestas na ordem em que foram listadas, e devolve o primeiro valor alternativo de sucesso. Se este valor de registo não estiver configurado, o lookup AlternateLoginId está confinado ao domínio atual.|

Para resolver problemas com iDs de login alternativos, utilize os passos recomendados para [erros de identificação de login alternativos](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções ip

Se necessitar de monitorizar a disponibilidade do servidor, como se os equilibradores de carga verificarem quais os servidores que estão a funcionar antes de enviar cargas de trabalho, não pretende que estas verificações sejam bloqueadas por pedidos de verificação. Em vez disso, crie uma lista de endereços IP que sabe que são usados por contas de serviço e desative os requisitos de autenticação multi-factor para essa lista.

Para configurar uma lista permitida `HKLM\SOFTWARE\Microsoft\AzureMfa` por IP, vá e configure o seguinte valor de registo:

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Vazio | Forneça uma lista separada do ponto-de-semi-cólon dos endereços IP. Inclua os endereços IP das máquinas onde os pedidos de serviço são originados, como o servidor NAS/VPN. As gamas ip e as subredes não são suportadas. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Esta chave de registo não é criada por predefinição pelo instalador e aparece um erro no registo AuthZOptCh quando o serviço é reiniciado. Este erro no registo pode ser ignorado, mas se esta chave de registo for criada e deixada vazia se não for necessária, a mensagem de erro não volta.

Quando um pedido vem de um endereço `IP_WHITELIST`IP que existe na verificação de duas etapas é ignorado. A lista IP é comparada com o endereço IP fornecido no atributo *ratNASIPAddress* do pedido RADIUS. Se um pedido radius chegar sem o atributo ratNASIPAddress, o seguinte aviso é registado: "P_WHITE_LIST_WARNING:IP Whitelist está a ser ignorado à medida que falta IP de origem no pedido DE RADIUS no atributo nasIpAddress."

## <a name="next-steps"></a>Passos seguintes

[Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
