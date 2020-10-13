---
title: Como planear o seu Azure Ative Directory aderir à implementação
description: Explica os passos necessários para implementar dispositivos aderidos a Azure AD no seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: de383bfa9f943cd5644d35ed83db8a80ec8017bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653218"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: Planeie a sua Azure AD aderir à implementação

A ad AD Azure permite-lhe juntar dispositivos diretamente ao Azure AD sem a necessidade de se juntar ao Ative Directory no local, mantendo os seus utilizadores produtivos e seguros. A ad AD Azure está pronta para implementações em escala e mira.   

Este artigo fornece-lhe as informações necessárias para planear a implementação da sua Azure AD.
 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com a [Introdução à gestão de dispositivos no Azure Ative Directory](./overview.md).

## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a implementação do seu Azure AD, deve familiarizar-se com:

> [!div class="checklist"]
> - Reveja os seus cenários
> - Reveja a sua infraestrutura de identidade
> - Avalie a gestão do seu dispositivo
> - Compreender considerações para aplicações e recursos
> - Compreenda as suas opções de provisionamento
> - Configurar o roaming do estado da empresa
> - Configure acesso condicional

## <a name="review-your-scenarios"></a>Reveja os seus cenários 

Embora a adada híbrida Azure possa ser preferida para certos cenários, a junção AD AD Azure permite-lhe transitar para um modelo de primeira nuvem com Windows. Se está a planear modernizar a gestão dos seus dispositivos e reduzir os custos de TI relacionados com dispositivos, a Azure AD junta-se a uma grande base para alcançar esses objetivos.  
 
Deve considerar a adesão do Azure AD se os seus objetivos se alinharem com os seguintes critérios:

- Está a adotar o Microsoft 365 como a suite de produtividade para os seus utilizadores.
- Pretende gerir dispositivos com uma solução de gestão de dispositivos em nuvem.
- Pretende simplificar o fornecimento de dispositivos para utilizadores distribuídos geograficamente.
- Planeia modernizar a sua infraestrutura de aplicações.

## <a name="review-your-identity-infrastructure"></a>Reveja a sua infraestrutura de identidade  

A Azure AD junta-se a obras com ambientes geridos e federados.  

### <a name="managed-environment"></a>Ambiente gerido

Um ambiente gerido pode ser implementado através de [Password Hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) ou [de passar por autenticação](../hybrid/how-to-connect-pta-quick-start.md) com único sinal sem emenda.

Estes cenários não requerem que configures um servidor da federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um fornecedor de identidade que apoie os protocolos WS-Trust e WS-Fed:

- **WS-Fed:** Este protocolo é necessário para aderir a um dispositivo ao Azure AD.
- **WS-Trust:** Este protocolo é necessário para iniciar seduca num dispositivo azure AD.

Quando estiver a utilizar o AD FS, tem de ativar os seguintes pontos finais WS-Trust: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Se o seu fornecedor de identidade não apoiar estes protocolos, a Azure AD não funciona de forma nativa. 

>[!NOTE]
> Atualmente, a Azure AD não funciona com [AD FS 2019 configurado com fornecedores de autenticação externa como o método de autenticação primária.](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) A Azure AD associa-se a predefinições à autenticação de senha como o método primário, o que resulta em falhas de autenticação neste cenário


### <a name="smartcards-and-certificate-based-authentication"></a>Smartcards e autenticação baseada em certificados

Não é possível utilizar cartões inteligentes ou autenticação baseada em certificados para juntar dispositivos ao Azure AD. No entanto, os smartcards podem ser utilizados para iniciar súpides nos dispositivos azure AD se tiver O FS configurado.

**Recomendação:** Implementar o Windows Hello for Business para autenticação forte e sem palavra-passe para dispositivos Windows 10.

### <a name="user-configuration"></a>Configuração do utilizador

Se criar utilizadores no seu:

- **No local, Ative Directory,** é necessário sincronizá-los para Azure AD utilizando [o Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md). 
- **Azure AD**, não é necessária uma configuração adicional.

As UPNs no local que são diferentes das UPNs AD Azure não são suportadas em dispositivos aderidos Azure AD. Se os seus utilizadores utilizarem uma UPN no local, deverá planear mudar para a utilização da sua UPN primária em Azure AD.

## <a name="assess-your-device-management"></a>Avalie a gestão do seu dispositivo

### <a name="supported-devices"></a>Dispositivos suportados

Azure AD junta-se:

