---
title: Configurar a extensão NPS da MFA do Azure | Documentos da Microsoft
description: Depois de instalar a extensão NPS, utilize estes passos para configuração avançada, como listas de permissões de IP e substituição de UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2690cb4243597c942b6679b5864016bf14fcbad1
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732428"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançada para a extensão NPS para multi-factor Authentication

A extensão de servidor de políticas de rede (NPS) estende os recursos de multi-factor Authentication do Azure com base na cloud na sua infraestrutura no local. Este artigo pressupõe que já tem a extensão instalada e querem saber como personalizar a extensão para necessidades. 

## <a name="alternate-login-id"></a>ID de início de sessão alternativo

Uma vez que a extensão NPS liga ao seu local e a cloud diretórios, poderá ocorrer um problema em que seus nomes de principal de utilizador de no local (UPNs) não correspondem os nomes na cloud. Para resolver este problema, utilize os IDs de início de sessão alternativo. 

Dentro da extensão NPS, pode designar um atributo do Active Directory a utilizar em vez do UPN para o Azure multi-factor Authentication. Isto permite-lhe proteger os seus recursos no local com verificação de dois passos sem modificar os UPNs no local. 

Para configurar os IDs de início de sessão alternativo, aceda a `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os valores de registo seguinte:

| Name | Type | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Vazio | Designe o nome de atributo do Active Directory que pretende utilizar em vez do UPN. Este atributo é utilizado como o atributo AlternateLoginId. Se este valor de registo é definido como um [atributo válido do Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (por exemplo, email ou displayName), em seguida, o valor desse atributo é utilizado em vez de UPN do utilizador para autenticação. Se este valor de registo está vazio ou não configurado, em seguida, AlternateLoginId está desativada e o UPN dos utilizadores é utilizado para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | booleano | Falso | Utilize este sinalizador para forçar a utilização de Catálogo Global para pesquisas LDAP ao procurar o AlternateLoginId. Configurar um controlador de domínio como um Catálogo Global, adicione o atributo AlternateLoginId ao Catálogo Global e, em seguida, ativar este sinalizador. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurada (não vazio), **este sinalizador é imposto como true**, independentemente do valor da configuração do Registro. Neste caso, a extensão NPS requer que o Catálogo Global ser configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Vazio | Forneça uma lista de ponto e vírgula separada de florestas para procurar. Por exemplo, *contoso.com;foobar.com*. Se este valor de registo estiver configurada, a extensão NPS pesquisa iterativamente todas as florestas na ordem em que eles foram listados e devolve o primeiro valor AlternateLoginId com êxito. Se este valor de registo não estiver configurada, a pesquisa de AlternateLoginId está limitada a domínio atual.|

Para solucionar problemas de início de sessão alternativo IDs, utilize os passos recomendados para [alternativo erros de ID de início de sessão](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções de IP

Se precisar de monitorizar a disponibilidade de servidor, como se a balanceadores de carga verificar quais servidores estão a executar antes de enviar as cargas de trabalho, não quer estas verificações até ser bloqueado por pedidos de verificação. Em vez disso, crie uma lista de endereços IP que sabe que são utilizados pelas contas de serviço e desativar os requisitos de multi-factor Authentication para essa lista. 

Para configurar umalista aprovada de IP, aceda a `HKLM\SOFTWARE\Microsoft\AzureMfa` e configure o seguinte valor de registo: 

| Name | Type | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Vazio | Forneça uma lista de ponto e vírgula separada de endereços IP. Inclua os endereços IP das máquinas onde os pedidos de serviço têm origem, como o servidor NAS/VPN. Não são suportadas intervalos de IP e sub-redes. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

Quando chegar uma solicitação de um endereço IP que existe na lista de permissões, verificação de dois passos é ignorada. A lista aprovada de IP é comparado com o endereço IP que é fornecido na *ratNASIPAddress* atributo do pedido RADIUS. Se um pedido RADIUS chega sem o atributo ratNASIPAddress, o seguinte aviso é registado: "Está a ser ignorada P_WHITE_LIST_WARNING::IP lista de permissões como IP de origem está em falta no pedido RADIUS no atributo NasIpAddress."

## <a name="next-steps"></a>Passos Seguintes

[Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
