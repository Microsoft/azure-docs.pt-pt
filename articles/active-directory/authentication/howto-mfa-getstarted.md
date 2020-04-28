---
title: Considerações de implantação para autenticação de multi-factor seleções do Azure
description: Conheça considerações de implementação e estratégia para implementação bem-sucedida da Autenticação Multi-Factor Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845a202faccbbe0a604560ac57ae30f87344b95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81451130"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Planeie uma implementação de autenticação multi-factor Azure

As pessoas estão a ligar-se aos recursos organizacionais em cenários cada vez mais complicados. As pessoas conectam-se a partir de dispositivos corporativos, pessoais e públicos dentro e fora da rede corporativa usando smartphones, tablets, computadores e portáteis, muitas vezes em várias plataformas. Neste mundo sempre conectado, multi-dispositivo e multiplataforma, a segurança das contas de utilizador é mais importante do que nunca. As palavras-passe, independentemente da sua complexidade, utilizadas entre dispositivos, redes e plataformas já não são suficientes para garantir a segurança da conta de utilizador, especialmente quando os utilizadores tendem a reutilizar senhas através de contas. Phishing sofisticado e outros ataques de engenharia social podem resultar em nomes de utilizadores e palavras-passe sendo postados e vendidos através da dark web.

[A Autenticação Multi-Factor Azure (MFA)](concept-mfa-howitworks.md) ajuda a salvaguardar o acesso a dados e aplicações. Fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar o [Acesso Condicional](../conditional-access/overview.md) para tornar a solução adequada às suas necessidades específicas.

Este guia de implementação mostra-lhe como planear e, em seguida, testar um lançamento de autenticação de multi-factor Azure.

