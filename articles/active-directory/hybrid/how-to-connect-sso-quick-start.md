---
title: 'Azure AD Connect: logon único contínuo-início rápido | Microsoft Docs'
description: Este artigo descreve como começar a usar Azure Active Directory logon único contínuo
services: active-directory
keywords: o que é Azure AD Connect, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 8cf1e5f9f47ebdc132bdc826af3e54d206095085
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603412"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory logon único contínuo: início rápido

## <a name="deploy-seamless-single-sign-on"></a>Implantar o logon único contínuo

O logon único contínuo (SSO contínuo) do Azure Active Directory (Azure AD) conecta automaticamente os usuários quando eles estão em seus desktops corporativos conectados à sua rede corporativa. O SSO contínuo fornece aos usuários acesso fácil aos seus aplicativos baseados em nuvem sem a necessidade de componentes locais adicionais.

Para implantar o SSO contínuo, siga estas etapas.

## <a name="step-1-check-the-prerequisites"></a>Etapa 1: verificar os pré-requisitos

Verifique se os seguintes pré-requisitos estão em vigor:

* **Configurar seu servidor de Azure ad Connect**: se você usar a [autenticação de passagem](how-to-connect-pta.md) como seu método de entrada, nenhuma verificação de pré-requisito adicional será necessária. Se você usar a [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) como seu método de entrada e se houver um firewall entre Azure ad Connect e o Azure AD, verifique se:
   - Você usa a versão 1.1.644.0 ou posterior de Azure AD Connect. 
   - Se o firewall ou o proxy permitir a lista de permissões de DNS, adicione as conexões à lista de permissões do **\*. msappproxy.net** pela porta 443. Caso contrário, permita o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente. Esse pré-requisito é aplicável somente quando você habilita o recurso. Ele não é necessário para entradas reais do usuário.

    >[!NOTE]
    >Azure AD Connect versões 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 têm um problema relacionado à sincronização de hash de senha. Se você _não_ pretende usar a sincronização de hash de senha em conjunto com a autenticação de passagem, leia as [notas de versão Azure ad Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) para saber mais.

* **Usar uma topologia de Azure ad Connect com suporte**: Verifique se você está usando uma das topologias com suporte do Azure ad Connect descritas [aqui](plan-connect-topologies.md).

    >[!NOTE]
    >O SSO contínuo dá suporte a várias florestas do AD, independentemente de as relações de confiança do AD entre elas ou não.

* **Configurar credenciais de administrador de domínio**: você precisa ter credenciais de administrador de domínio para cada floresta Active Directory que:
    * Você sincroniza com o Azure AD por meio do Azure AD Connect.
    * Contém os usuários que você deseja habilitar para o SSO contínuo.
    
* **Habilitar autenticação moderna**: você precisa habilitar a [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) em seu locatário para que esse recurso funcione.

* **Use as versões mais recentes dos clientes do office 365**: para obter uma experiência de logon silenciosa com clientes do Office 365 (Outlook, Word, Excel e outros), os usuários precisam usar as versões 16.0.8730. xxxx ou superior.

## <a name="step-2-enable-the-feature"></a>Etapa 2: habilitar o recurso

