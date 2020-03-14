---
title: Autenticação de passagem da AD Azure - Início rápido Microsoft Docs
description: Este artigo descreve como começar com a Autenticação Pass-through do Azure Ative Directory (Azure AD).
services: active-directory
keywords: Autenticação de passagem de ligação Azure AD, instala o Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: be6a6e9231b13c47d1421543464c720f6283b5f9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261245"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticação pass-through do Diretório Ativo Azure: Início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implementar autenticação pass-through azure AD

A Autenticação pass-through do Azure Ative Directory (Azure AD) permite que os seus utilizadores acedam tanto nas aplicações no local como nas aplicações baseadas na nuvem utilizando as mesmas palavras-passe. A Autenticação Pass-through assina os utilizadores validando as suas palavras-passe diretamente contra o Diretório Ativo no local.

>[!IMPORTANT]
>Se está a migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui](https://aka.ms/adfstoPTADPDownload).

Siga estas instruções para implementar a Autenticação Pass-through no seu inquilino:

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que estão em vigor os seguintes pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Diretório Ativo Azure

1. Crie uma conta de administrador global apenas em nuvem no seu inquilino Azure AD. Desta forma, pode gerir a configuração do seu inquilino caso os seus serviços no local falhem ou fiquem indisponíveis. Saiba adicionar uma conta de [administrador global só para a nuvem.](../active-directory-users-create-azure-portal.md) Completar este passo é fundamental para garantir que não seja trancado fora do seu inquilino.
2. Adicione um ou mais nomes de [domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu inquilino Azure AD. Os seus utilizadores podem iniciar sessão com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor que execute o Windows Server 2012 R2 ou mais tarde para executar o Azure AD Connect. Se ainda não estiver ativado, [ative o TLS 1.2 no servidor](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adicione o servidor à mesma floresta de Diretório Ativo que os utilizadores cujas palavras-passe precisa de validar.
2. Instale a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na etapa anterior. Se já tem o Azure AD Connect a funcionar, certifique-se de que a versão é de 1.1.750.0 ou mais tarde.

    >[!NOTE]
    >As versões Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash password. Se _não_ pretender utilizar a sincronização de hash de palavra-passe em conjunto com a Autenticação Pass-through, leia as notas de lançamento do [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identifique um ou mais servidores adicionais (executando o Windows Server 2012 R2 ou posteriormente, com TLS 1.2 ativado) onde pode executar agentes de autenticação autónomos. Estes servidores adicionais são necessários para garantir a elevada disponibilidade de pedidos para iniciar sessão. Adicione os servidores à mesma floresta de Diretório Ativo que os utilizadores cujas palavras-passe precisa de validar.

    >[!IMPORTANT]
    >Em ambientes de produção, recomendamos que tenha um mínimo de 3 Agentes de Autenticação em funcionamento sobre o seu inquilino. Existe um limite de sistema de 40 Agentes de Autenticação por inquilino. E como melhores práticas, trate todos os servidores executando agentes de autenticação como sistemas de nível 0 (ver [referência).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

4. Se houver uma firewall entre os seus servidores e a AD Azure, configure os seguintes itens:
   - Certifique-se de que os agentes de autenticação podem fazer pedidos de *saída* para a AD Azure sobre as seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Descarrega as listas de revogação do certificado (CRLs) enquanto valida o certificado SSL |
     | **443** | Lida com toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes de autenticação reportam o seu estado a cada dez minutos sobre a porta 8080, se a porta 443 não estiver disponível. Este estado é apresentado no portal Azure AD. A porta 8080 _não_ é utilizada para os sessão de inscrição do utilizador. |
     
     Se a sua firewall aplicar regras de acordo com os utilizadores originários, abra estas portas para tráfego a partir de serviços Windows que funcionam como um serviço de rede.
   - Se a sua firewall ou proxy permitir a listagem de dNS, as ligações whitelist para **\*.msappproxy.net** e **\*.servicebus.windows.net**. Caso contrário, permita o acesso às gamas IP do Centro de [Dados Azure,](https://www.microsoft.com/download/details.aspx?id=41653)que são atualizadas semanalmente.
   - Os seus Agentes de Autenticação precisam de acesso a **login.windows.net** e **login.microsoftonline.com** para registo inicial. Abra a sua firewall para os URLs também.
   - Para validação de certificados, desbloqueie os seguintes URLs: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**e **www\.microsoft.com:80.** Uma vez que estes URLs são utilizados para validação de certificados com outros produtos da Microsoft, pode já ter estes URLs desbloqueados.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar a autenticação pass-through através do [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Pode ativar a autenticação pass-through no servidor primário ou de encenação Do Azure AD Connect. É altamente recomendável que o ative a partir do servidor principal. Se estiver a configurar um servidor de encenação Azure AD Connect no futuro, **deve** continuar a escolher a Autenticação Pass-through como opção de entrada; escolher outra opção **irá desativar** a Autenticação Pass-through no inquilino e anular a definição no servidor primário.

Se estiver a instalar o Azure AD Connect pela primeira vez, escolha o caminho de [instalação personalizado](how-to-connect-install-custom.md). Na página **de início de sessão** do Utilizador, escolha a **Autenticação Pass-through** como **método De Sinal on**. Após a conclusão com sucesso, um Agente de Autenticação Pass-through é instalado no mesmo servidor que o Azure AD Connect. Além disso, a funcionalidade de Autenticação Pass-through está ativada no seu inquilino.

![Ligação Azure AD: entrada no utilizador](./media/how-to-connect-pta-quick-start/sso3.png)

Se já instalou o Azure AD Connect utilizando a [instalação expressa](how-to-connect-install-express.md) ou a via de [instalação personalizada,](how-to-connect-install-custom.md) selecione a tarefa de **entrada do utilizador Change** no Azure AD Connect e, em seguida, selecione **Next**. Em seguida, selecione **a autenticação pass-through** como método de inscrição. Após a conclusão com sucesso, um Agente de Autenticação Pass-through é instalado no mesmo servidor que o Azure AD Connect e a funcionalidade está ativada no seu inquilino.

![Azure AD Connect: Alterar o início de sessão do utilizador](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação Pass-through é uma característica ao nível do inquilino. Ligá-lo afeta o sessão para utilizadores em _todos os_ domínios geridos no seu inquilino. Se estiver a mudar de Ative Directory Federation Services (AD FS) para AD-through Authentication, deverá esperar pelo menos 12 horas antes de desligar a sua infraestrutura AD FS. Este tempo de espera é para garantir que os utilizadores podem continuar a iniciar sessão no Exchange ActiveSync durante a transição. Para obter mais ajuda na migração da AD FS para a Autenticação Pass-through, consulte o nosso plano de implementação detalhado publicado [aqui](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Passo 3: Testar a funcionalidade

Siga estas instruções para verificar se ativou corretamente a autenticação pass-through:

1. Inscreva-se no Centro de [Administração do Azure Ative Directory](https://aad.portal.azure.com) com as credenciais de administrador global para o seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**.
4. Verifique se a **função de autenticação Pass-through** aparece como **Ativada**.
5. Selecione **autenticação pass-through**. O painel **de autenticação Pass-through** lista os servidores onde os seus Agentes de Autenticação estão instalados.

![Centro de administração de Diretório Ativo Azure: Painel azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Centro de administração de Diretório Ativo Azure: Painel de Autenticação Pass-through](./media/how-to-connect-pta-quick-start/pta8.png)

Nesta fase, os utilizadores de todos os domínios geridos do seu inquilino podem iniciar sessão utilizando a Autenticação Pass-through. No entanto, os utilizadores de domínios federados continuam a inscrever-se utilizando AD FS ou outro provedor da federação que configurapreviapreviamente. Se converter um domínio de federado para gerido, todos os utilizadores desse domínio começam automaticamente a iniciar a sessão utilizando a Autenticação Pass-through. A funcionalidade de Autenticação Pass-through não afeta apenas os utilizadores em nuvem.

## <a name="step-4-ensure-high-availability"></a>Passo 4: Garantir a elevada disponibilidade

Se planeia implementar a Autenticação Pass-through num ambiente de produção, deverá instalar agentes adicionais de autenticação autónoma. Instale estes Agentes de Autenticação(s) em servidores(s) _diferentes_ daquele que executa o Azure AD Connect. Esta configuração proporciona-lhe uma elevada disponibilidade para pedidos de sessão de sessão do utilizador.

>[!IMPORTANT]
>Em ambientes de produção, recomendamos que tenha um mínimo de 3 Agentes de Autenticação em funcionamento sobre o seu inquilino. Existe um limite de sistema de 40 Agentes de Autenticação por inquilino. E como melhores práticas, trate todos os servidores executando agentes de autenticação como sistemas de nível 0 (ver [referência).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

A instalação de vários Agentes de Autenticação Pass-through garante uma elevada disponibilidade, mas não o equilíbrio de carga determinista entre os Agentes de Autenticação. Para determinar quantos Agentes de Autenticação precisa para o seu inquilino, considere o pico e a carga média de pedidos de inscrição que espera ver no seu inquilino. Como referência, um único Agente de Autenticação pode lidar com 300 a 400 autenticações por segundo num CPU padrão de 4 núcleos, servidor RAM de 16 GB.

Para estimar o tráfego da rede, utilize as seguintes orientações de dimensionamento:
- Cada pedido tem um tamanho de carga útil de bytes (0,5K + 1K * num_of_agents) ; ou é, dados da AD Azure para o Agente de Autenticação. Aqui, "num_of_agents" indica o número de Agentes de Autenticação registados no seu inquilino.
- Cada resposta tem um tamanho de carga útil de bytes de 1K; ou é, dados do Agente de Autenticação para a AD Azure.

Para a maioria dos clientes, três Agentes de Autenticação no total são suficientes para uma elevada disponibilidade e capacidade. Deve instalar agentes de autenticação perto dos seus controladores de domínio para melhorar a latência de acesso.

Para começar, siga estas instruções para descarregar o software do Agente de Autenticação:

1. Para descarregar a versão mais recente do Agente de Autenticação (versão 1.5.193.0 ou posterior), inscreva-se no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com) com as credenciais de administrador global do seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure AD Connect,** selecione a **autenticação pass-through**e, em seguida, selecione **Download Agent**.
4. Selecione os **termos aceitar e descarregar** botão.

![Centro de administração de diretório ativo Azure: Botão de agente de autenticação de descarregamento](./media/how-to-connect-pta-quick-start/pta9.png)

![Centro de administração de Diretório Ativo Azure: Painel de agente de descarregamento](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Também pode descarregar diretamente [o software do Agente de Autenticação.](https://aka.ms/getauthagent) Reveja e aceite os [Termos de Serviço](https://aka.ms/authagenteula) do Agente de Autenticação _antes_ de o instalar.

Existem duas formas de implantar um Agente de Autenticação Autónomo:

Em primeiro lugar, pode fazê-lo interativamente executando o Agente de Autenticação descarregado executável e fornecendo as credenciais de administrador global do seu inquilino quando solicitado.

Segundo, pode criar e executar um roteiro de implantação sem supervisão. Isto é útil quando pretende implementar vários Agentes de Autenticação ao mesmo tempo, ou instalar Agentes de Autenticação em servidores Windows que não tenham interface de utilizador ativado ou que não possa aceder com o Remote Desktop. Aqui estão as instruções sobre como usar esta abordagem:

1. Executar o seguinte comando para instalar um Agente de Autenticação: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Pode registar o Agente de Autenticação com o nosso serviço utilizando o Windows PowerShell. Crie um objeto de credenciais PowerShell `$cred` que contenha um nome de utilizador e senha de administrador global para o seu inquilino. Executar o seguinte comando, substituindo *\<nome de utilizador\>* e\<*palavra-passe\>:*

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Vá ao **C:\Program Files\Microsoft Azure AD Connect Authentication Agent** e execute o seguinte script usando o objeto `$cred` que criou:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Se um Agente de Autenticação for instalado numa Máquina Virtual, não pode clonar a Máquina Virtual para configurar outro Agente de Autenticação. Este método não é **suportado.**

## <a name="step-5-configure-smart-lockout-capability"></a>Passo 5: Configurar a capacidade de bloqueio inteligente

Smart Lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. Ao configurar as definições de Smart Lockout em Definições de AD E//ou ou de bloqueio apropriadas no Diretório Ativo no local, os ataques podem ser filtrados antes de chegarem ao Diretório Ativo. Leia [este artigo](../authentication/howto-password-smart-lockout.md) para saber mais sobre como configurar as definições de Smart Lockout no seu inquilino para proteger as suas contas de utilizador.

## <a name="next-steps"></a>Passos seguintes
- [Migrar de AD FS para a Autenticação Pass-through](https://aka.ms/adfstoptadp) - Um guia detalhado para migrar de AD FS (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Aprenda a configurar a capacidade de bloqueio inteligente no seu inquilino para proteger as contas do utilizador.
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários suportados com a Autenticação Pass-through e quais não são.
- [Mergulho profundo técnico](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de Autenticação Pass-through.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas para perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função de Autenticação Pass-through.
- [Mergulho profundo de segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta funcionalidade complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum de Diretório Ativo Azure para apresentar novos pedidos de funcionalidades.
