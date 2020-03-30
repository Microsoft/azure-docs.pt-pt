---
title: 'Azure AD Connect: Single Sign-On sem emenda - início rápido / início rápido Microsoft Docs'
description: Este artigo descreve como começar com o Azure Ative Directory Seamless Single Sign-On
services: active-directory
keywords: o que é Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ec56d37ca2c0a199968707b3d93f4797be2beca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261206"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Ative Directory Seamless Single Sign-On: Quick start

## <a name="deploy-seamless-single-sign-on"></a>Implementar um único sign-on sem emenda

O Azure Ative Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) assina automaticamente nos utilizadores quando estão nos seus desktops corporativos ligados à sua rede corporativa. O SSO sem emenda proporciona aos seus utilizadores um fácil acesso às suas aplicações baseadas na nuvem sem precisar de componentes adicionais no local.

Para implementar O SSO SSO sem emenda, siga estes passos.

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verificar os pré-requisitos

Certifique-se de que estão em vigor os seguintes pré-requisitos:

* **Configurar o servidor Azure AD Connect**: Se utilizar a [Autenticação Pass-through](how-to-connect-pta.md) como método de entrada, não é necessária uma verificação prévia adicional. Se utilizar a sincronização de hash de [palavra-passe](how-to-connect-password-hash-synchronization.md) como método de entrada e se houver uma firewall entre o Azure AD Connect e o Azure AD, certifique-se de que:
   - Utiliza a versão 1.1.644.0 ou mais tarde do Azure AD Connect. 
   - Se a sua firewall ou proxy permitir a listagem de dNS, whitelist as ligações aos ** \*.msappproxy.net** URLs sobre a porta 443. Caso contrário, permita o acesso às gamas IP do Centro de [Dados Azure,](https://www.microsoft.com/download/details.aspx?id=41653)que são atualizadas semanalmente. Este pré-requisito só é aplicável quando ativa a funcionalidade. Não é necessário para os insinsos reais do utilizador.

    >[!NOTE]
    >As versões Azure AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização de hash password. Se _não_ pretender utilizar a sincronização de hash de palavra-passe em conjunto com a Autenticação Pass-through, leia as notas de lançamento do [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para saber mais.

* **Utilize uma topologia de ligação Azure AD connect suportada**: Certifique-se de que está a utilizar uma das topoologias suportadas pelo Azure AD Connect [descritas aqui](plan-connect-topologies.md).

    >[!NOTE]
    >O SSO sem emenda suporta várias florestas de AD, quer existam confianças de AD entre elas ou não.

* **Configurar credenciais de administrador de domínio:** Você precisa de ter credenciais de administrador de domínio para cada floresta de Diretório Ativo que:
    * Sincroniza-se com o Azure AD através do Azure AD Connect.
    * Contém utilizadores que pretende ativar para SSO sem emenda.
    
* Ativar a **autenticação moderna**: Você precisa permitir a [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) no seu inquilino para que esta funcionalidade funcione.

* **Utilize as versões mais recentes dos clientes do Office 365**: Para obter uma experiência de inscrição silenciosa com clientes do Office 365 (Outlook, Word, Excel, entre outros), os seus utilizadores precisam de utilizar versões 16.0.8730.xxxx ou superiores.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar O SSO sem emenda através da [ligação ad azure](whatis-hybrid-identity.md).

>[!NOTE]
> Também pode [ativar o SSO sem emenda utilizando o PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) se o Azure AD Connect não satisfazer os seus requisitos. Utilize esta opção se tiver mais de um domínio por floresta de Diretório Ativo, e pretende ser mais direcionado sobre o domínio para o qual pretende ativar o SSO sem emenda.

Se estiver a fazer uma nova instalação do Azure AD Connect, escolha o caminho de [instalação personalizado](how-to-connect-install-custom.md). Na página **de início de sessão** do Utilizador, selecione o sinal único **ativar a** opção.

>[!NOTE]
> A opção só estará disponível para seleção se o método Sign On for **Password Hash Synchronization** ou **Pass-through Authentication**.

![Ligação Azure AD: entrada no utilizador](./media/how-to-connect-sso-quick-start/sso8.png)

Se já tiver uma instalação de Azure AD Connect, selecione a página **de entrada do utilizador Change** no Azure AD Connect e, em seguida, selecione **Next**. Se estiver a utilizar as versões Azure AD Connect 1.1.880.0 ou superior, o **único sinal de ativação na** opção será selecionado por defeito. Se estiver a utilizar versões mais antigas do Azure AD Connect, selecione o **sinal único Enable na** opção.

![Azure AD Connect: Alterar o início de sessão do utilizador](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Continue através do assistente até chegar ao **sinal único enable na** página. Fornecer credenciais de administrador de domínio para cada floresta de Diretório Ativo que:

* Sincroniza-se com o Azure AD através do Azure AD Connect.
* Contém utilizadores que pretende ativar para SSO sem emenda.

Após a conclusão do assistente, o SSO SSO sem emenda está ativado no seu inquilino.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. São usados apenas para ativar a funcionalidade.

Siga estas instruções para verificar se ativou corretamente o SSO sem emenda:

1. Inscreva-se no [Centro Administrativo Azure Ative Diretório](https://aad.portal.azure.com) com as credenciais de administrador global para o seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure AD Connect**.
4. Verifique se a função de **inscrição única sem emenda** aparece como **Ativada**.

![Portal Azure: Painel azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> O SSO SSO `AZUREADSSOACC` sem emenda cria uma conta de computador nomeada no seu Diretório Ativo (AD) no local em cada floresta ad. A `AZUREADSSOACC` conta do computador precisa de ser fortemente protegida por razões de segurança. Apenas os Administradores de Domínio devem ser capazes de gerir a conta do computador. Certifique-se de que a delegação kerberos na conta informática está desativada `AZUREADSSOACC` e que nenhuma outra conta no Diretório Ativo tem permissões de delegação na conta do computador. Guarde a conta do computador numa Unidade de Organização (OU) onde estejam a salvo de supressões acidentais e onde apenas os Administradores de Domínio tenham acesso.

>[!NOTE]
> Se estiver a utilizar arquiteturas pass-the-Hash e Credential De mitigação de roubos `AZUREADSSOACC` no seu ambiente no local, faça alterações adequadas para garantir que a conta de computador não acabe no recipiente de Quarentena. 

## <a name="step-3-roll-out-the-feature"></a>Passo 3: Desenrolar a funcionalidade

Pode lançar gradualmente SSO sem emenda aos seus utilizadores utilizando as instruções fornecidas abaixo. Começa por adicionar o seguinte URL Azure AD a todas ou selecionadas definições de zona intranet dos utilizadores utilizando a Política de Grupo no Diretório Ativo:

- `https://autologon.microsoftazuread-sso.com`

Além disso, você precisa ativar uma definição de política de zona Intranet chamada **Permitir atualizações para barra** de estado através de script através de Política de Grupo. 

>[!NOTE]
> As seguintes instruções funcionam apenas para o Internet Explorer e o Google Chrome no Windows (se partilhar um conjunto de URLs de sites fidedignos com o Internet Explorer). Leia a secção seguinte para obter instruções sobre como configurar o Mozilla Firefox e o Google Chrome no macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que precisa modificar as definições da zona intranet dos utilizadores?

Por padrão, o navegador calcula automaticamente a zona correta, seja na Internet ou na Intranet, a partir de um URL específico. Por exemplo, `http://contoso/` mapas para a zona `http://intranet.contoso.com/` intranet, enquanto mapas para a zona da Internet (porque o URL contém um período). Os navegadores não enviarão bilhetes Kerberos para um ponto final na nuvem, como o URL DaD Azure, a menos que adicione explicitamente o URL à zona intranet do navegador.

Existem duas formas de modificar as definições da zona intranet dos utilizadores:

| Opção | Consideração de administrador | Experiência de utilizador |
| --- | --- | --- |
| Política de grupo | Administração bloqueia edição de definições de zona intranet | Os utilizadores não podem modificar as suas próprias definições |
| Preferência política do grupo |  Administrador permite edição em definições de zona Intranet | Os utilizadores podem modificar as suas próprias definições |

### <a name="group-policy-option---detailed-steps"></a>Opção "Política de grupo" - Passos detalhados

1. Abra a ferramenta Editor de Gestão de Políticas do Grupo.
2. Editar a política de grupo que é aplicada a alguns ou a todos os seus utilizadores. Este exemplo utiliza a Política de **Domínio Predefinido**.
3. Navegue na política de **configuração** > do utilizador**Modelos** > **administrativos** > **Windows Components** > Internet**Explorer** > Internet Control**Panel** > **Security Page**. Em seguida, selecione **Site para Lista**de Atribuição de Zonas .
    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso6.png)
4. Ativar a apólice e, em seguida, introduzir os seguintes valores na caixa de diálogo:
   - **Nome de valor**: O URL Da AD Azure onde os bilhetes kerberos são encaminhados.
   - **Valor** (Dados): **1** indica a zona intranet.

     O resultado é o seguinte:

     Nome de valor:`https://autologon.microsoftazuread-sso.com`
  
     Valor (Dados): 1

   >[!NOTE]
   > Se pretender proibir alguns utilizadores de utilizarem SSO Sso sem emenda (por exemplo, se estes utilizadores iniciarem sessão em quiosques partilhados), detetete os valores anteriores para **4**. Esta ação adiciona o URL Azure AD à zona restrita, e falha o SSO sem emenda a toda a hora.
   >

5. Selecione **OK** e, em seguida, selecione **OK** novamente.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso7.png)

6. Navegue na política de **configuração** > do utilizador**Modelos** > **administrativos** > **Componentes do Windows Componentes** > **internet Explorer** > Internet**Control Panel** > **Security****Zone** > . Em seguida, **selecione Permitir atualizações para barra de estado através do script**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso11.png)

7. Ative a definição de política e, em seguida, selecione **OK**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opção "Preferência política do grupo" - Passos detalhados

1. Abra a ferramenta Editor de Gestão de Políticas do Grupo.
2. Editar a política de grupo que é aplicada a alguns ou a todos os seus utilizadores. Este exemplo utiliza a Política de **Domínio Predefinido**.
3. Navegue nas**preferências** > de **configuração** > do utilizador O**novo** > **item**do**registo** > de**definições** > do Windows .

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso15.png)

4. Introduza os seguintes valores em campos apropriados e **clique**OK .
   - **Caminho-chave**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nome de valor**: ***https***.
   - **Tipo de valor:** ***REG_DWORD***.
   - **Dados**de valor : ***000000001***.
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Considerações de navegador

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

Mozilla Firefox não usa automaticamente a autenticação Kerberos. Cada utilizador deve adicionar manualmente o URL Azure AD às suas definições firefox utilizando os seguintes passos:
1. Executar Firefox `about:config` e entrar na barra de endereços. Rejeite qualquer notificação que veja.
2. Procure a preferência da **rede.negotiate-auth.trusted-uris.** Esta preferência lista os sites fidedignos do Firefox para a autenticação Kerberos.
3. Clique à direita e selecione **Modificar**.
4. Entra `https://autologon.microsoftazuread-sso.com` no campo.
5. Selecione **OK** e, em seguida, reabra o navegador.

#### <a name="safari-macos"></a>Safari (macOS)

Certifique-se de que a máquina que funciona o macOS está unida à AD. As instruções para a adesão de AD ao seu dispositivo macOS estão fora do âmbito deste artigo.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base no Crómio (todas as plataformas)

Se tiver ultrapassado as definições de política [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) ou [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) no seu ambiente, certifique-se de que também lhes adiciona o URL ()`https://autologon.microsoftazuread-sso.com`do Azure AD.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge baseado em Crómio (macOS e outras plataformas não Windows)

Para o Microsoft Edge com base no Chromium no Mac OS e noutras plataformas não Windows, consulte o Microsoft Edge com base na Lista de Política de [Crómio](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) para obter informações sobre como adicionar o URL de AD Azure para autenticação integrada à sua lista de autorizações.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se tiver ultrapassado as definições de política [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) no seu ambiente, certifique-se de que adiciona também o URL ()`https://autologon.microsoftazuread-sso.com`do Azure AD.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS e outras plataformas não Windows)

