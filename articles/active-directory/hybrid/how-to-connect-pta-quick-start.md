---
title: Autenticação de pass-through do Azure AD - guia de introdução | Documentos da Microsoft
description: Este artigo descreve como começar com a autenticação pass-through do Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticação do Azure AD Connect pass-through, Active Directory, de instalação necessários componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51fc93f9508bada40885e41b39e8a87cf4e0bf3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101011"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticação pass-through do Azure Active Directory: Início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implementar a autenticação do Azure AD pass-through

Autenticação de pass-through do Azure Active Directory (Azure AD) permite aos utilizadores iniciar sessão no local e aplicações baseadas na cloud utilizando as mesmas palavras-passe. Autenticação pass-through assina os utilizadores validando as palavras-passe diretamente no Active Directory no local.

>[!IMPORTANT]
>Se estiver a migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhados publicado [aqui](https://aka.ms/adfstoPTADPDownload).

Siga estas instruções para implementar a autenticação pass-through no seu inquilino:

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos.

### <a name="in-the-azure-active-directory-admin-center"></a>No Centro de administração do Azure Active Directory

1. Crie uma conta de administrador global apenas na cloud no seu inquilino do Azure AD. Dessa forma, pode gerir a configuração do seu inquilino devem seus serviços no local falhar ou ficar indisponíveis. Saiba mais sobre [adicionar uma conta de administrador global apenas na cloud](../active-directory-users-create-azure-portal.md). Concluir este passo é fundamental assegurar que não fica bloqueado fora do seu inquilino.
2. Adicionar um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu inquilino do Azure AD. Os utilizadores podem a iniciar sessão com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor com o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Se não fez, a ativada [ativar o TLS 1.2 no servidor](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adicione o servidor à mesma floresta do Active Directory que os utilizadores cujas palavras-passe tem de validar.
2. Instalar o [a versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado no passo anterior. Se já tiver executado o Azure AD Connect, certifique-se de que a versão é 1.1.750.0 ou posterior.

    >[!NOTE]
    >O Azure AD Connect as versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash de palavra-passe. Se _não_ tenciona utilizar a sincronização de hash de palavra-passe em conjunto com a autenticação pass-through, leia o [notas de versão do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identificar um ou mais servidores adicionais (com o Windows Server 2012 R2 ou posterior, com TLS 1.2, ativado) onde é possível executar agentes de autenticação autónomo. Estes servidores adicionais são necessários para garantir a disponibilidade elevada de pedidos para iniciar sessão. Adicione os servidores à mesma floresta do Active Directory que os utilizadores cujas palavras-passe tem de validar.

    >[!IMPORTANT]
    >Em ambientes de produção, recomendamos que tenha um mínimo de 3 de autenticação de agentes em execução no seu inquilino. Existe um limite de sistema de 40 agentes de autenticação por inquilino. E como melhor prática, tratar todos os servidores com agentes de autenticação, como sistemas de 0 (consulte [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Se existir uma firewall entre os servidores e do Azure AD, configure os seguintes itens:
   - Certifique-se de que os agentes de autenticação podem efetuar *saída* pedidos para o Azure AD sobre as seguintes portas:

     | Número da porta | Como são utilizadas |
     | --- | --- |
     | **80** | Transfere as listas de revogação de certificados (CRL) ao validar o certificado SSL |
     | **443** | Processa todas as comunicações de saída com o serviço |
     | **8080** (opcional) | Agentes de autenticação reportam o estado de cada dez minutos através da porta 8080, se a porta 443 não está disponível. Este estado é apresentado no portal do Azure AD. A porta 8080 está _não_ utilizado para inícios de sessão do utilizador. |
     
     Se a firewall impuser regras de acordo com os utilizadores de origem, abra estas portas para o tráfego dos serviços do Windows que são executados como um serviço de rede.
   - Se sua firewall ou proxy permite que listas de permissões de DNS, ligações de lista de permissões para  **\*. msappproxy.net** e  **\*. servicebus.windows.net**. Se não, permitir o acesso para o [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que é atualizado semanalmente.
   - Os agentes de autenticação precisam acessar **login.windows.net** e **login.microsoftonline.com** para o registo inicial. Abra a firewall para essas URLs também.
   - Para validação do certificado, os seguintes URLs de desbloqueio: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, e **www \.microsoft.com:80**. Uma vez que estes URLs são utilizadas para validação de certificados com outros produtos da Microsoft que pode já ter estes URLs desbloqueados.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar a autenticação pass-through através de [do Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Pode ativar a autenticação pass-through no servidor primário ou de teste do Azure AD Connect. É altamente recomendável que habilitá-la do servidor primário. Se estiver a configurar um servidor de preparação do Azure AD Connect no futuro, **tem** continue para escolher a autenticação pass-through como a opção de início de sessão; escolher outra opção será **desativar** Autenticação pass-through no inquilino e ignorar a definição no servidor primário.

Se estiver a instalar o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizada](how-to-connect-install-custom.md). Na **sessão do utilizador** página, selecione **autenticação pass-through** como o **método de início de sessão**. A conclusão com êxito, um agente de autenticação pass-through está instalado no mesmo servidor do Azure AD Connect. Além disso, a funcionalidade de autenticação pass-through está ativada no seu inquilino.

![Azure AD Connect: Início de sessão do utilizador](./media/how-to-connect-pta-quick-start/sso3.png)

Se já tiver instalado o Azure AD Connect utilizando as [instalação rápida](how-to-connect-install-express.md) ou o [instalação personalizada](how-to-connect-install-custom.md) caminho, selecione o **alterar utilizador inicie sessão** tarefas no Azure AD Ligar e, em seguida, selecione **seguinte**. Em seguida, selecione **autenticação pass-through** como o método de início de sessão. A conclusão com êxito, um agente de autenticação pass-through é instalado no mesmo servidor do Azure AD Connect e a funcionalidade está ativada no seu inquilino.

![Azure AD Connect: Alterar início de sessão do utilizador](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Autenticação pass-through é um recurso de nível de inquilino. Ativá-la afeta o início de sessão para os utilizadores em todas _todos os_ os domínios geridos no seu inquilino. Se estiver a mudar de serviços de Federação do Active Directory (AD FS) para autenticação pass-through, deve esperar, pelo menos, 12 horas antes de encerrar a sua infraestrutura do AD FS. Este tempo de espera é garantir que os utilizadores podem manter início de sessão no Exchange ActiveSync durante a transição. Para obter mais ajuda sobre a migração do AD FS para autenticação pass-through, confira o nosso plano de implementação detalhados publicado [aqui](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Passo 3: A funcionalidade de teste

Siga estas instruções para verificar que ativou a autenticação pass-through corretamente:

1. Inicie sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global do seu inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**.
4. Certifique-se de que o **autenticação pass-through** funcionalidade é apresentado como **ativado**.
5. Selecione **autenticação pass-through**. O **autenticação pass-through** painel lista os servidores onde estão instalados os agentes de autenticação.

![Centro de administração do Azure Active Directory: Painel do Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centro de administração do Azure Active Directory: Painel de autenticação pass-through](./media/how-to-connect-pta-quick-start/pta8.png)

Nesta fase, os utilizadores de todos os domínios geridos no seu inquilino podem iniciar sessão utilizando a autenticação pass-through. No entanto, os utilizadores de domínios federados continuam iniciar sessão com o AD FS ou outro fornecedor de federação que configurou anteriormente. Se converter um domínio de federado gerenciados, todos os utilizadores de que o domínio iniciar automaticamente a iniciar sessão utilizando a autenticação pass-through. A funcionalidade de autenticação pass-through não afeta os utilizadores apenas na cloud.

## <a name="step-4-ensure-high-availability"></a>Passo 4: Certifique-se de elevada disponibilidade

Se planeia implementar autenticação pass-through num ambiente de produção, deve instalar agentes de autenticação adicional autónomo. Instalar estes agentes de autenticação no servidor (es) _outros_ que um AD Connect do Azure em execução. Esta configuração fornece elevada disponibilidade para pedidos de início de sessão do utilizador.

>[!IMPORTANT]
>Em ambientes de produção, recomendamos que tenha um mínimo de 3 de autenticação de agentes em execução no seu inquilino. Existe um limite de sistema de 40 agentes de autenticação por inquilino. E como melhor prática, tratar todos os servidores com agentes de autenticação, como sistemas de 0 (consulte [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Siga estas instruções para transferir o software do agente de autenticação:

1. Para transferir a versão mais recente do agente de autenticação (versão 1.5.193.0 ou posterior), iniciar sessão para o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com credenciais de administrador global do seu inquilino.
2. Selecione **do Azure Active Directory** no painel esquerdo.
3. Selecione **do Azure AD Connect**, selecione **autenticação pass-through**e, em seguida, selecione **transferir o agente**.
4. Selecione o **aceitar os termos e transferir** botão.

![Centro de administração do Azure Active Directory: Baixe o botão de agente de autenticação](./media/how-to-connect-pta-quick-start/pta9.png)

![Centro de administração do Azure Active Directory: Baixe o painel de agente](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Pode também diretamente [transferir o software do agente de autenticação](https://aka.ms/getauthagent). Rever e aceitar o agente de autenticação [termos de serviço](https://aka.ms/authagenteula) _antes de_ instalá-lo.

Existem duas formas de implementar um agente de autenticação de autónomo:

Em primeiro lugar, pode fazê-lo interativamente apenas executar o agente de autenticação transferido executável e fornecendo credenciais de administrador global do seu inquilino, quando lhe for pedido.

Em segundo lugar, pode criar e executar um script de implementação automática. Isto é útil quando pretender implementar vários agentes de autenticação de uma só vez ou instalar agentes de autenticação nos servidores de Windows que não têm a interface de utilizador ativado ou que não pode acessar com o ambiente de trabalho remoto. Aqui estão as instruções sobre como usar essa abordagem:

1. Execute o seguinte comando para instalar um agente de autenticação: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Pode registrar o agente de autenticação com o serviço com o Windows PowerShell. Criar um objeto de credenciais de PowerShell `$cred` que contém um nome de utilizador de administrador global e a palavra-passe para o seu inquilino. Execute o seguinte comando, substituindo *\<nome de utilizador\>* e  *\<palavra-passe\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Aceda a **ligar o agente de autenticação C:\Program Files\Microsoft Azure AD** e execute o script seguinte com o `$cred` objeto que criou:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Passos Seguintes
- [Migrar do AD FS para autenticação pass-through](https://aka.ms/adfstoptadp) -um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para autenticação pass-through.
- [Bloqueio do smart](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de bloqueio inteligente no seu inquilino para proteger contas de utilizador.
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais cenários são suportados com a autenticação pass-through e quais não são.
- [Análise técnica aprofundada](how-to-connect-pta-how-it-works.md): Compreenda como funciona o recurso de autenticação pass-through.
- [Perguntas mais frequentes sobre](how-to-connect-pta-faq.md): Encontre respostas para perguntas mais frequentes.
- [Resolver problemas de](tshoot-connect-pass-through-authentication.md): Saiba como resolver problemas comuns com a funcionalidade de autenticação pass-through.
- [Detalhada da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas sobre a funcionalidade de autenticação pass-through.
- [SSO totalmente integrado do Azure AD](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para pedidos de novas funcionalidades de ficheiros.