- Só é aplicável aos dispositivos do Windows 10. 
- Não é aplicável às versões anteriores do Windows ou de outros sistemas operativos. Se tiver dispositivos Windows 7/8.1, tem de fazer o upgrade para o Windows 10 para implementar a ad AD do Azure.
- É suportado para TPM 2.0 compatível com FIPS, mas não suportado para TPM 1.2. Se os seus dispositivos tiverem TPM 1.2 compatível com FIPS, deve desativá-los antes de prosseguir com a ad Azure. A Microsoft não fornece quaisquer ferramentas para desativar o modo FIPS para TPMs, uma vez que depende do fabricante TPM. Entre em contato com o seu hardware OEM para obter suporte.
 
**Recomendação:** Utilize sempre as mais recentes versões do Windows 10 para tirar partido das funcionalidades atualizadas.

### <a name="management-platform"></a>Plataforma de gestão

A gestão de dispositivos para dispositivos aderentes Azure AD baseia-se numa plataforma MDM como Intune e MDM CSPs. O Windows 10 tem um agente MDM incorporado que funciona com todas as soluções MDM compatíveis.

> [!NOTE]
> As políticas de grupo não são suportadas em dispositivos aderidos a Azure AD, uma vez que não estão ligadas ao Ative Directory no local. Gestão de dispositivos aderidos a Azure AD só é possível através do MDM

Existem duas abordagens para gerir dispositivos aderidos a Azure AD:

- **APENAS MDM** - Um dispositivo é gerido exclusivamente por um fornecedor de MDM como o Intune. Todas as políticas são entregues como parte do processo de inscrição do MDM. Para os clientes Azure AD Premium ou EMS, a inscrição no MDM é um passo automatizado que faz parte de uma junção AD Azure.
- **Cogestão** - Um dispositivo é gerido por um provedor de MDM e SCCM. Nesta abordagem, o agente SCCM é instalado num dispositivo gerido pelo MDM para administrar determinados aspetos.

