---
title: Início de sessão único com o Proxy de aplicações | Documentos da Microsoft
description: Aborda como fornecer início de sessão único com o Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82f32477efb2e45eb95651dd21ccd2ae3095e7c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431314"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Delegação restrita de Kerberos para início de sessão único às suas aplicações com o Proxy de aplicações

Pode fornecer início de sessão único para on-premises aplicações publicadas através do Proxy de aplicações que são protegidas com a autenticação integrada do Windows. Estas aplicações requerem um tíquete Kerberos para o acesso. Proxy de aplicações utiliza a delegação restrita de Kerberos (KCD) para suportar estas aplicações. 

Pode ativar o início de sessão único para as aplicações com autenticação integrada do Windows (IWA) ao dar permissão dos conectores do Proxy de aplicações no Active Directory para representar os utilizadores. Os conectores de utilizam esta permissão para enviar e receber tokens em seu nome.

## <a name="how-single-sign-on-with-kcd-works"></a>Como início de sessão único com KCD funciona
Este diagrama explica o fluxo quando um utilizador tenta aceder a uma aplicação de local em que utiliza o IWA.

![Diagrama de fluxo de autenticação do Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. O utilizador introduz o URL para aceder à aplicação de local no através do Proxy de aplicações.
2. Proxy da aplicação redireciona o pedido para serviços de autenticação do Azure AD para preauthenticate. Neste momento, aplica-se do Azure AD qualquer autenticação aplicável e políticas de autorização, como a autenticação multifator. Se o utilizador for validado, o Azure AD cria um token e envia-os para o utilizador.
3. O utilizador passa o token para o Proxy de aplicações.
4. Proxy da aplicação valida o token e obtém o nome Principal de utilizador (UPN) do mesmo e, em seguida, envia o pedido, o UPN e o nome Principal do serviço (SPN) para o conector através de um canal seguro dually autenticado.
5. O conector efetua a negociação de Kerberos Constrained Delegation (KCD) com o AD, representar o utilizador para obter um token de Kerberos para o aplicativo de no local.
6. Do Active Directory envia o token de Kerberos para a aplicação para o conector.
7. O conector envia a solicitação original para o servidor de aplicações, com o token Kerberos que recebeu do AD.
8. A aplicação envia a resposta para o conector, que, em seguida, é devolvido para o serviço de Proxy da aplicação e, finalmente, para o usuário.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a utilizar com o início de sessão único para aplicações de IWA, certifique-se de que o ambiente está preparado com as seguintes definições e as configurações:

* As aplicações como aplicações Web do SharePoint, são definidas para utilizar a autenticação integrada do Windows. Para obter mais informações, consulte [ativar o suporte para a autenticação Kerberos](https://technet.microsoft.com/library/dd759186.aspx), ou para SharePoint, consulte [planear para a autenticação Kerberos no SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Todas as suas aplicações tenham [nomes principais de serviço](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* O servidor que executa o conector e o servidor que executa a aplicação são associados a um domínio e parte do mesmo domínio ou domínios fidedignas. Para obter mais informações sobre associação a um domínio, consulte [associar um computador a um domínio](https://technet.microsoft.com/library/dd807102.aspx).
* O servidor que executa o conector tem acesso para ler o atributo TokenGroupsGlobalAndUniversal para os utilizadores. Essa configuração padrão poderá ter sido afetada pelo ambiente de proteção de segurança.

### <a name="configure-active-directory"></a>Configurar o Active Directory
A configuração do Active Directory varia, dependendo se o servidor de aplicações e o seu conector de Proxy de aplicações são no mesmo domínio ou não.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Conector e servidor de aplicativos no mesmo domínio
1. No Active Directory, aceda a **ferramentas** > **utilizadores e computadores**.
2. Selecione o servidor que executa o conector.
3. Com o botão direito e selecione **propriedades** > **delegação**.
4. Selecione **confiar no computador para delegação apenas aos serviços especificados**. 
5. Sob **serviços aos quais esta conta pode apresentar credenciais delegadas** adicionar o valor para a identidade SPN do servidor de aplicativos. Isto permite que o conector do Proxy de aplicações representar os utilizadores no AD contra os aplicativos definidos na lista.

   ![Captura de ecrã de janela de propriedades do conector-Server](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Conector e servidor de aplicativos em domínios diferentes
1. Para obter uma lista de pré-requisitos para trabalhar com KCD entre domínios, consulte [delegação restrita de Kerberos entre domínios](https://technet.microsoft.com/library/hh831477.aspx).
2. Utilize o `principalsallowedtodelegateto` propriedade no servidor do conector para ativar o Proxy de aplicações delegar para o servidor do conector. O servidor de aplicações é `sharepointserviceaccount` e o servidor de delegação é `connectormachineaccount`. Para o Windows 2012 R2, use este código como exemplo:

```powershell
$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
```

`sharepointserviceaccount` pode ser a conta de computador do SPS ou uma conta de serviço sob a qual está a executar o conjunto de aplicações do SPS.

## <a name="configure-single-sign-on"></a>Configurar o início de sessão único 
1. Publicar a sua aplicação, de acordo com as instruções descritas [publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md). Verifique se seleciona **do Azure Active Directory** como o **método de pré-autenticação**.
2. Depois da aplicação é apresentada na lista de aplicações empresariais, selecione-o e clique em **início de sessão único**.
3. Defina o modo de início de sessão único para **a autenticação integrada do Windows**.  
4. Introduza o **SPN da aplicação interna** do servidor de aplicativos. Neste exemplo, o SPN para a nossa aplicação publicada é http/www.contoso.com. Este SPN tem de estar na lista de serviços a que o conector pode apresentar credenciais delegadas. 
5. Escolha o **identidade delegada de início de sessão** para o conector para utilizar em nome dos seus utilizadores. Para obter mais informações, consulte [trabalhando com diferentes no local e de identidades de nuvem](#Working-with-different-on-premises-and-cloud-identities)

   ![Configuração da aplicação avançado](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO para aplicações de não-Windows

O fluxo de delegação de Kerberos no Proxy de aplicações do Azure AD é iniciado quando o Azure AD autentica o utilizador na cloud. Assim que a solicitação chega no local, o conector do Proxy de aplicações do Azure AD emite um tíquete Kerberos em nome do utilizador ao interagir com o Active Directory local. Este processo é referido como Kerberos Constrained Delegation (KCD). Na fase seguinte, é enviado um pedido para a aplicação de back-end com esta permissão Kerberos. 

Existem vários protocolos que definem como enviar esses pedidos. A maioria dos servidores de não-Windows esperar negociar com SPNEGO. Esse protocolo é suportado no Proxy de aplicações do Azure AD, mas está desabilitado por predefinição. Um servidor de pode ser configuradas para o SPNEGO ou KCD padrão, mas não ambos.

Se configurar uma máquina de conector para SPNEGO, certifique-se de que todos os outros conectores nesse grupo conector também estão configurados com SPNEGO. Era esperado o KCD padrão de aplicativos devem ser encaminhados através de outros conectores que não estão configurados para SPNEGO.
 

Para ativar SPNEGO:

1. Abra uma linha de comando que é executado como administrador.
2. Na linha de comandos, execute os seguintes comandos de servidores do conector que precisam SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Para obter mais informações sobre Kerberos, consulte [tudo que precisa saber sobre Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Aplicações de não-Windows, normalmente, nomes de utilizador do utilizador ou nomes de conta SAM em vez do domínio endereços de e-mail. Se essa situação aplica-se às suas aplicações, tem de configurar o campo de identidade delegada de início de sessão para ligar as identidades de cloud para as identidades de aplicação. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Trabalhando com diferentes no local e de identidades de nuvem
Proxy de aplicações parte do princípio de que os utilizadores têm exatamente a mesma identidade na cloud e no local. Se não for esse o caso, pode continuar a utilizar KCD para início de sessão único. Configurar uma **delegado a identidade de início de sessão** para cada aplicação para especificar que identidade a utilizar quando efetuar o início de sessão único.  

Esta capacidade permite que muitas organizações que têm diferentes no local e de identidades de nuvem para terem SSO a partir da cloud para aplicações no local sem a necessidade dos usuários insiram diferentes nomes de utilizador e palavras-passe. Isto inclui as organizações que:

* Ter vários domínios internamente (joe@us.contoso.com, joe@eu.contoso.com) e um único domínio na cloud (joe@contoso.com).
* Tem o nome de domínio não encaminháveis internos internamente (joe@contoso.usa) e uma legal na cloud.
* Não utilize nomes de domínio internamente (joe)
* Utilize aliases diferentes no local e na cloud. Por exemplo, joe-johns@contoso.com vs. joej@contoso.com  

Com o Proxy de aplicações, pode selecionar que identidade para utilizar para obter a permissão de Kerberos. Esta definição é por aplicação. Algumas dessas opções são adequadas para sistemas que não aceitamos o formato de endereço de e-mail, outras pessoas foram concebidas para início de sessão alternativo.

![Captura de ecrã de parâmetro de identidade de início de sessão de delegado](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Se for utilizada a identidade delegada de início de sessão, o valor pode não ser exclusivo em todos os domínios ou florestas na sua organização. Pode evitar este problema ao publicar estas duas vezes com dois grupos diferentes do conector de aplicações. Uma vez que cada aplicativo tem um público de utilizador diferente, pode associar seus conectores para outro domínio.

### <a name="configure-sso-for-different-identities"></a>Configurar o SSO para diferentes identidades
1. Configure definições do Azure AD Connect, para a identidade principal é o endereço de e-mail (correio). Isto é feito como parte do processo de personalizar, alterando a **Nome Principal de utilizador** campo nas definições de sincronização. Estas definições também determinam como os utilizadores iniciar sessão no Office 365, dispositivos Windows 10 e outras aplicações que utilizam o Azure AD como respetivo armazenamento de identidade.  
   ![Identificar os utilizadores captura de ecrã - lista pendente de nome Principal de utilizador](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Nas definições de configuração da aplicação para a aplicação que pretende modificar, selecione o **identidade delegada de início de sessão** a ser utilizado:

   * Nome Principal de utilizador (por exemplo, joe@contoso.com)
   * Alternate Nome Principal de utilizador (por exemplo, joed@contoso.local)
   * Parte do nome de utilizador do nome do Principal de utilizador (por exemplo, joe)
   * Parte do nome de utilizador do nome do Principal de utilizador alternativo (por exemplo, joed)
   * Nome da conta SAM no local (depende da configuração do controlador de domínio)

### <a name="troubleshooting-sso-for-different-identities"></a>Resolução de problemas de SSO para diferentes identidades
Se existir um erro no processo de SSO, aparece no registo de eventos de máquina do conector conforme explicado na [resolução de problemas](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
No entanto, em alguns casos, a solicitação é enviada com êxito para a aplicação de back-end enquanto esse aplicativo responde em várias outras respostas HTTP. Resolução de problemas nesses casos, deve começar examinando o número de evento 24029 na máquina do conector no registo de eventos de sessão do Proxy de aplicações. A identidade do utilizador que foi utilizada para a delegação é apresentado no campo "utilizador" dentro os detalhes do evento. Para ativar o registo da sessão, selecione **Mostrar analíticas e registos de depuração** no menu de vista do Visualizador de eventos.

## <a name="next-steps"></a>Passos Seguintes

* [Como configurar uma aplicação de Proxy de aplicações para utilizar a delegação restrita de Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Resolver problemas que possa ter com o Proxy de aplicações](application-proxy-troubleshoot.md)


Para obter as notícias e atualizações mais recentes, consulte o [blogue do Proxy da Aplicação](https://blogs.technet.com/b/applicationproxyblog/)