Habilite o SSO contínuo por meio de [Azure ad Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Você também pode [habilitar o SSO contínuo usando o PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) se Azure ad Connect não atender às suas necessidades. Use essa opção se você tiver mais de um domínio por Active Directory floresta e desejar ser mais direcionado ao domínio para o qual deseja habilitar o SSO contínuo.

Se você estiver fazendo uma nova instalação do Azure AD Connect, escolha o [caminho de instalação personalizada](how-to-connect-install-custom.md). Na página de **entrada do usuário** , selecione a opção **habilitar logon único** .

>[!NOTE]
> A opção estará disponível para seleção somente se o método de logon for **sincronização de hash de senha** ou **autenticação de passagem**.

![Azure AD Connect: entrada do usuário](./media/how-to-connect-sso-quick-start/sso8.png)

Se você já tiver uma instalação do Azure AD Connect, selecione a página **alterar entrada do usuário** em Azure ad Connect e, em seguida, selecione **Avançar**. Se você estiver usando Azure AD Connect versões 1.1.880.0 ou superior, a opção **habilitar logon único** será selecionada por padrão. Se você estiver usando versões mais antigas do Azure AD Connect, selecione a opção **habilitar logon único** .

![Azure AD Connect: alterar a entrada do usuário](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Continue no assistente até chegar à página **habilitar logon único** . Forneça credenciais de administrador de domínio para cada floresta Active Directory que:

* Você sincroniza com o Azure AD por meio do Azure AD Connect.
* Contém os usuários que você deseja habilitar para o SSO contínuo.

Após a conclusão do assistente, o SSO contínuo é habilitado em seu locatário.

>[!NOTE]
> As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Eles são usados apenas para habilitar o recurso.

Siga estas instruções para verificar se você habilitou o SSO contínuo corretamente:

1. Entre no [centro administrativo Azure Active Directory](https://aad.portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Selecione **Azure Active Directory** no painel esquerdo.
3. Selecione **Azure ad Connect**.
4. Verifique se o recurso **logon único contínuo** aparece como **habilitado**.

![Portal do Azure: painel de Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> O SSO contínuo cria uma conta de computador chamada `AZUREADSSOACC` no seu Active Directory (AD) local em cada floresta do AD. A conta de computador `AZUREADSSOACC` precisa estar fortemente protegida por motivos de segurança. Somente administradores de domínio devem ser capazes de gerenciar a conta de computador. Verifique se a delegação de Kerberos na conta do computador está desabilitada e se nenhuma outra conta no Active Directory tem permissões de delegação na conta de computador `AZUREADSSOACC`. Armazene a conta de computador em uma UO (unidade organizacional) em que elas estão protegidas contra exclusões acidentais e em que somente administradores de domínio têm acesso.

>[!NOTE]
> Se você estiver usando as arquiteturas de mitigação de roubo de credencial e de passagem de hash no seu ambiente local, faça as alterações apropriadas para garantir que a conta de computador `AZUREADSSOACC` não termine no contêiner de quarentena. 

## <a name="step-3-roll-out-the-feature"></a>Etapa 3: distribuir o recurso

Você pode distribuir gradualmente o SSO contínuo para seus usuários usando as instruções fornecidas abaixo. Você começa adicionando a seguinte URL do Azure AD a todas ou a configurações de zona de intranet dos usuários selecionados usando Política de Grupo no Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Além disso, você precisa habilitar uma configuração de política de zona de intranet chamada **permitir atualizações na barra de status por** meio de script por meio de política de grupo. 

>[!NOTE]
> As instruções a seguir funcionam apenas para o Internet Explorer e Google Chrome no Windows (se ele compartilhar um conjunto de URLs de sites confiáveis com o Internet Explorer). Leia a próxima seção para obter instruções sobre como configurar o Mozilla Firefox e o Google Chrome no macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Por que você precisa modificar as configurações de zona da intranet dos usuários?

Por padrão, o navegador calcula automaticamente a zona correta, a Internet ou a intranet, de uma URL específica. Por exemplo, `http://contoso/` mapeia para a zona da intranet, enquanto `http://intranet.contoso.com/` mapeia para a zona da Internet (porque a URL contém um ponto). Os navegadores não enviarão tíquetes Kerberos para um ponto de extremidade de nuvem, como a URL do Azure AD, a menos que você adicione explicitamente a URL à zona da intranet do navegador.

Há duas maneiras de modificar as configurações de zona da intranet dos usuários:

| Opção | Consideração do administrador | Experiência do usuário |
| --- | --- | --- |
| Política de grupo | O administrador bloqueia a edição das configurações de zona da intranet | Os usuários não podem modificar suas próprias configurações |
| Preferência de política de grupo |  O administrador permite a edição nas configurações de zona da intranet | Os usuários podem modificar suas próprias configurações |

### <a name="group-policy-option---detailed-steps"></a>Opção "política de grupo"-etapas detalhadas

1. Abra a ferramenta Editor de Gerenciamento de Política de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou a todos os seus usuários. Este exemplo usa a **política de domínio padrão**.
3. Navegue até **configuração do usuário** > **política** > **Modelos Administrativos** > **componentes do Windows** > **Internet Explorer** > **painel de controle da Internet** >  **Página de segurança**. Em seguida, selecione **lista de atribuição de site a zona**.
    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso6.png)
4. Habilite a política e, em seguida, insira os seguintes valores na caixa de diálogo:
   - **Nome do valor**: a URL do Azure AD em que os tíquetes Kerberos são encaminhados.
   - **Valor** (dados): **1** indica a zona da intranet.

     O resultado é semelhante a este:

     Nome do valor: `https://autologon.microsoftazuread-sso.com`
  
     Valor (dados): 1

   >[!NOTE]
   > Se você quiser impedir que alguns usuários usem o SSO contínuo (por exemplo, se esses usuários entrarem em quiosques compartilhados), defina os valores anteriores como **4**. Essa ação adiciona a URL do Azure AD à zona restrita e falha no SSO contínuo o tempo todo.
   >

5. Selecione **OK**e, em seguida, selecione **OK** novamente.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso7.png)

6. Navegue até **configuração do usuário** > **política** > **Modelos Administrativos** > **componentes do Windows** > **Internet Explorer** > **painel de controle da Internet** >  **Página de segurança** > **zona da intranet**. Em seguida, selecione **permitir atualizações na barra de status por meio de script**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso11.png)

7. Habilite a configuração de política e, em seguida, selecione **OK**.

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opção "preferência de política de grupo"-etapas detalhadas

1. Abra a ferramenta Editor de Gerenciamento de Política de Grupo.
2. Edite a política de grupo que é aplicada a alguns ou a todos os seus usuários. Este exemplo usa a **política de domínio padrão**.
3. Navegue até **configuração do usuário** > **preferências** > **configurações do Windows** > **registro** > **novo** **item do registro** > .

    ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso15.png)

