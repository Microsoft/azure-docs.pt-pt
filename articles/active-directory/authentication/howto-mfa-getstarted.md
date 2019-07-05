---
title: Planejar e executar uma implementação de multi-factor Authentication - Azure Active Directory
description: Planeamento da implementação do Microsoft Azure multi-factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 296486755f0935e4eb8dcdb663da46ef60ef38bc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441017"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planear uma implementação de multi-factor Authentication do Azure com base na cloud

As pessoas estão a ligar aos recursos organizacionais em cenários de cada vez mais complicados. As pessoas se conectam a partir de dispositivos pertencentes à organização, pessoais e públicos e desativar à rede empresarial com smartphones, tablets, PCs e laptops, muitas vezes em várias plataformas. Neste mundo sempre conectados, vários dispositivos e várias plataformas, a segurança de contas de utilizador é mais importante do que nunca. Palavras-passe, independentemente da respetiva complexidade, utilizada em várias plataformas, redes e dispositivos já não são suficientes para garantir a segurança da conta de utilizador, especialmente quando os usuários tendem a reutilização de palavras-passe em várias contas. Sofisticados phishing e outra ataques podem resultar em nomes de utilizador e palavras-passe que está a ser publicados e produtos vendidos em web escuro a engenharia social.

[O Azure multi-factor Authentication (MFA)](concept-mfa-howitworks.md) ajuda a salvaguardar o acesso aos dados e aplicações. Ele fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar [acesso condicional](../conditional-access/overview.md) tornar a solução de acordo com as suas necessidades específicas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implementação do Azure multi-factor Authentication, há itens de pré-requisitos que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| **Apenas na cloud** ambiente de identidade com autenticação moderna | **Não existem tarefas de pré-requisitos adicionais** |
| **Híbrido** cenários de identidade | [O Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implementada e identidades de utilizador são sincronizadas ou federadas com os locais Active Directory Domain Services com o Azure Active Directory. |
| As aplicações legadas publicados para o acesso à nuvem no local | O Azure AD [Proxy de aplicações](../manage-apps/application-proxy.md) é implementada. |
| Utilizar o MFA do Azure com a autenticação RADIUS | R [servidor de políticas de rede (NPS)](howto-mfa-nps-extension.md) é implementada. |
| Os utilizadores têm o Microsoft Office 2010 ou anterior ou Apple Mail para o iOS 11 ou anterior | Atualizar para o [Microsoft Office 2013 ou posterior](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e Apple mail para o iOS 12 ou posterior. Acesso condicional não é suportado pelos protocolos de autenticação. |

## <a name="plan-user-rollout"></a>Planear a implementação de utilizador

Seu plano de implementação MFA deve incluir uma implantação piloto seguida por fases de implementação que estão dentro de sua capacidade de suporte. Iniciar a sua distribuição por aplicação das políticas de acesso condicional para um pequeno grupo de utilizadores piloto. Depois de avaliar o efeito sobre o que os usuários piloto, processo usado e comportamentos de registo, pode adicionar mais grupos à política ou adicionar mais utilizadores para os grupos existentes.

### <a name="user-communications"></a>Comunicações de utilizador

É fundamental para informar os utilizadores, em planeadas comunicações, sobre as alterações futuras, os requisitos de registo do MFA do Azure e quaisquer ações de utilizador necessário. Recomendamos que o serviço de comunicações são desenvolvidas em conjunto com representantes de sua organização, por exemplo, os departamentos de comunicações, gerenciamento de alterações ou recursos humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e [documentação do utilizador final](../user-help/security-info-setup-signin.md) para ajudar a suas comunicações de rascunho. Pode enviar aos utilizadores [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) registar-se diretamente ao selecionar o **informações de segurança** ligações nessa página.

## <a name="deployment-considerations"></a>Considerações sobre implementação

O Azure multi-factor Authentication é implementada através da imposição de políticas com acesso condicional. R [política de acesso condicional](../conditional-access/overview.md) pode exigir que os usuários executem autenticação multifator quando determinados critérios forem atendidos, tais como:

* Todos os utilizadores, um utilizador específico, o membro de uma função, grupo ou atribuído
* Aplicação da cloud que está sendo acessada
* Plataforma de dispositivo
* Estado do dispositivo
* Localização de rede ou o localizados geograficamente endereço IP
* Aplicações de cliente
* Início de sessão risco (requer o Identity Protection)
* Dispositivo conforme
* Dispositivo de associado ao Azure AD híbrido
* Aplicação aprovada do cliente

Utilize os personalizáveis cartazes e modelos de e-mail na [materiais de implementação da autenticação multifator](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para implementar a autenticação multifator para sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Ativar a multi-factor Authentication com o acesso condicional

Políticas de acesso condicional impõem o registo, exigindo que os utilizadores de anular o registo concluir o registo no primeiro início de sessão, uma importante consideração de segurança.

[O Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribui para uma política de registo e políticas de deteção e remediação de risco automatizada para a história de multi-factor Authentication. As políticas podem ser criadas para forçar as alterações de palavra-passe quando existe uma ameaça de identidade comprometida ou exigir a MFA quando um início de sessão é considerado arriscado pelos seguintes [eventos](../reports-monitoring/concept-risk-events.md):

* Fuga de credenciais
* Inícios de sessão de endereços IP anónimos
* Deslocação impossível para localizações atípicas
* Inícios de sessão de localizações desconhecidas
* Inícios de sessão de dispositivos infetados
* Inícios de sessão de endereços IP com atividades suspeitas

Alguns dos eventos de risco detetados pelo Azure Active Directory Identity Protection ocorrem em tempo real e alguns requerem processamento offline. Os administradores podem optar por bloquear os utilizadores que apresentam comportamentos de risco e remediar manualmente, exigem uma alteração de palavra-passe ou exigem uma autenticação multifator como parte das suas políticas de acesso condicional.

## <a name="define-network-locations"></a>Definir localizações de rede

Recomendamos que as organizações a utilizam o acesso condicional para definir a sua rede usando [localizações com nome](../conditional-access/location-condition.md#named-locations). Se sua organização estiver a utilizar o Identity Protection, considere a utilização de políticas baseadas em risco, em vez de localizações com nome.

### <a name="configuring-a-named-location"></a>Configurar um local nomeado

1. Open **do Azure Active Directory** no portal do Azure
2. Clique em **acesso condicional**
3. Clique em **localizações com nome**
4. Clique em **nova localização**
5. Na **nome** campo, forneça um nome significativo
6. Selecione se está a definir o local usando intervalos de IP ou países/regiões
   1. Se utilizar intervalos de IP
      1. Decida se pretende marcar a localização como fidedigna. Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador. Apenas deve marca esta localização como fidedigna se souber os intervalos IP introduzidos estão bem estabelecidos e são credíveis na sua organização.
      2. Especifique os intervalos IP
   2. Se utilizar países/regiões
      1. Expanda o menu pendente e selecione os países ou regiões em que pretende definir para esta localização com nome.
      2. Decida se pretende incluir áreas desconhecidas. Áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Clique em **Criar**

## <a name="plan-authentication-methods"></a>Métodos de autenticação do plano

Os administradores podem escolher o [métodos de autenticação](../authentication/concept-authentication-methods.md) que pretende disponibilizar para os utilizadores. É importante permitir que mais do que um único método de autenticação para que os utilizadores têm um método de cópia de segurança disponível no caso do método principal não está disponível. Os métodos seguintes estão disponíveis para os administradores ativem:

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

Uma notificação push é enviada para a aplicação Microsoft Authenticator no seu dispositivo móvel. O utilizador vê a notificação e seleciona **aprovar** para concluir a verificação. Notificações push através de uma aplicação móvel fornecem a opção menos intrusiva para os utilizadores. Eles também são a opção mais fiável e segura, porque utilizam uma ligação de dados em vez de telefonia.

> [!NOTE]
> Se a organização tiver funcionários trabalhando em ou em viagem para a China, o **notificação através de aplicação móvel** método no **dispositivos Android** não funciona no país. Métodos alternativos devem ser disponibilizados para os utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

Uma aplicação móvel, como a aplicação Microsoft Authenticator gera um novo código de verificação OATH cada 30 segundos. O utilizador introduz o código de verificação a interface de início de sessão. A opção de aplicação móvel pode ser utilizada se ou não o telefone terá uma data ou um sinal via rede móvel.

### <a name="call-to-phone"></a>Ligar para telefone

Uma chamada de voz automatizada é colocada ao usuário. O utilizador atender a chamada e pressionamentos **#** no teclado do telefone para aprovar a autenticação. Chamada para o telefone é um método de cópia de segurança importante para o código de notificação ou verificação de uma aplicação móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

É enviada uma mensagem de texto que contém um código de verificação para o utilizador, é pedido ao utilizador para introduzir o código de verificação na interface de início de sessão.

### <a name="choose-verification-options"></a>Escolher opções de verificação

1. Navegue até **do Azure Active Directory**, **utilizadores**, **multi-factor Authentication**.

   ![Aceder ao portal do multi-factor Authentication do painel de utilizadores do Azure AD no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. O novo separador que se abre, navegue até **definições do serviço**.
1. Sob **opções de verificação**, verificar todas as caixas para os métodos disponíveis para os utilizadores.

   ![Configurar métodos de verificação no separador de definições do serviço de multi-factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Guardar**.
1. Fechar o **definições do serviço** separador.

## <a name="plan-registration-policy"></a>Planear a política de registo

Os administradores devem determinar como os utilizadores irão registar seus métodos. As organizações devem [ativar a nova experiência de registo combinado](howto-registration-mfa-sspr-combined.md) para MFA do Azure e a palavra-passe self-service (SSPR) de reposição. SSPR permite aos utilizadores para repor a palavra-passe de forma segura utilizando os mesmos métodos que utilizam a autenticação multifator. Recomendamos que isso combinado de registo, atualmente em pré-visualização pública, porque é uma excelente experiência para os utilizadores, com a capacidade de registar uma vez para ambos os serviços. Ativar os mesmos métodos para SSPR e o MFA do Azure irá permitir que os utilizadores estar registada para utilizar os dois recursos.

### <a name="registration-with-identity-protection"></a>Registo com o Identity Protection

Se sua organização estiver a utilizar o Azure Active Directory Identity Protection, [configurar a política de registo MFA](../identity-protection/howto-mfa-policy.md) para solicitar os seus utilizadores se registarem na próxima vez que iniciar sessão interativamente.

### <a name="registration-without-identity-protection"></a>Registo sem proteção de identidade

Se a sua organização não tiver licenças que ativar o Identity Protection, é pedido aos utilizadores para se registar na próxima vez que a MFA é necessária ao iniciar sessão. Os utilizadores não podem ser registados para MFA se eles não utilizarem aplicações protegidas com a MFA. É importante obter todos os utilizadores que registou, para que pessoas mal-intencionadas não é possível adivinhar a palavra-passe de um utilizador e registe-se para a MFA no seu nome, efetivamente assumir o controlo da conta.

#### <a name="enforcing-registration"></a>Impor o registo

Utilize os seguintes passos um acesso condicional política pode forçar os utilizadores para se registrar para o multi-factor Authentication

1. Crie um grupo, adicione todos os utilizadores não estão atualmente registados.
2. Utilizar acesso condicional, impor o multi-factor authentication para este grupo de acesso a todos os recursos.
3. Periodicamente, reavaliar a associação de grupo e remover utilizadores que registaram do grupo.

Pode identificar registados e não registrados utilizadores do MFA do Azure com comandos do PowerShell que contam com o [módulo do PowerShell do MSOnline](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar utilizadores registados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar os usuários não registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores do MFA por utilizador para o acesso condicional com base em MFA

Se os utilizadores foram ativados com por utilizador ativado e imposta Azure multi-factor Authentication o PowerShell seguinte pode ajudá-lo a fazer a conversão para o acesso condicional com base em multi-factor Authentication.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-MFA {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="plan-conditional-access-policies"></a>Planear políticas de acesso condicional

Para planejar sua estratégia de política de acesso condicional, que irá determinar quando são necessários o MFA e outros controles, consulte [o que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md).

É importante que impeça o inadvertidamente a ser impedido de aceder ao inquilino do Azure AD. Pode mitigar o impacto dessa falta inadvertida de acesso administrativo por [a criação de dois ou mais contas de acesso de emergência no seu inquilino](../users-groups-roles/directory-emergency-access.md) e excluindo-os da sua política de acesso condicional.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta de administrador global.
1. Navegue até **do Azure Active Directory**, **acesso condicional**.
1. Selecione **nova política**.
1. Forneça um nome significativo para a sua política.
1. Sob **utilizadores e grupos**:
   * Sobre o **inclusão** separador, selecione a **todos os utilizadores** botão de opção
   * Sobre o **excluir** separador, marque a caixa **utilizadores e grupos** e escolha as suas contas de acesso de emergência.
   * Clique em **Concluído**.
1. Sob **aplicações na Cloud**, selecione a **todas as aplicações na cloud** botão de opção.
   * OPCIONALMENTE: Sobre o **excluir** separador, selecione aplicações na cloud que sua organização não exija a MFA para.
   * Clique em **Concluído**.
1. Sob **condições** secção:
   * OPCIONALMENTE: Se tiver ativado o Azure Identity Protection, pode optar por avaliar o risco de início de sessão como parte da política.
   * OPCIONALMENTE: Se tiver configurado a locais confiáveis ou localizações com nome, poderá especificar a incluir ou excluir esses locais da política.
1. Sob **concessão**, certifique-se a **conceder acesso** botão de opção está selecionada.
    * Marque a caixa **exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignorar a **sessão** secção.
1. Definir o **ativar política** alternar para **no**.
1. Clique em **Criar**.

![Criar uma política de acesso condicional para ativar a MFA para os utilizadores do portal do Azure no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planear a integração com sistemas no local

Alguns aplicativos legados e no local que não autenticam diretamente no Azure AD exigem passos adicionais para utilizar a MFA, incluindo:

* Herdado aplicações no local, que precisa de utilizar o proxy de aplicações.
* Aplicações no local RADIUS, que terá de utilizar o adaptador MFA com o servidor NPS.
* Aplicações no local do AD FS, que terá de utilizar o adaptador MFA com AD FS 2016.

Aplicativos que autenticar-se diretamente com o Azure AD e possuem autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem fazer de uso do acesso condicional políticas diretamente.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Utilizar o MFA do Azure com o Proxy de aplicações do Azure AD

Aplicações que residem no local pode ser publicado para o Azure AD através de inquilino [Proxy de aplicações do Azure AD](../manage-apps/application-proxy.md) e pode tirar partido do Azure multi-factor Authentication se estiverem configurados para utilizar o Azure AD pré-autenticação.

Esses aplicativos são sujeitos a políticas de acesso condicional que impõem o multi-factor Authentication do Azure, assim como qualquer outra aplicação do Azure integradas no AD.

Da mesma forma, se o Azure multi-factor Authentication é imposto para todos os utilizadores inícios de sessão, no local serão protegidas aplicações publicadas com o Proxy de aplicações do Azure AD.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrar o multi-factor Authentication com o servidor de políticas de rede

A extensão de servidor de políticas de rede (NPS) para o MFA do Azure adiciona capacidades MFA com base na cloud à sua infraestrutura de autenticação através de seus servidores existentes. Com a extensão NPS, pode adicionar a chamada telefónica, mensagem de texto ou verificação de aplicação do telefone para o fluxo de autenticação existente. Esta integração tem as seguintes limitações:

* Com o protocolo de CHAPv2, são suportados apenas notificações de push da aplicação de autenticador e chamada de voz.
* Não não possível aplicar políticas de acesso condicional.

A extensão NPS age como um adaptador entre RADIUS e baseado na nuvem do MFA do Azure para fornecer um segundo fator de autenticação para proteger [VPN](howto-mfa-nps-extension-vpn.md), [ligações de Gateway de ambiente de trabalho remoto](howto-mfa-nps-extension-rdg.md), ou outros com capacidade de RADIUS aplicações. Os utilizadores que Registre-se para a MFA do Azure neste ambiente vai ser submetido à todas as tentativas de autenticação, a falta de políticas de acesso condicional significa que a MFA é sempre necessária.

#### <a name="implementing-your-nps-server"></a>Implementar o servidor NPS

Se tiver uma instância NPS implementada e em utilização, de referência [integrar a infraestrutura NPS existente com o Azure multi-factor Authentication](howto-mfa-nps-extension.md). Se estiver a configurar NPS pela primeira vez, consulte [servidor de políticas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para obter instruções. Documentação de orientação de resolução de problemas pode ser encontrada no artigo [resolver mensagens de erro da extensão NPS para multi-factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparar o NPS para os utilizadores que não estejam inscritos para a MFA

Escolha o que acontece quando os utilizadores que não estejam inscritos com a MFA tentarem autenticar. Utilize a definição de registo `REQUIRE_USER_MATCH` no caminho do registo `HKLM\Software\Microsoft\AzureMFA` para controlar o comportamento de funcionalidade. Esta definição não tem uma opção de configuração única.

| Chave | Value | Predefinição |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADEIRO / FALSO | Não definido (equivalente ao verdadeiro) |

O objetivo desta definição é determinar o que fazer quando um utilizador não estiver inscrito para a MFA. Os efeitos da alteração desta definição estão listados na tabela abaixo.

| Definições | Estado MFA de utilizador | Efeitos |
| --- | --- | --- |
| Chave não existe | Não inscrito | Submissão da MFA for concluído com êxito |
| Valor definido como True / não definido | Não inscrito | Submissão da MFA for concluído com êxito |
| Chave definida como False | Não inscrito | Autenticação sem MFA |
| Chave definida como False ou verdadeiro | Inscritos | Tem de ser autenticado com a MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrar com serviços de Federação do Active Directory

Se sua organização estiver federada com o Azure AD, pode utilizar [multi-factor Authentication para proteger os recursos do AD FS](multi-factor-authentication-get-started-adfs.md), tanto no local e na cloud. MFA do Azure permite-lhe reduzir as palavras-passe e fornecer uma forma mais segura de autenticar. A partir do Windows Server 2016, pode agora configurar MFA do Azure para a autenticação primária.

Ao contrário com o AD FS no Windows Server 2012 R2, o adaptador do AD FS 2016 MFA do Azure integra-se diretamente com o Azure AD e não necessita de um servidor de MFA do Azure no local. O adaptador de MFA do Azure está incorporado no Windows Server 2016 e não é necessário para uma instalação adicional.

Quando utilizar o MFA do Azure com o AD FS 2016 e o aplicativo de destino está sujeita à política de acesso condicional, existem considerações adicionais:

* Acesso condicional está disponível quando o aplicativo é uma entidade confiadora para o Azure AD, federada com o AD FS 2016.
* Acesso condicional não está disponível quando o aplicativo é uma entidade confiadora para o AD FS 2016 e é gerido ou Federado com o AD FS 2016.
* Acesso condicional também não está disponível quando o AD FS 2016 está configurado para utilizar o MFA do Azure como método de autenticação primária.

#### <a name="ad-fs-logging"></a>Registo do AD FS

Padrão do AD FS 2016 iniciar sessão no registo de segurança do Windows e o registo de administrador do AD FS, contém informações sobre pedidos de autenticação e o sucesso ou fracasso. Dados de registo de eventos dentro desses eventos irão indicar se o MFA do Azure foi utilizado. Por exemplo, pode conter um ID de evento de auditoria FS AD 1200:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerir certificados

Em cada servidor do AD FS, no computador local Store My, haverá uma MFA do Azure com assinatura automática de certificado com o título de UO = Microsoft AD FS do MFA do Azure, que contém a data de expiração do certificado. Verificar o período de validade deste certificado em cada servidor do AD FS para determinar a data de expiração.

Se o período de validade dos seus certificados está prestes a expirar, [gerar e verificar um novo certificado MFA em cada servidor do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

As seguintes orientações detalhes sobre como gerir os certificados MFA do Azure nos seus servidores do AD FS. Quando configurar o AD FS com o MFA do Azure, os certificados gerados por meio do `New-AdfsAzureMfaTenantCertificate` cmdlet do PowerShell são válidas durante dois anos. Renovar e instalar os certificados renovados antes da expiração para ovoid interrupções no serviço MFA.

## <a name="implement-your-plan"></a>Implementar o seu plano

Agora que tenha se planejado sua solução, pode implementar, seguindo os passos abaixo:

1. Cumprir os pré-requisitos necessários
   1. Implementar [do Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para qualquer cenários híbridos
   1. Implementar [Proxy de aplicações do Azure AD](../manage-apps/application-proxy.md) para em todas as aplicações no local publicados para o acesso à nuvem
   1. Implementar [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Certifique-se de que os utilizadores atualizaram para versões suportadas do Microsoft Office com autenticação moderna ativada
1. Configurar escolhida [métodos de autenticação](#choose-verification-options)
1. Definir sua [localizações de rede com nome](../conditional-access/location-condition.md#named-locations)
1. Selecione grupos para começar a implementar o MFA.
1. Configurar seu [políticas de acesso condicional](#create-conditional-access-policy)
1. Configurar a política de registo MFA
   1. [MFA combinado e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Com [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Envie comunicações de utilizador e obter os utilizadores para se inscreverem em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Mantenha um registo de quem está inscrito](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Gerir a sua solução

Relatórios para a MFA do Azure

O Azure multi-factor Authentication fornece relatórios através do portal do Azure:

| Relatório | Location | Descrição |
| --- | --- | --- |
| Alertas de fraude e de utilização | O Azure AD > inícios de sessão | Fornece informações sobre a utilização global, resumo por utilizador e detalhes de utilizador como um histórico de alertas de fraude apresentados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Resolver problemas MFA

Encontrar soluções para problemas comuns da MFA do Azure com o [artigo de resolução de problemas do Azure multi-factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) do Microsoft Support Center.

## <a name="next-steps"></a>Passos Seguintes

* [What are authentication methods?](concept-authentication-methods.md) (O que são os métodos de autenticação?)
* [Ativar o registo convergido para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD](concept-registration-mfa-sspr-converged.md)
* Por que foi que o usuário é solicitado ou não lhe for pedido para executar a MFA? Consulte a secção [relatório de inícios de sessão do Azure AD nos relatórios de documento de multi-factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
