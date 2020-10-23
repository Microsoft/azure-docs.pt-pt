---
title: Considerações de implantação para autenticação multi-factor Azure
description: Conheça as considerações de implementação e estratégia para a implementação bem sucedida da Autenticação Multi-Factor Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3d03f46e3948d1134c442f93af2e8f274dcd256
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92366484"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Planeie uma implementação de autenticação multi-factor Azure

As pessoas estão a ligar-se aos recursos organizacionais em cenários cada vez mais complicados. As pessoas conectam-se a partir de dispositivos pessoais, pessoais e públicos da organização dentro e fora da rede corporativa usando smartphones, tablets, computadores e portáteis, muitas vezes em várias plataformas. Neste mundo sempre conectado, multi-dispositivos e multiplataformas, a segurança das contas dos utilizadores é mais importante do que nunca. As palavras-passe, independentemente da sua complexidade, utilizadas em dispositivos, redes e plataformas, já não são suficientes para garantir a segurança da conta de utilizador, especialmente quando os utilizadores tendem a reutilizar palavras-passe através das contas. Phishing sofisticado e outros ataques de engenharia social podem resultar em nomes de utilizadores e senhas sendo postados e vendidos através da dark web.

[A Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) ajuda a salvaguardar o acesso a dados e aplicações. Fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar [o Acesso Condicional](../conditional-access/overview.md) para que a solução se adapte às suas necessidades específicas.

Este guia de implementação mostra-lhe como planear e, em seguida, testar um roll-out de autenticação multi-factor Azure.