4. Insira os seguintes valores nos campos apropriados e clique em **OK**.
   - **Caminho da chave**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon***
   - **Nome do valor**: ***https***.
   - **Tipo de valor**: ***REG_DWORD***.
   - **Dados do valor**: ***00000001***.
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Início de sessão único](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Considerações do navegador

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (todas as plataformas)

O Mozilla Firefox não usa automaticamente a autenticação Kerberos. Cada usuário deve adicionar manualmente a URL do Azure AD às configurações do Firefox usando as seguintes etapas:
1. Execute o Firefox e insira `about:config` na barra de endereços. Ignore todas as notificações que você vir.
2. Procure a preferência **Network. Negotiate. Trusted-URIs** . Essa preferência lista os sites confiáveis do Firefox para autenticação Kerberos.
3. Clique com o botão direito do mouse e selecione **Modificar**.
4. Insira `https://autologon.microsoftazuread-sso.com` no campo.
5. Selecione **OK** e reabra o navegador.

#### <a name="safari-macos"></a>Safari (macOS)

Verifique se o computador que executa o macOS está unido ao AD. As instruções para o AD-unindo seu dispositivo macOS estão fora do escopo deste artigo.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge com base em Chromium (todas as plataformas)

Se você tiver substituído as configurações de política [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) ou [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) no seu ambiente, certifique-se de adicionar a URL do Azure AD (`https://autologon.microsoftazuread-sso.com`) também a elas.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge com base em Chromium (macOS e outras plataformas que não sejam Windows)

Para o Microsoft Edge com base em Chromium em Mac OS e outras plataformas que não sejam Windows, consulte [o Microsoft Edge com base na lista de políticas do Chromium](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) para obter informações sobre como adicionar a URL do Azure ad para autenticação integrada à sua lista de permissões.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (todas as plataformas)

Se você tiver substituído as configurações de política [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) ou [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) no seu ambiente, certifique-se de adicionar a URL do Azure AD (`https://autologon.microsoftazuread-sso.com`) também a elas.

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS e outras plataformas que não são Windows)

