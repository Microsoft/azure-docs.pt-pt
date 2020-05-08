---
title: 'Azure AD Connect: Problemas de conectividade Azure AD Microsoft Docs'
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f55f291575aea40cba8551a5fec535f63a90150c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610450"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Conectividade AD de Troubleshoot Azure
Este artigo explica como funciona a conectividade entre o Azure AD Connect e o Azure AD E como resolver problemas de conectividade. Estes problemas são mais prováveis de serem vistos num ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Problemas de conectividade no assistente de instalação
O Azure AD Connect está a utilizar a Autenticação Moderna (utilizando a biblioteca ADAL) para autenticação. O assistente de instalação e o motor de sincronização propriamente dito requerem que a máquina.config esteja corretamente configurada, uma vez que estas duas são aplicações .NET.

Neste artigo, mostramos como fabrikam se conecta ao Azure AD através do seu proxy. O servidor proxy é nomeado fabrikamproxy e está usando a porta 8080.

Primeiro, temos de ter a certeza de que [**a máquina.config**](how-to-connect-install-prerequisites.md#connectivity) está corretamente configurada.
![máquinaconfig](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Em alguns blogs não Microsoft, está documentado que devem ser feitas alterações para miiserver.exe.config. No entanto, este ficheiro é substituído em todas as atualizações, pelo que mesmo que funcione durante a instalação inicial, o sistema deixa de funcionar na primeira atualização. Por essa razão, a recomendação é atualizar machine.config.
>
>

O servidor proxy também deve ter os URLs necessários abertos. A lista oficial está documentada nos intervalos de [endereços do Office 365 URLs e IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Destes URLs, a tabela a seguir é o mínimo absoluto para poder ligar-se ao Azure AD. Esta lista não inclui quaisquer funcionalidades opcionais, tais como a reescrita de palavra-passe, ou Azure AD Connect Health. Está documentado aqui para ajudar na resolução de problemas para a configuração inicial.

| do IdP | Porta | Descrição |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Usado para baixar listas de CRL. |
| \*.verisign.com |HTTP/80 |Usado para baixar listas de CRL. |
| \*.entrust.net |HTTP/80 |Usado para baixar listas de CRL para MFA. |
| \*.windows.net |HTTPS/443 |Costumava inscrever-se no Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Usado para MFA. |
| \*.microsoftonline.com |HTTPS/443 |Usado para configurar o seu diretório De AD Azure e dados de importação/exportação. |

## <a name="errors-in-the-wizard"></a>Erros no assistente
O assistente de instalação está a utilizar dois contextos de segurança diferentes. Na página **Connect to Azure AD,** está a utilizar o atualmente assinado no utilizador. Na página **Configure,** está a mudar para a conta que [executa o serviço para o motor de sincronização](reference-connect-accounts-permissions.md#adsync-service-account). Se houver algum problema, parece muito provável que já esteja na página **AD Connect to Azure** no assistente, uma vez que a configuração proxy é global.

Os seguintes problemas são os erros mais comuns que se encontram no assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O assistente de instalação não foi corretamente configurado
Este erro aparece quando o próprio assistente não consegue alcançar o proxy.
![nomachineconfig](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Se vir este erro, verifique se a [máquina.config](how-to-connect-install-prerequisites.md#connectivity) foi corretamente configurada.
* Se isso parecer correto, siga os passos em Verificar a [conectividade proxy](#verify-proxy-connectivity) para ver se o problema também está presente fora do assistente.

### <a name="a-microsoft-account-is-used"></a>Uma conta Microsoft é usada
Se utilizar uma **conta Microsoft** em vez de uma conta de escola **ou organização,** verá um erro genérico.
![Uma Conta Microsoft é usada](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>O ponto final do MFA não pode ser alcançado
Este erro aparece se **https://secure.aadcdn.microsoftonline-p.com** o ponto final não puder ser atingido e a sua administração global tiver MFA ativada.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Se vir este erro, verifique se o ponto final **secure.aadcdn.microsoftonline-p.com** foi adicionado ao representante.

### <a name="the-password-cannot-be-verified"></a>A palavra-passe não pode ser verificada
Se o assistente de instalação tiver sucesso na ligação ao Azure AD, ![mas a senha em si não pode ser verificada, vê este erro: Má palavra-passe.](./media/tshoot-connect-connectivity/badpassword.png)

* A palavra-passe é uma senha temporária e deve ser alterada? É realmente a senha correta? Tente iniciar sessão `https://login.microsoftonline.com` em (noutro computador que não o servidor Azure AD Connect) e verifique se a conta é utilizável.

### <a name="verify-proxy-connectivity"></a>Verificar a conectividade proxy
Para verificar se o servidor Azure AD Connect tem conectividade real com o Proxy e a Internet, utilize algum PowerShell para ver se o proxy está a permitir pedidos web ou não. Num pedido powerShell, `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`corra. (Tecnicamente, a `https://login.microsoftonline.com` primeira chamada é para e este URI também funciona, mas o outro URI é mais rápido para responder.)

O PowerShell utiliza a configuração em machine.config para contactar o proxy. As definições em winhttp/netsh não devem ter impacto nestas cmdlets.

Se o proxy estiver corretamente configurado, deve ![obter um estatuto de sucesso: proxy200](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Se receber **Não conseguir ligar-se ao servidor remoto,** então o PowerShell está a tentar fazer uma chamada direta sem utilizar o proxy ou o DNS não está corretamente configurado. Certifique-se de que o ficheiro **machine.config** está corretamente configurado.
![incapaz de ligar](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Se o proxy não estiver corretamente configurado, obtém um erro: ![proxy200](./media/tshoot-connect-connectivity/invokewebrequest403.png)
![proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Erro | Texto de erro | Comentário |
| --- | --- | --- |
| 403 |Proibido |O representante não foi aberto para o URL solicitado. Reveja a configuração de procuração e certifique-se de que os [URLs foram abertos.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) |
| 407 |Autenticação por procuração necessária |O servidor proxy exigiu um sinal de inscrição e nenhum foi fornecido. Se o seu servidor proxy necessitar de autenticação, certifique-se de que esta definição está configurada na máquina.config. Certifique-se também de que está a utilizar contas de domínio para o utilizador que executa o assistente e para a conta de serviço. |

### <a name="proxy-idle-timeout-setting"></a>Configuração de tempo limite de procuração
Quando a Azure AD Connect enviar um pedido de exportação para a Azure AD, a Azure AD pode demorar até 5 minutos a processar o pedido antes de gerar uma resposta. Isto pode acontecer especialmente se houver uma série de objetos de grupo com grandes membros do grupo incluídos no mesmo pedido de exportação. Certifique-se de que o tempo limite de ocioso proxy está configurado para ser superior a 5 minutos. Caso contrário, o problema de conectividade intermitente com a AD Azure pode ser observado no servidor Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre azure AD Connect e Azure AD
Se seguiu todos estes passos anteriores e ainda não consegue ligar-se, pode, neste momento, começar a olhar para os registos de rede. Esta secção está a documentar um padrão de conectividade normal e bem-sucedido. Também está a listar arenques vermelhos comuns que podem ser ignorados quando está a ler os registos da rede.

* Há chamadas `https://dc.services.visualstudio.com`para. Não é necessário ter este URL aberto no proxy para que a instalação tenha sucesso e estas chamadas podem ser ignoradas.
* Você vê que a resolução do DNS lista os anfitriões reais para estar no espaço de nome DNS nsatc.net e outros espaços de nome não sob microsoftonline.com. No entanto, não existem pedidos de serviço web nos nomes reais do servidor e não tem de adicionar estes URLs ao proxy.
* Os pontos finais adminwebservice e provisioningapi são pontos finais de descoberta e usados para encontrar o ponto final real para usar. Estes pontos finais são diferentes dependendo da sua região.

### <a name="reference-proxy-logs"></a>Registos de procuração de referência
Aqui está uma lixeira de um registo de procuração real e a página do assistente de instalação de onde foi tirada (foram removidas as entradas duplicadas para o mesmo ponto final). Esta secção pode ser usada como referência para os seus próprios registos de procuração e rede. Os pontos finais reais podem ser diferentes no seu ambiente (em particular os URLs em *itálico).*

**Ligar ao Azure AD**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*âncora bba800*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configurar**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*âncora bba800*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*âncora bba900*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*âncora bba800*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Sincronização Inicial**

| Hora | do IdP |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*âncora bba900*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*âncora bba800*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Erros de autenticação
Esta secção cobre erros que podem ser devolvidos da ADAL (a biblioteca de autenticação utilizada pelo Azure AD Connect) e da PowerShell. O erro explicado deve ajudá-lo a entender os seus próximos passos.

### <a name="invalid-grant"></a>Subvenção inválida
Nome de utilizador ou palavra-passe inválido. Para mais informações, consulte [A palavra-passe não pode ser verificada](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido
O seu diretório Azure AD não pode ser encontrado ou resolvido. Talvez tente iniciar sessão com um nome de utilizador num domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Descoberta do Reino do Utilizador falhou
Problemas de configuração de rede ou procuração. A rede não pode ser contactada. Consulte problemas de [conectividade Troubleshoot no assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Palavra-passe do utilizador expirada
As suas credenciais expiraram. Mude a sua senha.

### <a name="authorization-failure"></a>Falha de Autorização
Não autorizou o utilizador a realizar a ação em Azure AD.

### <a name="authentication-canceled"></a>Autenticação Cancelada
O desafio de autenticação multifactor (MFA) foi cancelado.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Ligar-se a MS Online Falhado
A autenticação foi bem sucedida, mas a Azure AD PowerShell tem um problema de autenticação.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Papel de administrador global da Azure AD necessário
O utilizador foi autenticado com sucesso. No entanto, o utilizador não tem o papel de administrador global atribuído. É [assim que pode atribuir](../users-groups-roles/directory-assign-admin-roles.md) ao utilizador o papel de administrador global.

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Gestão privilegiada de identidade habilitada
A autenticação foi um sucesso. A gestão privilegiada da identidade foi ativada e você não é atualmente um administrador global. Para mais informações, consulte [A Gestão de Identidade Privilegiada.](../privileged-identity-management/pim-getting-started.md)

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informações da Empresa Indisponíveis
A autenticação foi um sucesso. Não conseguiu obter informações da empresa da Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informações de domínio indisponíveis
A autenticação foi um sucesso. Não conseguiu obter informações de domínio da Azure AD.

### <a name="unspecified-authentication-failure"></a>Falha de autenticação não especificada
Mostrado como erro inesperado no assistente de instalação. Pode acontecer se tentar usar uma **Conta Microsoft** em vez de uma conta de escola **ou organização.**

## <a name="troubleshooting-steps-for-previous-releases"></a>Passos de resolução de problemas para lançamentos anteriores.
Com lançamentos a começar com a construção número 1.1.105.0 (lançado em fevereiro de 2016), o assistente de inscrição foi reformado. Esta secção e a configuração já não devem ser necessárias, mas são mantidas como referência.

Para que o único sinal em assistente funcione, winhttp deve ser configurado. Esta configuração pode ser feita com [**netsh**](how-to-connect-install-prerequisites.md#connectivity).
![netsh](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O assistente de inscrição não foi corretamente configurado
Este erro aparece quando o assistente de inscrição não consegue contactar o representante ou o representante não está a permitir o pedido.
![nonetsh](./media/tshoot-connect-connectivity/nonetsh.png)

* Se vir este erro, olhe para a configuração de procuração em [netsh](how-to-connect-install-prerequisites.md#connectivity) e verifique se está correto.
  ![netshshow](./media/tshoot-connect-connectivity/netshshow.png)
* Se isso parecer correto, siga os passos em Verificar a [conectividade proxy](#verify-proxy-connectivity) para ver se o problema também está presente fora do assistente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
