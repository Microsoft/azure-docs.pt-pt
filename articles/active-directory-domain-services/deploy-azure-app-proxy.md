---
title: Implementar proxy de aplicação AD Azure para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como fornecer acesso seguro a aplicações internas para trabalhadores remotos, implantando e configurando o Azure Ative Directory Application Proxy num domínio gerido por Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8305b2190cf3b157973f5844c3237ffe73adba66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619921"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Implementar Proxy de aplicação AD Azure para acesso seguro a aplicações internas num domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Com os Serviços de Domínio AD Azure (Azure AD DS), pode levantar e deslocar aplicações antigas que correm no local para Azure. Azure Ative Directory (AD) Application Proxy ajuda-o a apoiar os trabalhadores remotos publicando de forma segura essas aplicações internas parte de um domínio gerido Azure AD DS para que possam ser acedidos através da internet.

Se é novo no Azure AD Application Proxy e quer saber mais, consulte [Como fornecer acesso remoto seguro a aplicações internas](../active-directory/manage-apps/application-proxy.md).

Este artigo mostra-lhe como criar e configurar um conector Proxy aplicação AD Azure para fornecer acesso seguro a aplicações num domínio gerido.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
    * Uma **licença Azure AD Premium** é necessária para usar o Azure AD Application Proxy.
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Criar um Windows VM de domínio

Para encaminhar o tráfego para aplicações em execução no seu ambiente, instale o componente de conector de aplicação AD AD Azure. Este conector Proxy da aplicação AD Azure deve ser instalado numa máquina virtual do Windows Server (VM) que se una ao domínio gerido. Para algumas aplicações, pode implementar vários servidores que cada um tem o conector instalado. Esta opção de implementação dá-lhe maior disponibilidade e ajuda a lidar com cargas de autenticação mais pesadas.

O VM que executa o conector Azure AD Application Proxy deve estar na mesma, ou numa rede virtual esprevada como o seu domínio gerido. Os VMs que depois acolhem as aplicações que publica através do Application Proxy também devem ser implantados na mesma rede virtual Azure.

Para criar um VM para o conector Proxy aplicação AD Azure, complete os seguintes passos:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado para os utilizadores dentro do domínio gerido. Os VMs para Azure AD Application Proxy e que executam as suas aplicações devem fazer parte do OU personalizado, não do *padrão AAD DC Computers* OU.
1. [Junte-se ao domínio das máquinas virtuais][create-join-windows-vm], tanto a que executa o conector Proxy de aplicação AD AD Azure, como as que executam as suas aplicações, para o domínio gerido. Crie estas contas de computador no OU personalizado a partir do passo anterior.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Descarregue o conector Proxy da aplicação AD Azure

