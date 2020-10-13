---
title: 'Azure AD Connect: Problemas de conectividade Azure AD de resolução de problemas Microsoft Docs'
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
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c46d977b6ce4eaa62aefc6874ce2b855a4711670
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317517"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Problemas Azure AD conectividade
Este artigo explica como funciona a conectividade entre o Azure AD Connect e o AD Azure e como resolver problemas de conectividade. Estes problemas são mais prováveis de serem vistos num ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Problemas de conectividade de resolução de problemas no assistente de instalação
O Azure AD Connect está a utilizar a Autenticação Moderna (utilizando a biblioteca ADAL) para autenticação. O assistente de instalação e o motor de sincronização propriamente dito exigem machine.config sejam corretamente configurados, uma vez que estes dois são aplicações .NET.

Neste artigo, mostramos como a Fabrikam se conecta ao Azure AD através do seu representante. O servidor proxy chama-se fabrikamproxy e está a usar a porta 8080.

Em primeiro lugar, temos de ter a certeza [** demachine.config**](how-to-connect-install-prerequisites.md#connectivity) está corretamente configurado e o serviço **Microsoft Azure AD Sync** foi reiniciado uma vez após a atualização de ficheiros machine.config.
![A screenshot mostra parte do ficheiro de pontos config da máquina.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> Em alguns blogs não microsoft, está documentado que devem ser feitas alterações para miiserver.exe.config em vez disso. No entanto, este ficheiro é substituído em todas as atualizações, pelo que mesmo que funcione durante a instalação inicial, o sistema deixa de funcionar na primeira atualização. Por essa razão, a recomendação é atualizar machine.config.
>
>

O servidor proxy também deve ter os URLs necessários abertos. A lista oficial está documentada nos [intervalos de endereços URLs e IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Destes URLs, a tabela a seguir é o mínimo absoluto para poder ligar-se ao Azure AD. Esta lista não inclui quaisquer funcionalidades opcionais, tais como a writeback de palavras-passe ou a Azure AD Connect Health. Está documentado aqui para ajudar na resolução de problemas para a configuração inicial.

| URL | Porta | Descrição |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Usado para descarregar listas de CRL. |
| \*.verisign.com |HTTP/80 |Usado para descarregar listas de CRL. |
| \*.entrust.net |HTTP/80 |Usado para descarregar listas de CRL para MFA. |
| \*.windows.net |HTTPS/443 |Costumava entrar no Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Usado para MFA. |
| \*.microsoftonline.com |HTTPS/443 |Usado para configurar o seu diretório AD Azure e dados de importação/exportação. |

## <a name="errors-in-the-wizard"></a>Erros no assistente
O assistente de instalação está a utilizar dois contextos de segurança diferentes. Na página **Connect to Azure AD,** está a utilizar o utilizador atualmente assinado. Na página **Configure,** está a mudar para a conta que [executa o serviço para o motor de sincronização](reference-connect-accounts-permissions.md#adsync-service-account). Se houver algum problema, parece que já está na página **De AD Do Connect to Azure** no assistente, uma vez que a configuração de procuração é global.

Os seguintes problemas são os erros mais comuns que se encontram no assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O assistente de instalação não foi corretamente configurado
Este erro aparece quando o próprio assistente não consegue chegar ao representante.
![A imagem mostra um erro: Incapaz de validar credenciais.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Se vir este erro, verifique se o [machine.config](how-to-connect-install-prerequisites.md#connectivity) foi corretamente configurado.
* Se isso parecer correto, siga os passos na [conectividade de procuração](#verify-proxy-connectivity) para ver se o problema também está presente fora do assistente.

### <a name="a-microsoft-account-is-used"></a>Uma conta Microsoft é usada
Se utilizar uma **conta Microsoft** em vez de uma conta de escola **ou organização,** verá um erro genérico.
![Uma conta Microsoft é utilizada](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>O ponto final do MFA não pode ser alcançado
Este erro aparece se o ponto final **https://secure.aadcdn.microsoftonline-p.com** não for alcançado e o seu administrador global tiver MFA ativado.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Se vir este erro, verifique se o ponto final **secure.aadcdn.microsoftonline-p.com** foi adicionado ao representante.

### <a name="the-password-cannot-be-verified"></a>A palavra-passe não pode ser verificada
Se o assistente de instalação tiver sucesso na ligação ao Azure AD, mas a palavra-passe em si não pode ser verificada, vê este erro: ![ Má palavra-passe.](./media/tshoot-connect-connectivity/badpassword.png)

* A palavra-passe é uma senha temporária e deve ser alterada? É realmente a palavra-passe correta? Tente iniciar sação `https://login.microsoftonline.com` (noutro computador que não o servidor AZure AD Connect) e verifique se a conta é utilizável.

### <a name="verify-proxy-connectivity"></a>Verificar conectividade por procuração
Para verificar se o servidor AZure AD Connect tem conectividade real com o Proxy e a Internet, utilize algum PowerShell para ver se o representante está a permitir ou não pedidos web. Num pedido powerShell, corra `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Tecnicamente, a primeira chamada é `https://login.microsoftonline.com` e este URI também funciona, mas o outro URI é mais rápido de responder.)

O PowerShell utiliza a configuração em machine.config para contactar o proxy. As definições em winhttp/netsh não devem afetar estes cmdlets.

Se o proxy estiver corretamente configurado, deverá obter um estado de sucesso: ![ Screenshot que mostre o estado de sucesso quando o representante está configurado corretamente.](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Se receber **Não conseguir ligar-se ao servidor remoto,** então o PowerShell está a tentar fazer uma chamada direta sem utilizar o proxy ou o DNS não está corretamente configurado. Certifique-se de que o ** ficheiromachine.config** está corretamente configurado.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Se o representante não estiver corretamente configurado, obtém-se um erro: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Erro | Texto de erro | Comentário |
| --- | --- | --- |
| 403 |Proibido |O representante não foi aberto para a URL solicitada. Reveja a configuração de procuração e certifique-se de que os [URLs foram abertos.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) |
| 407 |Autenticação por procuração necessária |O servidor proxy requereu uma sindução e nenhum foi fornecido. Se o seu servidor proxy necessitar de autenticação, certifique-se de que esta definição está configurada no machine.config. Certifique-se também de que está a utilizar contas de domínio para o utilizador que executa o assistente e para a conta de serviço. |

### <a name="proxy-idle-timeout-setting"></a>Definição de tempo limite de proxy ocioso
Quando o Azure AD Connect envia um pedido de exportação para a Azure AD, a Azure AD pode demorar até 5 minutos para processar o pedido antes de gerar uma resposta. Isto pode acontecer especialmente se houver uma série de objetos de grupo com grandes membros do grupo incluídos no mesmo pedido de exportação. Certifique-se de que o tempo de atividade do Proxy está configurado para ser superior a 5 minutos. Caso contrário, o problema de conectividade intermitente com o Azure AD pode ser observado no servidor Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre Azure AD Connect e Azure AD
Se seguiu todos estes passos anteriores e ainda não consegue ligar-se, pode neste momento começar a procurar registos de rede. Esta secção documenta um padrão de conectividade normal e bem sucedido. Também está a listar arenques vermelhos comuns que podem ser ignorados quando estiver a ler os registos de rede.

* Há chamadas `https://dc.services.visualstudio.com` para. Não é necessário ter este URL aberto no proxy para que a instalação tenha sucesso e estas chamadas podem ser ignoradas.
* Pode ver que a resolução do DNS lista os anfitriões reais para estar no espaço de nome DNS nsatc.net e outros espaços de nomes não sob microsoftonline.com. No entanto, não existem quaisquer pedidos de serviço web sobre os nomes reais do servidor e não tem de adicionar estes URLs ao representante.
* Os pontos finais adminwebservice e provisioningapi são pontos finais de descoberta e usados para encontrar o ponto final real para usar. Estes pontos finais são diferentes dependendo da sua região.

### <a name="reference-proxy-logs"></a>Registos de procuração de referência
Aqui está uma lixeira de um registo de procuração real e a página de assistente de instalação de onde foi tirada (foram removidas as entradas duplicadas para o mesmo ponto final). Esta secção pode ser usada como referência para os seus próprios registos de procuração e rede. Os pontos finais reais podem ser diferentes no seu ambiente (em particular os URLs em *itálico).*

**Ligar ao Azure AD**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-âncora*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configurar**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-âncora*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-âncora*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-âncora*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Sincronização inicial**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-âncora*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-âncora*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Erros de autenticação
Esta secção cobre erros que podem ser devolvidos da ADAL (a biblioteca de autenticação utilizada por Azure AD Connect) e PowerShell. O erro explicado deve ajudá-lo a entender os próximos passos.

### <a name="invalid-grant"></a>Subsídio inválido
Nome de utilizador ou senha inválido. Para mais informações, consulte [a palavra-passe não pode ser verificada.](#the-password-cannot-be-verified)

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido
O seu diretório AD Azure não pode ser encontrado ou resolvido. Talvez tente iniciar sessão com um nome de utilizador num domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Descoberta do Reino do Utilizador falhou
Problemas de configuração de rede ou procuração. A rede não pode ser alcançada. Consulte [problemas de conectividade de resolução de problemas no assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Senha de utilizador expirada
As suas credenciais expiraram. Mude a sua senha.

### <a name="authorization-failure"></a>Falha de autorização
Não autorizou o utilizador a realizar ações no Azure AD.

### <a name="authentication-canceled"></a>Autenticação cancelada
O desafio de autenticação multi-factor (MFA) foi cancelado.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Conecte-se à MS Online Falhado
A autenticação foi bem sucedida, mas o Azure AD PowerShell tem um problema de autenticação.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Papel de administrador global da Azure AD necessário
O utilizador foi autenticado com sucesso. No entanto, o utilizador não é atribuído a um papel de administração global. É [assim que pode atribuir o papel de administrador global](../users-groups-roles/directory-assign-admin-roles.md) ao utilizador.

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Gestão de Identidade Privilegiada Habilitada
A autenticação foi um sucesso. A gestão de identidade privilegiada foi ativada e atualmente não é administrador global. Para mais informações, consulte [Gestão de Identidade Privilegiada.](../privileged-identity-management/pim-getting-started.md)

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Informações da empresa indisponíveis
A autenticação foi um sucesso. Não foi possível obter informações da empresa da Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Informações de domínio indisponíveis
A autenticação foi um sucesso. Não foi possível obter informações de domínio da Azure AD.

### <a name="unspecified-authentication-failure"></a>Falha de autenticação não especificada
Apresentado como erro inesperado no assistente de instalação. Pode acontecer se tentar utilizar uma **Conta Microsoft** em vez de uma conta de escola **ou organização.**

## <a name="troubleshooting-steps-for-previous-releases"></a>Passos de resolução de problemas para lançamentos anteriores.
Com os lançamentos a começar com o número 1.1.105.0 (lançado em fevereiro de 2016), o assistente de inscrição foi reformado. Esta secção e a configuração não devem continuar a ser necessárias, mas são mantidas como referência.

Para que o único sinal no assistente funcione, o winhttp deve ser configurado. Esta configuração pode ser feita com [**netsh**](how-to-connect-install-prerequisites.md#connectivity).
![A screenshot mostra uma janela de pedido de comando a executar a ferramenta netsh para definir um proxy.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O assistente de inscrição não foi corretamente configurado
Este erro aparece quando o assistente de inscrição não consegue chegar ao representante ou o representante não está a permitir o pedido.
![A screenshot mostra um erro: Incapaz de validar credenciais, Verificar a conectividade da rede e as definições de firewall ou proxy.](./media/tshoot-connect-connectivity/nonetsh.png)

* Se vir este erro, consulte a configuração de procuração na [netsh](how-to-connect-install-prerequisites.md#connectivity) e verifique se está correta.
  ![A screenshot mostra uma janela de pedido de comando que executa a ferramenta netsh para mostrar a configuração de procuração.](./media/tshoot-connect-connectivity/netshshow.png)
* Se isso parecer correto, siga os passos na [conectividade de procuração](#verify-proxy-connectivity) para ver se o problema também está presente fora do assistente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