Para o Google Chrome em Mac OS e outras plataformas que não são do Windows, consulte [a lista de políticas de projeto do Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) para obter informações sobre como adicionar a URL do Azure Active Directory a ela para autenticação integrada.

O uso de extensões de Política de Grupo de Active Directory de terceiros para distribuir a URL do Azure AD para o Firefox e o Google Chrome em usuários do Mac está fora do escopo deste artigo.

#### <a name="known-browser-limitations"></a>Limitações de navegador conhecidas

O SSO contínuo não funciona no modo de navegação particular nos navegadores Firefox e Microsoft Edge. Ele também não funcionará no Internet Explorer se o navegador estiver sendo executado no modo protegido avançado. Para a próxima versão do Microsoft Edge com base no Chromium, ele não funcionará no modo InPrivate e convidado por design.

## <a name="step-4-test-the-feature"></a>Etapa 4: testar o recurso

Para testar o recurso para um usuário específico, verifique se todas as condições a seguir estão em vigor:
  - O usuário entra em um dispositivo corporativo.
  - O dispositivo está ingressado em seu domínio de Active Directory. O dispositivo _não_ precisa ser [ingressado no Azure ad](../active-directory-azureadjoin-overview.md).
  - O dispositivo tem uma conexão direta com seu DC (controlador de domínio), seja na rede corporativa com ou sem fio ou por meio de uma conexão de acesso remoto, como uma conexão VPN.
  - Você [distribuiu o recurso](##step-3-roll-out-the-feature) para esse usuário por meio de política de grupo.

Para testar o cenário em que o usuário insere somente o nome de usuário, mas não a senha:
   - Entre no `https://myapps.microsoft.com/` em uma nova sessão privada do navegador.

Para testar o cenário em que o usuário não precisa inserir o nome de usuário ou a senha, use uma destas etapas: 
   - Entre no `https://myapps.microsoft.com/contoso.onmicrosoft.com` em uma nova sessão privada do navegador. Substitua *contoso* pelo nome do locatário.
   - Entre no `https://myapps.microsoft.com/contoso.com` em uma nova sessão privada do navegador. Substitua *contoso.com* por um domínio verificado (não um domínio federado) em seu locatário.

## <a name="step-5-roll-over-keys"></a>Etapa 5: sobrepor as chaves

Na etapa 2, Azure AD Connect cria contas de computador (representando o Azure AD) em todas as Active Directory florestas nas quais você habilitou o SSO contínuo. Para saber mais, confira [Azure Active Directory logon único contínuo: aprofundamento técnico](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>A chave de descriptografia Kerberos em uma conta de computador, se vazada, pode ser usada para gerar tíquetes Kerberos para qualquer usuário em sua floresta do AD. Os atores mal-intencionados podem representar as entradas do Azure AD para usuários comprometidos. É altamente recomendável que você reverta periodicamente essas chaves de descriptografia Kerberos: pelo menos uma vez a cada 30 dias.

Para obter instruções sobre como sobrepor chaves, consulte [Azure Active Directory logon único contínuo: perguntas](how-to-connect-sso-faq.md)frequentes. Estamos trabalhando em uma capacidade de introduzir a substituição automatizada de chaves.

>[!IMPORTANT]
>Você não precisa fazer essa etapa _imediatamente_ depois de habilitar o recurso. Passe as chaves de descriptografia Kerberos pelo menos uma vez a cada 30 dias.

## <a name="next-steps"></a>Passos seguintes

- Aprofundamento [técnico](how-to-connect-sso-how-it-works.md): entenda como funciona o recurso de logon único contínuo.
- [Perguntas](how-to-connect-sso-faq.md)frequentes: Obtenha respostas para perguntas frequentes sobre o logon único contínuo.
- [Solução de problemas](tshoot-connect-sso.md): saiba como resolver problemas comuns com o recurso de logon único contínuo.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Use o fórum de Azure Active Directory para arquivar novas solicitações de recurso.
