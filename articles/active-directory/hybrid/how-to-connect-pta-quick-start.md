---
title: Autenticação de passagem do Azure AD – início rápido | Microsoft Docs
description: Este artigo descreve como começar a usar a autenticação de passagem do Azure Active Directory (AD do Azure).
services: active-directory
keywords: Azure AD Connect autenticação de passagem, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 1fe38b434c4e54b375b22d76c573d3bbe88b0e16
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889945"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory autenticação de passagem: início rápido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implantar a autenticação de passagem do Azure AD

A autenticação de passagem do Azure Active Directory (AD do Azure) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. A autenticação de passagem assina os usuários no Validando suas senhas diretamente no local Active Directory.

>[!IMPORTANT]
>Se você estiver migrando do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem, é altamente recomendável que você siga nosso guia de implantação detalhado publicado [aqui](https://aka.ms/adfstoPTADPDownload).

Siga estas instruções para implantar a autenticação de passagem em seu locatário:

## <a name="step-1-check-the-prerequisites"></a>Etapa 1: verificar os pré-requisitos

Verifique se os pré-requisitos a seguir estão em vigor.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). A conclusão desta etapa é essencial para garantir que você não fique bloqueado do seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor que executa o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Se ainda não estiver habilitado, [habilite o TLS 1,2 no servidor](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adicione o servidor à mesma floresta Active Directory que os usuários cujas senhas você precisa validar.
2. Instale a [versão mais recente do Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na etapa anterior. Se você já tiver Azure AD Connect em execução, verifique se a versão é 1.1.750.0 ou posterior.

    >[!NOTE]
    >Azure AD Connect versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado à sincronização de hash de senha. Se você _não_ pretende usar a sincronização de hash de senha em conjunto com a autenticação de passagem, leia as [notas de versão do Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identifique um ou mais servidores adicionais (executando o Windows Server 2012 R2 ou posterior, com TLS 1,2 habilitado) em que você pode executar agentes de autenticação autônomos. Esses servidores adicionais são necessários para garantir a alta disponibilidade das solicitações para entrar. Adicione os servidores à mesma floresta Active Directory que os usuários cujas senhas você precisa validar.

    >[!IMPORTANT]
    >Em ambientes de produção, recomendamos que você tenha um mínimo de 3 agentes de autenticação em execução no seu locatário. Há um limite do sistema de 40 agentes de autenticação por locatário. E, como prática recomendada, trate todos os servidores que executam agentes de autenticação como sistemas de camada 0 (consulte a [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Se houver um firewall entre seus servidores e o Azure AD, configure os seguintes itens:
   - Verifique se os agentes de autenticação podem fazer solicitações de *saída* para o Azure ad nas seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Baixa as listas de certificados revogados (CRLs) ao validar o certificado SSL |
     | **443** | Manipula toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes de autenticação relatam seu status a cada dez minutos pela porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do AD do Azure. A porta 8080 _não_ é usada para entradas de usuário. |
     
     Se o firewall impõe regras de acordo com os usuários de origem, abra essas portas para o tráfego de serviços do Windows que são executados como um serviço de rede.
   - Se o firewall ou o proxy permitir a lista de permissões de DNS, as conexões de lista de permissões para **\*. msappproxy.net** e **\*. ServiceBus.Windows.net**. Caso contrário, permita o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Os agentes de autenticação precisam de acesso a **login.Windows.net** e **login.microsoftonline.com** para o registro inicial. Abra seu firewall para essas URLs também.
   - Para a validação de certificado, desbloqueie as seguintes URLs: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**e **www\.Microsoft.com:80**. Como essas URLs são usadas para validação de certificado com outros produtos da Microsoft, você pode já ter essas URLs desbloqueadas.

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

Habilite a autenticação de passagem por meio de [Azure ad Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Você pode habilitar a autenticação de passagem na Azure AD Connect servidor primário ou de preparo. É altamente recomendável habilitá-lo do servidor primário. Se estiver configurando um servidor de preparo Azure AD Connect no futuro, você **deverá** continuar a escolher a autenticação de passagem como a opção de entrada; escolher outra opção **desabilitará** a autenticação de passagem no locatário e substituirá a configuração no servidor primário.

Se você estiver instalando Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](how-to-connect-install-custom.md). Na página de **entrada do usuário** , escolha autenticação de **passagem** como o método de **logon**. Após a conclusão bem-sucedida, um agente de autenticação de passagem é instalado no mesmo servidor que Azure AD Connect. Além disso, o recurso de autenticação de passagem é habilitado em seu locatário.

![Azure AD Connect: entrada do usuário](./media/how-to-connect-pta-quick-start/sso3.png)

Se você já tiver instalado o Azure AD Connect usando a [instalação expressa](how-to-connect-install-express.md) ou o caminho de [instalação personalizada](how-to-connect-install-custom.md) , selecione a tarefa **alterar entrada do usuário** em Azure ad Connect e, em seguida, selecione **Avançar**. Em seguida, selecione **autenticação de passagem** como o método de entrada. Após a conclusão bem-sucedida, um agente de autenticação de passagem é instalado no mesmo servidor que Azure AD Connect e o recurso é habilitado em seu locatário.

![Azure AD Connect: alterar a entrada do usuário](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>A autenticação de passagem é um recurso de nível de locatário. Ligá-lo afeta a entrada para usuários em _todos_ os domínios gerenciados em seu locatário. Se você estiver alternando de Serviços de Federação do Active Directory (AD FS) (AD FS) para autenticação de passagem, deverá aguardar pelo menos 12 horas antes de desligar sua infraestrutura de AD FS. Esse tempo de espera é para garantir que os usuários possam continuar entrando no Exchange ActiveSync durante a transição. Para obter mais ajuda sobre como migrar do AD FS para a autenticação de passagem, confira nosso plano de implantação detalhado publicado [aqui](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Etapa 3: testar o recurso

Siga estas instruções para verificar se você habilitou a autenticação de passagem corretamente:

1. Entre no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure ad Connect**.
4. Verifique se o recurso de **autenticação de passagem** aparece como **habilitado**.
5. Selecione **autenticação de passagem**. O painel **autenticação de passagem** lista os servidores nos quais os agentes de autenticação estão instalados.

![Centro de administração do Azure Active Directory: Azure AD Connect painel](./media/how-to-connect-pta-quick-start/pta7.png)

![Centro de administração do Azure Active Directory: painel de autenticação de passagem](./media/how-to-connect-pta-quick-start/pta8.png)

Nesse estágio, os usuários de todos os domínios gerenciados em seu locatário podem entrar usando a autenticação de passagem. No entanto, os usuários de domínios federados continuam a entrar usando AD FS ou outro provedor de Federação que você configurou anteriormente. Se você converter um domínio de federado para gerenciado, todos os usuários desse domínio começarão a entrar automaticamente usando a autenticação de passagem. O recurso de autenticação de passagem não afeta os usuários somente na nuvem.

## <a name="step-4-ensure-high-availability"></a>Etapa 4: garantir a alta disponibilidade

Se você planeja implantar a autenticação de passagem em um ambiente de produção, instale agentes de autenticação autônomos adicionais. Instale esses agentes de autenticação no (s) servidor (es) que _não_ seja aquele em execução Azure ad Connect. Essa configuração fornece alta disponibilidade para solicitações de entrada do usuário.

>[!IMPORTANT]
>Em ambientes de produção, recomendamos que você tenha um mínimo de 3 agentes de autenticação em execução no seu locatário. Há um limite do sistema de 40 agentes de autenticação por locatário. E, como prática recomendada, trate todos os servidores que executam agentes de autenticação como sistemas de camada 0 (consulte a [referência](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

A instalação de vários agentes de autenticação de passagem garante a alta disponibilidade, mas não o balanceamento de carga determinístico entre os agentes de autenticação. Para determinar quantos agentes de autenticação você precisa para seu locatário, considere o pico e a carga média de solicitações de entrada que você espera ver em seu locatário. Como um benchmark, um único agente de autenticação pode lidar com 300 a 400 autenticações por segundo em uma CPU padrão de 4 núcleos, servidor de 16 GB de RAM.

Para estimar o tráfego de rede, use as seguintes diretrizes de dimensionamento:
- Cada solicitação tem um tamanho de carga de (0,5 K + 1K * num_of_agents) bytes; ou seja, dados do Azure AD para o agente de autenticação. Aqui, "num_of_agents" indica o número de agentes de autenticação registrados em seu locatário.
- Cada resposta tem um tamanho de payload de 1K bytes; ou seja, dados do agente de autenticação para o Azure AD.

Para a maioria dos clientes, três agentes de autenticação no total são suficientes para alta disponibilidade e capacidade. Você deve instalar agentes de autenticação próximos aos controladores de domínio para melhorar a latência de entrada.

Para começar, siga estas instruções para baixar o software do agente de autenticação:

1. Para baixar a versão mais recente do agente de autenticação (versão 1.5.193.0 ou posterior), entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global do seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure ad Connect**, selecione **autenticação de passagem**e, em seguida, selecione **baixar agente**.
4. Selecione o botão **aceitar termos & baixar** .

![Centro de administração do Azure Active Directory: botão baixar agente de autenticação](./media/how-to-connect-pta-quick-start/pta9.png)

![Centro de administração do Azure Active Directory: painel de download do agente](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Você também pode [baixar diretamente o software do agente de autenticação](https://aka.ms/getauthagent). Examine e aceite os [termos de serviço](https://aka.ms/authagenteula) do agente de autenticação _antes_ de instalá-lo.

Há duas maneiras de implantar um agente de autenticação autônomo:

Primeiro, você pode fazer isso interativamente executando apenas o executável do agente de autenticação baixado e fornecendo as credenciais de administrador global do seu locatário quando solicitado.

Em segundo lugar, você pode criar e executar um script de implantação autônoma. Isso é útil quando você deseja implantar vários agentes de autenticação ao mesmo tempo ou instalar agentes de autenticação em servidores Windows que não têm a interface do usuário habilitada ou que você não pode acessar com o Área de Trabalho Remota. Aqui estão as instruções sobre como usar essa abordagem:

1. Execute o seguinte comando para instalar um agente de autenticação: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Você pode registrar o agente de autenticação com nosso serviço usando o Windows PowerShell. Crie um objeto de credenciais do PowerShell `$cred` que contenha um nome de usuário de administrador global e uma senha para seu locatário. Execute o comando a seguir, substituindo *\<nome de usuário\>* e *\<senha\>* :

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Acesse **c:\Arquivos de programas\microsoft Azure ad Connect agente de autenticação** e execute o seguinte script usando o `$cred` objeto que você criou:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Se um agente de autenticação estiver instalado em uma máquina virtual, você não poderá clonar a máquina virtual para configurar outro agente de autenticação. Não há **suporte para**este método.

## <a name="step-5-configure-smart-lockout-capability"></a>Etapa 5: configurar o recurso de bloqueio inteligente

O bloqueio inteligente ajuda a bloquear atores ruins que estão tentando adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. Ao definir as configurações de bloqueio inteligente no Azure AD e/ou configurações de bloqueio apropriadas no Active Directory local, os ataques podem ser filtrados antes de alcançarem Active Directory. Leia [Este artigo](../authentication/howto-password-smart-lockout.md) para saber mais sobre como definir as configurações de bloqueio inteligente em seu locatário para proteger suas contas de usuário.

## <a name="next-steps"></a>Passos seguintes
- [Migrar de AD FS para autenticação de passagem](https://aka.ms/adfstoptadp) – um guia detalhado para migrar do AD FS (ou outras tecnologias de Federação) para a autenticação de passagem.
- [Bloqueio inteligente](../authentication/howto-password-smart-lockout.md): saiba como configurar o recurso de bloqueio inteligente em seu locatário para proteger as contas de usuário.
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte com a autenticação de passagem e quais não são.
- [Aprofundamento técnico](how-to-connect-pta-how-it-works.md): entenda como funciona o recurso de autenticação de passagem.
- [Perguntas](how-to-connect-pta-faq.md)frequentes: Encontre respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de autenticação de passagem.
- [Aprofundamento da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas sobre o recurso de autenticação de passagem.
- [SSO contínuo do Azure ad](how-to-connect-sso.md): Saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Use o fórum de Azure Active Directory para arquivar novas solicitações de recurso.