Se estiver a utilizar políticas de grupo, avalie a sua paridade de política de MDM utilizando a [Ferramenta de Análise de Migração do MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Reveja as políticas apoiadas e não apoiadas para determinar se pode utilizar uma solução MDM em vez de políticas de grupo. Para políticas não apoiadas, considere o seguinte:

- As políticas não apoiadas são necessárias para que a Azure AD se junte a dispositivos ou utilizadores?
- As políticas não apoiadas são aplicáveis numa implantação orientada para a nuvem?

Se a sua solução MDM não estiver disponível através da galeria de aplicações Azure AD, pode adicioná-la seguindo o processo delineado na integração do [Azure Ative Directory com o MDM.](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) 

Através da cogestão, pode utilizar o SCCM para gerir certos aspetos dos seus dispositivos enquanto as políticas são entregues através da sua plataforma MDM. O Microsoft Intune permite a cogestão com a SCCM. Para obter mais informações sobre cogestão para dispositivos Windows 10, veja [o que é cogestão?](/configmgr/core/clients/manage/co-management-overview) Se utilizar um produto MDM diferente do Intune, consulte o seu fornecedor de MDM sobre cenários de cogestão aplicáveis.

**Recomendação:** Considere a gestão do MDM apenas para dispositivos ad Azure.

## <a name="understand-considerations-for-applications-and-resources"></a>Compreender considerações para aplicações e recursos

Recomendamos que as aplicações migratórias de instalações para cloud para uma melhor experiência do utilizador e controlo de acesso. No entanto, os dispositivos aderidos a Azure AD podem fornecer acesso sem problemas a aplicações tanto no local como em aplicações em nuvem. Para obter mais informações, consulte [como o SSO para os recursos no local funciona em dispositivos ligados à Azure AD](azuread-join-sso.md).

As secções seguintes listam considerações para diferentes tipos de aplicações e recursos.

### <a name="cloud-based-applications"></a>Aplicações baseadas em nuvem

Se uma aplicação for adicionada à galeria de aplicações AZure AD, os utilizadores obtêm dispositivos de ad AD Azure. Não é necessária nenhuma configuração adicional. Os utilizadores obtêm SSO em ambos os navegadores Microsoft Edge e Chrome. Para o Chrome, é necessário implementar a extensão de [Contas Windows 10.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) 

Todas as aplicações Win32 que:

- Confie no Gestor de Conta Web (WAM) para pedidos simbólicos também obtenha SSO em dispositivos aderidos Azure AD. 
- Não confie no WAM pode solicitar aos utilizadores a autenticação. 

### <a name="on-premises-web-applications"></a>Aplicações web no local

Se as suas aplicações forem personalizadas construídas e/ou hospedadas no local, tem de as adicionar aos sites fidedignos do seu navegador para:

- Permitir que a autenticação integrada do Windows funcione 
- Fornecer uma experiência SSO sem solicitação aos utilizadores. 

Se utilizar O FS AD, consulte [Verificar e gerir um único sinal de s-on com AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação:** Considere hospedar-se na nuvem (por exemplo, Azure) e integrar-se com a Azure AD para uma melhor experiência.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplicações no local que se baseiam em protocolos legados

Os utilizadores obtêm SSO de dispositivos aderidos a Azure AD se o dispositivo tiver acesso a um controlador de domínio. 

**Recomendação:** Implementar [o proxy da App AD Azure](../manage-apps/application-proxy.md) para permitir um acesso seguro a estas aplicações.

### <a name="on-premises-network-shares"></a>Ações de rede no local

Os seus utilizadores têm SSO de Azure AD ligado a dispositivos quando um dispositivo tem acesso a um controlador de domínio no local.

### <a name="printers"></a>Impressoras

Para impressoras, é necessário implementar [uma impressão em nuvem híbrida](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para descobrir impressoras em dispositivos aderidos Azure. 

Embora as impressoras não possam ser automaticamente descobertas num ambiente apenas na nuvem, os seus utilizadores também podem usar o caminho unc das impressoras para adicioná-las diretamente. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicações no local com base na autenticação da máquina

Os dispositivos azure AD não suportam aplicações no local que dependam da autenticação da máquina. 

**Recomendação:** Considere retirar estas aplicações e mudar-se para as suas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços de Ambiente de Trabalho Remoto

A ligação remota de ambiente de trabalho a um dispositivo aderido a Azure AD requer que a máquina hospedeira seja aderida ao Azure AD ou a AD Híbrida Azure. O ambiente de trabalho remoto de um dispositivo não associado ou não-Windows não é suportado. Para mais informações, consulte [Connect to remote Azure AD](/windows/client-management/connect-to-remote-aadj-pc)

A partir da atualização do Windows 10 2004, os utilizadores também podem utilizar o ambiente de trabalho remoto a partir de um dispositivo AZure AD registado no Windows 10 para um dispositivo AD Azure. 

## <a name="understand-your-provisioning-options"></a>Compreenda as suas opções de provisionamento
**Nota:** Os dispositivos de ad AD não podem ser implantados usando a Ferramenta de Preparação do Sistema (Sysprep) ou ferramentas de imagem semelhantes

Pode providenciar a ad AD Azure usando as seguintes abordagens:

- **Self-service in OOBE/Settings** - No modo de self-service, os utilizadores passam pelo processo de ad Azure, quer durante o Windows out of Box Experience (OOBE) quer a partir de Definições do Windows. Para mais informações, consulte [Junte o seu dispositivo de trabalho à rede da sua organização.](../user-help/user-help-join-device-on-network.md) 
- **Windows Autopilot** - Windows Autopilot permite pré-configuração de dispositivos para uma experiência mais suave no OOBE para executar uma junta AD Azure. Para mais informações, consulte a [visão geral do Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Inscrição a granel** - A inscrição a granel permite que um administrador impulsionado Azure AD se junte usando uma ferramenta de provisionamento a granel para configurar dispositivos. Para obter mais informações, consulte [a inscrição em massa para dispositivos Windows](/intune/windows-bulk-enroll).
 
Aqui está uma comparação destas três abordagens 
 
| Elemento | Configuração de self-service | Windows Autopilot | Inscrição em massa |
| --- | --- | --- | --- |
| Requerem interação do utilizador para configurar | Sim | Sim | Não |
| Exigir esforço de TI | Não | Sim | Sim |
| Fluxos aplicáveis | Definições de & OOBE | Apenas OOBE | Apenas OOBE |
| Direitos de administrador local para o utilizador primário | Sim, por defeito. | Configurável | Não |
| Requera suporte ao OEM do dispositivo | Não | Sim | Não |
| Versões suportadas | 1511+ | 1709+ | 1703+ |
 
Escolha a sua abordagem de implantação ou abordagens, revendo o quadro acima e revendo as seguintes considerações para a adoção de qualquer uma das abordagens:  

- Os seus utilizadores são experientes em analisar a configuração por si mesmos? 
   - O self-service pode funcionar melhor para estes utilizadores. Considere o Windows Autopilot para melhorar a experiência do utilizador.  
- Os seus utilizadores são remotos ou dentro das instalações corporativas? 
   - O self-service ou o Autopilot funcionam melhor para os utilizadores remotos para uma configuração sem ações. 
- Prefere um utilizador conduzido ou uma configuração gerida por administradores? 
   - A inscrição a granel funciona melhor para a implementação impulsionada por administradores para configurar dispositivos antes de ser entregue aos utilizadores.     
- Compra dispositivos a partir de 1-2 OEMS ou tem uma ampla distribuição de dispositivos OEM?  
   - Se comprar de OEMs limitados que também suportam o Autopilot, poderá beneficiar de uma integração mais apertada com o Autopilot. 

## <a name="configure-your-device-settings"></a>Configure as definições do seu dispositivo

O portal Azure permite-lhe controlar a implementação de dispositivos aderidos a Azure AD na sua organização. Para configurar as definições relacionadas, na **página do Diretório Ativo Azure,** selecione `Devices > Device settings` .

### <a name="users-may-join-devices-to-azure-ad"></a>Os utilizadores podem associar dispositivos ao Azure AD

Desconfie esta opção para **Todos** ou **Selecionados** com base no âmbito da sua implementação e em quem pretende permitir configurar um dispositivo azure AD. 

![Os utilizadores podem associar dispositivos ao Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locais adicionais nos dispositivos associados do Azure AD

Escolha **Selecionado** e selecione os utilizadores que pretende adicionar ao grupo de administradores locais em todos os dispositivos aderidos a Azure AD. 

![Administradores locais adicionais nos dispositivos associados do Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Requerem que a Auth multi-factor se junte a dispositivos

Selecione **"Sim,** se exigir que os utilizadores realizem MFA enquanto juntam dispositivos ao Azure AD. Para os utilizadores que unem dispositivos ao Azure AD utilizando o MFA, o próprio dispositivo torna-se um segundo fator.

![Requerem que a Auth multi-factor se junte a dispositivos](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Configure as suas definições de mobilidade

Antes de configurar as suas definições de mobilidade, poderá ter de adicionar primeiro um fornecedor de MDM.

**Para adicionar um provedor de MDM:**

1. Na **página Azure Ative Directory**, na secção **Gerir,** clique `Mobility (MDM and MAM)` em . 
1. Clique **na aplicação Adicionar**.
1. Selecione o seu fornecedor DEM na lista.

   ![Adicionar uma aplicação](./media/azureadjoin-plan/04.png)

Selecione o seu fornecedor DEM para configurar as definições relacionadas. 

### <a name="mdm-user-scope"></a>Âmbito do utilizador MDM

Selecione **Some** ou **All** com base no âmbito da sua implementação. 

![Âmbito do utilizador MDM](./media/azureadjoin-plan/05.png)

Com base no seu âmbito, um dos seguintes acontece: 

- **O utilizador está no âmbito do MDM**: Se tiver uma subscrição Azure AD Premium, a inscrição de MDM é automatizada juntamente com a ad Azure. Todos os utilizadores com âmbito de aplicação devem ter uma licença adequada para o seu MDM. Se a inscrição no MDM falhar neste cenário, a adada Azure também será revertida.
- **O utilizador não está no âmbito do MDM**: Se os utilizadores não estiverem no âmbito do MDM, a Azure AD junta-se a conclusões sem qualquer inscrição de MDM. Isto resulta num dispositivo não gerido.

### <a name="mdm-urls"></a>URLs MDM

Existem três URLs que estão relacionados com a sua configuração de MDM:

- URL dos termos de utilização da MDM
- URL de deteção da MDM 
- URL de conformidade da MDM

![Adicionar uma aplicação](./media/azureadjoin-plan/06.png)

Cada URL tem um valor predefinido. Se estes campos estiverem vazios, contacte o seu fornecedor de MDM para mais informações.

### <a name="mam-settings"></a>Definições MAM

O MAM não se aplica à aderimento da Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Configurar o roaming do estado da empresa

Se pretender ativar o roaming do Estado para a Azure AD para que os utilizadores possam sincronizar as suas definições através dos dispositivos, consulte [Enable Enterprise State Roaming in Azure Ative Directory](enterprise-state-roaming-enable.md). 

**Recomendação**: Ative esta definição mesmo para dispositivos híbridos Azure AD.

## <a name="configure-conditional-access"></a>Configure acesso condicional

Se tiver um fornecedor DEM configurado para os seus dispositivos aderentes Azure AD, o fornecedor sinaliza o dispositivo conforme conforme o dispositivo assim que o dispositivo estiver sob gestão. 

![Dispositivo conforme](./media/azureadjoin-plan/46.png)

Pode utilizar esta implementação para [exigir dispositivos geridos para acesso a aplicações na nuvem com acesso condicional.](../conditional-access/require-managed-devices.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Junte-se a um novo dispositivo Windows 10 com Azure AD durante uma primeira execução](azuread-joined-devices-frx.md) 
>  [Junte-se ao seu dispositivo de trabalho na rede da sua organização](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
