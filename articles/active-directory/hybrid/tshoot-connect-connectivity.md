---
title: 'Azure AD Connect: Resolver problemas de conectividade | Documentos da Microsoft'
description: Explica como resolver problemas de conectividade com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0afc31bf08a5037d91885bc6a85c6aeaf858825
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436668"
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Resolver problemas de conectividade com o Azure AD Connect
Este artigo explica como funciona a conectividade entre o Azure AD Connect e o Azure AD e como resolver problemas de conectividade. Estes problemas são mais probabilidades de ser vistos num ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Resolver problemas de conectividade no Assistente de instalação
O Azure AD Connect está a utilizar autenticação moderna (usando a biblioteca ADAL) para autenticação. O Assistente de instalação e o motor de sincronização adequado exigem Machine. config para ser corretamente configurados, uma vez que estes dois são aplicativos .NET.

Neste artigo, vamos mostrar como a Fabrikam liga-se para o Azure AD através do seu proxy. O servidor proxy com o nome fabrikamproxy e estiver a utilizar a porta 8080.

Primeiro, precisamos garantir [ **Machine. config** ](how-to-connect-install-prerequisites.md#connectivity) está configurado corretamente.  
![machineconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Em alguns blogs de não-Microsoft, ele está documentado que as alterações devem ser feitas miiserver.exe.config em vez disso. No entanto, esse arquivo é substituído em cada atualização assim, mesmo que se ele funciona durante a instalação inicial, o sistema deixa de funcionar na primeira atualização. Por esse motivo, a recomendação é atualizar o Machine. config em vez disso.
>
>

O servidor proxy tem de ter também os URLs necessários abertos. A lista oficial está documentada no [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

URLs, a tabela seguinte é o mínimo absoluto, para conseguir ligar ao Azure AD de todo. Esta lista não inclui quaisquer funcionalidades opcionais, como a repetição de escrita de palavra-passe ou o Azure AD Connect Health. Foi documentado aqui para ajudar na resolução de problemas para a configuração inicial.

| do IdP | Porta | Descrição |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Utilizado para transferir as listas CRL. |
| \*.verisign.com |HTTP/80 |Utilizado para transferir as listas CRL. |
| \*.entrust.net |HTTP/80 |Utilizado para transferir as listas CRL para a MFA. |
| \*.windows.net |HTTPS/443 |Utilizado para iniciar sessão Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Utilizado para a MFA. |
| \*.microsoftonline.com |HTTPS/443 |Utilizado para configurar o diretório do Azure AD e importar/exportar dados. |

## <a name="errors-in-the-wizard"></a>Erros no Assistente
O Assistente de instalação está a utilizar dois contextos de segurança diferentes. Na página **ligar para o Azure AD**, está a utilizar o utilizador atualmente com sessão iniciada. Na página **configurar**, ele está mudando para o [conta que executa o serviço para o motor de sincronização](reference-connect-accounts-permissions.md#adsync-service-account). Se houver um problema, parece muito provavelmente já os **ligar para o Azure AD** página do assistente, uma vez que a configuração de proxy é global.

Os seguintes problemas são os erros mais comuns que encontrar no Assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O Assistente de instalação não foi corretamente configurado
Este erro ocorre quando o assistente em si não é possível alcançar o proxy.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Se vir este erro, verifique se o [Machine. config](how-to-connect-install-prerequisites.md#connectivity) foi configurado corretamente.
* Se parecer que o correto, siga os passos em [verificar a conectividade de proxy](#verify-proxy-connectivity) para ver se o problema está presente fora também o assistente.

### <a name="a-microsoft-account-is-used"></a>É utilizada uma conta Microsoft
Se utilizar um **conta Microsoft** em vez de um **escola ou organização** conta, verá um erro genérico.  
![É utilizada uma Account Microsoft](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Não é possível alcançar o ponto de extremidade do MFA
Este erro ocorre se o ponto final **https://secure.aadcdn.microsoftonline-p.com** não é possível alcançar e o administrador global tiver a MFA ativada.  
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Se vir este erro, certifique-se de que o ponto final **secure.aadcdn.microsoftonline p.com** foi adicionado ao proxy.

### <a name="the-password-cannot-be-verified"></a>Não é possível verificar a palavra-passe
Se o Assistente de instalação for concluída com êxito na conexão com o Azure AD, mas a palavra-passe em si não é possível verificar que se este erro ocorrer:  
![Palavra-passe incorreta.](./media/tshoot-connect-connectivity/badpassword.png)

* É a palavra-passe uma palavra-passe temporária e tem de ser alterada? É, na verdade, a palavra-passe correta? Tente iniciar sessão para https://login.microsoftonline.com (em outro computador do servidor do Azure AD Connect) e certifique-se de que a conta pode ser utilizada.

### <a name="verify-proxy-connectivity"></a>Verificar a conectividade de proxy
Para verificar se o servidor do Azure AD Connect tem conectividade real com o Proxy e a Internet, utilize o PowerShell para ver se o proxy está a permitir solicitações da web ou não. Na linha de comandos do PowerShell, execute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tecnicamente a primeira chamada é https://login.microsoftonline.com e este URI também funciona, mas outro URI é mais rápido responder.)

PowerShell utiliza a configuração no Machine. config em contato com o proxy. As definições no winhttp/netsh não devem afetar estes cmdlets.

Se o proxy está configurado corretamente, deve obter um Estado de êxito: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Se receber **não é possível ligar ao servidor remoto**, em seguida, PowerShell está a tentar fazer uma chamada direta sem utilizar o proxy ou DNS não está configurado corretamente. Certifique-se de que o **Machine. config** arquivo está configurado corretamente.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Se o proxy não está configurado corretamente, obtém um erro: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Erro | Texto do erro | Comentário |
| --- | --- | --- |
| 403 |Proibido |O proxy não tiver sido aberto para o URL pedido. Reveja a configuração de proxy e certifique-se de que o [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) foram abertas. |
| 407 |Autenticação do proxy necessária |O servidor proxy necessário um início de sessão e não foi fornecido nenhum. Se o servidor proxy requer autenticação, lembre-se de que tem esta definição configurada no Config. Certifique-se também que estiver a utilizar contas de domínio para o utilizador que executa o assistente e para a conta de serviço. |

### <a name="proxy-idle-timeout-setting"></a>Definição de tempo limite de inatividade de proxy
Quando o Azure AD Connect envia um pedido de exportação para o Azure AD, o Azure AD pode demorar até 5 minutos para processar o pedido antes de gerar uma resposta. Isto pode acontecer, especialmente se existirem vários objetos de grupo com associações a grupos grandes incluídos no mesmo pedido de exportação. Certifique-se de que o tempo de limite de inatividade de Proxy está configurado para ser superior a 5 minutos. Caso contrário, o problema de conectividade intermitente com o Azure AD pode ser observado no servidor do Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre o Azure AD Connect e o Azure AD
Se tiver seguido todos estes passos anteriores e ainda não é possível ligar, poderá neste momento de começar a ver registos de rede. Esta secção é documentar um padrão de conectividade normal e com êxito. Ele também é listagem herrings red comuns que podem ser ignoradas quando estiver lendo os registos de rede.

* Existem chamadas para https://dc.services.visualstudio.com. Não é necessário ter esta aberto de URL no proxy para a instalação com êxito e essas chamadas podem ser ignoradas.
* Verá que a resolução de dns lista os anfitriões reais em nsatc.net de espaço de nome DNS e outros espaços de nomes não está sob o microsoftonline.com. No entanto, não existem quaisquer pedidos de serviço web nos nomes dos servidores reais e não é necessário adicionar esses URLs para o proxy.
* Os pontos de extremidade adminwebservice e provisioningapi são pontos finais de deteção e utilizado para localizar o ponto final real para utilizar. Estes pontos finais são diferentes consoante a sua região.

### <a name="reference-proxy-logs"></a>Registos de proxy de referência
Aqui está um despejo de um registo de proxy real e a página do Assistente de instalação de onde foi efetuada (entradas duplicadas para o mesmo ponto final tem sido removidas). Esta seção pode ser usada como uma referência para os seus próprios registos de proxy e de rede. Os pontos de extremidade reais poderão ser diferentes no seu ambiente (em particular essas URLs *itálico*).

**Ligar ao Azure AD**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configurar**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Sincronização inicial**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Erros de autenticação
Esta secção abrange os erros que podem ser devolvidos da ADAL (a biblioteca de autenticação utilizada pelo Azure AD Connect) e o PowerShell. O erro explicado deve ajudá-lo a em compreender os passos seguintes.

### <a name="invalid-grant"></a>Concessão inválido
Nome de utilizador ou palavra-passe inválido. Para obter mais informações, consulte [não é possível verificar a palavra-passe](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido
Diretório do Azure AD não pode ser encontrado ou resolvido. Talvez tentar iniciar sessão com um nome de utilizador num domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Falha na deteção de Realm de utilizador
Problemas de configuração do proxy ou de rede. Não é possível aceder à rede. Ver [resolver problemas de conectividade no Assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Palavra-passe do utilizador expirou
As suas credenciais tem expirado. Altere a palavra-passe.

### <a name="authorization-failure"></a>Falha de autorização
Falha ao autorizar o utilizador para efetuar a ação no Azure AD.

### <a name="authentication-canceled"></a>Autenticação foi cancelada
O desafio de autenticação multifator (MFA) foi cancelado.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Ligar a MS Online falhou
A autenticação foi efetuada com êxito, mas o Azure AD PowerShell tem um problema de autenticação.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Função de Administrador Global do Azure AD necessária
Utilizador foi autenticado com êxito. No entanto, utilizador não está atribuído a função de administrador global. Isto é [como pode atribuir a função de administrador global](../users-groups-roles/directory-assign-admin-roles.md) ao usuário. 

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management ativada
A autenticação foi efetuada com êxito. Tiver sido ativada a gestão de identidades privilegiadas e atualmente não é um administrador global. Para obter mais informações, consulte [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informações da empresa indisponíveis
A autenticação foi efetuada com êxito. Não foi possível obter as informações da empresa a partir do Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informações de domínio não disponíveis
A autenticação foi efetuada com êxito. Não foi possível obter as informações de domínio do Azure AD.

### <a name="unspecified-authentication-failure"></a>Falha de autenticação não especificado
Mostrado como erro inesperado no Assistente de instalação. Pode acontecer se tentar usar um **Microsoft Account** em vez de um **conta escolar ou da organização**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Passos de resolução de problemas para versões anteriores.
Com as versões a partir do número de compilação 1.1.105.0 (lançado em Fevereiro de 2016), o Assistente de início de sessão foi extinguida. Esta secção e a configuração deixou de ser necessários, mas é mantido como referência.

Para o início de sessão único no Assistente para funcionar, tem de configurar o winhttp. Esta configuração pode ser feita com [ **netsh**](how-to-connect-install-prerequisites.md#connectivity).  
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O Assistente de início de sessão não foi corretamente configurado
Este erro ocorre quando o Assistente de início de sessão não é possível alcançar o proxy ou o proxy não está a permitir que o pedido.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Se vir este erro, observe a configuração de proxy no [netsh](how-to-connect-install-prerequisites.md#connectivity) e certifique-se de que está correto.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Se parecer que o correto, siga os passos em [verificar a conectividade de proxy](#verify-proxy-connectivity) para ver se o problema está presente fora também o assistente.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
