---
title: Como planejar sua implementação de junção de Azure Active Directory (Azure AD) | Microsoft Docs
description: Explica as etapas necessárias para implementar dispositivos ingressados no Azure AD em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e58762bd5bf4342804767a200c94b432dd152a0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562220"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: Planear a sua implementação de associação do Azure AD

O ingresso no Azure AD permite que você ingresse dispositivos diretamente no Azure AD sem a necessidade de ingressar no local Active Directory enquanto mantém seus usuários produtivos e seguros. O ingresso no Azure AD está pronto para empresas para implantações em escala e com escopo.   

Este artigo fornece as informações necessárias para planejar sua implementação de ingresso no Azure AD.
 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com a [introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planejar sua implementação de ingresso no Azure AD, você deve se familiarizar com:

|   |   |
|---|---|
|![Verificação][1]|Examine seus cenários|
|![Verificação][1]|Examine sua infraestrutura de identidade|
|![Verificação][1]|Avaliar o gerenciamento de dispositivos|
|![Verificação][1]|Entenda as considerações para aplicativos e recursos|
|![Verificação][1]|Entenda suas opções de provisionamento|
|![Verificação][1]|Configurar roaming de estado da empresa|
|![Verificação][1]|Configurar o acesso condicional|

## <a name="review-your-scenarios"></a>Examine seus cenários 

Embora a junção híbrida do Azure AD possa ser preferida para determinados cenários, o ingresso no Azure AD permite que você faça a transição para um modelo de nuvem em primeiro lugar com o Windows. Se você estiver planejando modernizar o gerenciamento de dispositivos e reduzir os custos de ti relacionados a dispositivos, o ingresso no Azure AD fornecerá uma ótima base voltada para atingir esses objetivos.  
 
Você deve considerar o ingresso no Azure AD se suas metas se alinharem com os seguintes critérios:

- Você está adotando Microsoft 365 como o conjunto de produtividade para seus usuários.
- Você deseja gerenciar dispositivos com uma solução de gerenciamento de dispositivo de nuvem.
- Você deseja simplificar o provisionamento de dispositivos para usuários distribuídos geograficamente.
- Você planeja modernizar sua infraestrutura de aplicativo.

## <a name="review-your-identity-infrastructure"></a>Examine sua infraestrutura de identidade  

O ingresso no Azure AD funciona com ambientes gerenciados e federados.  

### <a name="managed-environment"></a>Ambiente gerenciado

Um ambiente gerenciado pode ser implantado por meio da [sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ou da autenticação de [passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) com logon único contínuo.

Esses cenários não exigem que você configure um servidor de Federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que ofereça suporte a protocolos WS-Trust e WS-Drive:

- **WS-Fed:** Esse protocolo é necessário para ingressar um dispositivo no Azure AD.
- **WS-Trust:** Esse protocolo é necessário para entrar em um dispositivo ingressado no Azure AD. 

Se seu provedor de identidade não oferecer suporte a esses protocolos, o ingresso no Azure AD não funcionará nativamente. A partir do Windows 10 1809, os usuários podem entrar em um dispositivo ingressado no Azure AD com um provedor de identidade baseado em SAML por meio de [entrada na Web no Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Atualmente, a entrada na Web é um recurso de visualização e não é recomendada para implantações de produção.

### <a name="smartcards-and-certificate-based-authentication"></a>Cartão inteligente e autenticação baseada em certificado

Não é possível usar cartões inteligentes ou autenticação baseada em certificado para unir dispositivos ao Azure AD. No entanto, os cartões inteligentes podem ser usados para entrar em dispositivos ingressados no Azure AD se você tiver AD FS configurados.

**Recomendação** Implemente o Windows Hello for Business para autenticação forte e sem senha em dispositivos Windows 10.

### <a name="user-configuration"></a>Configuração do usuário

Se você criar usuários no seu:

- **Active Directory locais**, você precisa sincronizá-los para o Azure AD usando [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure ad**, nenhuma configuração adicional é necessária.

Os UPNs locais diferentes dos UPNs do Azure AD não têm suporte em dispositivos adicionados ao Azure AD. Se os usuários usarem um UPN local, você deverá planejar alternar para usar seu UPN primário no Azure AD.

## <a name="assess-your-device-management"></a>Avaliar o gerenciamento de dispositivos

### <a name="supported-devices"></a>Dispositivos suportados

Ingresso no Azure AD:

- É aplicável somente a dispositivos Windows 10. 
- Não é aplicável a versões anteriores do Windows ou outros sistemas operacionais. Se você tiver dispositivos Windows 7/8.1, deverá atualizar para o Windows 10 para implantar o ingresso no Azure AD.
- Não tem suporte em dispositivos com TPM no modo FIPS.
 
**Recomendação** Sempre use a versão mais recente do Windows 10 para aproveitar os recursos atualizados.

### <a name="management-platform"></a>Plataforma de gerenciamento

O gerenciamento de dispositivos para dispositivos ingressados no Azure AD é baseado em uma plataforma MDM, como o Intune, e CSPs de MDM. O Windows 10 tem um agente MDM interno que funciona com todas as soluções de MDM compatíveis.

> [!NOTE]
> Não há suporte para políticas de grupo em dispositivos ingressados no Azure AD, pois eles não estão conectados a Active Directory locais. O gerenciamento de dispositivos ingressados no Azure AD só é possível por meio do MDM

Há duas abordagens para gerenciar dispositivos ingressados no Azure AD:

- **Somente MDM –** um dispositivo é gerenciado exclusivamente por um provedor de MDM, como o Intune. Todas as políticas são entregues como parte do processo de registro do MDM. Para clientes Azure AD Premium ou EMS, o registro de MDM é uma etapa automatizada que faz parte de uma junção do Azure AD.
- **Cogerenciamento** – um dispositivo é gerenciado por um provedor de MDM e SCCM. Nessa abordagem, o agente do SCCM é instalado em um dispositivo gerenciado por MDM para administrar determinados aspectos.

Se você estiver usando políticas de grupo, avalie sua paridade de política de MDM usando a [ferramenta de análise de migração do MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Examine as políticas com e sem suporte para determinar se você pode usar uma solução de MDM em vez de políticas de grupo. Para políticas sem suporte, considere o seguinte:

- As políticas sem suporte são necessárias para usuários ou dispositivos ingressados no Azure AD?
- As políticas sem suporte são aplicáveis em uma implantação controlada por nuvem?

Se sua solução de MDM não estiver disponível por meio da Galeria de aplicativos do Azure AD, você poderá adicioná-la seguindo o processo descrito em [Azure Active Directory integração com o MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Por meio do cogerenciamento, você pode usar o SCCM para gerenciar determinados aspectos de seus dispositivos enquanto as políticas são entregues por meio da sua plataforma MDM. Microsoft Intune habilita o cogerenciamento com o SCCM. Para obter mais informações, consulte [cogerenciamento para dispositivos Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Se você usar um produto MDM diferente do Intune, verifique com seu provedor de MDM em cenários de cogerenciamento aplicáveis.

**Recomendação** Considere o gerenciamento somente MDM para dispositivos ingressados no Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Entenda as considerações para aplicativos e recursos

É recomendável migrar aplicativos do local para a nuvem para melhorar a experiência do usuário e o controle de acesso. No entanto, os dispositivos adicionados ao Azure AD podem fornecer acesso direto aos aplicativos locais e na nuvem. Para obter mais informações, consulte [como o SSO para recursos locais funciona em dispositivos ingressados no Azure ad](azuread-join-sso.md).

As seções a seguir listam considerações para diferentes tipos de aplicativos e recursos.

### <a name="cloud-based-applications"></a>Aplicativos baseados em nuvem

Se um aplicativo for adicionado à galeria de aplicativos do Azure AD, os usuários obterão SSO por meio de dispositivos ingressados no Azure AD. Nenhuma configuração adicional é necessária. Os usuários obtêm SSO em ambos os navegadores, Microsoft Edge e Chrome. Para o Chrome, você precisa implantar a [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Todos os aplicativos Win32 que:

- Confiar no Gerenciador de contas da Web (WAM) para solicitações de token também obtém SSO em dispositivos ingressados no Azure AD. 
- Não confie no WAM pode solicitar aos usuários a autenticação. 

### <a name="on-premises-web-applications"></a>Aplicativos Web locais

Se seus aplicativos forem personalizados criados e/ou hospedados localmente, você precisará adicioná-los aos sites confiáveis do navegador para:

- Habilitar a autenticação integrada do Windows para funcionar 
- Forneça uma experiência de SSO sem solicitação aos usuários. 

Se você usar AD FS, consulte [verificar e gerenciar o logon único com o AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação** Considere a hospedagem na nuvem (por exemplo, o Azure) e a integração com o Azure AD para uma melhor experiência.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplicativos locais que dependem de protocolos herdados

Os usuários obtêm SSO de dispositivos ingressados no Azure AD se o dispositivo tiver acesso a um controlador de domínio. 

**Recomendação** Implante [aplicativo Azure ad proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para habilitar o acesso seguro para esses aplicativos.

### <a name="on-premises-network-shares"></a>Compartilhamentos de rede locais

Os usuários têm o SSO de dispositivos ingressados no Azure AD quando um dispositivo tem acesso a um controlador de domínio local.

### <a name="printers"></a>Print

Para impressoras, você precisa implantar a [impressão de nuvem híbrida](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para descobrir impressoras em dispositivos ingressados no Azure AD. 

Embora as impressoras não possam ser descobertas automaticamente em um ambiente somente na nuvem, os usuários também podem usar o caminho UNC das impressoras para adicioná-las diretamente. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicativos locais que dependem da autenticação do computador

Os dispositivos ingressados no Azure AD não dão suporte a aplicativos locais que dependem da autenticação do computador. 

**Recomendação** Considere a desativação desses aplicativos e a mudança para suas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços de Ambiente de Trabalho Remoto

A conexão de área de trabalho remota para um dispositivo ingressado no Azure AD exige que o computador host seja associado ao Azure ad ou ao Azure AD híbrido. Não há suporte para a área de trabalho remota de um dispositivo não-associado ou não Windows. Para obter mais informações, consulte [conectar-se ao PC ingressado no Azure ad remoto](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Entenda suas opções de provisionamento

Você pode provisionar o ingresso no Azure AD usando as seguintes abordagens:

- **Autoatendimento em OOBE/Settings** -no modo de autoatendimento, os usuários passam pelo processo de ingresso no Azure ad durante as configurações de Oobe (configuração inicial do usuário) ou do Windows. Para obter mais informações, consulte [unir seu dispositivo de trabalho à rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- O **piloto automático do Windows** – o Windows Autopilot permite a pré-configuração de dispositivos para uma experiência mais suave no OOBE para executar uma junção do Azure AD. Para obter mais informações, consulte a [visão geral do Windows AutoPilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Registro em massa** – o registro em massa permite que um administrador baseado no Azure ad ingresse usando uma ferramenta de provisionamento em massa para configurar dispositivos. Para obter mais informações, consulte [registro em massa para dispositivos Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Aqui está uma comparação dessas três abordagens 
 
|   | Configuração de autoatendimento | Piloto automático do Windows | Inscrição em massa |
| --- | --- | --- | --- |
| Exigir interação do usuário para configurar | Sim | Sim | Não |
| Exigir esforço de ti | Não | Sim | Sim |
| Fluxos aplicáveis | Configurações de & do OOBE | Somente OOBE | Somente OOBE |
| Direitos de administrador local para o usuário primário | Sim, por padrão | Configurável | Não |
| Exigir suporte para OEM do dispositivo | Não | Sim | Não |
| Versões suportadas | 1511 + | 1709 + | 1703 + |
 
Escolha a abordagem ou abordagens de implantação examinando a tabela acima e revisando as seguintes considerações para adotar qualquer uma das abordagens:  

- Seus usuários têm o conhecimento técnico para passar pela própria instalação? 
   - O autoatendimento pode funcionar melhor para esses usuários. Considere o Windows AutoPilot para aprimorar a experiência do usuário.  
- Os usuários são remotos ou dentro do local corporativo? 
   - O autoatendimento ou o AutoPilot funcionam melhor para usuários remotos para uma configuração sem complicações. 
- Você prefere uma configuração orientada por usuário ou gerenciada por administrador? 
   - O registro em massa funciona melhor para a implantação controlada pelo administrador para configurar dispositivos antes de passar para os usuários.     
- Você adquire dispositivos de 1-2 OEMS ou tem uma ampla distribuição de dispositivos OEM?  
   - Se estiver comprando de OEMs limitados que também dão suporte ao AutoPilot, você poderá se beneficiar de uma integração mais rígida com o piloto automático. 

## <a name="configure-your-device-settings"></a>Definir as configurações do dispositivo

O portal do Azure permite que você controle a implantação de dispositivos ingressados no Azure AD na sua organização. Para definir as configurações relacionadas, na **página Azure Active Directory**, selecione `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Os utilizadores podem associar dispositivos ao Azure AD

Defina essa opção como **All** ou **Selected** com base no escopo da sua implantação e que você deseja permitir para configurar um dispositivo ingressado no Azure AD. 

![Os utilizadores podem associar dispositivos ao Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locais adicionais nos dispositivos associados do Azure AD

Escolha **selecionado** e selecione os usuários que você deseja adicionar ao grupo Administradores locais em todos os dispositivos ingressados no Azure AD. 

![Administradores locais adicionais nos dispositivos associados do Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Exigir autenticação multifator para unir dispositivos

Selecione **"Sim** se você precisar que os usuários executem o MFA ao unir dispositivos ao Azure AD. Para os usuários que ingressam dispositivos no Azure AD usando o MFA, o próprio dispositivo se torna um 2º fator.

![Exigir autenticação multifator para unir dispositivos](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Definir suas configurações de mobilidade

Antes de definir as configurações de mobilidade, talvez seja necessário adicionar um provedor de MDM primeiro.

**Para adicionar um provedor de MDM**:

1. Na **página Azure Active Directory**, na seção **gerenciar** , clique em `Mobility (MDM and MAM)`. 
1. Clique em **Adicionar aplicativo**.
1. Selecione seu provedor de MDM na lista.

   ![Adicionar uma Aplicação](./media/azureadjoin-plan/04.png)

Selecione seu provedor de MDM para definir as configurações relacionadas. 

### <a name="mdm-user-scope"></a>Âmbito do utilizador MDM

Selecione **alguma** ou **todas as** opções com base no escopo da sua implantação. 

![Âmbito do utilizador MDM](./media/azureadjoin-plan/05.png)

Com base no seu escopo, uma das seguintes situações ocorrerá: 

- O **usuário está no escopo do MDM**: Se você tiver uma assinatura Azure AD Premium, o registro de MDM será automatizado junto com o ingresso no Azure AD. Todos os usuários com escopo devem ter uma licença apropriada para o MDM. Se o registro do MDM falhar nesse cenário, o ingresso no Azure AD também será revertido.
- O **usuário não está no escopo do MDM**: Se os usuários não estiverem no escopo do MDM, o ingresso no Azure AD será concluído sem qualquer registro de MDM. Isso resulta em um dispositivo não gerenciado.

### <a name="mdm-urls"></a>URLs de MDM

Há três URLs relacionadas à sua configuração de MDM:

- URL dos termos de utilização do MDM
- URL de deteção de MDM 
- URL de conformidade do MDM

![Adicionar uma Aplicação](./media/azureadjoin-plan/06.png)

Cada URL tem um valor padrão predefinido. Se esses campos estiverem vazios, entre em contato com seu provedor de MDM para obter mais informações.

### <a name="mam-settings"></a>Configurações de MAM

O MAM não se aplica ao ingresso no Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Configurar roaming de estado da empresa

Se você quiser habilitar o roaming de estado para o Azure AD para que os usuários possam sincronizar suas configurações entre dispositivos, consulte [habilitar Enterprise State roaming em Azure Active Directory](enterprise-state-roaming-enable.md). 

**Recomendação**: Habilite essa configuração mesmo para dispositivos ingressados no Azure AD híbrido.

## <a name="configure-conditional-access"></a>Configurar o acesso condicional

Se você tiver um provedor de MDM configurado para seus dispositivos ingressados no Azure AD, o provedor sinalizará o dispositivo como compatível assim que o dispositivo estiver sob gerenciamento. 

![Dispositivo em conformidade](./media/azureadjoin-plan/46.png)

Você pode usar essa implementação para [exigir dispositivos gerenciados para acesso de aplicativo de nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ingressar em um novo dispositivo Windows 10 com o Azure ad durante uma primeira execução](azuread-joined-devices-frx.md)
> [ingressar seu dispositivo de trabalho na rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
