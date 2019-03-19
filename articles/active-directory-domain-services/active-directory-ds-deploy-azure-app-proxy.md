---
title: 'Azure Active Directory Domain Services: Implementar o Proxy de aplicações do Azure Active Directory | Documentos da Microsoft'
description: Utilizar o Proxy de aplicações do Azure AD em domínios geridos do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 867d061e46494e5ef65340ce325a71638acc8dfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099632"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implementar o Proxy de aplicações do Azure AD num domínio gerido do Azure AD Domain Services
Proxy de aplicações do Azure Active Directory (AD) ajuda-o a oferecer suporte a funcionários remotos ao publicar aplicações no local para ser acedido através da internet. Com o Azure AD Domain Services, pode agora migração lift-and-shift aplicações legadas em execução no local para serviços de infraestrutura do Azure. Em seguida, pode publicar esses aplicativos usando o Proxy de aplicações do Azure AD, para fornecer acesso remoto seguro aos utilizadores na sua organização.

Se estiver familiarizado com o Proxy de aplicações do Azure AD, saiba mais sobre esta funcionalidade com o seguinte artigo: [Como fornecer acesso remoto seguro a aplicações no local](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:

1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. Uma **licença do Azure AD básico ou Premium** é necessária para utilizar o Proxy de aplicações do Azure AD.
4. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](active-directory-ds-getting-started.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarefa 1 - Proxy de aplicações de ativar o Azure AD para o diretório do Azure AD
Execute os seguintes passos para ativar o Proxy de aplicação do Azure AD para o diretório do Azure AD.

1. Inicie sessão como administrador no [portal do Azure](https://portal.azure.com).

2. Clique em **do Azure Active Directory** para exibir a descrição geral de diretório. Clique em **aplicações empresariais**.

    ![Selecione o diretório do Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Clique em **proxy de aplicações**. Se não tiver uma subscrição do Azure AD básico ou do Azure AD Premium, verá uma opção para ativar uma versão de avaliação. Botão de alternar **ativar o Proxy de aplicações?** para **ativar** e clique em **guardar**.

    ![Ativar o Proxy da aplicação](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Para transferir o conector, clique nas **conector** botão.

    ![Transferir conector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na página de transferência, aceite os termos de licença e contrato de privacidade e clique nas **transferir** botão.

    ![Confirmar transferência](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarefa 2 - aprovisionar servidores de Windows associados a um domínio para implementar o conector do Proxy de aplicações do Azure AD
Precisa associados a um domínio do Windows Server máquinas virtuais no qual pode instalar o conector de Proxy de aplicações do Azure AD. Para algumas aplicações, pode optar por aprovisionar vários servidores em que o conector é instalado. Esta opção de implementação dá-lhe maior disponibilidade e ajuda a lidar com cargas mais pesadas de autenticação.

Aprovisione os servidores do conector na mesma rede virtual (ou uma rede virtual em modo de peering e ligado), em que tiver ativado o seu domínio gerido do Azure AD Domain Services. Da mesma forma, os servidores que alojam aplicações que publicar através do Proxy de aplicação tem de ser instalado na mesma rede virtual do Azure.

Para aprovisionar servidores do conector, siga as tarefas descritas no artigo intitulado [associar uma máquina de virtual do Windows a um domínio gerido](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarefa 3 - instale e registe o conector de Proxy de aplicações do Azure AD
Anteriormente, pode aprovisionar uma máquina virtual do Windows Server e associados a um domínio gerido. Nesta tarefa, é possível instalar o conector do Proxy de aplicações do Azure AD nesta máquina virtual.

1. Copie o pacote de instalação do conector para a VM em que instalou o conector de Proxy de aplicações Web do Azure AD.

2. Execute **AADApplicationProxyConnectorInstaller.exe** na máquina virtual. Aceite os termos de licença de software.

    ![Aceitar termos para instalação](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante a instalação, é solicitado que registe o conector com o Proxy de aplicações de diretório do Azure AD.
   * Fornecer seu **credenciais de administrador global do Azure AD**. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * A conta de administrador utilizada para registar o conector têm de pertencer ao mesmo diretório onde ativou o serviço de Proxy de aplicações. Por exemplo, se o domínio de inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio válido.
   * Se a configuração de segurança avançada do IE estiver ativada para o servidor onde está a instalar o conector, o ecrã de registo poderá estar bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
   * Se o registo do conetor falhar, veja [Resolver Problemas da Proxy da Aplicação](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantir que o conector funciona corretamente, execute o Azure AD Application Proxy de conector solucionador de problemas. Deverá ver um relatório com êxito depois de executar a resolução de problemas.

    ![Sucesso de resolução de problemas](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Deverá ver o conector recentemente instalado listado na página de proxy da aplicação no diretório do Azure AD.

    ![](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Pode optar por instalar os conectores em vários servidores para garantir a elevada disponibilidade para a autenticação de aplicações publicadas através do Proxy de aplicações do Azure AD. Execute os mesmos passos indicados acima para instalar o conector em outros servidores associados ao seu domínio gerido.
>
>

## <a name="next-steps"></a>Próximos Passos
Tem de configurar o Proxy de aplicações do Azure AD e integrou ao seu domínio gerido do Azure AD Domain Services.

* **Migre as suas aplicações para máquinas virtuais do Azure:** Pode migração lift-and-shift as aplicações a partir de servidores no local para máquinas virtuais do Azure associadas ao seu domínio gerido. Se o fizer, ajuda-o se livrar dos custos de infraestrutura de servidores no local a executar.

* **Publicar aplicações com o Proxy de aplicações do Azure AD:** Publica aplicações em execução nas suas máquinas virtuais do Azure com o Proxy de aplicações do Azure AD. Para obter mais informações, consulte [publicar aplicações com o Proxy de aplicações do Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Nota de implementação - aplicações publicar IWA (autenticação integrada do Windows) com o Proxy de aplicações do Azure AD
Ative o início de sessão único para as aplicações com autenticação integrada do Windows (IWA) ao conceder permissão de Conetores Proxy de aplicações para representar os utilizadores e enviar e receber tokens em seu nome. Configure a delegação restringida de Kerberos (KCD) para o conector conceder as permissões necessárias para aceder a recursos no domínio gerido. Utilize o mecanismo KCD baseada em recursos em domínios geridos para uma maior segurança.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Ativar baseada em recursos a delegação restringida de Kerberos para o conector do Proxy de aplicações do Azure AD
O conector do Proxy de aplicações do Azure deve ser configurado para delegação restringida de Kerberos (KCD), pelo que pode representar os utilizadores no domínio gerido. Num domínio gerido do Azure AD Domain Services, não tem privilégios de administrador de domínio. Por conseguinte, **tradicional KCD de ao nível da conta não é possível configurar um domínio gerido**.

Utilizar KCD baseada em recursos, conforme descrito neste [artigo](active-directory-ds-enable-kcd.md).

> [!NOTE]
> Tem de ser membro do grupo "Administradores do AAD DC", para administrar o domínio gerido utilizando cmdlets do PowerShell do AD.
>
>

Utilize o cmdlet do PowerShell Get-ADComputer para obter as definições para o computador no qual está instalado o conector do Proxy de aplicações do Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Por esse motivo, utilize o cmdlet Set-ADComputer para configurar o KCD baseada em recursos para o servidor de recurso.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Se tiver implementado múltiplos conectores do Proxy de aplicações no seu domínio gerido, tem de configurar o KCD baseada em recursos para cada instância desse tipo conector.


## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](active-directory-ds-getting-started.md)
* [Configurar a delegação restrita de Kerberos num domínio gerido](active-directory-ds-enable-kcd.md)
* [Descrição geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
