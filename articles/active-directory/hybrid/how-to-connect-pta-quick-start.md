---
title: Autenticação pass-through AZure AD - Quickstart / Microsoft Docs
description: Este artigo descreve como começar com a autenticação pass-through do Azure Ative Directory (Azure AD).
services: active-directory
keywords: Autenticação pass-through Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bdfb1ca21860f1dc338f85a82caf643f9f7be6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973206"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Autenticação pass-through do Azure Ative Directory: Quickstart

## <a name="deploy-azure-ad-pass-through-authentication"></a>Implementar autenticação pass-through Ad Azure

A Azure Ative Directory (Azure AD) A Autenticação pass-through permite que os seus utilizadores entrem no local e aplicações baseadas na nuvem utilizando as mesmas palavras-passe. A autenticação pass-through assina os utilizadores através da validação das suas palavras-passe diretamente contra o Ative Directory no local.

>[!IMPORTANT]
>Se estiver a migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through, recomendamos vivamente que siga o nosso guia de implementação detalhado publicado [aqui.](https://aka.ms/adfstoPTADPDownload)

>[!NOTE]
>Se implementar a Authentication Pass Through Com a nuvem do Governo Azure, consulte [considerações de identidade híbrida para o Governo Azure](./reference-connect-government-cloud.md).

Siga estas instruções para implementar a autenticação pass-through no seu inquilino:

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verifique os pré-requisitos

Certifique-se de que existem os seguintes pré-requisitos.

>[!IMPORTANT]
>Do ponto de vista da segurança, os administradores devem tratar o servidor que executa o agente PTA como se fosse um controlador de domínio.  Os servidores de agentes PTA devem ser endurecidos ao longo das mesmas linhas que os descritos na [garantia de controladores de domínio contra ataque](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Ative Directory

1. Crie uma conta de administrador global apenas em nuvem no seu inquilino AZure AD. Desta forma, pode gerir a configuração do seu inquilino caso os seus serviços no local falhem ou fiquem indisponíveis. Saiba mais [sobre a adição de uma conta de administrador global apenas na nuvem.](../fundamentals/add-users-azure-active-directory.md) Completar este passo é fundamental para garantir que não fique trancado fora do seu inquilino.
2. Adicione um ou mais [nomes de domínio personalizados](../fundamentals/add-custom-domain.md) ao seu inquilino AZure AD. Os seus utilizadores podem iniciar sôms com um destes nomes de domínio.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor que executa o Windows Server 2012 R2 ou mais tarde para executar O AZure AD Connect. Se ainda não estiver ativado, [ative o TLS 1.2 no servidor](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Adicione o servidor à mesma floresta ative de Diretório que os utilizadores cujas palavras-passe precisa de validar.
2. Instale a [versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado no passo anterior. Se já tiver o Azure AD Connect a funcionar, certifique-se de que a versão é 1.1.750.0 ou mais tarde.

    >[!NOTE]
    >As versões AZURE AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização da hash da palavra-passe. Se _não_ pretender utilizar a sincronização de hash de palavra-passe em conjunto com a Autenticação Pass-through, leia as notas de lançamento do [Azure AD Connect](./reference-connect-version-history.md).

3. Identifique um ou mais servidores adicionais (executando o Windows Server 2012 R2 ou mais tarde, com OTS 1.2 ativado) onde pode executar Agentes de Autenticação Autónomos. Estes servidores adicionais são necessários para garantir a elevada disponibilidade de pedidos para iniciar sinserção. Adicione os servidores à mesma floresta ative de Diretório que os utilizadores cujas palavras-passe precisa de validar.

    >[!IMPORTANT]
    >Em ambientes de produção, recomendamos que tenha um mínimo de 3 Agentes de Autenticação em execução no seu inquilino. Existe um limite de sistema de 40 Agentes de Autenticação por inquilino. E como melhores práticas, trate todos os servidores que executam agentes de autenticação como sistemas tier 0 (ver [referência).](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

4. Se houver uma firewall entre os seus servidores e a Azure AD, configure os seguintes itens:
   - Certifique-se de que os Agentes de Autenticação podem fazer pedidos *de saída* para a Azure AD nas seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Descarrega as listas de revogação de certificados (CRLs) ao mesmo tempo que valida o certificado TLS/SSL |
     | **443** | Lida com toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os Agentes de Autenticação reportam o seu estado a cada dez minutos sobre a porta 8080, se a porta 443 não estiver disponível. Este estado é apresentado no portal AD Azure. A porta 8080 _não_ é utilizada para os pedidos de s presenciar. |
     
     Se o seu firewall aplicar as regras de acordo com os utilizadores originários, abra estas portas para tráfego a partir de serviços Windows que funcionam como um serviço de rede.
   - Se a sua firewall ou proxy permitir a whitelisting do DNS, adicione ligações a **\* .msappproxy.net** e **\* .servicebus.windows.net**. Caso contrário, permita o acesso aos intervalos IP do [datacenter Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Os seus Agentes de Autenticação precisam de acesso a **login.windows.net** e **login.microsoftonline.com** para inscrição inicial. Abra a firewall para os URLs também.
    - Para validação de certificados, desbloqueie os seguintes URLs: **crl3.digicert.com:80,** **crl4.digicert.com:80,** **ocsp.digicert.com:80,** **www \. d-trust.net:80**, **root-c3-ca2-2009.ocsp.d-trust.net:80,** **crl.microsoft.com:80,** **oneocsp.microsoft.com:80** e **ocsp.msocsp.com:80**. Uma vez que estes URLs são utilizados para validação de certificados com outros produtos da Microsoft, pode já ter estes URLs desbloqueados.

### <a name="azure-government-cloud-prerequisite"></a>Azure Government nuvem pré-requisito
Antes de ativar a autenticação pass-through através do Azure AD Connect com o Passo 2, descarregue a mais recente versão do agente PTA a partir do portal Azure.  Tem de se certificar de que o seu agente é versões **1.5.1742.0.** ou posterior.  Para verificar o seu agente consulte [agentes de autenticação de upgrade](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Depois de descarregar a versão mais recente do agente, proceda com as instruções abaixo para configurar Pass-Through Autenticação através do Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar a autenticação pass-through [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Pode ativar a autenticação pass-through no servidor primário ou de paragem do Azure AD Connect. É altamente recomendável que o ative a partir do servidor primário. Se estiver a configurar um servidor de paragem Azure AD Connect no futuro, **deve** continuar a escolher a Autenticação Pass-through como opção de inscrição; a escolha de outra opção irá **desativar** a autenticação pass-through no arrendatário e anular a definição no servidor primário.

Se estiver a instalar o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](how-to-connect-install-custom.md). Na página de início de **Pass-through Authentication** **sôm.** **Sign On method** Após a conclusão com sucesso, um Agente de Autenticação Pass-through é instalado no mesmo servidor que o Azure AD Connect. Além disso, a função de Autenticação Pass-through está ativada no seu inquilino.

![Azure AD Connect: Iniciar sação do utilizador](./media/how-to-connect-pta-quick-start/sso3.png)

Se já instalou o Azure AD Connect utilizando a [instalação expressa](how-to-connect-install-express.md) ou o caminho [de instalação personalizado,](how-to-connect-install-custom.md) selecione a tarefa **de inscrição** do utilizador Change no Azure AD Connect e, em seguida, selecione **Seguinte**. Em seguida, selecione **a autenticação pass-through** como o método de inscrição. Após a conclusão com sucesso, um Agente de Autenticação Pass-through é instalado no mesmo servidor que o Azure AD Connect e a funcionalidade está ativada no seu inquilino.

![Azure AD Connect: Alterar o súmido do utilizador](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>A autenticação pass-through é uma característica ao nível do inquilino. Ligá-lo afeta o sinal de inscrição para utilizadores em _todos os_ domínios geridos no seu inquilino. Se estiver a mudar de Serviços da Federação de Diretórios Ativos (AD FS) para Autenticação Pass-through, deverá esperar pelo menos 12 horas antes de desligar a sua infraestrutura AD FS. Este tempo de espera é para garantir que os utilizadores podem continuar a iniciar sessão no Exchange ActiveSync durante a transição. Para obter mais ajuda na migração da AD FS para a Authentication Pass-through, consulte o nosso plano de implementação detalhado publicado [aqui.](https://aka.ms/adfstoptadpdownload)

## <a name="step-3-test-the-feature"></a>Passo 3: Testar a funcionalidade

Siga estas instruções para verificar se ativou corretamente a autenticação pass-through:

1. Inscreva-se no [Azure Ative Directory admin center](https://aad.portal.azure.com) com as credenciais de administrador global para o seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure Ad Connect**.
4. Verifique se a **função de autenticação Pass-through** aparece como **Ativada**.
5. Selecione **a autenticação pass-through**. O **painel de autenticação Pass-through** lista os servidores onde estão instalados os seus Agentes de Autenticação.

![Centro de administração Azure Ative Directory: Painel de ligação Azure AD](./media/how-to-connect-pta-quick-start/pta7.png)

![Centro de administração Azure Ative Directory: Painel de autenticação pass-through](./media/how-to-connect-pta-quick-start/pta8.png)

Nesta fase, os utilizadores de todos os domínios geridos no seu inquilino podem iniciar sação utilizando a Autenticação Pass-through. No entanto, os utilizadores de domínios federados continuam a fazer sedudas utilizando FS AD ou outro provedor de federação que já configuraram anteriormente. Se converter um domínio de federado para gerido, todos os utilizadores desse domínio começam automaticamente a iniciar sessão utilizando a Autenticação Pass-through. A funcionalidade de Autenticação Pass-through não afeta apenas os utilizadores na nuvem.

## <a name="step-4-ensure-high-availability"></a>Passo 4: Garantir alta disponibilidade

Se pretender implantar a autenticação pass-through num ambiente de produção, deverá instalar agentes de autenticação autónomos adicionais. Instale estes Agentes de Autenticação no(s) servidor(s) _que não_ o que está a funcionar Azure AD Connect. Esta configuração proporciona-lhe uma elevada disponibilidade para pedidos de inscrição do utilizador.

>[!IMPORTANT]
>Em ambientes de produção, recomendamos que tenha um mínimo de 3 Agentes de Autenticação em execução no seu inquilino. Existe um limite de sistema de 40 Agentes de Autenticação por inquilino. E como melhores práticas, trate todos os servidores que executam agentes de autenticação como sistemas tier 0 (ver [referência).](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

A instalação de vários Agentes de Autenticação Pass-through garante uma elevada disponibilidade, mas não um equilíbrio de carga determinístico entre os Agentes de Autenticação. Para determinar quantos Agentes de Autenticação precisa para o seu inquilino, considere o pico e a carga média de pedidos de inscrição que espera ver no seu inquilino. Como referência, um único Agente de Autenticação pode lidar com 300 a 400 autenticações por segundo num CPU padrão de 4 núcleos, servidor RAM de 16 GB.

Para estimar o tráfego da rede, utilize as seguintes orientações de dimensionamento:
- Cada pedido tem um tamanho de carga útil de bytes (0,5K + 1K * num_of_agents), ou seja, dados da Azure AD ao Agente de Autenticação. Aqui, "num_of_agents" indica o número de Agentes de Autenticação registados no seu inquilino.
- Cada resposta tem um tamanho de carga útil de 1K bytes, ou seja, dados do Agente de Autenticação para Azure AD.

Para a maioria dos clientes, três Agentes de Autenticação no total são suficientes para alta disponibilidade e capacidade. Deve instalar agentes de autenticação perto dos seus controladores de domínio para melhorar a latência de inscrição.

Para começar, siga estas instruções para descarregar o software Do Agente de Autenticação:

1. Para fazer o download da versão mais recente do Agente de Autenticação (versão 1.5.193.0 ou posterior), inscreva-se no [centro de administração Azure Ative Com](https://aad.portal.azure.com) as credenciais de administrador global do seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**, selecione **a autenticação pass-through** e, em seguida, selecione **Download Agent**.
4. Selecione os termos aceitar & botão **de descarregamento.**

![Centro de administração Azure Ative: Baixar botão de agente de autenticação](./media/how-to-connect-pta-quick-start/pta9.png)

![Centro de administração Azure Ative Directory: Download Agent pane](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Também pode descarregar diretamente [o software Agente de Autenticação.](https://aka.ms/getauthagent) Reveja e aceite os [Termos de Serviço](https://aka.ms/authagenteula) do Agente de Autenticação _antes_ de o instalar.

Existem duas formas de implantar um agente de autenticação autónomo:

Em primeiro lugar, pode fazê-lo interativamente executando o Agente de Autenticação descarregado executável e fornecendo as credenciais globais de administrador do seu inquilino quando solicitado.

Em segundo lugar, pode criar e executar um script de implementação sem supervisão. Isto é útil quando pretender implementar vários Agentes de Autenticação de uma só vez, ou instalar Agentes de Autenticação em servidores do Windows que não tenham a interface do utilizador ativada, ou que não possa aceder com o Ambiente de Trabalho Remoto. Aqui estão as instruções sobre como usar esta abordagem:

1. Executar o seguinte comando para instalar um Agente de Autenticação: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q` .
2. Pode registar o Agente de Autenticação com o nosso serviço utilizando o Windows PowerShell. Crie um objeto powerShell Credentials `$cred` que contenha um nome de utilizador e senha de utilizador de administrador global para o seu inquilino. Executar o seguinte comando, substituição *\<username\>* *\<password\>* e:

  ```powershell
  $User = "<username>"
  $PlainPassword = '<password>'
  $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
  ```
3. Vá a **C:\Program Files\Microsoft Azure AD Connect Authentication Agent** e executar o seguinte script usando o `$cred` objeto que criou:

  ```powershell
  RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication
  ```

>[!IMPORTANT]
>Se um Agente de Autenticação for instalado numa Máquina Virtual, não é possível clonar a Máquina Virtual para configurar outro Agente de Autenticação. Este método não é **suportado.**

## <a name="step-5-configure-smart-lockout-capability"></a>Passo 5: Configurar a capacidade de bloqueio inteligente

Smart Lockout ajuda a bloquear maus atores que estão a tentar adivinhar as palavras-passe dos seus utilizadores ou a usar métodos de força bruta para entrar. Ao configurar as definições de Bloqueio Inteligente em AD Azure e/ou configurações de bloqueio apropriadas no Ative Directory, os ataques podem ser filtrados antes de chegarem ao Ative Directory. Leia [este artigo](../authentication/howto-password-smart-lockout.md) para saber mais sobre como configurar as definições de Smart Lockout no seu inquilino para proteger as suas contas de utilizador.

## <a name="next-steps"></a>Passos seguintes
- [Migrar de AD FS para Autenticação Pass-through](https://aka.ms/adfstoptadp) - Um guia detalhado para migrar de FS AD (ou outras tecnologias da federação) para a Autenticação Pass-through.
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Saiba como configurar a capacidade de Bloqueio Inteligente no seu inquilino para proteger as contas do utilizador.
- [Limitações atuais](how-to-connect-pta-current-limitations.md): Saiba quais os cenários suportados com a Autenticação Pass-through e quais não são.
- [Mergulho técnico profundo](how-to-connect-pta-how-it-works.md): Compreenda como funciona a função de autenticação pass-through.
- [Perguntas frequentes](how-to-connect-pta-faq.md): Encontre respostas a perguntas frequentes.
- [Resolução de problemas](tshoot-connect-pass-through-authentication.md): Aprenda a resolver problemas comuns com a função De autenticação Pass-through.
- [Mergulho profundo de](how-to-connect-pta-security-deep-dive.md)segurança : Obtenha informações técnicas sobre a função de autenticação pass-through.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Saiba mais sobre esta característica complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Azure Ative Directory Forum para apresentar novos pedidos de funcionalidades.
