---
title: 'Azure AD Connect: Single Sign-On sem emenda - | de arranque rápido Microsoft Docs'
description: Este artigo descreve como começar com o Azure Ative Directory Seamless Single Sign-On
services: active-directory
keywords: o que é Azure AD Connect, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 349aef1bb9382eec19d9ad9c7f6d4579c82b62de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043942"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Ative Directory Sem Emenda Single Sign-On: Quickstart

## <a name="deploy-seamless-single-sign-on"></a>Implementar Sign-On única sem emenda

Azure Ative Directory (Azure AD) Seamless Single Sign-On (Seamless SSO) assina automaticamente nos utilizadores quando estão nos seus desktops corporativos que estão ligados à sua rede corporativa. O Seamless SSO proporciona aos seus utilizadores um fácil acesso às suas aplicações baseadas na nuvem sem precisar de componentes adicionais no local.

Para implementar o SSO sem emenda, siga estes passos.

## <a name="step-1-check-the-prerequisites"></a>Passo 1: Verifique os pré-requisitos

Certifique-se de que existem os seguintes pré-requisitos:

* **Configurar o seu servidor Azure AD Connect**: Se utilizar [a Autenticação Pass-through](how-to-connect-pta.md) como método de entrada, não é necessária nenhuma verificação prévia adicional. Se utilizar [a sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) como método de entrada e se houver uma firewall entre Azure AD Connect e AZure AD, certifique-se de que:
   - Utiliza a versão 1.1.644.0 ou mais tarde do Azure AD Connect. 
   - Se a sua firewall ou proxy permitir, adicione as ligações à lista permitida para **\* URLs de .msappproxy.net** sobre a porta 443. Se você precisar de um URL específico em vez de um wildcard para configuração de procuração, você pode configurar **tenantid.registration.msappproxy.net**, onde tenantid é o GUID do inquilino onde você está configurando a funcionalidade. Se as exceções de procuração baseadas em URL não forem possíveis na sua organização, pode, em vez disso, permitir o acesso aos intervalos IP do [datacenter Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente. Este pré-requisito só é aplicável quando ativar a funcionalidade. Não é necessário para as insuposições reais do utilizador.

    >[!NOTE]
    >As versões AZURE AD Connect 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado com a sincronização da hash da palavra-passe. Se _não_ pretender utilizar a sincronização de hash de palavra-passe em conjunto com a Autenticação Pass-through, leia as notas de lançamento do [Azure AD Connect](./reference-connect-version-history.md) para saber mais.
    
    >[!NOTE]
    >Se tiver um representante HTTP de saída, certifique-se de que este URL, autologon.microsoftazuread-sso.com, está na lista permitida. Deve especificar este URL explicitamente, uma vez que o wildcard não pode ser aceite. 

* **Utilize uma topologia suportada do Azure AD Connect**: Certifique-se de que está a utilizar uma das topologias suportadas do Azure AD Connect [descritas aqui](plan-connect-topologies.md).

    >[!NOTE]
    >O SSO sem emenda suporta várias florestas de AD, quer existam ou não fidedignos entre eles.

* **Configurar credenciais de administrador de domínio**: É necessário ter credenciais de administrador de domínio para cada floresta de Diretório Ativo que:
    * Sincroniza-se com a Azure AD através do Azure AD Connect.
    * Contém utilizadores que pretende ativar para SSO sem emenda.
    
* **Ativar a autenticação moderna**: É necessário ativar a [autenticação moderna](/office365/enterprise/modern-auth-for-office-2013-and-2016) no seu inquilino para que esta funcionalidade funcione.

* **Utilize as versões mais recentes dos clientes da Microsoft 365**: Para obter uma experiência de login silenciosa com clientes Microsoft 365 (Outlook, Word, Excel, entre outros), os seus utilizadores precisam de utilizar as versões 16.0.8730.xxxx ou superiores.

## <a name="step-2-enable-the-feature"></a>Passo 2: Ativar a funcionalidade

Ativar o SSO sem emenda através [do Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Também pode [ativar o Seamless SSO utilizando o PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) se o Azure AD Connect não cumprir os seus requisitos. Utilize esta opção se tiver mais do que um domínio por floresta de Diretório Ativo, e pretender ser mais direcionado sobre o domínio para o qual pretende ativar o SSO sem emenda.

Se estiver a fazer uma nova instalação do Azure AD Connect, escolha o [caminho de instalação personalizado.](how-to-connect-install-custom.md) Na página de início de  **sôm.**

>[!NOTE]
> A opção só estará disponível para seleção se o método Sign On for **a Sincronização de Hash password** ou a **autenticação pass-through.**

![Azure AD Connect: Iniciar sação do utilizador](./media/how-to-connect-sso-quick-start/sso8.png)

Se já tiver uma instalação do Azure AD Connect, selecione a página **de insusição** do utilizador Change no Azure AD Connect e, em seguida, selecione **Next**. Se estiver a utilizar as versões Azure AD Connect 1.1.880.0 ou superior, o **sinal único ativado na** opção será selecionado por predefinição. Se estiver a utilizar versões mais antigas do Azure AD Connect, selecione o **sinal único de Ativação na** opção.

![Azure AD Connect: Alterar o súmido do utilizador](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Continue através do assistente até chegar ao **sinal de Enable na** página. Fornecer credenciais de administrador de domínio para cada floresta ative directory que:

* Sincroniza-se com a Azure AD através do Azure AD Connect.
* Contém utilizadores que pretende ativar para SSO sem emenda.

Após a conclusão do assistente, o Seamless SSO está ativado no seu inquilino.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. São usados apenas para ativar a funcionalidade.

Siga estas instruções para verificar se ativou o SSO sem emenda corretamente:

1. Inscreva-se no [Centro Administrativo Azure Ative Com](https://aad.portal.azure.com) as credenciais de administrador global para o seu inquilino.
2. Selecione **Azure Ative Directory** no painel esquerdo.
3. Selecione **Azure Ad Connect**.
4. Verifique se a função **de sinal de inscrição única** sem emenda aparece como **Ativada**.

![Portal Azure: Painel de ligação Azure AD](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> O Seamless SSO cria uma conta de computador nomeada `AZUREADSSOACC` no seu Ative Directory (AD) em cada floresta de AD. A `AZUREADSSOACC` conta do computador precisa de ser fortemente protegida por razões de segurança. Apenas os Administradores de Domínio devem ser capazes de gerir a conta do computador. Certifique-se de que a delegação kerberos na conta do computador está desativada e que nenhuma outra conta no Ative Directory tem permissões de delegação na `AZUREADSSOACC` conta do computador. Guarde a conta do computador numa Unidade de Organização (OU) onde estão a salvo de supressões acidentais e onde apenas os Administradores de Domínio têm acesso.

>[!NOTE]
> Se estiver a utilizar as arquiteturas de mitigação de roubos pass-the-Hash e Credenciais no seu ambiente no local, faça alterações apropriadas para garantir que a conta do `AZUREADSSOACC` computador não acabe no recipiente de Quarentena. 

## <a name="step-3-roll-out-the-feature"></a>Passo 3: Lançar a funcionalidade

Pode lançar gradualmente o Seamless SSO aos seus utilizadores utilizando as instruções fornecidas abaixo. Começa por adicionar o seguinte URL AD AD do Azure a todas ou selecionadas definições de zona intranet dos utilizadores, utilizando a Política de Grupo no Diretório Ativo:

- `https://autologon.microsoftazuread-sso.com`

Além disso, precisa de ativar uma definição de política de zona intranet chamada **Permitir atualizações para a barra de estado através do script** através da Política de Grupo. 

>[!NOTE]
> As seguintes instruções funcionam apenas para o Internet Explorer, Microsoft Edge e Google Chrome no Windows (se partilhar um conjunto de URLs de site fidedignos com o Internet Explorer). Leia a próxima secção para obter instruções sobre como configurar o Mozilla Firefox e o Google Chrome no macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que precisa modificar as definições da zona intranet dos utilizadores?

Por padrão, o navegador calcula automaticamente a zona correta, internet ou Intranet, a partir de um URL específico. Por exemplo, `http://contoso/` mapeia para a zona Intranet, enquanto `http://intranet.contoso.com/` que mapeia para a zona da Internet (porque o URL contém um período). Os navegadores não enviarão bilhetes Kerberos para um ponto final em nuvem, como o URL AD Azure, a menos que adicione explicitamente o URL à zona intranet do navegador.

Existem duas formas de modificar as definições da zona intranet dos utilizadores:

| Opção | Consideração de administração | Experiência do utilizador |
| --- | --- | --- |
| Política de grupo | Administrador bloqueia edição das definições da zona Intranet | Os utilizadores não podem modificar as suas próprias definições |
| Preferência política de grupo |  Administração permite edição nas definições da zona Intranet | Os utilizadores podem modificar as suas próprias definições |

### <a name="group-policy-option---detailed-steps"></a>Opção "política de grupo" - Passos detalhados

1. Abra a ferramenta Editor de Gestão de Políticas de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou a todos os seus utilizadores. Este exemplo utiliza **a Política de Domínio Padrão.**
3. Navegue para as políticas **de configuração do utilizador**  >    >  **Modelos administrativos Windows**  >  **Componentes** Internet  >  **Explorer Internet** Control  >  **Panel**  >  **Security Page**. Em seguida, selecione **Site para a Lista de Atribuição de Zonas**.
    ![Screenshot que mostra a "Página de Segurança" com "Lista de Atribuição de Zonas" selecionada.](./media/how-to-connect-sso-quick-start/sso6.png)
4. Ativar a política e, em seguida, introduzir os seguintes valores na caixa de diálogo:
   - **Nome de valor**: A URL AD Azure onde os bilhetes Kerberos são reencaminhados.
   - **Valor** (Dados): **1** indica a zona Intranet.

     O resultado tem o seguinte aspeto:

     Nome de valor: `https://autologon.microsoftazuread-sso.com`
  
     Valor (Dados): 1

   >[!NOTE]
   > Se pretender proibir alguns utilizadores de utilizarem o SSO sem emenda (por exemplo, se estes utilizadores iniciarem sing em quiosques partilhados), decrete os valores anteriores para **4**. Esta ação adiciona o URL AD AD Azure à zona restrita, e falha em SSO sem emenda a toda a hora.
   >

5. Selecione **OK** e, em seguida, selecione **OK** novamente.

    ![Screenshot que mostra a janela "Mostrar Conteúdo" com uma atribuição de zona selecionada.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Navegue para as políticas **de configuração do utilizador**  >    >  **Modelos administrativos Windows**  >  **Componentes** Do Internet  >  **Explorer Internet** Control  >  **Panel**  >  **Security**  >  **Zone**. Em seguida, **selecione Permitir atualizações para a barra de estado através do script**.

    ![Screenshot que mostra a página "Intranet Zone" com "Permitir atualizações para a barra de estado via script" selecionada.](./media/how-to-connect-sso-quick-start/sso11.png)

7. Ativar a definição de política e, em seguida, selecionar **OK**.

    ![Screenshot que mostra a janela "Permitir atualizações para barra de estado via script" com a definição de política ativada.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opção "preferência de política de grupo" - Passos detalhados

1. Abra a ferramenta Editor de Gestão de Políticas de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou a todos os seus utilizadores. Este exemplo utiliza **a Política de Domínio Padrão.**
3. Navegue para as preferências **de configuração do**  >    >  **utilizador, as definições**  >  **do Windows registam**  >  **novo** item  >  **de registo**.

    ![Screenshot que mostra "Registry" selecionado e "Registry Item" selecionado.](./media/how-to-connect-sso-quick-start/sso15.png)

4. Introduza os seguintes valores nos campos apropriados e clique **em OK**.
   - **Caminho chave**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon_**
   - **Nome do valor**: **_https_**
   - **Tipo de valor**: **_REG_DWORD_**
   - **Dados de valor:** **_0000001_**
 
     ![Screenshot que mostra a janela "Novas Propriedades de Registo".](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Considerações do navegador

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não utiliza automaticamente a autenticação Kerberos. Cada utilizador deve adicionar manualmente o URL AD AD do Azure às suas definições firefox utilizando os seguintes passos:
1. Corra o Firefox e insira `about:config` na barra de endereços. Dispense quaisquer notificações que veja.
2. Procure a **preferência network.negotiate-auth.trust-uris.** Esta preferência lista os sites fidedignos do Firefox para a autenticação Kerberos.
3. Clique à direita e selecione **Modificar.**
4. Entra `https://autologon.microsoftazuread-sso.com` no campo.
5. Selecione **OK** e, em seguida, reabra o navegador.

#### <a name="safari-macos"></a>Safari (macOS)

Certifique-se de que a máquina que está a funcionar o macOS está associada à AD. As instruções para a adesão a AD do seu dispositivo macOS estão fora do âmbito deste artigo.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge baseado em Chromium (todas as plataformas)

Se tiver ultrapassado as definições de política [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) ou [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) no seu ambiente, certifique-se de que também lhe adiciona o URL do Azure AD `https://autologon.microsoftazuread-sso.com` ( ) também.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge com base no Chromium (macOS e outras plataformas não-Windows)

Para o Microsoft Edge baseado no Chromium em macOS e outras plataformas não-Windows, consulte [o Microsoft Edge com base na Lista de Políticas de Crómio](/DeployEdge/microsoft-edge-policies#authserverallowlist) para obter informações sobre como adicionar o URL AD AZure para autenticação integrada na sua lista de admissões.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se tiver ultrapassado as definições de política [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) no seu ambiente, certifique-se de que também lhes adiciona o URL do Azure AD `https://autologon.microsoftazuread-sso.com` ( ) também.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS e outras plataformas não-Windows)

Para o Google Chrome no macOS e outras plataformas não Windows, consulte a [Lista de Política do Projeto de Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como controlar a lista de permitis do URL AD Azure para a autenticação integrada.

A utilização de extensões de política de grupo de diretório ativo de terceiros para lançar o URL AD AD Azure para firefox e Google Chrome em utilizadores mac está fora do âmbito deste artigo.

#### <a name="known-browser-limitations"></a>Limitações conhecidas do navegador

O Seamless SSO não funciona em modo de navegação privada nos navegadores Firefox e Microsoft Edge (legado). Também não funciona no Internet Explorer se o navegador estiver a funcionar em modo Protegido Melhorado. O Seamless SSO suporta a próxima versão do Microsoft Edge com base no Chromium e funciona no modo InPrivate e Guest por design.

## <a name="step-4-test-the-feature"></a>Passo 4: Testar a funcionalidade

Para testar a funcionalidade para um utilizador específico, certifique-se de que estão todas as seguintes condições:
  - O utilizador assina um dispositivo corporativo.
  - O dispositivo está associado ao seu domínio Ative Directory. O dispositivo _não_ precisa de ser [Azure AD Joined](../devices/overview.md).
  - O dispositivo tem uma ligação direta ao seu controlador de domínio (DC), quer na rede com fios ou sem fios da empresa, quer através de uma ligação de acesso remoto, como uma ligação VPN.
  - Lançou [a funcionalidade](#step-3-roll-out-the-feature) a este utilizador através da Política de Grupo.

Para testar o cenário em que o utilizador introduz apenas o nome de utilizador, mas não a palavra-passe:
   - Inscreva-se em ' https://myapps.microsoft.com/ . Certifique-se de limpar a cache do navegador ou usar uma nova sessão de navegador privado com qualquer um dos navegadores suportados em modo privado.

Para testar o cenário em que o utilizador não tem de introduzir o nome de utilizador ou a palavra-passe, utilize um destes passos: 
   - Entre em `https://myapps.microsoft.com/contoso.onmicrosoft.com` sessão para ter certeza de limpar a cache do navegador ou usar uma nova sessão de navegador privado com qualquer um dos navegadores suportados em modo privado. Substitua *o Contoso* pelo nome do seu inquilino.
   - Inscreva-se `https://myapps.microsoft.com/contoso.com` numa nova sessão de navegador privado. Substitua *contoso.com* por um domínio verificado (não um domínio federado) no seu inquilino.

## <a name="step-5-roll-over-keys"></a>Passo 5: Rolar as chaves

No Passo 2, o Azure AD Connect cria contas de computador (representando a Azure AD) em todas as florestas de Diretório Ativo em que ativou o Seamless SSO. Para saber mais, consulte [o Azure Ative Directory Seamless Single Sign-On: Mergulho profundo técnico.](how-to-connect-sso-how-it-works.md)

>[!IMPORTANT]
>A chave de desencriptação Kerberos numa conta de computador, se vazada, pode ser usada para gerar bilhetes Kerberos para qualquer utilizador na sua floresta de AD. Os atores maliciosos podem então fazer-se passar por ad-ins Azure para utilizadores comprometidos. Recomendamos vivamente que rebolar periodicamente estas chaves de desencriptação Kerberos - pelo menos uma vez a cada 30 dias.

Para obter instruções sobre como rolar as teclas, consulte [O Diretório Azure Ative Sem Emenda Único Sinal: Perguntas frequentes](how-to-connect-sso-faq.md). Estamos a trabalhar numa capacidade de introduzir o rolo automático de chaves.

>[!IMPORTANT]
>Não precisa de fazer este passo _imediatamente_ depois de ter ativado a funcionalidade. Revir as chaves de desencriptação Kerberos pelo menos uma vez a cada 30 dias.

## <a name="next-steps"></a>Passos seguintes

- [Mergulho profundo técnico](how-to-connect-sso-how-it-works.md): Compreenda como funciona a funcionalidade de Sign-On Sem Emenda.
- [Perguntas frequentes](how-to-connect-sso-faq.md): Obtenha respostas a perguntas frequentes sobre o Sign-On Único Sem Emenda.
- [Resolução de problemas](tshoot-connect-sso.md): Aprenda a resolver problemas comuns com a funcionalidade Single Sign-On Sem emenda.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Azure Ative Directory Forum para apresentar novos pedidos de funcionalidades.
