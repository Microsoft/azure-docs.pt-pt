---
title: Como planear a implementação do seu Diretório Ativo Azure
description: Explica os passos necessários para implementar dispositivos de ad adesão ao Azure AD no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672685"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: Planear a sua adad Azure aderir à implementação

A adesão do Azure AD permite-lhe juntar dispositivos diretamente ao Azure AD sem a necessidade de se juntar ao Ative Directory no local, mantendo os seus utilizadores produtivos e seguros. A adesão à Azure AD está pronta para a empresa para implementações em escala e com âmbito.   

Este artigo fornece-lhe a informação de que precisa para planear a implementação da sua adad Azure.
 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que está familiarizado com a Introdução à Gestão de [Dispositivos no Diretório Ativo Azure](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a implementação do seu Anúncio Azure, deve familiarizar-se com:

|   |   |
|---|---|
|![Marcar][1]|Reveja os seus cenários|
|![Marcar][1]|Reveja a sua infraestrutura de identidade|
|![Marcar][1]|Avalie a gestão do seu dispositivo|
|![Marcar][1]|Compreender considerações para aplicações e recursos|
|![Marcar][1]|Compreenda as suas opções de provisionamento|
|![Marcar][1]|Configure o roaming do estado empresarial|
|![Marcar][1]|Configurar acesso condicional|

## <a name="review-your-scenarios"></a>Reveja os seus cenários 

Embora a adesão da Hybrid Azure AD possa ser preferível para determinados cenários, a adesão ao Azure AD permite-lhe fazer a transição para um modelo de primeira nuvem com windows. Se está a planear modernizar a gestão dos seus dispositivos e reduzir os custos de TI relacionados com dispositivos, a adesão da Azure AD fornece uma grande base para alcançar esses objetivos.  
 
Deve considerar a adesão do Azure AD se os seus objetivos se alinharem com os seguintes critérios:

- Está a adotar o Microsoft 365 como o pacote de produtividade para os seus utilizadores.
- Pretende gerir dispositivos com uma solução de gestão de dispositivos em nuvem.
- Pretende simplificar o fornecimento de dispositivos para utilizadores distribuídos geograficamente.
- Planeia modernizar a sua infraestrutura de aplicação.

## <a name="review-your-identity-infrastructure"></a>Reveja a sua infraestrutura de identidade  

A Azure AD junta-se a trabalhos com ambos, ambientes geridos e federados.  

### <a name="managed-environment"></a>Ambiente gerido

Um ambiente gerido pode ser implementado através [de Password Hash Sync](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ou Pass Through [Authentication](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) with Seamless Single Sign On.

Estes cenários não requerem que configure um servidor da federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um fornecedor de identidade que apoie tanto os protocolos WS-Trust como WS-Fed:

- **WS-Fed:** Este protocolo é necessário para aderir a um dispositivo à Azure AD.
- **WS-Trust:** Este protocolo é necessário para iniciar sessão num dispositivo azure AD.

Quando estiver a utilizar AD FS, tem de ativar os seguintes pontos finais wS-Trust:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Se o seu fornecedor de identidade não apoiar estes protocolos, a adesão à Azure AD não funciona de forma nativa. 

>[!NOTE]
> Atualmente, a adesão da Azure AD não funciona com [a AD FS 2019 configurada com fornecedores externos de autenticação como o método de autenticação primária.](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) A Azure AD junta-se a incumprimentos à autenticação de palavra-passe como método primário, o que resulta em falhas de autenticação neste cenário


### <a name="smartcards-and-certificate-based-authentication"></a>Smartcards e autenticação baseada em certificados

Não é possível utilizar smartcards ou autenticação baseada em certificados para aderir a dispositivos ao Azure AD. No entanto, os smartcards podem ser usados para iniciar sessão em dispositivos de ad ad ida e volta se tiver AD FS configurado.

**Recomendação:** Implemente o Windows Hello for Business para uma autenticação forte e sem palavra-passe para dispositivos Windows 10.

### <a name="user-configuration"></a>Configuração do utilizador

Se criar utilizadores no seu:

- **No local, diretório ativo,** precisa de sincronizá-los para Azure [AD utilizando o Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, não é necessária nenhuma configuração adicional.

As UPNs no local que são diferentes das UPNs Azure AD não são suportadas em dispositivos ligados à Azure AD. Se os seus utilizadores utilizarem uma UPN no local, deverá planear mudar para a utilização da sua UPN primária em Azure AD.

## <a name="assess-your-device-management"></a>Avalie a gestão do seu dispositivo

### <a name="supported-devices"></a>Dispositivos suportados

Azure AD junta-se:

- Só é aplicável aos dispositivos do Windows 10. 
- Não é aplicável a versões anteriores do Windows ou de outros sistemas operativos. Se tiver dispositivos Windows 7/8.1, tem de fazer o upgrade para o Windows 10 para implementar a ada do Azure.
- Não é suportado em dispositivos com TPM no modo FIPS.
 
**Recomendação:** Utilize sempre o mais recente lançamento do Windows 10 para tirar partido das funcionalidades atualizadas.

### <a name="management-platform"></a>Plataforma de gestão

A gestão de dispositivos ligados ao Azure AD baseia-se numa plataforma de MDM como Intune e MDM CSPs. O Windows 10 tem um agente MDM incorporado que trabalha com todas as soluções compatíveis com MDM.

> [!NOTE]
> As políticas do grupo não são suportadas em dispositivos ligados à AD Azure, uma vez que não estão ligados ao Diretório Ativo no local. A gestão de dispositivos adestados da Azure AD só é possível através do MDM

Existem duas abordagens para gerir dispositivos aderes à Azure AD:

- **Apenas MDM** - Um dispositivo é gerido exclusivamente por um fornecedor de MDM como o Intune. Todas as políticas são entregues como parte do processo de inscrição do MDM. Para os clientes Azure AD Premium ou EMS, a inscrição no MDM é um passo automatizado que faz parte de uma adesão à Azure AD.
- **Co-management** - Um dispositivo é gerido por um fornecedor de MDM e SCCM. Nesta abordagem, o agente SCCM é instalado num dispositivo gerido pelo MDM para administrar determinados aspetos.

Se estiver a utilizar políticas de grupo, avalie a sua paridade política de MDM utilizando a Ferramenta de Análise migratória [do MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Reveja políticas apoiadas e não apoiadas para determinar se pode utilizar uma solução DEMDM em vez de políticas do Grupo. Para políticas não apoiadas, considere o seguinte:

- As políticas não suportadas são necessárias para dispositivos ou utilizadores adeferidos do Azure AD?
- As políticas não apoiadas são aplicáveis numa implantação impulsionada pela nuvem?

Se a sua solução MDM não estiver disponível através da galeria de aplicações Azure AD, pode adicioná-la após o processo delineado na integração do [Diretório Ativo Azure com o MDM.](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) 

Através da cogestão, pode utilizar o SCCM para gerir certos aspetos dos seus dispositivos enquanto as políticas são entregues através da sua plataforma MDM. A Microsoft Intune permite a cogestão com o SCCM. Para obter mais informações sobre a cogestão dos dispositivos Windows 10, consulte [o que é a cogestão?](/configmgr/core/clients/manage/co-management-overview) Se utilizar um produto MDM que não seja o Intune, consulte o seu fornecedor de MDM em cenários de cogestão aplicáveis.

**Recomendação:** Considere agestão apenas do MDM para dispositivos aderidos da Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Compreender considerações para aplicações e recursos

Recomendamos que as aplicações migratórias do local para a nuvem para uma melhor experiência do utilizador e controlo de acesso. No entanto, os dispositivos ligados à Azure AD podem fornecer perfeitamente acesso a aplicações no local, no local e na nuvem. Para mais informações, consulte [como o SSO para os recursos no local funciona em dispositivos ligados à Azure AD](azuread-join-sso.md).

As seguintes secções listam considerações para diferentes tipos de aplicações e recursos.

### <a name="cloud-based-applications"></a>Aplicações baseadas em nuvem

Se uma aplicação for adicionada à galeria de aplicações Azure AD, os utilizadores obtêm SSO através de dispositivos aderes a Azure AD. Não é necessária nenhuma configuração adicional. Os utilizadores obtêm SSO em ambos os navegadores Microsoft Edge e Chrome. Para o Chrome, é necessário implementar a extensão das [Contas Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Todas as aplicações Win32 que:

- Confie no Web Account Manager (WAM) para pedidos simbólicos também obtenha SSO em dispositivos aderes a Azure AD. 
- Não confie no WAM pode solicitar aos utilizadores a autenticação. 

### <a name="on-premises-web-applications"></a>Aplicações web no local

Se as suas aplicações forem personalizadas e/ou hospedadas no local, precisa adicioná-las aos sites fidedignos do seu navegador para:

- Ativar a autenticação integrada do Windows para funcionar 
- Forneça uma experiência SSO sem aviso prévio aos utilizadores. 

Se utilizar AD FS, consulte [Verificar e gerir um único sinal com AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação:** Considere hospedar-se na nuvem (por exemplo, Azure) e integrar-se com a Azure AD para uma melhor experiência.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplicações no local que dependem de protocolos antigos

Os utilizadores obtêm SSO a partir de dispositivos aderes a AD Azure se o dispositivo tiver acesso a um controlador de domínio. 

**Recomendação:** Implemente [o proxy da App AD Azure](/azure/active-directory/manage-apps/application-proxy) para permitir o acesso seguro a estas aplicações.

### <a name="on-premises-network-shares"></a>Ações da rede no local

Os seus utilizadores têm dispositivos sSO a partir do Azure AD quando um dispositivo tem acesso a um controlador de domínio no local.

### <a name="printers"></a>Impressoras

Para impressoras, é necessário implementar [impressões híbridas](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) em nuvem para descobrir impressoras em dispositivos ligados ao Azure AD. 

Embora as impressoras não possam ser automaticamente descobertas num ambiente apenas em nuvem, os seus utilizadores também podem usar o caminho UNC das impressoras para as adicionar diretamente. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicações no local que dependem da autenticação automática

Os dispositivos aderes à Azure AD não suportam aplicações no local que dependem da autenticação automática. 

**Recomendação:** Considere retirar estas aplicações e mudar-se para as suas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços de Ambiente de Trabalho Remoto

A ligação remota de desktop a um dispositivo Azure AD requeira que a máquina de acolhimento seja a de Azure AD ou a AD Hybrid Azure. O ambiente de trabalho remoto de um dispositivo não-windows não equipado ou não é suportado. Para mais informações, consulte [Connect to remote Azure AD joined pc](/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Compreenda as suas opções de provisionamento

Pode fornecer a adesão da Azure AD utilizando as seguintes abordagens:

- **Self-service em OOBE/Definições** - No modo self-service, os utilizadores passam pelo processo de adesão do Azure AD, quer durante o Windows out of Box Experience (OOBE) quer a partir de Definições do Windows. Para mais informações, consulte [Adere ao seu dispositivo](/azure/active-directory/user-help/user-help-join-device-on-network)de trabalho na rede da sua organização . 
- **Windows Autopilot** - O Windows Autopilot permite a pré-configuração de dispositivos para uma experiência mais suave no OOBE para realizar uma adesão ao Azure AD. Para mais informações, consulte a [visão geral do Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Inscrição** a granel - A inscrição a granel permite que um administrador impulsionado pelo Azure AD se junte utilizando uma ferramenta de provisionamento a granel para configurar dispositivos. Para mais informações, consulte a [inscrição em massa para dispositivos Windows](/intune/windows-bulk-enroll).
 
Aqui está uma comparação destas três abordagens 
 
|   | Configuração de self-service | Windows Autopilot | Inscrição em massa |
| --- | --- | --- | --- |
| Exigir interação do utilizador para configurar | Sim | Sim | Não |
| Exigir esforço de TI | Não | Sim | Sim |
| Fluxos aplicáveis | Definições de & OOBE | Apenas oOBE | Apenas oOBE |
| Direitos de administração locais para o utilizador primário | Sim, por defeito | Configurável | Não |
| Exigir suporte OEM do dispositivo | Não | Sim | Não |
| Versões suportadas | 1511+ | 1709+ | 1703+ |
 
Escolha a sua abordagem de implantação ou abordagens, revendo o quadro acima e revendo as seguintes considerações para a adoção de qualquer uma das abordagens:  

- Os seus utilizadores são experientes em analisar a configuração? 
   - O self-service pode funcionar melhor para estes utilizadores. Considere o Windows Autopilot para melhorar a experiência do utilizador.  
- Os seus utilizadores estão remotos ou dentro das instalações corporativas? 
   - O self-service ou o Autopilot funcionam melhor para utilizadores remotos para uma configuração sem aborrecimentos. 
- Prefere uma configuração dirigida pelo utilizador ou gerida por administradores? 
   - A inscrição a granel funciona melhor para a implementação orientada pela administração para configurar dispositivos antes de entregar aos utilizadores.     
- Compra dispositivos a partir de 1-2 OEMS, ou tem uma ampla distribuição de dispositivos OEM?  
   - Se comprar em OEMs limitados que também suportam o Autopilot, pode beneficiar de uma integração mais apertada com o Autopilot. 

## <a name="configure-your-device-settings"></a>Configure as definições do seu dispositivo

O portal Azure permite controlar a implementação de dispositivos azure AD unidos na sua organização. Para configurar as definições relacionadas, na página `Devices > Device settings`do **Diretório Ativo Azure,** selecione .

### <a name="users-may-join-devices-to-azure-ad"></a>Os utilizadores podem juntar dispositivos à Azure AD

Detete esta opção para **All** ou **Selected** com base no âmbito da sua implementação e quem pretende permitir configurar um dispositivo azure AD. 

![Os utilizadores podem juntar dispositivos à Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locais adicionais em dispositivos aderes a AD Azure

Escolha **Selecionado** e selecione os utilizadores que pretende adicionar ao grupo de administradores locais em todos os dispositivos ligados ao Azure AD. 

![Administradores locais adicionais em dispositivos aderes a AD Azure](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Requerem a Auth multifactor para se juntar aos dispositivos

Selecione **"Sim,** se necessitar que os utilizadores realizem MFA ao mesmo tempo que unem dispositivos ao Azure AD. Para os utilizadores que juntam dispositivos ao Azure AD utilizando mFA, o dispositivo torna-se um 2º fator.

![Requerem a Auth multifactor para se juntar aos dispositivos](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Configure as suas definições de mobilidade

Antes de configurar as definições de mobilidade, poderá ter de adicionar primeiro um fornecedor de MDM.

**Para adicionar um fornecedor de MDM:**

1. Na página do **Diretório Ativo Azure,** `Mobility (MDM and MAM)`na secção **Gerir,** clique em . 
1. Clique na **aplicação Adicionar**.
1. Selecione o seu fornecedor de MDM da lista.

   ![Adicionar uma aplicação](./media/azureadjoin-plan/04.png)

Selecione o seu fornecedor de MDM para configurar as definições relacionadas. 

### <a name="mdm-user-scope"></a>Âmbito do utilizador do MDM

Selecione **Alguns** ou **Todos** com base no âmbito da sua implementação. 

![Âmbito do utilizador do MDM](./media/azureadjoin-plan/05.png)

Com base no seu âmbito, um dos seguintes acontece: 

- **O utilizador encontra-se no âmbito do MDM**: Se tiver uma subscrição Azure AD Premium, a inscrição no MDM é automatizada juntamente com a adesão do Azure AD. Todos os utilizadores de âmbito de aplicação devem ter uma licença adequada para o seu MDM. Se a inscrição no MDM falhar neste cenário, a adesão à Azure AD também será relançada.
- **O utilizador não está no âmbito do MDM**: Se os utilizadores não estiverem no âmbito do MDM, o Azure AD junta-se a completos sem qualquer inscrição no MDM. Isto resulta num dispositivo não gerido.

### <a name="mdm-urls"></a>MDM URLs

Existem três URLs que estão relacionados com a sua configuração de MDM:

- URL dos termos de utilização da MDM
- URL de deteção da MDM 
- URL de conformidade da MDM

![Adicionar uma aplicação](./media/azureadjoin-plan/06.png)

Cada URL tem um valor predefinido por defeito. Se estes campos estiverem vazios, contacte o seu fornecedor de MDM para obter mais informações.

### <a name="mam-settings"></a>Definições de MAM

A MAM não se aplica à adesão à Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Configure o roaming do estado empresarial

Se pretender ativar o roaming estatal para o Azure AD para que os utilizadores possam sincronizar as suas definições através de dispositivos, consulte [Enable Enterprise State Roaming no Diretório Ativo Azure](enterprise-state-roaming-enable.md). 

**Recomendação**: Ativar esta definição mesmo para dispositivos híbridos Azure AD.

## <a name="configure-conditional-access"></a>Configurar acesso condicional

Se tiver um fornecedor de MDM configurado para os dispositivos adestados do Azure AD, o fornecedor sinaliza o dispositivo assim que o dispositivo estiver sob gestão. 

![Dispositivo conforme](./media/azureadjoin-plan/46.png)

Pode utilizar esta implementação para [exigir dispositivos geridos para acesso](../conditional-access/require-managed-devices.md)a aplicações na nuvem com Acesso Condicional .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Junte-se a um novo dispositivo Windows 10 com AD Azure durante uma primeira execução](azuread-joined-devices-frx.md)
> [Junte-se ao seu dispositivo](/azure/active-directory/user-help/user-help-join-device-on-network) de trabalho na rede da sua organização

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