Execute os seguintes passos para descarregar o conector Azure AD Application Proxy. O ficheiro de configuração que descarrega é copiado para o seu VM Proxy app na secção seguinte.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de utilizador que tenha permissões *de administrador da Enterprise* em Azure AD.
1. Procure e selecione **O Diretório Ativo Azure** no topo do portal e, em seguida, escolha **aplicações Enterprise**.
1. Selecione O representante de **aplicação** do menu do lado esquerdo. Para criar o seu primeiro conector e ativar o App Proxy, selecione o link para **descarregar um conector**.
1. Na página de descarregamento, aceite os termos da licença e o contrato de privacidade e, em seguida, **selecione Aceite os termos & Descarregue.**

    ![Descarregue o conector Proxy app Ad Azure](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Instale e registe o conector Proxy de aplicação AD Azure

Com um VM pronto a ser usado como conector Proxy aplicação AD Azure, agora copie e execute o ficheiro de configuração descarregado a partir do portal Azure.

1. Copie o ficheiro de configuração do conector de aplicação AD Azure para o seu VM.
1. Executar o ficheiro de configuração, tal como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos da licença de software.
1. Durante a instalação, é solicitado que registe o conector com o Application Proxy no seu diretório AD Azure.
   * Forneça as credenciais para um administrador global no seu diretório AZure AD. As credenciais de administrador global da Azure AD podem ser diferentes das credenciais do Azure no portal

        > [!NOTE]
        > A conta de administrador global utilizada para registar o conector deve pertencer ao mesmo diretório onde ativa o serviço Application Proxy.
        >
        > Por exemplo, se o domínio AD Azure for *contoso.com*, o administrador global deve ser `admin@contoso.com` ou outro pseudónimo válido nesse domínio.

   * Se a configuração de segurança melhorada do Internet Explorer for ligada para o VM onde instala o conector, o ecrã de registo poderá estar bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Reforçada do Internet Explorer durante o processo de instalação.
   * Se o registo do conector falhar, consulte [o Proxy de aplicação de resolução de problemas](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. No final da configuração, é mostrada uma nota para ambientes com um representante de saída. Para configurar o conector Azure AD Application Proxy para trabalhar através do representante de saída, executar o script fornecido, como `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na página de procuração de aplicação no portal Azure, o novo conector está listado com um estado de *Ativo,* como mostra o seguinte exemplo:

    ![O novo conector Proxy de aplicação AD AZure mostrado como ativo no portal Azure](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Para fornecer alta disponibilidade para aplicações autenticadas através do Azure AD Application Proxy, pode instalar conectores em vários VMs. Repita os mesmos passos listados na secção anterior para instalar o conector em outros servidores unidos ao domínio gerido.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Permitir a delegação restrita kerberos baseada em recursos

Se pretender utilizar um único sinal de acesso às suas aplicações utilizando a Autenticação Integrada do Windows (IWA), conceda aos conectores de aplicação AD Ad Proxy permissão para personificar os utilizadores e enviar e receber fichas em seu nome. Para conceder estas permissões, configura a delegação restrita kerberos (KCD) para que o conector aceda a recursos no domínio gerido. Como não tem privilégios de administrador de domínio num domínio gerido, o KCD de nível de conta tradicional não pode ser configurado num domínio gerido. Em vez disso, utilize o KCD baseado em recursos.

Para obter mais informações, consulte [a delegação restrita Configure Kerberos (KCD) nos Serviços de Domínio do Diretório Ativo Azure](deploy-kcd.md).

> [!NOTE]
> Você deve ser inscrito em uma conta de utilizador que é um membro do grupo de *administradores AD DC Azure* no seu inquilino AZure AD para executar os seguintes cmdlets PowerShell.
>
> O computador conta para o seu conector de aplicativo VM e VMs de aplicação devem estar em um OU personalizado onde você tem permissões para configurar KCD baseado em recursos. Não é possível configurar o KCD baseado em recursos para uma conta de computador no contentor *AAD DC Computers* incorporado.

Utilize o [Computador Get-AD][Get-ADComputer] para recuperar as definições do computador no qual está instalado o conector Proxy da aplicação AD Azure. A partir da sua gestão de domínio VM e inseriu-se como conta de utilizador que é membro do grupo de *administradores AD DC Azure,* executar os seguintes cmdlets.

O exemplo seguinte obtém informações sobre a conta do computador denominada *appproxy.aaddscontoso.com*. Forneça o seu próprio nome de computador para a aplicação Azure AD Proxy VM configurada nos passos anteriores.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Para cada servidor de aplicações que executa as aplicações por trás do Azure AD Application Proxy, utilize o [cmdlet Set-ADComputer][Set-ADComputer] PowerShell para configurar o KCD baseado em recursos. No exemplo seguinte, o conector proxy de aplicação AD AZure é autorizado a utilizar o *computador appserver.aaddscontoso.com:*

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Se implementar vários conectores proxy de aplicação AD Azure, tem de configurar kCD baseado em recursos para cada instância do conector.

## <a name="next-steps"></a>Passos seguintes

Com o Azure AD Application Proxy integrado com Azure AD DS, publique aplicações para os utilizadores acederem. Para obter mais informações, consulte [as aplicações de publicação utilizando o Azure AD Application Proxy](../active-directory/manage-apps/application-proxy-add-on-premises-application.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer