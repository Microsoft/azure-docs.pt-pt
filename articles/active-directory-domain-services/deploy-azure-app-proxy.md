---
title: Implementar procuração de aplicação ad azure para serviços de domínio azure ad [ Microsoft Docs
description: Saiba como fornecer acesso seguro a aplicações internas para trabalhadores remotos, implementando e configurando o Proxy de Aplicação de Diretório Ativo Azure num domínio gerido pelo Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c6e4e6a45fbbeab64184d8ae4b0684ba055d7735
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613978"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Implemente procuração de aplicação ad azure para acesso seguro a aplicações internas num domínio gerido pelos Serviços de Domínio Da Azure AD

Com o Azure AD Domain Services (Azure AD DS), pode levantar e mudar aplicações antigas que executam no local em Azure. A Procuração de Aplicação de Diretório Ativo Azure (AD) ajuda-o a apoiar os trabalhadores remotos publicando de forma segura essas aplicações internas parte de um domínio gerido pelo Azure AD DS para que possam ser acedidos através da internet.

Se é novo no Procurador de Aplicação AD Azure e quer saber mais, consulte [como fornecer acesso remoto seguro a aplicações internas](../active-directory/manage-apps/application-proxy.md).

Este artigo mostra-lhe como criar e configurar um conector de proxy de aplicação AD Azure para fornecer acesso seguro a aplicações num domínio gerido pelo Azure AD DS.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
    * É necessária uma **licença Azure AD Premium** para utilizar o Proxy de Aplicação AD Azure.
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Criar um VM Windows de domínio

Para encaminhar o tráfego para aplicações em funcionamento no seu ambiente, instale o componente de conector Proxy de Aplicação AD Azure. Este conector Proxy de Aplicação AD Azure deve ser instalado em máquinas virtuais do Windows Server (VM) que se juntem ao domínio gerido pelo Azure AD DS. Para algumas aplicações, pode implementar vários servidores que cada um tem o conector instalado. Esta opção de implementação dá-lhe uma maior disponibilidade e ajuda a lidar com cargas de autenticação mais pesadas.

O VM que gere o conector De Proxy de Aplicação AD Azure deve estar na mesma rede virtual, ou numa rede virtual em que tenha ativado o Azure AD DS. Os VMs que depois acolhem as aplicações que publica utilizando o Proxy de aplicação também devem ser implantados na mesma rede virtual Azure.

Para criar um VM para o conector Proxy de Aplicação AD Azure, complete os seguintes passos:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado aos utilizadores dentro do domínio gerido pelo Azure AD DS. Os VMs para o Proxy de Aplicação AD Azure e que executam as suas aplicações devem fazer parte do OU personalizado, não do *AAD DC Computers* OU padrão.
1. [Junte-se ao domínio das máquinas virtuais,][create-join-windows-vm]tanto a que executa o conector De Procuração de Aplicação AD Azure, como as que executam as suas aplicações, para o domínio gerido pelo Azure AD DS. Crie estas contas de computador na UA personalizada a partir do passo anterior.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Descarregue o conector proxy de aplicação da AD Azure

Execute os seguintes passos para descarregar o conector De Proxy de Aplicação AD Azure. O ficheiro de configuração que descarrega é copiado para o seu App Proxy VM na secção seguinte.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de utilizador que tenha permissões de *administrador da Enterprise* em Azure AD.
1. Procure e selecione **Azure Ative Directory** no topo do portal e, em seguida, escolha **aplicações Enterprise**.
1. Selecione **proxy de aplicação** do menu do lado esquerdo. Para criar o seu primeiro conector e ativar o Proxy da Aplicação, selecione o link para **descarregar um conector**.
1. Na página de descarregamento, aceite os termos da licença e o acordo de privacidade e, em seguida, **selecione Aceitar termos & Download**.

    ![Descarregue o conector De Proxy da App AD Azure](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instalar e registar o conector proxy de aplicação da AD Azure

Com um VM pronto a ser utilizado como conector De Procuração de Aplicação AD Azure, copia e executa o ficheiro de configuração descarregado do portal Azure.

1. Copie o ficheiro de configuração do conector proxy de aplicação AD Azure para o seu VM.
1. Executar o ficheiro de configuração, tal como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos da licença de software.
1. Durante a instalação, é solicitado que registe o conector com o Proxy de Aplicação no seu diretório Azure AD.
   * Forneça as credenciais para um administrador global no seu diretório Azure AD. As credenciais de administrador global da AD Azure podem ser diferentes das suas credenciais Azure no portal

        > [!NOTE]
        > A conta de administrador global utilizada para registar o conector deve pertencer ao mesmo diretório onde ativa o serviço Proxy de Aplicação.
        >
        > Por exemplo, se o domínio AD Azure for `admin@aaddscontoso.com` *aaddscontoso.com,* o administrador global deve ser ou outro pseudónimo válido nesse domínio.

   * Se a configuração de segurança melhorada do Internet Explorer for ligada para o VM onde instala o conector, o ecrã de registo poderá estar bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Melhorada do Internet Explorer durante o processo de instalação.
   * Se o registo do conector falhar, consulte [o Proxy da Aplicação Troubleshoot](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. No final da configuração, é mostrada uma nota para ambientes com procuração de saída. Para configurar o conector proxy de aplicação AD Azure para `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`trabalhar através do proxy de saída, executar o script fornecido, como .
1. Na página proxy da Aplicação no portal Azure, o novo conector está listado com um estatuto de *Ative,* como mostra o seguinte exemplo:

    ![O novo conector De Procuração de Aplicação AD Azure mostrado como ativo no portal Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Para fornecer uma elevada disponibilidade para aplicações autenticadas através do Proxy de Aplicação AD Azure, pode instalar conectores em vários VMs. Repita os mesmos passos listados na secção anterior para instalar o conector noutros servidores ligados ao domínio gerido pelo Azure AD DS.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Permitir a delegação limitada de Kerberos baseada em recursos

Se pretender utilizar um único sessão nas suas aplicações utilizando a Autenticação Integrada do Windows (IWA), conceda aos conectores de Procuração de Aplicação AD Azure a permissão para personificar os utilizadores e enviar e receber fichas em seu nome. Para conceder estas permissões, configura a delegação restrita kerberos (KCD) para que o conector aceda aos recursos no domínio gerido pela AD DS azure. Como não tem privilégios de administrador de domínio num domínio gerido por AD DS azure, o KCD tradicional de nível de conta não pode ser configurado num domínio gerido. Em vez disso, utilize o KCD baseado em recursos.

Para mais informações, consulte a delegação restrita da [Configure Kerberos (KCD) nos Serviços de Domínio de Diretório Ativo azure.](deploy-kcd.md)

> [!NOTE]
> Você deve ser inscrito numa conta de utilizador que seja membro do grupo de *administradores da Azure AD DC* no seu inquilino Azure AD para executar os seguintes cmdlets PowerShell.
>
> As contas do computador para o seu VM do conector Proxy da aplicação e vMs de aplicação devem estar num OU personalizado onde você tem permissões para configurar o KCD baseado em recursos. Não é possível configurar o KCD baseado em recursos para uma conta de computador no recipiente incorporado da *AAD DC Computers.*

Utilize o [Get-ADComputer][Get-ADComputer] para recuperar as definições do computador no qual o conector Proxy de Aplicação AD Azure está instalado. A partir da sua VM de gestão filiada em domínio e registrado como conta de utilizador que é membro do grupo de *administradores da AD DC Azure,* executa os seguintes cmdlets.

O exemplo seguinte obtém informações sobre a conta de computador chamada *appproxy.aaddscontoso.com*. Forneça o seu próprio nome de computador para o Proxy VM da aplicação AD Azure configurado nos passos anteriores.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Para cada servidor de aplicação que executa as aplicações por trás do Proxy de aplicação AD Azure, utilize o cmdlet [Set-ADComputer][Set-ADComputer] PowerShell para configurar o KCD baseado em recursos. No exemplo seguinte, o conector Proxy de Aplicação AD Azure tem permissão para utilizar o computador *appserver.aaddscontoso.com:*

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se implementar vários conectores de proxy de aplicação AD Azure, tem de configurar o KCD baseado em recursos para cada instância de conector.

## <a name="next-steps"></a>Passos seguintes

Com a Aplicação Azure AD Proxy integrada com o Azure AD DS, publique aplicações para os utilizadores acederem. Para mais informações, consulte a publicação de aplicações utilizando o Proxy de [Aplicação AD Azure](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