Para ver rapidamente a Autenticação Multi-Factor Azure em ação e, em seguida, voltar a entender considerações adicionais de implementação:

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implementação da Autenticação Multi-Factor Azure, existem itens pré-requisitos que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| Ambiente de identidade **só em nuvem** com autenticação moderna | **Sem tarefas pré-requisitos adicionais** |
| **Cenários** de identidade híbrida | O [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implantado e as identidades dos utilizadores são sincronizadas ou federadas com os Serviços de Domínio ativo do Diretório Ativo no local com o Diretório Ativo Azure. |
| Aplicações antigas no local publicadas para acesso à nuvem | O Proxy [de Aplicação](../manage-apps/application-proxy.md) AD Azure está implantado. |
| Utilização de MFA Azure com Autenticação RADIUS | Um Servidor de Política de [Rede (NPS)](howto-mfa-nps-extension.md) está implantado. |
| Os utilizadores têm o Microsoft Office 2010 ou mais cedo, ou o Apple Mail para iOS 11 ou mais cedo | Upgrade para [microsoft Office 2013 ou mais tarde](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e correio apple para iOS 12 ou posterior. O Acesso Condicional não é suportado por protocolos de autenticação legados. |

## <a name="plan-user-rollout"></a>Planejar o lançamento do utilizador

O seu plano de lançamento do MFA deve incluir uma implantação piloto seguida de ondas de implantação que estão dentro da sua capacidade de suporte. Inicie o seu lançamento aplicando as suas políticas de Acesso Condicional a um pequeno grupo de utilizadores piloto. Depois de avaliar o efeito sobre os utilizadores piloto, processo utilizado e comportamentos de registo, pode adicionar mais grupos à política ou adicionar mais utilizadores aos grupos existentes.

### <a name="user-communications"></a>Comunicações de utilizadores

É fundamental informar os utilizadores, nas comunicações planeadas, sobre as próximas alterações, os requisitos de registo do Azure MFA e quaisquer ações necessárias para o utilizador. Recomendamos que as comunicações sejam desenvolvidas em conjunto com representantes da sua organização, tais como departamentos de Comunicação, Gestão de Mudanças ou Recursos Humanos.

A Microsoft fornece [modelos](https://aka.ms/mfatemplates) de comunicação e [documentação de utilizador final](../user-help/security-info-setup-signin.md) para ajudar a redigir as suas comunicações. Pode enviar os [https://myprofile.microsoft.com](https://myprofile.microsoft.com) utilizadores para se registarem diretamente selecionando os links **de Informação** de Segurança nessa página.

## <a name="deployment-considerations"></a>Considerações sobre implementação

A autenticação multifactor Azure é implementada através da aplicação de políticas com acesso condicional. Uma política de [acesso condicional](../conditional-access/overview.md) pode exigir que os utilizadores realizem a autenticação de vários fatores quando determinados critérios são cumpridos, tais como:

* Todos os utilizadores, um utilizador específico, membro de um grupo ou função atribuída
* Aplicação em nuvem específica a ser acedida
* Plataforma de dispositivo
* Estado do dispositivo
* Localização da rede ou endereço IP geo-localizado
* Aplicações de cliente
* Risco de inscrição (requer proteção de identidade)
* Dispositivo conforme
* Dispositivo adestado pela Hybrid Azure
* Pedido de cliente aprovado

Utilize os cartazes personalizáveis e os modelos de e-mail em materiais de [autenticação multifactor](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para lançar a autenticação de vários fatores à sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Ativar a autenticação de vários fatores com acesso condicional

As políticas de Acesso Condicional impõem o registo, exigindo que os utilizadores não registados completem o registo no primeiro início de sessão, uma importante consideração de segurança.

[A Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribui tanto para uma política de registo e políticas automatizadas de deteção e reparação de riscos para a história de autenticação multi-factor Azure. As políticas podem ser criadas para forçar alterações de palavra-passe quando existe uma ameaça de identidade comprometida ou exigir MFA quando um inserimento é considerado arriscado pelos [seguintes eventos:](../reports-monitoring/concept-risk-events.md)

* Credenciais vazadas
* Inícios de sessão de endereços IP anónimos
* Deslocação impossível para localizações atípicas
* Inícios de sessão de localizações desconhecidas
* Inícios de sessão de dispositivos infetados
* Inscrições a partir de endereços IP com atividades suspeitas

Algumas das deteções de risco detetadas pela Azure Ative Directory Identity Protection ocorrem em tempo real e algumas requerem processamento offline. Os administradores podem optar por bloquear os utilizadores que apresentem comportamentos de risco e remediarmanualmente, exigir uma alteração de palavra-passe ou exigir uma autenticação de vários fatores como parte das suas políticas de Acesso Condicional.

## <a name="define-network-locations"></a>Definir localizações de rede

Recomendamos que as organizações utilizem o Acesso Condicional para definir a sua rede utilizando [locais nomeados.](../conditional-access/location-condition.md#named-locations) Se a sua organização estiver a usar proteção de identidade, considere usar políticas baseadas no risco em vez de locais nomeados.

### <a name="configuring-a-named-location"></a>Configurar uma localização nomeada

1. Open **Azure Ative Diretório** no portal Azure
2. Selecione **Segurança**
3. Under **Manage**, escolha **Locais Nomeados**
4. Selecione **nova localização**
5. No campo **Nome,** forneça um nome significativo
6. Selecione se está a definir a localização utilizando *gamas IP* ou *Países/Regiões*
   1. Se utilizar *gamas IP*
      1. Decida se marca como *local de confiança.* Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador. Só marque este local como confiável se souber que as gamas IP introduzidas são estabelecidas e credíveis na sua organização.
      2. Especificar as gamas IP
   2. Se utilizar *países/regiões*
      1. Expanda o menu suspenso e selecione os países ou regiões que deseja definir para este local nomeado.
      2. Decida se *deve incluir áreas desconhecidas.* Áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Selecione **Criar**

## <a name="plan-authentication-methods"></a>Métodos de autenticação do plano

Os administradores podem escolher os métodos de [autenticação](../authentication/concept-authentication-methods.md) que pretendem disponibilizar aos utilizadores. É importante permitir mais do que um método de autenticação único para que os utilizadores tenham um método de backup disponível caso o seu método principal não esteja disponível. Estão disponíveis os seguintes métodos para os administradores permitirem:

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

Uma notificação push é enviada para a aplicação Microsoft Authenticator no seu dispositivo móvel. O utilizador vê a notificação e seleciona **Aprovar** para completar a verificação. As notificações push através de uma aplicação móvel fornecem a opção menos intrusiva para os utilizadores. São também a opção mais fiável e segura porque utilizam uma ligação de dados em vez de telefonia.

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a **Notificação através** do método de aplicação móvel em **dispositivos Android** não funciona nesse país. Devem ser disponibilizados métodos alternativos para esses utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

Uma aplicação móvel como a aplicação Microsoft Authenticator gera um novo código de verificação DOPR a cada 30 segundos. O utilizador introduz o código de verificação na interface de entrada. A opção de aplicação móvel pode ser usada se o telefone tem ou não um sinal de dados ou celular.

### <a name="call-to-phone"></a>Chamada para telefone

É colocada uma chamada de voz automatizada ao utilizador. O utilizador atende a **#** chamada e pressiona o teclado do telefone para aprovar a sua autenticação. A chamada para o telefone é um ótimo método de backup para notificação ou código de verificação a partir de uma aplicação móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto que contenha um código de verificação é enviada ao utilizador, o utilizador é solicitado a introduzir o código de verificação na interface de entrada.

### <a name="choose-verification-options"></a>Escolha opções de verificação

1. Navegue no **Diretório Ativo do Azure,** **utilizadores,** **autenticação de vários fatores.**

   ![Aceder ao portal de autenticação multi-factor da lâmina dos utilizadores da AD Azure no portal Azure](media/howto-mfa-getstarted/users-mfa.png)

1. No novo separador que abre navegue para **as definições**de serviço .
1. No âmbito das opções de **verificação,** verifique todas as caixas para obter métodos disponíveis para os utilizadores.

   ![Configurar métodos de verificação no separador de definições de definições de serviço de autenticação multi-factor](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Guardar**.
1. Feche o separador de definições de **serviço.**

## <a name="plan-registration-policy"></a>Política de registo de planos

Os administradores devem determinar como os utilizadores irão registar os seus métodos. As organizações devem [permitir a nova experiência](howto-registration-mfa-sspr-combined.md) de registo combinado para o Azure MFA e o reset de senha de autosserviço (SSPR). O SSPR permite que os utilizadores reporem a sua palavra-passe de forma segura utilizando os mesmos métodos que utilizam para a autenticação de vários fatores. Recomendamos este registo combinado porque é uma excelente experiência para os utilizadores, com a capacidade de se registar uma vez para ambos os serviços. Permitir os mesmos métodos para SSPR e MFA Azure permitirá que os seus utilizadores estejam registados para utilizar ambas as funcionalidades.

### <a name="registration-with-identity-protection"></a>Registo com Proteção de Identidade

Se a sua organização estiver a utilizar a Proteção de Identidade do Diretório Ativo Azure, configure a política de [registo de MFA](../identity-protection/howto-mfa-policy.md) para levar os seus utilizadores a registarem-se da próxima vez que assinarem interactivamente.

### <a name="registration-without-identity-protection"></a>Registo sem Proteção de Identidade

Se a sua organização não tiver licenças que permitam a Proteção de Identidade, os utilizadores são solicitados a registar-se da próxima vez que o MFA for necessário no início do sessão. Os utilizadores podem não estar registados para mfa se não utilizarem aplicações protegidas com MFA. É importante que todos os utilizadores sejam registados de modo a que os maus atores não possam adivinhar a palavra-passe de um utilizador e registar-se para mfa em seu nome, assumindo efetivamente o controlo da conta.

#### <a name="enforcing-registration"></a>Aplicação do registo

Utilizando os seguintes passos, uma política de acesso condicional pode forçar os utilizadores a registarem-se para autenticação multi-factor

1. Crie um grupo, adicione todos os utilizadores que não estão registados atualmente.
2. Utilizando o Acesso Condicional, aplique a autenticação de vários fatores para este grupo para acesso a todos os recursos.
3. Periodicamente, reavalie a adesão ao grupo e remova os utilizadores que se tenham registado no grupo.

Pode identificar utilizadores de MFA Azure registados e não registados com comandos PowerShell que dependem do [módulo MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar utilizadores registados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar utilizadores não registados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores de MFA por utilizador para MFA baseado em Acesso Condicional

Se os seus utilizadores estivessem ativados utilizando a autenticação de multifactor azure ativada e forçada, o seguinte PowerShell pode ajudá-lo a efetuar a conversão para a Autenticação Multi-Factor Azure baseada em Acesso Condicional.

Execute esta PowerShell numa janela ISE ou guarde como um `.PS1` ficheiro para executar localmente.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Recentemente mudamos o comportamento e o script PowerShell acima em conformidade. Anteriormente, o guião salvou os métodos de MFA, desativou o MFA e restaurou os métodos. Isto já não é necessário agora que o comportamento padrão para desativar não limpa os métodos.

## <a name="plan-conditional-access-policies"></a>Políticas de Acesso Condicional do Plano

Para planear a sua estratégia de política de Acesso Condicional, que determinará quando são necessários MFA e outros controlos, consulte [o What is Conditional Access in Azure Ative Directory?](../conditional-access/overview.md)

É importante que evite ser inadvertidamente trancado fora do seu inquilino Azure AD. Pode mitigar o impacto desta falta inadvertida de acesso [administrativo, criando duas ou mais contas](../users-groups-roles/directory-emergency-access.md) de acesso de emergência no seu inquilino e excluindo-as da sua política de Acesso Condicional.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo do Azure.
1. Selecione **Nova política.**
   ![Criar uma política de acesso condicional para permitir o MFA para utilizadores do portal Azure em grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Forneça um nome significativo para a sua política.
1. No âmbito **dos utilizadores e grupos:**
   * No separador **Incluir,** selecione o botão de rádio **todos os utilizadores**
   * No separador **Excluir,** verifique a caixa para **Utilizadores e grupos** e escolha as suas contas de acesso de emergência.
   * Clique em **Concluído**.
1. Em **aplicativos Cloud**, selecione o botão de rádio **all apps cloud.**
   * OPCIONALmente: No separador **Excluir,** escolha aplicações em nuvem para as quais a sua organização não exija MFA.
   * Clique em **Concluído**.
1. Em **condições:**
   * OPCIONALmente: Se tiver ativado a Proteção de Identidade Azure, pode optar por avaliar o risco de entrada como parte da apólice.
   * OPCIONALmente: Se tiver configurado locais de confiança ou locais nomeados, pode especificar incluir ou excluir esses locais da apólice.
1. No âmbito do **Grant,** certifique-se de que o botão de rádio **grant access** é selecionado.
    * Verifique se a caixa **requer a autenticação de vários fatores.**
    * Clique em **Selecionar**.
1. Ignore a secção **sessão.**
1. Desative a **política ativa** para **o toggle**on . .
1. Clique em **Criar**.

## <a name="plan-integration-with-on-premises-systems"></a>Integração de planos com sistemas no local

Algumas aplicações antigas e no local que não autenticam diretamente contra a AD Azure exigem medidas adicionais para utilizar o MFA, incluindo:

* Aplicações antigas no local, que terão de utilizar o proxy da Aplicação.
* Aplicações RADIUS no local, que terão de utilizar adaptador MFA com servidor NPS.
* Aplicações AD FS no local, que terão de utilizar adaptador MFA com AD FS 2016 ou mais recentes.

As aplicações que autenticam diretamente com a AD Azure e têm autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem utilizar diretamente as políticas de Acesso Condicional.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Utilizar O MFA Azure com procuração de aplicação ad-ad azure

As candidaturas residentes no local podem ser publicadas ao seu inquilino Azure AD através do Procurador de [Aplicação Azure AD](../manage-apps/application-proxy.md) e podem tirar partido da Autenticação Multi-Factor Azure se estiverem configuradas para utilizar a pré-autenticação da AD Azure.

Estas aplicações estão sujeitas a políticas de Acesso Condicional que impõem a Autenticação Multi-Factor Azure, tal como qualquer outra aplicação integrada em Azure AD.

Da mesma forma, se a Autenticação Multi-Factor Azure for executada para todos os sign-ins do utilizador, as aplicações no local publicadas com o Proxy de Aplicação AD Azure serão protegidas.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integração da autenticação multi-factor Azure com servidor de política de rede

A extensão do Servidor de Política de Rede (NPS) para O MFA Azure adiciona capacidades de MFA baseadas na nuvem à sua infraestrutura de autenticação utilizando os seus servidores existentes. Com a extensão NPS, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicações telefónicas ao fluxo de autenticação existente. Esta integração tem as seguintes limitações:

* Com o protocolo CHAPv2, apenas são suportadas notificações push de aplicação autenticadora e chamada de voz.
* As políticas de acesso condicional não podem ser aplicadas.

A extensão NPS funciona como um adaptador entre o RADIUS e o Azure MFA baseado na nuvem para fornecer um segundo fator de autenticação para proteger as ligações [VPN,](howto-mfa-nps-extension-vpn.md) [Remote Desktop Gateway,](howto-mfa-nps-extension-rdg.md)ou outras aplicações capazes de RADIUS. Os utilizadores que se registem no Azure MFA neste ambiente serão desafiados para todas as tentativas de autenticação, a falta de políticas de Acesso Condicional significa que o MFA é sempre necessário.

#### <a name="implementing-your-nps-server"></a>Implementação do seu servidor NPS

Se tiver uma instância NPS implantada e já em uso, referência [Integre a infraestrutura de NPS existente com autenticação multi-factor Azure](howto-mfa-nps-extension.md). Se estiver a configurar o NPS pela primeira vez, consulte o Servidor de Política de [Rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para obter instruções. A orientação de resolução de problemas pode ser encontrada no artigo [Resolver mensagens de erro da extensão NPS para autenticação de multi-factores Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Prepare NPS para utilizadores que não estejam matriculados para MFA

Escolha o que acontece quando os utilizadores que não estão matriculados com MFA tentam autenticar. Utilize a definição `REQUIRE_USER_MATCH` de registo `HKLM\Software\Microsoft\AzureMFA` na trajetória de registo para controlar o comportamento da funcionalidade. Esta definição tem uma única opção de configuração.

| Chave | Valor | Predefinição |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADE / FALSO | Não definido (equivalente a TRUE) |

O objetivo desta definição é determinar o que fazer quando um utilizador não está inscrito para mfa. Os efeitos da alteração desta definição estão listados na tabela abaixo.

| Definições | Estado do MFA do utilizador | Efeitos |
| --- | --- | --- |
| A chave não existe | Não inscrito | Desafio do MFA não tem sucesso |
| Conjunto de valor para True / não definido | Não inscrito | Desafio do MFA não tem sucesso |
| Conjunto de chaves para falso | Não inscrito | Autenticação sem MFA |
| Conjunto de chaves para falso ou verdadeiro | Enrolled (Inscrito) | Deve autenticar com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrar com serviços da Federação de Diretório Ativo

Se a sua organização estiver federada com a Azure AD, pode utilizar a [Autenticação Multi-Factor Azure para garantir recursos AD FS,](multi-factor-authentication-get-started-adfs.md)tanto no local como na nuvem. O Azure MFA permite-lhe reduzir as palavras-passe e fornecer uma forma mais segura de autenticar. A partir do Windows Server 2016, já pode configurar o Azure MFA para a autenticação primária.

Ao contrário do AD FS no Windows Server 2012 R2, o adaptador AD FS 2016 Azure MFA integra-se diretamente com o Azure AD e não necessita de um servidor Azure MFA no local. O adaptador Azure MFA está integrado no Windows Server 2016, e não há necessidade de uma instalação adicional.

Ao utilizar o Azure MFA com AD FS 2016 e a aplicação-alvo está sujeita à política de Acesso Condicional, existem considerações adicionais:

* O Acesso Condicional está disponível quando a candidatura é uma parte de fundo para a Azure AD, federada com AD FS 2016 ou mais recente.
* O Acesso Condicional não está disponível quando a candidatura é uma parte de base para a AD FS 2016 ou AD FS 2019 e é gerido ou federado com AD FS 2016 ou AD FS 2019.
* O Acesso Condicional também não está disponível quando a AD FS 2016 ou AD FS 2019 estiver configurada para utilizar o Azure MFA como método de autenticação primária.

#### <a name="ad-fs-logging"></a>Exploração madeireira AD FS

O registo de registo sinuoso standard AD FS 2016 e 2019 no Registo de Segurança do Windows e no registo de administrador ad FS, contém informações sobre pedidos de autenticação e o seu sucesso ou falha. Os dados de registo de eventos dentro destes eventos indicarão se o Azure MFA foi utilizado. Por exemplo, um AD FS Auditing Event ID 1200 pode conter:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerir certificados

Em cada servidor AD FS, no computador local My Store, haverá um certificado Azure MFA auto-assinado intitulado OU=Microsoft AD FS Azure MFA, que contém a data de validade do certificado. Verifique o período de validade deste certificado em cada servidor AD FS para determinar a data de validade.

Se o período de validade dos seus certificados estiver perto da expiração, [gere e verifique um novo certificado MFA em cada servidor AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

As seguintes orientações detalham como gerir os certificados Azure MFA nos seus servidores AD FS. Quando configurar AD FS com MFA Azure, os `New-AdfsAzureMfaTenantCertificate` certificados gerados através do cmdlet PowerShell são válidos por dois anos. Renovar e instalar os certificados renovados antes da expiração de interrupções otadas no serviço MFA.

## <a name="implement-your-plan"></a>Implementar o seu plano

Agora que planeou a sua solução, pode implementar seguindo os passos abaixo:

1. Conheça os pré-requisitos necessários
   1. Implementar [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para quaisquer cenários híbridos
   1. Implemente [a Aplicação De Aplicação Azure AD Proxy](../manage-apps/application-proxy.md) para quaisquer aplicações no local publicadas para acesso à nuvem
   1. Implementar [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Garantir que os utilizadores atualizaram para versões suportadas do Microsoft Office com autenticação moderna ativada
1. Configurar [métodos de autenticação escolhidos](#choose-verification-options)
1. Defina as [localizações da sua rede nomeada](../conditional-access/location-condition.md#named-locations)
1. Selecione grupos para começar a lançar MFA.
1. Configure as suas políticas de [acesso condicional](#create-conditional-access-policy)
1. Configure a sua política de registo de MFA
   1. [MFA combinado e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Com [Proteção de Identidade](../identity-protection/howto-mfa-policy.md)
1. Envie comunicações de utilizadores e faça com que os utilizadores se inscrevam em[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Acompanhe quem está inscrito.](#identify-non-registered-users)

> [!TIP]
> Utilizadores de nuvem do governo podem inscrever-se em[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gerir a sua solução

Relatórios para o Azure MFA

A autenticação de vários fatores Azure fornece relatórios através do portal Azure:

| Relatório | Localização | Descrição |
| --- | --- | --- |
| Alertas de utilização e fraude | Azure AD > Sign-ins | Fornece informações sobre a utilização global, resumo do utilizador e detalhes do utilizador; bem como um histórico de alertas de fraude apresentados durante o intervalo de data especificado. |

## <a name="troubleshoot-mfa-issues"></a>Problemas problemas com mfa

Encontre soluções para problemas comuns com o Azure MFA no artigo de [autenticação multi-factor de Resolução de Problemas Azure Azure](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) no Microsoft Support Center.

## <a name="next-steps"></a>Passos seguintes

Para ver a autenticação azure multi-factor em ação, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-enable-azure-mfa.md)