Para ver rapidamente a autenticação multi-factor do Azure em ação e depois voltar a compreender considerações adicionais de implementação:

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implementação da Autenticação Multi-Factor Azure, existem itens pré-requisitos que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| Ambiente de identidade **apenas** em nuvem com autenticação moderna | **Sem tarefas pré-requisitos adicionais** |
| **Cenários de** identidade híbrida | [O Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implantado e as identidades dos utilizadores são sincronizadas ou federadas com os serviços de domínio do Diretório Ativo no local com o Azure Ative Directory. |
| Aplicações antigas no local publicadas para acesso à nuvem | A azure AD [Application Proxy](../manage-apps/application-proxy.md) está implantado. |
| Utilização de Azure MFA com autenticação RADIUS | É implantado um [Servidor de Política de Rede (NPS).](howto-mfa-nps-extension.md) |
| Os utilizadores têm o Microsoft Office 2010 ou mais cedo, ou o Apple Mail para iOS 11 ou mais cedo | Upgrade para [o Microsoft Office 2013 ou mais tarde](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e apple mail para iOS 12 ou mais tarde. O Acesso Condicional não é suportado por protocolos de autenticação de legados. |

## <a name="plan-user-rollout"></a>Implementação do utilizador do plano

O seu plano de lançamento do MFA deve incluir uma implantação piloto seguida de ondas de implantação que estão dentro da sua capacidade de suporte. Inicie o seu lançamento aplicando as suas políticas de Acesso Condicional a um pequeno grupo de utilizadores piloto. Depois de avaliar o efeito nos utilizadores-piloto, processo utilizado e comportamentos de registo, pode adicionar mais grupos à política ou adicionar mais utilizadores aos grupos existentes.

### <a name="user-communications"></a>Comunicações de utilizadores

É fundamental informar os utilizadores, em comunicações planeadas, sobre as próximas alterações, os requisitos de registo do Azure MFA e quaisquer ações necessárias para o utilizador. Recomendamos que as comunicações sejam desenvolvidas em conjunto com representantes de dentro da sua organização, tais como departamentos de Comunicação, Gestão de Alterações ou Recursos Humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e [documentação de utilizador final](../user-help/security-info-setup-signin.md) para ajudar a elaborar as suas comunicações. Pode enviar os utilizadores [https://myprofile.microsoft.com](https://myprofile.microsoft.com) para se registarem diretamente, selecionando os links **de Informações de Segurança** nessa página.

## <a name="deployment-considerations"></a>Considerações sobre implementação

A autenticação multi-factor Azure é implementada através da aplicação de políticas com Acesso Condicional. Uma política de acesso condicional pode exigir que os utilizadores realizem a autenticação de vários fatores quando determinados critérios forem cumpridos, tais como:

* Todos os utilizadores, um utilizador específico, membro de um grupo ou papel atribuído
* Aplicação de nuvem específica a ser acedida
* Plataforma de dispositivo
* Estado do dispositivo
* Localização da rede ou endereço IP geo-localizado
* Aplicações de cliente
* Risco de inscrição (requer proteção de identidade)
* Dispositivo conforme
* Dispositivo híbrido Azure AD
* Pedido de cliente aprovado

Use os cartazes personalizáveis e modelos [de e-mail em materiais de implantação de autenticação multi-factor](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) para lançar a autenticação de vários fatores para a sua organização.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Ativar a autenticação multi-factor com acesso condicional

As políticas de acesso condicional impõem o registo, exigindo que os utilizadores não registados completem o registo à primeira sedubragem, uma importante consideração de segurança.

[A Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) contribui com uma política de registo e políticas automatizadas de deteção e reparação de riscos para a história de autenticação multi-factor Azure. As políticas podem ser criadas para forçar alterações de palavra-passe quando há uma ameaça de identidade comprometida ou exigir MFA quando uma entrada é considerada arriscada pelos [seguintes eventos:](../identity-protection/overview-identity-protection.md)

* Fuga de credenciais
* Inícios de sessão de endereços IP anónimos
* Deslocação impossível para localizações atípicas
* Inícios de sessão de localizações desconhecidas
* Inícios de sessão de dispositivos infetados
* Insuposições de endereços IP com atividades suspeitas

Algumas das deteções de risco detetadas pela Azure Ative Directory Identity Protection ocorrem em tempo real e algumas requerem processamento offline. Os administradores podem optar por bloquear utilizadores que apresentam comportamentos de risco e remediar manualmente, exigir uma alteração de palavra-passe ou exigir uma autenticação multi-factor como parte das suas políticas de Acesso Condicional.

## <a name="define-network-locations"></a>Definir localizações de rede

Recomendamos que as organizações utilizem o Acesso Condicional para definir a sua rede utilizando [locais nomeados.](../conditional-access/location-condition.md#named-locations) Se a sua organização estiver a usar a Proteção de Identidade, considere usar políticas baseadas no risco em vez de localizações nomeadas.

### <a name="configuring-a-named-location"></a>Configurar um local nomeado

1. Abra **o Diretório Ativo Azure** no portal Azure
2. Selecione **Segurança**
3. Em **Gestão**, escolha **Localizações Nomeadas**
4. Selecione **nova localização**
5. No campo **Nome,** forneça um nome significativo
6. Selecione se está a definir a localização utilizando *gamas IP* ou *Países/Regiões*
   1. Se utilizar *gamas IP*
      1. Decida se *mark como local de confiança*. Iniciar sessão a partir de uma localização fidedigna reduz o risco de início de sessão de um utilizador. Apenas marque esta localização como confiável se souber que as gamas IP inseridas são estabelecidas e credíveis na sua organização.
      2. Especificar os intervalos IP
   2. Se utilizar *países/regiões*
      1. Expanda o menu suspenso e selecione os países ou regiões que deseja definir para este local nomeado.
      2. Decida se *inclui áreas desconhecidas.* Áreas desconhecidas são endereços IP que não podem ser mapeados para um país/região.
7. Selecione **Criar**

## <a name="plan-authentication-methods"></a>Métodos de autenticação do plano

Os administradores podem escolher os [métodos de autenticação](../authentication/concept-authentication-methods.md) que pretendem disponibilizar para os utilizadores. É importante permitir mais do que um único método de autenticação para que os utilizadores tenham um método de backup disponível no caso do seu método principal estar indisponível. Estão disponíveis os seguintes métodos para os administradores permitirem:

> [!TIP]
> A Microsoft recomenda a utilização do Microsoft Authenticator (aplicação móvel) como o método principal para a autenticação multi-factor Azure para uma experiência de utilizador mais segura e melhorada. A aplicação Microsoft Authenticator também [cumpre](https://azure.microsoft.com/resources/microsoft-nist/) os níveis de garantia do Instituto Nacional de Normas e Tecnologia. 

### <a name="notification-through-mobile-app"></a>Notificação através de aplicação móvel

Uma notificação push é enviada para a aplicação Microsoft Authenticator no seu dispositivo móvel. O utilizador vê a notificação e seleciona **Aprovar** para completar a verificação. As notificações push através de uma aplicação móvel fornecem a opção menos intrusiva para os utilizadores. São também a opção mais fiável e segura porque utilizam uma ligação de dados em vez de telefonia.

> [!NOTE]
> Se a sua organização tem pessoal a trabalhar ou a viajar para a China, a Notificação através do método **de aplicação móvel** em **dispositivos Android** não funciona nesse país/região. Devem ser disponibilizados métodos alternativos para esses utilizadores.

### <a name="verification-code-from-mobile-app"></a>Código de verificação da aplicação móvel

Uma aplicação móvel como a aplicação Microsoft Authenticator gera um novo código de verificação do OATH a cada 30 segundos. O utilizador introduz o código de verificação na interface de entrada. A opção de aplicação móvel pode ser usada quer o telefone tenha ou não um dado ou sinal celular.

### <a name="call-to-phone"></a>Chamada para telefone

É feita uma chamada de voz automatizada ao utilizador. O utilizador atende a chamada e pressiona **#** o teclado do telefone para aprovar a sua autenticação. A chamada para o telefone é um ótimo método de backup para notificação ou código de verificação a partir de uma aplicação móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto que contenha um código de verificação é enviada ao utilizador, o utilizador é solicitado a introduzir o código de verificação na interface de entrada.

### <a name="choose-verification-options"></a>Escolha opções de verificação

1. Navegue pelo **Azure Ative Directory**, **Utilizadores,** **Autenticação multi-factor**.

   ![Aceder ao portal de autenticação multi-factor a partir da lâmina dos utilizadores AD do Azure no portal Azure](media/howto-mfa-getstarted/users-mfa.png)

1. No novo separador que abre a navegação para **as definições de serviço**.
1. Nas opções de **verificação,** verifique todas as caixas para saber os métodos disponíveis para os utilizadores.

   ![Configurar métodos de verificação no separador de definições de autenticação multi-factor](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Guardar**.
1. Feche o **separador de definições de serviço.**

## <a name="plan-registration-policy"></a>Política de registo de planos

Os administradores devem determinar como os utilizadores irão registar os seus métodos. As organizações devem [permitir a nova experiência de registo combinado](howto-registration-mfa-sspr-combined.md) para Azure MFA e reset de senha de autosserviço (SSPR). A SSPR permite que os utilizadores repuvam a sua palavra-passe de forma segura utilizando os mesmos métodos que utilizam para a autenticação de vários fatores. Recomendamos este registo combinado porque é uma ótima experiência para os utilizadores, com a capacidade de se registar uma vez para ambos os serviços. Permitir os mesmos métodos para SSPR e Azure MFA permitirá que os seus utilizadores estejam registados para utilizar ambas as funcionalidades.

### <a name="registration-with-identity-protection"></a>Registo com Proteção de Identidade

Se a sua organização estiver a utilizar a Azure Ative Directory Identity Protection, [configuure a política de registo do MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) para solicitar aos seus utilizadores que se registem na próxima vez que assinarem interativamente.

### <a name="registration-without-identity-protection"></a>Registo sem Proteção de Identidade

Se a sua organização não tiver licenças que permitam a Proteção de Identidade, os utilizadores são solicitados a registar-se da próxima vez que o MFA for necessário no início de saúde. Os utilizadores podem não estar registados para MFA se não utilizarem aplicações protegidas com MFA. É importante que todos os utilizadores se registem para que os maus atores não possam adivinhar a palavra-passe de um utilizador e registar-se para MFA em seu nome, assumindo efetivamente o controlo da conta.

#### <a name="enforcing-registration"></a>Inscrição na execução

Utilizando os seguintes passos, uma política de acesso condicional pode forçar os utilizadores a registarem-se para autenticação multi-factor

1. Crie um grupo, adicione todos os utilizadores que não estão registados atualmente.
2. Utilizando o Acesso Condicional, imponha a autenticação multi-fator para este grupo para acesso a todos os recursos.
3. Periodicamente, reavalie a adesão ao grupo e remova os utilizadores que se tenham registado no grupo.

Pode identificar utilizadores Azure MFA registados e não registados com comandos PowerShell que dependem do [módulo MSOnline PowerShell](/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar utilizadores registados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar utilizadores não registados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Converter utilizadores de MFA por utilizador para MFA baseado em acesso condicional

Se os seus utilizadores estavam ativados utilizando a autenticação multi-factor Azure ativada e aplicada a Azure, o seguinte PowerShell pode ajudá-lo a escoar a conversão para autenticação multi-factor de acesso condicional.

Executar este PowerShell numa janela ISE ou guardar como `.PS1` um ficheiro para ser executado localmente.

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
> Recentemente mudamos o comportamento e o script PowerShell acima em conformidade. Anteriormente, o script guardou os métodos MFA, desativou o MFA e restaurou os métodos. Isto já não é necessário agora que o comportamento padrão para desativar não limpa os métodos.

## <a name="plan-conditional-access-policies"></a>Políticas de acesso condicional do plano

Para planear a sua estratégia de política de acesso condicional, que determinará quando são necessários MFA e outros controlos, consulte as [políticas comuns de acesso condicional](../conditional-access/concept-conditional-access-policy-common.md).

É importante que evite ser inadvertidamente trancado fora do seu inquilino AZure AD. Pode atenuar o impacto desta inadvertida falta de acesso administrativo [criando duas ou mais contas de acesso de emergência no seu inquilino](../roles/security-emergency-access.md) e excluindo-as da sua política de Acesso Condicional.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Conditional Access**.
1. Selecione **Nova política**.
   ![Criar uma política de acesso condicional para permitir que os utilizadores do portal Azure em grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Forneça um nome significativo para a sua política.
1. Sob **utilizadores e grupos:**
   * No **separador Incluir,** selecione o botão de rádio **Todos os utilizadores**
   * No **separador Excluir,** verifique a caixa para **Utilizadores e grupos** e escolha as suas contas de acesso de emergência.
   * Clique em **Done** (Concluído).
1. Nas **aplicações Cloud**, selecione o botão de rádio **All cloud apps.**
   * OPCIONALMENTE: No separador **Excluir,** escolha aplicações em nuvem para as quais a sua organização não necessita de MFA.
   * Clique em **Done** (Concluído).
1. Secção **de Condições:**
   * OPCIONAL: Se tiver ativado a Proteção de Identidade Azure, pode optar por avaliar o risco de inscrição como parte da política.
   * OPCIONALMENTE: Se tiver configurado localizações fidedignas ou locais nomeados, pode especificar para incluir ou excluir essas localizações da apólice.
1. Em **Grant,** certifique-se de que o botão de rádio **de acesso Grant** está selecionado.
    * Verifique se a caixa é **necessária para a autenticação de vários fatores.**
    * Clique em **Selecionar**.
1. Ignore a secção **de Sessão.**
1. Desace a **política de ativação** para **on**.
1. Clique em **Criar**.

## <a name="plan-integration-with-on-premises-systems"></a>A integração do plano com os sistemas no local

Algumas aplicações antigas e no local que não autenticam diretamente contra a Azure AD requerem medidas adicionais para utilizar o MFA, incluindo:

* Aplicações antigas no local, que terão de usar o proxy da Aplicação.
* Aplicações RADIUS no local, que terão de utilizar o adaptador MFA com servidor NPS.
* Aplicações AD FS no local, que terão de usar o adaptador MFA com AD FS 2016 ou mais recentes.

As aplicações que autenticam diretamente com a AZure AD e possuem autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem utilizar diretamente as políticas de Acesso Condicional.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Use Azure MFA com Azure AD Application Proxy

As aplicações que residem no local podem ser publicadas no seu inquilino Azure AD através da [Azure AD Application Proxy](../manage-apps/application-proxy.md) e podem tirar partido da Autenticação Multi-Factor Azure se estiverem configuradas para utilizar a pré-autenticação Azure AD.

Estas aplicações estão sujeitas a políticas de Acesso Condicional que impõem a autenticação multi-factor Azure, tal como qualquer outra aplicação integrada a AD Azure.

Da mesma forma, se a autenticação multi-factor Azure for aplicada para todos os pedidos de inscrição do utilizador, as aplicações no local publicadas com o Azure AD Application Proxy serão protegidas.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integração da autenticação multi-factor do Azure com o Servidor de Política de Rede

A extensão do Network Policy Server (NPS) para Azure MFA adiciona capacidades MFA baseadas na nuvem à sua infraestrutura de autenticação utilizando os servidores existentes. Com a extensão NPS, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicativos telefónicos ao fluxo de autenticação existente. Esta integração tem as seguintes limitações:

* Com o protocolo CHAPv2, apenas notificações push de aplicação autenticadora e chamada de voz são suportadas.
* As políticas de acesso condicional não podem ser aplicadas.

A extensão NPS funciona como um adaptador entre RADIUS e Azure MFA baseado na nuvem para fornecer um segundo fator de autenticação para proteger as ligações [VPN,](howto-mfa-nps-extension-vpn.md)Remote Desktop Gateway , ou [outras](howto-mfa-nps-extension-rdg.md)aplicações com capacidade RADIUS. Os utilizadores que se inscrevam no Azure MFA neste ambiente serão desafiados para todas as tentativas de autenticação, a falta de políticas de Acesso Condicional significa que o MFA é sempre necessário.

#### <a name="implementing-your-nps-server"></a>Implementação do seu servidor NPS

Se tiver uma instância NPS implantada e já em uso, referência [Integre a sua infraestrutura de NPS existente com autenticação multi-factor Azure](howto-mfa-nps-extension.md). Se estiver a configurar NPS pela primeira vez, consulte o [Network Policy Server (NPS)](/windows-server/networking/technologies/nps/nps-top) para obter instruções. A orientação de resolução de problemas pode ser encontrada no artigo [Resolver mensagens de erro da extensão NPS para autenticação multi-factor Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparar NPS para utilizadores que não estão inscritos para MFA

Escolha o que acontece quando os utilizadores que não estão matriculados com MFA tentam autenticar. Utilize a definição de registo `REQUIRE_USER_MATCH` no caminho do registo para controlar o comportamento da `HKLM\Software\Microsoft\AzureMFA` funcionalidade. Esta definição tem uma única opção de configuração.

| Chave | Valor | Predefinição |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | VERDADEIRO / FALSO | Não definido (equivalente a VERDADEIRO) |

O objetivo desta definição é determinar o que fazer quando um utilizador não está inscrito para MFA. Os efeitos da alteração desta definição estão listados na tabela abaixo.

| Definições | Estado do MFA do utilizador | Efeitos |
| --- | --- | --- |
| A chave não existe | Não matriculado | Desafio da MFA não tem sucesso |
| Valor definido para Verdadeiro / Não Definido | Não matriculado | Desafio da MFA não tem sucesso |
| Conjunto de chave para falso | Não matriculado | Autenticação sem MFA |
| Conjunto de chave para falso ou verdadeiro | Enrolled (Inscrito) | Deve autenticar com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integração com Serviços da Federação de Diretórios Ativos

Se a sua organização estiver federada com Azure AD, pode utilizar [a Autenticação Multi-Factor Azure para garantir recursos AD FS](multi-factor-authentication-get-started-adfs.md), tanto no local como na nuvem. O Azure MFA permite-lhe reduzir senhas e fornecer uma forma mais segura de autenticar. A partir do Windows Server 2016, já pode configurar o Azure MFA para a autenticação primária.

Ao contrário do que acontece com o AD FS no Windows Server 2012 R2, o adaptador AD FS 2016 Azure MFA integra-se diretamente com a Azure AD e não necessita de um servidor Azure MFA no local. O adaptador Azure MFA foi integrado no Windows Server 2016 e não há necessidade de uma instalação adicional.

Ao utilizar o Azure MFA com AD FS 2016 e a aplicação-alvo está sujeita à política de acesso condicional, existem considerações adicionais:

* O Acesso Condicional está disponível quando a aplicação é uma parte dependente da Azure AD, federada com AD FS 2016 ou mais recente.
* O Acesso Condicional não está disponível quando a aplicação é uma parte dependente da AD FS 2016 ou AD FS 2019 e é gerida ou federada com AD FS 2016 ou AD FS 2019.
* O Acesso Condicional também não está disponível quando a AD FS 2016 ou AD FS 2019 estiver configurada para utilizar o Azure MFA como método de autenticação primária.

#### <a name="ad-fs-logging"></a>Registo de AD FS

O registo standard AD FS 2016 e 2019 no Registo de Segurança do Windows e no registo AD FS Admin, contém informações sobre pedidos de autenticação e o seu sucesso ou falha. Os dados de registo de eventos dentro destes eventos indicarão se a Azure MFA foi utilizada. Por exemplo, um ID de Evento de Auditoria da AD FS 1200 pode conter:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerir certificados

Em cada servidor AD FS, no computador local My Store, haverá um certificado Azure MFA auto-assinado intitulado OU=Microsoft AD FS Azure MFA, que contém a data de validade do certificado. Verifique o período de validade deste certificado em cada servidor AD FS para determinar a data de validade.

Se o período de validade dos seus certificados estiver próximo de expirar, [gere e verifique um novo certificado MFA em cada servidor AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Os seguintes detalhes de orientação como gerir os certificados Azure MFA nos seus servidores AD FS. Quando configurar a AD FS com Azure MFA, os certificados gerados através do `New-AdfsAzureMfaTenantCertificate` cmdlet PowerShell são válidos por dois anos. Renovar e instalar os certificados renovados antes da expiração para perturbações ovais no serviço MFA.

## <a name="implement-your-plan"></a>Implemente o seu plano

Agora que planeou a sua solução, pode implementar seguindo os passos abaixo:

1. Conheça os pré-requisitos necessários
   1. Implementar [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para quaisquer cenários híbridos
   1. Implementar [Proxy aplicação AD Azure](../manage-apps/application-proxy.md) para quaisquer aplicações no local publicadas para acesso à nuvem
   1. Implementar [NPS](/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Garantir que os utilizadores têm atualizado para versões suportadas do Microsoft Office com a autenticação moderna ativada
1. Configurar métodos de [autenticação escolhidos](#choose-verification-options)
1. Defina as [localizações da rede nomeadas](../conditional-access/location-condition.md#named-locations)
1. Selecione grupos para começar a lançar MFA.
1. Configure as suas [políticas de acesso condicional](#create-conditional-access-policy)
1. Configure a sua política de registo de MFA
   1. [MFA combinado e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Com [Proteção de Identidade](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Envie comunicações de utilizadores e faça com que os utilizadores se inscrevam em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Acompanhe quem está matriculado.](#identify-non-registered-users)

> [!TIP]
> Utilizadores de nuvem do governo podem inscrever-se em [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Gerir a sua solução

Relatórios da Azure MFA

A Azure Multi-Factor Authentication fornece relatórios através do portal Azure:

| Relatório | Localização | Descrição |
| --- | --- | --- |
| Alertas de utilização e fraude | Azure AD > inscrições | Fornece informações sobre a utilização geral, o resumo do utilizador e os detalhes do utilizador; bem como um histórico de alertas de fraude apresentados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Problemas de resolução de problemas do MFA

Encontre soluções para problemas comuns com o Azure MFA no [artigo de autenticação multi-factor Azure de resolução de problemas](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) no Microsoft Support Center.

## <a name="next-steps"></a>Passos seguintes

Para ver a autenticação multi-factor Azure em ação, complete o seguinte tutorial:

> [!div class="nextstepaction"]
> [Ativar a Multi-Factor Authentication do Azure](tutorial-enable-azure-mfa.md)
