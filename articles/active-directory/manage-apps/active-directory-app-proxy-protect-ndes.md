---
title: Integre com Proxy de aplicação AD em um servidor NDES
titleSuffix: Azure Active Directory
description: Orientação sobre a implementação de um Proxy de aplicação de diretório ativo Azure para proteger o seu servidor NDES.
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257901"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integre com o Azure AD Application Proxy em um servidor do Serviço de Inscrição de Dispositivos de Rede (NDES)

Azure Ative Directory (AD) Application Proxy permite-lhe publicar aplicações dentro da sua rede. Estas aplicações são como sites SharePoint, Microsoft Outlook Web App e outras aplicações web. Também fornece acesso seguro aos utilizadores fora da sua rede via Azure.

Se é novo no Azure AD Application Proxy e quer saber mais, consulte [acesso remoto a aplicações no local através do Azure AD Application Proxy](application-proxy.md).

O Azure AD Application Proxy é construído em Azure. Ele dá-lhe uma enorme quantidade de largura de banda de rede e infraestrutura de servidor para uma melhor proteção contra ataques de negação de serviço distribuídos (DDOS) e disponibilidade soberba. Além disso, não há necessidade de abrir portas de firewall externas para a sua rede no local e não é necessário nenhum servidor DMZ. Todo o tráfego é originado à entrada. Para obter uma lista completa de portas de saída, consulte [Tutorial: Adicione uma aplicação no local para acesso remoto através do Application Proxy in Azure Ative Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Azure AD Application Proxy é uma funcionalidade que só está disponível se estiver a utilizar as edições Premium ou Basic do Azure Ative Directory. Para mais informações, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/). 
> Se tiver licenças do Enterprise Mobility Suite (EMS), pode utilizar esta solução.
> O conector Proxy da aplicação AD Azure só instala no Windows Server 2012 R2 ou mais tarde. Este é também um requisito do servidor NDES.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Instale e registe o conector no servidor NDES

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como administrador de aplicação do diretório que utiliza o Application Proxy. Por exemplo, se o domínio do inquilino for contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro pseudónimo de administração nesse domínio.
1. Selecione o seu nome de utilizador no canto superior direito. Verifique se está inscrito num diretório que utiliza o Application Proxy. Se precisar de alterar os diretórios, selecione **o diretório da Switch** e escolha um diretório que utilize o Application Proxy.
1. No painel de navegação esquerdo, selecione **Azure Ative Directory**.
1. Em **Gestão**, selecione **Application proxy**.
1. Selecione **Baixar o serviço de conector**.

    ![Baixe o serviço de conector para ver os Termos de Serviço](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Leia os Termos de Serviço. Quando estiver pronto, **selecione Aceitar termos & Descarregar**.
1. Copie o ficheiro de configuração do conector de aplicação AD Azure para o seu servidor NDES. 
   > Pode instalar o conector em qualquer servidor dentro da sua rede corporativa com acesso a NDES. Não é necessário instalá-lo no próprio servidor NDES.
1. Executar o ficheiro de configuração, tal como *AADApplicationProxyConnectorInstaller.exe*. Aceite os termos da licença de software.
1. Durante a instalação, é solicitado que registe o conector com o Application Proxy no seu diretório AD Azure.
   * Forneça as credenciais para um administrador global ou de aplicação no seu diretório AZure AD. As credenciais globais ou de administrador de aplicação da Azure podem ser diferentes das credenciais do Azure no portal.

        > [!NOTE]
        > A conta de administrador global ou de aplicação utilizada para registar o conector deve pertencer ao mesmo diretório onde ativa o serviço Application Proxy.
        >
        > Por exemplo, se o domínio AD Azure for *contoso.com,* o administrador global/de aplicação deve ser `admin@contoso.com` ou outro pseudónimo válido nesse domínio.

   * Se a configuração de segurança melhorada do Internet Explorer for ligada para o servidor onde instala o conector, o ecrã de registo poderá estar bloqueado. Para permitir o acesso, siga as instruções na mensagem de erro ou desligue a Segurança Reforçada do Internet Explorer durante o processo de instalação.
   * Se o registo do conector falhar, consulte [o Proxy de aplicação de resolução de problemas](application-proxy-troubleshoot.md).
1. No final da configuração, é mostrada uma nota para ambientes com um representante de saída. Para configurar o conector Azure AD Application Proxy para trabalhar através do representante de saída, executar o script fornecido, como `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` .
1. Na página de procuração de aplicação no portal Azure, o novo conector está listado com um estado de *Ativo,* como mostra o seguinte exemplo:

    ![O novo conector Proxy de aplicação AD AZure mostrado como ativo no portal Azure](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Para fornecer alta disponibilidade para aplicações autenticadas através do Azure AD Application Proxy, pode instalar conectores em vários VMs. Repita os mesmos passos listados na secção anterior para instalar o conector em outros servidores unidos ao domínio gerido Azure AD DS.

1. Após uma instalação bem sucedida, volte ao portal Azure.

1. Selecione **aplicações da Enterprise**.

   ![certifique-se de que está a envolver as partes interessadas certas](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selecione **+Nova Aplicação** e, em seguida, selecione **a aplicação no local**. 

1. Na **aplicação Adicionar a sua própria aplicação no local,** configuure os seguintes campos:

   * **Nome**: Introduza um nome para o pedido.
   * **Url interno**: Introduza o URL/FQDN interno do seu servidor NDES no qual instalou o conector.
   * **Pré Autenticação**: Selecione **Passthrough**. Não é possível usar qualquer forma de pré-autenticação. O protocolo utilizado para pedidos de certificados (SCEP) não fornece tal opção.
   * Copie o **URL externo** fornecido para a sua área de transferência.

1. **Selecione +Adicionar** para guardar a sua aplicação.

1. Teste se pode aceder ao seu servidor NDES através do proxy Azure AD Application, colando o link que copiou no passo 15 para um browser. Deverá ver uma página de boas-vindas IIS padrão.

1. Como teste final, adicione o *caminhomscep.dll* ao URL existente que colou no passo anterior:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Deverá ver uma resposta **HTTP Error 403 – Proibida.**

1. Altere o URL NDES fornecido (via Microsoft Intune) para dispositivos. Esta alteração pode estar no Microsoft Endpoint Configuration Manager ou no Microsoft Endpoint Manager.

   * Para o Gestor de Configuração, vá ao ponto de registo do certificado e ajuste o URL. Este URL é o que os dispositivos chamam e apresentam o seu desafio.
   * Para Intune autónomo, edite ou crie uma nova política SCEP e adicione o novo URL.

## <a name="next-steps"></a>Passos seguintes

Com o Azure AD Application Proxy integrado com o NDES, publique aplicações para os utilizadores acederem. Para obter mais informações, consulte [as aplicações de publicação utilizando o Azure AD Application Proxy](./application-proxy-add-on-premises-application.md).
