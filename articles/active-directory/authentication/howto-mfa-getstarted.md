---
title: Implantar a autenticação multifator do Azure-Azure Active Directory
description: Planejamento de implantação do Autenticação Multifator do Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e91a82442b2d436736719d629f70c8dec0e5a6c
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558543"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planejando uma implantação da autenticação multifator do Azure baseada em nuvem

As pessoas estão se conectando a recursos organizacionais em cenários cada vez mais complicados. As pessoas se conectam de dispositivos corporativos, pessoais e públicos na rede corporativa usando Smart Phones, tablets, PCs e laptops, geralmente em várias plataformas. Nesse mundo sempre conectado, de vários dispositivos e de várias plataformas, a segurança das contas de usuário é mais importante do que nunca. As senhas, não importa sua complexidade, usadas em dispositivos, redes e plataformas não são mais suficientes para garantir a segurança da conta de usuário, especialmente quando os usuários tendem a reutilizar senhas em contas. Um phishing sofisticado e outros ataques de engenharia social podem resultar na postagem e venda de nomes de acessados e de senhas na Web escura.

A [autenticação multifator do Azure (MFA)](concept-mfa-howitworks.md) ajuda a proteger o acesso a dados e aplicativos. Ele fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar o [acesso condicional](../conditional-access/overview.md) para fazer com que a solução atenda às suas necessidades específicas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implantação da autenticação multifator do Azure, há itens de pré-requisito que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| Ambiente **de identidade somente em nuvem** com autenticação moderna | **Nenhuma tarefa de pré-requisito adicional** |
| Cenários de identidade **híbrida** | [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) é implantado e as identidades de usuário são sincronizadas ou federadas com o Active Directory Domain Services local com Azure Active Directory. |
| Aplicativos herdados locais publicados para acesso à nuvem | O [proxy de aplicativo](../manage-apps/application-proxy.md) do Azure AD é implantado. |
| Usando o Azure MFA com autenticação RADIUS | Um [servidor de diretivas de rede (NPS)](howto-mfa-nps-extension.md) é implantado. |
| Os usuários têm Microsoft Office 2010 ou anterior ou o Apple mail para iOS 11 ou anterior | Atualize para o [Microsoft Office 2013 ou posterior](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e o Apple mail para IOS 12 ou posterior. Não há suporte para o acesso condicional por protocolos de autenticação herdados. |

## <a name="plan-user-rollout"></a>Planejar a distribuição do usuário

Seu plano de distribuição do MFA deve incluir uma implantação piloto seguida de ondas de implantação que estão dentro de sua capacidade de suporte. Comece sua distribuição aplicando suas políticas de acesso condicional a um pequeno grupo de usuários piloto. Depois de avaliar o efeito sobre os usuários piloto, os processos usados e os comportamentos de registro, você pode adicionar mais grupos à política ou adicionar mais usuários aos grupos existentes.

### <a name="user-communications"></a>Comunicações do usuário

É essencial informar os usuários, em comunicações planejadas, sobre alterações futuras, requisitos de registro do Azure MFA e quaisquer ações de usuário necessárias. Recomendamos que as comunicações sejam desenvolvidas em conjunto com os representantes de dentro de sua organização, como um departamento de comunicações, gerenciamento de alterações ou recursos humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e documentação do [usuário final](../user-help/security-info-setup-signin.md) para ajudar a rascunhar suas comunicações. Você pode enviar usuários para [https://myprofile.microsoft.com](https://myprofile.microsoft.com) registrar-se diretamente, selecionando os links de **informações de segurança** nessa página.

## <a name="deployment-considerations"></a>Considerações sobre implementação

A autenticação multifator do Azure é implantada pela imposição de políticas com acesso condicional. Uma [política de acesso condicional](../conditional-access/overview.md) pode exigir que os usuários executem a autenticação multifator quando determinados critérios forem atendidos, como:

* Todos os usuários, um usuário específico, um membro de um grupo ou uma função atribuída
* Aplicativo de nuvem específico que está sendo acessado
* Plataforma do dispositivo
* Estado do dispositivo
* Local de rede ou endereço IP localizado geograficamente
* Aplicações de cliente
* Risco de entrada (requer a proteção de identidade)
* Dispositivo conforme
* Dispositivo ingressado no Azure AD híbrido
* Aplicativo cliente aprovado

Use os cartazes personalizáveis e modelos de email em [materiais de distribuição da autenticação multifator](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para distribuir a autenticação multifator para sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitar a autenticação multifator com acesso condicional

As políticas de acesso condicional impõem o registro, exigindo que usuários não registrados concluam o registro na primeira entrada, uma consideração de segurança importante.

[Azure ad Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribui para uma política de registro e para detecção automatizada de riscos e políticas de correção para a história da autenticação multifator do Azure. As políticas podem ser criadas para forçar alterações de senha quando há uma ameaça de identidade comprometida ou exigir MFA quando uma entrada é considerada arriscada pelos seguintes [eventos](../reports-monitoring/concept-risk-events.md):

* Credenciais vazadas
* Inícios de sessão de endereços IP anónimos
* Deslocação impossível para localizações atípicas
* Inícios de sessão de localizações desconhecidas
* Inícios de sessão de dispositivos infetados
* Entradas de endereços IP com atividades suspeitas

Algumas das detecções de risco detectadas pelo Azure Active Directory Identity Protection ocorrem em tempo real e algumas exigem processamento offline. Os administradores podem optar por bloquear os usuários que apresentam comportamentos arriscados e corrigir manualmente, exigir uma alteração de senha ou exigir uma autenticação multifator como parte de suas políticas de acesso condicional.

## <a name="define-network-locations"></a>Definir locais de rede

Recomendamos que as organizações usem o acesso condicional para definir sua rede usando [locais nomeados](../conditional-access/location-condition.md#named-locations). Se sua organização estiver usando a proteção de identidade, considere o uso de políticas baseadas em risco em vez de locais nomeados.

### <a name="configuring-a-named-location"></a>Configurando um local nomeado

1. Abrir **Azure Active Directory** no portal do Azure
2. Clique em **acesso condicional**
3. Clique em **locais nomeados**
4. Clique em **novo local**
5. No campo **nome** , forneça um nome significativo
6. Selecione se você está definindo o local usando intervalos de IP ou países/regiões
   1. Se estiver usando intervalos de IP
      1. Decida se deseja marcar o local como confiável. Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador. Marque esta localização como confiável se você souber que os intervalos de IP inseridos são estabelecidos e confiáveis em sua organização.
      2. Especificar os intervalos de IP
   2. Se estiver usando países/regiões
      1. Expanda o menu suspenso e selecione os países ou regiões que você deseja definir para esse local nomeado.
      2. Decida se as áreas desconhecidas devem ser incluídas. Áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Clique em **Criar**.

## <a name="plan-authentication-methods"></a>Planejar métodos de autenticação

Os administradores podem escolher os [métodos de autenticação](../authentication/concept-authentication-methods.md) que desejam disponibilizar para os usuários. É importante permitir mais do que um único método de autenticação para que os usuários tenham um método de backup disponível caso o método principal não esteja disponível. Os seguintes métodos estão disponíveis para que os administradores habilitem:

### <a name="notification-through-mobile-app"></a>Notificação por meio do aplicativo móvel

Uma notificação por push é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel. O usuário exibe a notificação e seleciona **aprovar** para concluir a verificação. As notificações por Push por meio de um aplicativo móvel fornecem a opção menos invasiva para os usuários. Eles também são a opção mais confiável e segura porque usam uma conexão de dados em vez de telefonia.

> [!NOTE]
> Se sua organização tiver funcionários trabalhando ou viajando para a China, a **notificação por meio** do método de aplicativo móvel em **dispositivos Android** não funcionará nesse país. Os métodos alternativos devem ser disponibilizados para esses usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação do aplicativo móvel

Um aplicativo móvel como o Microsoft Authenticator aplicativo gera um novo código de verificação OATH a cada 30 segundos. O usuário insere o código de verificação na interface de entrada. A opção aplicativo móvel pode ser usada independentemente de o telefone ter ou não um sinal de celular ou de dados.

### <a name="call-to-phone"></a>Ligar para telefone

Uma chamada de voz automática é colocada para o usuário. O usuário responde à chamada e pressiona **#** no teclado do telefone para aprovar sua autenticação. A chamada para Phone é um excelente método de backup para notificação ou código de verificação de um aplicativo móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto que contém um código de verificação é enviada ao usuário, o usuário é solicitado a inserir o código de verificação na interface de entrada.

### <a name="choose-verification-options"></a>Escolher opções de verificação

1. Navegue até **Azure Active Directory**, **usuários**, **autenticação multifator**.

   ![Acessando o portal de autenticação multifator da folha usuários do Azure AD no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Na nova guia que é aberta, navegue até **configurações de serviço**.
1. Em **Opções de verificação**, marque todas as caixas para os métodos disponíveis para os usuários.

   ![Configurando métodos de verificação na guia Configurações do serviço de autenticação multifator](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Guardar**.
1. Feche a guia **configurações de serviço** .

## <a name="plan-registration-policy"></a>Política de registro de plano

Os administradores devem determinar como os usuários registrarão seus métodos. As organizações devem [habilitar a nova experiência de registro combinada](howto-registration-mfa-sspr-combined.md) para MFA do Azure e redefinição de senha de autoatendimento (SSPR). O SSPR permite que os usuários redefinam sua senha de maneira segura usando os mesmos métodos que usam para autenticação multifator. Recomendamos esse registro combinado, atualmente em visualização pública, porque é uma ótima experiência para os usuários, com a capacidade de registrar uma vez para ambos os serviços. Habilitar os mesmos métodos para SSPR e Azure MFA permitirá que os usuários sejam registrados para usar os dois recursos.

### <a name="registration-with-identity-protection"></a>Registro com proteção de identidade

Se sua organização estiver usando Azure Active Directory Identity Protection, [Configure a política de registro de MFA](../identity-protection/howto-mfa-policy.md) para solicitar que os usuários se registrem na próxima vez que entrarem de forma interativa.

### <a name="registration-without-identity-protection"></a>Registro sem proteção de identidade

Se sua organização não tiver licenças que habilitam a proteção de identidade, os usuários serão solicitados a se registrar na próxima vez que a MFA for necessária na entrada. Os usuários não poderão ser registrados para MFA se não usarem aplicativos protegidos com MFA. É importante obter todos os usuários registrados para que atores ruins não possam adivinhar a senha de um usuário e se registrar para MFA em seu nome, efetivamente assumindo o controle da conta.

#### <a name="enforcing-registration"></a>Impondo registro

Usando as etapas a seguir, uma política de acesso condicional pode forçar os usuários a se registrarem para a autenticação multifator

1. Crie um grupo, adicione todos os usuários que não estão registrados no momento.
2. Usando o acesso condicional, imponha a autenticação multifator para esse grupo para acesso a todos os recursos.
3. Periodicamente, reavalie a associação de grupo e remova os usuários que se registraram do grupo.

Você pode identificar usuários do Azure MFA registrados e não registrados com comandos do PowerShell que dependem do [módulo do PowerShell do MSOnline](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar usuários registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar usuários não registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter usuários de MFA por usuário para MFA baseada em acesso condicional

Se os usuários tiverem sido habilitados usando a autenticação multifator habilitada por usuário e impostas, o PowerShell a seguir poderá ajudá-lo a fazer a conversão para o acesso condicional com base na autenticação multifator do Azure.

Execute este PowerShell em uma janela do ISE ou salve como um. Arquivo PS1 a ser executado localmente.

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
> Alteramos recentemente o comportamento e o script do PowerShell acima de acordo. Anteriormente, o script economizou os métodos de MFA, desabilitou a MFA e restaurei os métodos. Isso não é mais necessário agora que o comportamento padrão para desabilitar não limpa os métodos.

## <a name="plan-conditional-access-policies"></a>Planejar políticas de acesso condicional

Para planejar sua estratégia de política de acesso condicional, que determinará quando a MFA e outros controles são necessários, consulte [o que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md).

É importante que você impeça o bloqueio inadvertidamente do seu locatário do Azure AD. Você pode mitigar o impacto dessa falta inadvertida de acesso administrativo [criando duas ou mais contas de acesso de emergência em seu locatário](../users-groups-roles/directory-emergency-access.md) e excluindo-as da sua política de acesso condicional.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue até **Azure Active Directory**, **acesso condicional**.
1. Selecione **nova política**.
   ![criar uma política de acesso condicional para habilitar a MFA para portal do Azure usuários no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Forneça um nome significativo para sua política.
1. Em **usuários e grupos**:
   * Na guia **incluir** , selecione o botão de opção **todos os usuários**
   * Na guia **excluir** , marque a caixa de **usuários e grupos** e escolha suas contas de acesso de emergência.
   * Clique em **Concluído**.
1. Em **aplicativos de nuvem**, selecione o botão de opção **todos os aplicativos de nuvem** .
   * OPCIONALmente: na guia **excluir** , escolha aplicativos de nuvem para os quais sua organização não requer MFA.
   * Clique em **Concluído**.
1. Na seção **condições** :
   * OPCIONALmente: se você tiver habilitado a proteção de identidade do Azure, poderá optar por avaliar o risco de entrada como parte da política.
   * OPCIONALmente: se você tiver configurado locais confiáveis ou locais nomeados, poderá especificar para incluir ou excluir esses locais da política.
1. Em **conceder**, verifique se o botão de opção **conceder acesso** está selecionado.
    * Marque a caixa para **exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignore a seção de **sessão** .
1. Defina ativar/desativar **política** como **ativado**.
1. Clique em **Criar**.

## <a name="plan-integration-with-on-premises-systems"></a>Planejar a integração com sistemas locais

Alguns aplicativos herdados e locais que não se autenticam diretamente no Azure AD exigem etapas adicionais para usar a MFA, incluindo:

* Aplicativos locais herdados, que precisarão usar o proxy de aplicativo.
* Aplicativos RADIUS locais, que precisarão usar o adaptador MFA com o servidor NPS.
* Os aplicativos AD FS locais, que precisarão usar o adaptador MFA com AD FS 2016 ou mais recente.

Aplicativos que se autenticam diretamente com o Azure AD e têm autenticação moderna (WS-enalimentado, SAML, OAuth, OpenID Connect) podem fazer uso de políticas de acesso condicional diretamente.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usar o Azure MFA com o Azure Proxy de Aplicativo do AD

Os aplicativos que residem localmente podem ser publicados em seu locatário do Azure AD por meio do [azure proxy de aplicativo do AD](../manage-apps/application-proxy.md) e podem aproveitar a autenticação multifator do Azure se estiverem configurados para usar a pré-autenticação do Azure AD.

Esses aplicativos estão sujeitos a políticas de acesso condicional que impõem a autenticação multifator do Azure, assim como qualquer outro aplicativo integrado ao Azure AD.

Da mesma forma, se a autenticação multifator do Azure for imposta para todas as entradas de usuário, os aplicativos locais publicados com o Azure Proxy de Aplicativo do AD serão protegidos.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrando a autenticação multifator do Azure com o servidor de políticas de rede

A extensão do NPS (servidor de políticas de rede) para o Azure MFA adiciona recursos de MFA baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão do NPS, você pode adicionar a verificação de chamada telefônica, mensagem de texto ou aplicativo de telefone ao seu fluxo de autenticação existente. Essa integração tem as seguintes limitações:

* Com o protocolo CHAPv2, somente as notificações por push do aplicativo autenticador e a chamada de voz têm suporte.
* As políticas de acesso condicional não podem ser aplicadas.

A extensão do NPS atua como um adaptador entre o RADIUS e o Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para proteger [VPN](howto-mfa-nps-extension-vpn.md), [área de trabalho remota conexões de gateway](howto-mfa-nps-extension-rdg.md)ou outros aplicativos compatíveis com RADIUS. Os usuários que se registram para o Azure MFA nesse ambiente serão desafiados em todas as tentativas de autenticação, a falta de políticas de acesso condicional significa que a MFA é sempre necessária.

#### <a name="implementing-your-nps-server"></a>Implementando seu servidor NPS

Se você já tiver uma instância do NPS implantada e em uso, [a referência integrará sua infraestrutura de NPS existente com a autenticação multifator do Azure](howto-mfa-nps-extension.md). Se você estiver configurando o NPS pela primeira vez, consulte [servidor de diretivas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para obter instruções. As diretrizes de solução de problemas podem ser encontradas no artigo [resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparar o NPS para usuários que não estão registrados para MFA

Escolha o que acontece quando os usuários que não estão registrados com o MFA tentam se autenticar. Use a configuração do registro `REQUIRE_USER_MATCH` no caminho do registro `HKLM\Software\Microsoft\AzureMFA` para controlar o comportamento do recurso. Essa configuração tem uma única opção de configuração.

| Chave | Valor | Predefinição |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADEIRO/FALSO | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não está registrado para MFA. Os efeitos da alteração dessa configuração são listados na tabela a seguir.

| Definições | Status de MFA do usuário | Effect |
| --- | --- | --- |
| A chave não existe | Não registrado | O desafio de MFA não foi bem-sucedido |
| Valor definido como true/not set | Não registrado | O desafio de MFA não foi bem-sucedido |
| Chave definida como false | Não registrado | Autenticação sem MFA |
| Chave definida como falsa ou verdadeira | Registrados | Deve autenticar com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrar com o Serviços de Federação do Active Directory (AD FS)

Se sua organização for federada com o Azure AD, você poderá usar a [autenticação multifator do Azure para proteger AD FS recursos](multi-factor-authentication-get-started-adfs.md), tanto localmente quanto na nuvem. O Azure MFA permite que você reduza as senhas e forneça uma maneira mais segura de autenticar. A partir do Windows Server 2016, agora você pode configurar o Azure MFA para autenticação primária.

Ao contrário do AD FS no Windows Server 2012 R2, o adaptador do Azure MFA AD FS 2016 é integrado diretamente ao Azure AD e não requer um servidor Azure MFA local. O adaptador do Azure MFA é integrado ao Windows Server 2016 e não há necessidade de uma instalação adicional.

Ao usar o Azure MFA com AD FS 2016 e o aplicativo de destino está sujeito à política de acesso condicional, há considerações adicionais:

* O acesso condicional está disponível quando o aplicativo é uma terceira parte confiável do Azure AD, federado com AD FS 2016 ou mais recente.
* O acesso condicional não está disponível quando o aplicativo é uma terceira parte confiável para AD FS 2016 ou AD FS 2019 e é gerenciado ou federado com AD FS 2016 ou AD FS 2019.
* O acesso condicional também não está disponível quando AD FS 2016 ou AD FS 2019 está configurado para usar o Azure MFA como o método de autenticação principal.

#### <a name="ad-fs-logging"></a>Log de AD FS

O log padrão AD FS 2016 e 2019 no log de segurança do Windows e no log de administração do AD FS, contém informações sobre solicitações de autenticação e seu êxito ou falha. Os dados do log de eventos dentro desses eventos indicarão se o Azure MFA foi usado. Por exemplo, um AD FS ID de evento 1200 de auditoria pode conter:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerenciar certificados

Em cada servidor de AD FS, no computador local meu repositório, haverá um certificado do Azure MFA autoassinado intitulado OU = Microsoft AD FS Azure MFA, que contém a data de expiração do certificado. Verifique o período de validade deste certificado em cada servidor de AD FS para determinar a data de validade.

Se o período de validade de seus certificados estiver se aproximando da expiração, [gere e verifique um novo certificado MFA em cada servidor de AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

As diretrizes a seguir detalham como gerenciar os certificados do Azure MFA em seus servidores de AD FS. Quando você configura AD FS com o Azure MFA, os certificados gerados por meio do cmdlet `New-AdfsAzureMfaTenantCertificate` do PowerShell são válidos por 2 anos. Renove e instale os certificados renovados antes da expiração para Ovoid interrupções no serviço MFA.

## <a name="implement-your-plan"></a>Implementar seu plano

Agora que você planejou sua solução, poderá implementar o seguindo as etapas abaixo:

1. Atender a todos os pré-requisitos necessários
   1. Implantar [Azure ad Connect](../hybrid/whatis-hybrid-identity.md) para cenários híbridos
   1. Implantar [proxy de aplicativo do AD do Azure](../manage-apps/application-proxy.md) para em qualquer aplicativo local publicado para acesso à nuvem
   1. Implantar o [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Verifique se os usuários atualizaram para as versões com suporte do Microsoft Office com a autenticação moderna habilitada
1. Configurar [métodos de autenticação](#choose-verification-options) escolhidos
1. Definir seus [locais de rede nomeados](../conditional-access/location-condition.md#named-locations)
1. Selecione grupos para começar a distribuir a MFA.
1. Configurar suas [políticas de acesso condicional](#create-conditional-access-policy)
1. Configurar sua política de registro de MFA
   1. [MFA e SSPR combinadas](howto-registration-mfa-sspr-combined.md)
   1. Com a [proteção de identidade](../identity-protection/howto-mfa-policy.md)
1. Enviar comunicações do usuário e fazer com que os usuários se registrem em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Controlar quem está registrado](#identify-non-registered-users)

> [!TIP]
> Os usuários de nuvem governamental podem se registrar em [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gerenciar sua solução

Relatórios para o Azure MFA

A autenticação multifator do Azure fornece relatórios por meio do portal do Azure:

| Relatório | Localização | Descrição |
| --- | --- | --- |
| Alertas de uso e fraude | Entradas de > do Azure AD | Fornece informações sobre o uso geral, Resumo do usuário e detalhes do usuário; bem como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Solucionar problemas de MFA

Encontre soluções para problemas comuns com o Azure MFA no [artigo solução de problemas da autenticação multifator do Azure](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) no centro de suporte da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [What are authentication methods?](concept-authentication-methods.md) (O que são os métodos de autenticação?)
* [Habilitar o registro convergido para a autenticação multifator do Azure e a redefinição de senha de autoatendimento do Azure AD](concept-registration-mfa-sspr-converged.md)
* Por que um usuário foi solicitado ou não solicitado a executar MFA? Consulte a seção [relatório de entradas do Azure AD no documento relatórios no Azure autenticação multifator](howto-mfa-reporting.md#azure-ad-sign-ins-report).
