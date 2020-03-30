---
title: Integrar com procuração de aplicação aD num servidor NDES
titleSuffix: Azure Active Directory
description: Orientação sobre a implementação de um Proxy de aplicação de diretório ativo Azure para proteger o seu servidor NDES.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032260"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrar com o Proxy de Aplicação AD Azure num servidor do Serviço de Inscrição de Dispositivos de Rede (NDES)

A Procuração de Aplicação de Diretório Ativo Azure (AD) permite-lhe publicar aplicações dentro da sua rede. Estas aplicações são tais como sites SharePoint, Microsoft Outlook Web App e outras aplicações web. Também fornece acesso seguro aos utilizadores fora da sua rede via Azure.

Se é novo na Procuração de Aplicação AD Azure e quer saber mais, consulte o acesso remoto a aplicações no local através do Procuração de [Aplicações AD Azure](application-proxy.md).

A Procuração de Aplicação Azure AD é construída em Azure. Dá-lhe uma enorme quantidade de largura de banda de rede e infraestrutura de servidor para uma melhor proteção contra ataques de negação de serviço distribuídos (DDOS) e uma excelente disponibilidade. Além disso, não há necessidade de abrir portas de firewall externas para a sua rede no local e não é necessário nenhum servidor DMZ. Todo o tráfego é originado de entrada. Para obter uma lista completa de portas de saída, consulte [Tutorial: Adicione um pedido no local para acesso remoto através do Application Proxy no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

> O Procurador de Aplicação Azure AD é uma funcionalidade que só está disponível se estiver a utilizar as edições Premium ou Básicas do Diretório Ativo Azure. Para mais informações, consulte [os preços do Diretório Ativo azure.](https://azure.microsoft.com/pricing/details/active-directory/) 
> Se tiver licenças da Enterprise Mobility Suite (EMS), poderá utilizar esta solução.
> O conector Proxy de Aplicação AD Azure só se instala no Windows Server 2012 R2 ou posteriormente. Este é também um requisito do servidor NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instale e registe o conector no servidor NDES

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de candidatura sintetizador do diretório que utiliza o Application Proxy. Por exemplo, se o domínio do inquilino admin@contoso.com for contoso.com, o administrador deve ser ou qualquer outro pseudónimo administrativo nesse domínio.
1. Selecione o seu nome de utilizador no canto superior direito. Verifique se está inscrito num diretório que usa procuração de aplicação. Se precisar de mudar de diretórios, selecione **'Diretório' da Switch** e escolha um diretório que utilize proxy de aplicação.
1. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
1. Em **'Gerir',** selecione **Proxy de Aplicação**.
1. Selecione **serviço de conector de descarregamento**.

    ![Descarregue o serviço de conector para ver os Termos de Serviço](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leia os Termos de Serviço. Quando estiver pronto, **selecione Aceitar termos & Baixar**.
1. Copie o ficheiro de configuração do conector proxy de aplicação AD Azure para o seu servidor NDES. 
   > Pode instalar o conector em qualquer servidor dentro da sua rede corporativa com acesso ao NDES. Não é preciso instalá-lo no próprio servidor NDES.
1. Executar o ficheiro de configuração, tal como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos da licença de software.
1. Durante a instalação, é solicitado que registe o conector com o Proxy de Aplicação no seu diretório Azure AD.
   * Forneça as credenciais para um administrador global ou de aplicação no seu diretório Azure AD. As credenciais de administrador global ou de aplicação da Azure AD podem ser diferentes das suas credenciais Azure no portal.

        > [!NOTE]
        > A conta global ou administradora de aplicação utilizada para registar o conector deve pertencer ao mesmo diretório onde ativa o serviço Proxy de Aplicação.
        >
        > Por exemplo, se o domínio AD Azure for *contoso.com,* o administrador global/aplicação deve ser `admin@contoso.com` ou outro pseudónimo válido nesse domínio.

   * Se a configuração de segurança melhorada do Internet Explorer for ligada para o servidor onde instala o conector, o ecrã de registo poderá estar bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Melhorada do Internet Explorer durante o processo de instalação.
   * Se o registo do conector falhar, consulte [o Proxy da Aplicação Troubleshoot](application-proxy-troubleshoot.md).
1. No final da configuração, é mostrada uma nota para ambientes com procuração de saída. Para configurar o conector proxy de aplicação AD Azure para `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`trabalhar através do proxy de saída, executar o script fornecido, como .
1. Na página proxy da Aplicação no portal Azure, o novo conector está listado com um estatuto de *Ative,* como mostra o seguinte exemplo:

    ![O novo conector De Procuração de Aplicação AD Azure mostrado como ativo no portal Azure](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Para fornecer uma elevada disponibilidade para aplicações autenticadas através do Proxy de Aplicação AD Azure, pode instalar conectores em vários VMs. Repita os mesmos passos listados na secção anterior para instalar o conector noutros servidores ligados ao domínio gerido pelo Azure AD DS.

1. Após uma instalação bem sucedida, volte ao portal Azure.

1. Selecione **aplicações Enterprise**.

   ![garantir que está a envolver as partes interessadas certas](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **Selecione +Nova Aplicação**e, em seguida, selecione a **aplicação No-Local**. 

1. Na **aplicação Adicionar a sua própria aplicação no local,** configure os seguintes campos:

   * **Nome**: Insira um nome para a aplicação.
   * **Url interno**: Introduza o URL/FQDN interno do seu servidor NDES no qual instalou o conector.
   * **Pré-Autenticação**: Selecione **Passthrough**. Não é possível usar qualquer forma de pré-autenticação. O protocolo utilizado para pedidos de certificados (SCEP) não fornece tal opção.
   * Copie o **URL Externo** fornecido para a sua área de prancheta.

1. Selecione **+Adicionar** para salvar a sua aplicação.

1. Teste se pode aceder ao seu servidor NDES através do proxy da Aplicação AD Azure, colando o link que copiou no passo 10 para um browser. Você deve ver uma página de boas-vindas padrão IIS.

1. Como teste final, adicione o caminho *mscep.dll* ao URL existente que colou no passo anterior:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Deve ver uma resposta **HTTP Error 403 – Proibida.**

1. Alterar o URL NDES fornecido (via Microsoft Intune) para dispositivos, esta alteração pode estar no Microsoft Endpoint Configuration Center ou no Intune Cloud.

   * Para o Centro de Configuração, vá ao Ponto de Registo do Certificado (CRP) e ajuste o URL. Este URL é o que os dispositivos chamam e apresentam o seu desafio.
   * Para Intune Cloud Only, também conhecido como Intune Autónomo, ou edita ou cria uma nova política SCEP e adicione o novo URL.

## <a name="next-steps"></a>Passos seguintes

Com a Aplicação Azure AD Proxy integrada com o NDES, publique aplicações para os utilizadores acederem. Para mais informações, consulte a publicação de aplicações utilizando o Proxy de [Aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).
