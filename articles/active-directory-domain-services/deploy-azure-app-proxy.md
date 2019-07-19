---
title: 'Azure Active Directory Domain Services: Implantar Proxy de Aplicativo do AD do Azure | Microsoft Docs'
description: Usar o Azure Proxy de Aplicativo do AD em Azure Active Directory Domain Services domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: eca421697081310b1bf245172b3ff125e11c8728
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234179"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implantar Proxy de Aplicativo do AD do Azure em um domínio Azure AD Domain Services gerenciado
O proxy de aplicativo Azure Active Directory (AD) ajuda você a dar suporte a funcionários remotos Publicando aplicativos locais para serem acessados pela Internet. Com o Azure AD Domain Services, agora você pode migrar e deslocar aplicativos herdados executados localmente para os serviços de infraestrutura do Azure. Em seguida, você pode publicar esses aplicativos usando o Proxy de Aplicativo do AD do Azure, para fornecer acesso remoto seguro aos usuários em sua organização.

Se você for novo no Proxy de Aplicativo do AD do Azure, saiba mais sobre esse recurso com o seguinte artigo: [Como fornecer acesso remoto seguro a aplicativos locais](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:

1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. Uma **licença Azure ad Basic ou Premium** é necessária para usar o proxy de aplicativo do AD do Azure.
4. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarefa 1-habilitar o Azure Proxy de Aplicativo do AD para seu diretório do Azure AD
Execute as etapas a seguir para habilitar o Proxy de Aplicativo do AD do Azure para seu diretório do Azure AD.

1. Entre como administrador no [portal do Azure](https://portal.azure.com).

2. Clique em **Azure Active Directory** para exibir a visão geral do diretório. Clique em **aplicativos empresariais**.

    ![Selecionar diretório do Azure AD](./media/app-proxy/app-proxy-enable-start.png)
3. Clique em **proxy de aplicativo**. Se você não tiver uma assinatura Azure AD Basic ou Azure AD Premium, verá uma opção para habilitar uma avaliação. Alterne **habilitar proxy de aplicativo?** para **habilitar** e clique em **salvar**.

    ![Ativar Proxy de aplicações](./media/app-proxy/app-proxy-enable-proxy-blade.png)
4. Para baixar o conector, clique no botão **conector** .

    ![Baixar o conector](./media/app-proxy/app-proxy-enabled-download-connector.png)
5. Na página de download, aceite os termos de licença e o contrato de privacidade e clique no botão **baixar** .

    ![Confirmar download](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarefa 2-provisionar servidores Windows ingressados no domínio para implantar o conector de Proxy de Aplicativo do AD do Azure
Você precisa de máquinas virtuais do Windows Server ingressadas no domínio, nas quais você pode instalar o conector de Proxy de Aplicativo do AD do Azure. Para alguns aplicativos, você pode optar por provisionar vários servidores nos quais o conector está instalado. Essa opção de implantação oferece maior disponibilidade e ajuda a lidar com cargas de autenticação mais pesadas.

Provisione os servidores do conector na mesma rede virtual (ou em uma rede virtual conectada/emparelhada), na qual você habilitou seu Azure AD Domain Services domínio gerenciado. Da mesma forma, os servidores que hospedam os aplicativos que você publica por meio do proxy de aplicativo precisam ser instalados na mesma rede virtual do Azure.

Para provisionar servidores de conector, siga as tarefas descritas no artigo intitulada [ingressar uma máquina virtual do Windows em um domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarefa 3-instalar e registrar o conector de Proxy de Aplicativo do AD do Azure
Anteriormente, você provisionou uma máquina virtual do Windows Server e ingressou nela no domínio gerenciado. Nesta tarefa, você instalará o conector de Proxy de Aplicativo do AD do Azure nesta máquina virtual.

1. Copie o pacote de instalação do conector para a VM na qual você instala o conector de proxy de aplicativo Web do Azure AD.

2. Execute **AADApplicationProxyConnectorInstaller. exe** na máquina virtual. Aceite os termos de licença de software.

    ![Aceitar os termos da instalação](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante a instalação, será solicitado que você registre o conector com o proxy de aplicativo do seu diretório do Azure AD.
   * Forneça suas **credenciais de administrador global do Azure ad**. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
   * A conta de administrador usada para registrar o conector deve pertencer ao mesmo diretório em que você habilitou o serviço de proxy de aplicativo. Por exemplo, se o domínio do locatário for contoso.com, o administrador deverá admin@contoso.com ser ou qualquer outro alias válido nesse domínio.
   * Se a configuração de segurança reforçada do IE estiver ativada para o servidor em que você está instalando o conector, a tela de registro poderá ser bloqueada. Para permitir o acesso, siga as instruções na mensagem de erro. Certifique-se de que a Segurança Avançada do Internet Explorer está desativada.
   * Se o registo do conetor falhar, veja [Resolver Problemas da Proxy da Aplicação](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantir que o conector funcione corretamente, execute o solucionador de problemas do conector do Proxy de Aplicativo do AD do Azure. Você deverá ver um relatório bem-sucedido depois de executar a solução de problemas.

    ![Êxito do solucionador de problemas](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Você deve ver o conector recém-instalado listado na página proxy de aplicativo no seu diretório do Azure AD.

    ![No portal do Azure, o conector instalado é exibido como disponível](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Você pode optar por instalar conectores em vários servidores para garantir a alta disponibilidade para autenticar aplicativos publicados por meio do Proxy de Aplicativo do AD do Azure. Execute as mesmas etapas listadas acima para instalar o conector em outros servidores ingressados em seu domínio gerenciado.
>
>

## <a name="next-steps"></a>Próximos Passos
Você configurou o Proxy de Aplicativo do AD do Azure e o integrou ao seu Azure AD Domain Services domínio gerenciado.

* **Migre seus aplicativos para máquinas virtuais do Azure:** Você pode mover e deslocar seus aplicativos de servidores locais para máquinas virtuais do Azure ingressadas em seu domínio gerenciado. Isso ajuda você a se livrar dos custos de infraestrutura da execução de servidores locais.

* **Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure:** Publicar aplicativos em execução em suas máquinas virtuais do Azure usando o Proxy de Aplicativo do AD do Azure. Para obter mais informações, consulte [publicar aplicativos usando o Azure proxy de aplicativo do AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Observação de implantação-publicar aplicativos IWA (autenticação integrada do Windows) usando o Azure Proxy de Aplicativo do AD
Habilite o logon único para seus aplicativos usando a autenticação integrada do Windows (IWA) concedendo permissão aos conectores de proxy de aplicativo para representar usuários e enviar e receber tokens em seu nome. Configure a delegação restrita de Kerberos (KCD) para o conector para conceder as permissões necessárias para acessar recursos no domínio gerenciado. Use o mecanismo KCD baseado em recursos em domínios gerenciados para aumentar a segurança.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Habilitar a delegação restrita de Kerberos baseada em recursos para o conector de Proxy de Aplicativo do AD do Azure
O conector de proxy Aplicativo Azure deve ser configurado para a delegação restrita de Kerberos (KCD), para que ele possa representar usuários no domínio gerenciado. Em um domínio gerenciado Azure AD Domain Services, você não tem privilégios de administrador de domínio. Portanto, o **KCD de nível de conta tradicional não pode ser configurado em um domínio gerenciado**.

Use KCD com base em recursos conforme descrito neste [artigo](deploy-kcd.md).

> [!NOTE]
> Você precisa ser um membro do grupo "administradores do controlador de domínio do AAD" para administrar o domínio gerenciado usando cmdlets do PowerShell do AD.
>
>

Use o cmdlet Get-ADComputer do PowerShell para recuperar as configurações do computador no qual o conector do Azure Proxy de Aplicativo do AD está instalado.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Depois disso, use o cmdlet Set-ADComputer para configurar o KCD baseado em recursos para o servidor de recursos.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Se você tiver implantado vários conectores de proxy de aplicativo em seu domínio gerenciado, será necessário configurar o KCD baseado em recursos para cada instância de conector.


## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](create-instance.md)
* [Configurar a delegação restrita de Kerberos em um domínio gerenciado](deploy-kcd.md)
* [Visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