Para o Google Chrome no Mac OS e outras plataformas não Windows, consulte a [Lista de Políticas do Projeto Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como whitelist o URL DaD Azure para autenticação integrada.

A utilização de extensões de política de grupo de diretórios ativos de terceiros para lançar o URL Da AD Azure para o Firefox e o Google Chrome nos utilizadores de Mac está fora do âmbito deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do navegador

O SSO sem emenda não funciona em modo de navegação privada nos navegadores Firefox e Microsoft Edge. Também não funciona no Internet Explorer se o navegador estiver em execução no modo Protected Melhorado. Para a próxima versão do Microsoft Edge com base no Chromium, não funcionará no modo InPrivate e Guest por design.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Para testar a funcionalidade para um utilizador específico, certifique-se de que estão em vigor todas as seguintes condições:
  - O utilizador assina um dispositivo corporativo.
  - O dispositivo é unido ao seu domínio de Diretório Ativo. O dispositivo _não_ precisa de ser [Azure AD Joined](../active-directory-azureadjoin-overview.md).
  - O dispositivo tem uma ligação direta ao seu controlador de domínio (DC), seja na rede corporativa com fios ou sem fios ou através de uma ligação de acesso remoto, como uma ligação VPN.
  - Lançou [a funcionalidade](#step-3-roll-out-the-feature) para este utilizador através da Política de Grupo.

Para testar o cenário em que o utilizador introduz apenas o nome de utilizador, mas não a palavra-passe:
   - Inscreva-se `https://myapps.microsoft.com/` numa nova sessão de navegador privado.

Para testar o cenário em que o utilizador não tenha de introduzir o nome de utilizador ou a palavra-passe, utilize um destes passos: 
   - Inscreva-se `https://myapps.microsoft.com/contoso.onmicrosoft.com` numa nova sessão de navegador privado. Substitua *contoso* pelo nome do seu inquilino.
   - Inscreva-se `https://myapps.microsoft.com/contoso.com` numa nova sessão de navegador privado. Substitua *contoso.com* por um domínio verificado (não um domínio federado) no seu inquilino.

## <a name="step-5-roll-over-keys"></a>Passo 5: Rolar sobre as teclas

No Passo 2, o Azure AD Connect cria contas de computador (representando a AD Azure) em todas as florestas de Diretório Ativo em que permitiu o SSO sem emenda. Para saber mais, consulte [Azure Ative Directory Seamless Single Sign-On: Technical deep dive](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>A chave de desencriptação Kerberos numa conta de computador, se vazada, pode ser usada para gerar bilhetes Kerberos para qualquer utilizador na sua floresta ad. Atores maliciosos podem então personificar os sign-ins Da AD azure para utilizadores comprometidos. Recomendamos vivamente que revire periodicamente estas chaves de desencriptação Kerberos - pelo menos uma vez a cada 30 dias.

Para obter instruções sobre como rebolar as teclas, consulte [o Azure Ative Directory SeamlessY Single Sign-On: Perguntas frequentes](how-to-connect-sso-faq.md). Estamos a trabalhar numa capacidade de introduzir o capotamento automatizado das teclas.

>[!IMPORTANT]
>Não precisa de fazer este passo _imediatamente_ depois de ter ativado a funcionalidade. Rever as chaves de desencriptação kerberos pelo menos uma vez a cada 30 dias.

## <a name="next-steps"></a>Passos seguintes

- [Mergulho profundo técnico](how-to-connect-sso-how-it-works.md): Compreenda como funciona a função de inscrição única sem emenda.
- [Perguntas frequentes](how-to-connect-sso-faq.md): Obtenha respostas a perguntas frequentes sobre o Single Sign-On sem emenda.
- [Resolução de problemas](tshoot-connect-sso.md): Aprenda a resolver problemas comuns com a funcionalidade Single Sign-On Sem Emenda.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum de Diretório Ativo Azure para apresentar novos pedidos de funcionalidades.
