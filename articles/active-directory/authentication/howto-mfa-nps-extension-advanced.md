---
title: Configurar a extensão NPS do Azure MFA-Azure Active Directory
description: Depois de instalar a extensão NPS, use estas etapas para configuração avançada, como lista de permissões de IP e substituição de UPN.
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848362"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançadas para a extensão NPS para autenticação multifator

A extensão NPS (servidor de políticas de rede) estende seus recursos de autenticação multifator do Azure baseados em nuvem para sua infraestrutura local. Este artigo pressupõe que você já tem a extensão instalada e agora deseja saber como personalizar a extensão para suas necessidades. 

## <a name="alternate-login-id"></a>ID de logon alternativa

Como a extensão do NPS se conecta aos seus diretórios locais e na nuvem, você pode encontrar um problema em que os UPNs (nomes de entidade de usuário) locais não correspondem aos nomes na nuvem. Para resolver esse problema, use IDs de logon alternativas. 

Na extensão do NPS, você pode designar um atributo Active Directory a ser usado no lugar do UPN para a autenticação multifator do Azure. Isso permite que você proteja seus recursos locais com verificação em duas etapas sem modificar seus UPNs locais. 

Para configurar IDs de logon alternativas, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os seguintes valores de registro:

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Empty | Designe o nome do atributo Active Directory que você deseja usar em vez do UPN. Esse atributo é usado como o atributo AlternateLoginId. Se esse valor de registro for definido como um [atributo de Active Directory válido](https://msdn.microsoft.com/library/ms675090.aspx) (por exemplo, mail ou DisplayName), o valor do atributo será usado no lugar do UPN do usuário para autenticação. Se esse valor de registro estiver vazio ou não estiver configurado, AlternateLoginId será desabilitado e o UPN do usuário será usado para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | Falso | Use esse sinalizador para forçar o uso do catálogo global para pesquisas LDAP ao procurar AlternateLoginId. Configure um controlador de domínio como um catálogo global, adicione o atributo AlternateLoginId ao catálogo global e, em seguida, habilite esse sinalizador. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado (não vazio), **esse sinalizador será imposto como verdadeiro**, independentemente do valor da configuração do registro. Nesse caso, a extensão do NPS requer que o catálogo global seja configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Empty | Forneça uma lista de florestas separadas por ponto e vírgula para pesquisar. Por exemplo, *contoso. com; foobar. com*. Se esse valor de registro estiver configurado, a extensão do NPS pesquisará iterativamente todas as florestas na ordem em que elas foram listadas e retornará o primeiro valor de AlternateLoginId bem-sucedido. Se esse valor de registro não estiver configurado, a pesquisa AlternateLoginId será confinada para o domínio atual.|

Para solucionar problemas com IDs de logon alternativo, use as etapas recomendadas para [erros de ID de logon alternativo](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções de IP

Se você precisar monitorar a disponibilidade do servidor, como se os balanceadores de carga verificarem quais servidores estão em execução antes de enviar cargas de trabalho, você não quer que essas verificações sejam bloqueadas por solicitações de verificação. Em vez disso, crie uma lista de endereços IP que você sabe que são usados por contas de serviço e desabilite os requisitos de autenticação multifator para essa lista.

Para configurar uma lista de permissões de IP, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e configure o seguinte valor de registro:

| Nome | Tipo | Valor predefinido | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Empty | Forneça uma lista de endereços IP separados por ponto e vírgula. Inclua os endereços IP de computadores nos quais as solicitações de serviço são originadas, como o servidor NAS/VPN. Não há suporte para intervalos de IP e sub-redes. <br><br> Por exemplo, *10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> Essa chave do registro não é criada por padrão pelo instalador e um erro aparece no log AuthZOptCh quando o serviço é reiniciado. Esse erro no log pode ser ignorado, mas se essa chave do registro for criada e deixada vazia se não for necessária, a mensagem de erro não será retornada.

Quando uma solicitação chega de um endereço IP que existe na `IP_WHITELIST`, a verificação em duas etapas é ignorada. A lista de IPs é comparada ao endereço IP que é fornecido no atributo *ratNASIPAddress* da solicitação RADIUS. Se uma solicitação RADIUS entrar sem o atributo ratNASIPAddress, o seguinte aviso será registrado: "P_WHITE_LIST_WARNING:: a lista de permissões de IP está sendo ignorada, pois o IP de origem está ausente na solicitação RADIUS no atributo NasIpAddress".

## <a name="next-steps"></a>Passos seguintes

[Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
