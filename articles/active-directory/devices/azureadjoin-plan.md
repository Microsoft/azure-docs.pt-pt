---
title: Como planear a implementação de associação do Azure Active Directory (Azure AD) | Documentos da Microsoft
description: Explica os passos necessários para implementar o Azure AD dispositivos associados no seu ambiente.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b681915eb3ad9960f335d46508b857fa6ec2e20
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551777"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: Planear a sua implementação de associação do Azure AD


Associação do Azure AD permite-lhe associar dispositivos diretamente para o Azure AD sem a necessidade de associar ao Active Directory no local, mantendo os seus utilizadores produtivos e seguros. Associação do Azure AD está pronto para empresas para à escala e implementações de âmbito.   

Este artigo fornece as informações necessárias planear a implementação de associação do Azure AD.

 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que está familiarizado com o [introdução à gestão de dispositivos no Azure Active Directory](../device-management-introduction.md).



## <a name="plan-your-implementation"></a>Planeie a sua implementação

Para planear a implementação de associação do Azure AD, deverá familiarizar-se com:

|   |   |
|---|---|
|![Marcar][1]|Analisar os seus cenários|
|![Marcar][1]|Reveja a infraestrutura de identidade|
|![Marcar][1]|Avaliar a gestão de dispositivos|
|![Marcar][1]|Compreenda as considerações para aplicações e recursos|
|![Marcar][1]|Compreender as opções de aprovisionamento|
|![Marcar][1]|Configurar o roaming de estado empresarial|
|![Marcar][1]|Configurar o acesso condicional|







## <a name="review-your-scenarios"></a>Analisar os seus cenários 

Embora híbrida associação do Azure AD pode ser preferencial para determinados cenários, o associação do Azure AD permite-lhe efetuar a transição para um modelo de cloud-first com Windows. Se estiver a planear modernizar a gestão de dispositivos e reduzir os custos de TI relacionados com o dispositivo, a associação do Azure AD fornece um ótimo alicerce para alcançar esses objetivos.  

 
Deve considerar a associação do Azure AD Se os objetivos de alinham com os seguintes critérios:

- Estão adotando o Microsoft 365 como o conjunto de produtividade para os seus utilizadores.

- Pretender gerir dispositivos com uma solução de gestão de dispositivos na cloud.

- Pretende simplificar o aprovisionamento de dispositivos para os utilizadores distribuídos geograficamente.

- Planeja modernize a sua infraestrutura de aplicativo.
 

 

## <a name="review-your-identity-infrastructure"></a>Reveja a infraestrutura de identidade  

Associação do Azure AD funciona com ambientes e não geridos federados.  


### <a name="managed-environment"></a>Ambiente gerenciado

Um ambiente gerenciado pode ser implementado por meio [sincronização de Hash de palavra-passe](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ou [passar através de autenticação](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) com o início de sessão único totalmente integrado.

Estes cenários não requerem que configurar um servidor de Federação para autenticação.


### <a name="federated-environment"></a>Ambiente federado

Ambiente federado, deve ter um fornecedor de identidade que oferece suporte a protocolos WS-Trust e WS-Fed:

- **WS-Fed:** Esse protocolo é necessário para associar um dispositivo ao Azure AD.

- **WS-Trust:** Esse protocolo é necessário para iniciar sessão num dispositivo associado ao Azure AD. 

Se o fornecedor de identidade não suportar estes protocolos, associação do Azure AD não funciona de forma nativa. A partir do Windows 10 1809, os utilizadores podem iniciar sessão num dispositivo associado ao Azure AD com um fornecedor de identidade baseado em SAML através de [web início de sessão no Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Atualmente, web início de sessão é uma funcionalidade como só de pré-visualização.


### <a name="smartcards-and-certificate-based-authentication"></a>Autenticação baseada em certificados e smart cards

Não é possível utilizar smart cards ou a autenticação baseada em certificado para associar dispositivos ao Azure AD. No entanto, os cartões inteligentes podem ser utilizado para iniciar sessão em dispositivos associados ao Azure AD, se tiver o AD FS configurado.

**Recomendação:** Implemente o Windows Hello para empresas para a autenticação forte, sem palavra-passe em dispositivos Windows 10.


### <a name="user-configuration"></a>Configuração do utilizador

Se criar os utilizadores na sua:

- **Active Directory no local**, terá de sincronizá-los ao Azure AD com [do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 

- **O Azure AD**, nenhuma configuração adicional é necessária.

No local UPNs diferentes do Azure AD UPNs não são suportadas em dispositivos associados ao Azure AD. Se os utilizadores utilizarem um UPN no local, deverá planear passar a utilizar o respetivo UPN principal no Azure AD.



## <a name="assess-your-device-management"></a>Avaliar a gestão de dispositivos

### <a name="supported-devices"></a>Dispositivos suportados

Associação ao Azure AD:

- Só é aplicável a dispositivos Windows 10. 

- Não é aplicável a versões anteriores do Windows ou outros sistemas operacionais. Se tiver dispositivos do Windows 7/8.1, tem de atualizar para o Windows 10 para implementar a associação do Azure AD.

- Não é suportada em dispositivos com o TPM no modo FIPS.
 
**Recomendação:** Utilize sempre a versão mais recente do Windows 10 para tirar partido das funcionalidades atualizadas.


### <a name="management-platform"></a>Plataforma de gestão

Gestão de dispositivos para dispositivos associados ao Azure AD se baseia numa plataforma MDM, como o Intune) e CSPs de MDM. Windows 10 tem um agente MDM incorporado que funciona com todas as soluções MDM compatíveis.

Existem duas abordagens para gerir o Azure AD dispositivos associados ao:

- **Só de MDM** -um dispositivo exclusivamente é gerido por um fornecedor MDM, como o Intune. Todas as políticas são fornecidas como parte do processo de inscrição de MDM. Para os clientes do Azure AD Premium ou EMS, a inscrição MDM é uma etapa automatizada que faz parte de uma associação do Azure AD.

- **Cogestão** -um dispositivo é gerido por um fornecedor de MDM e o SCCM. Nesta abordagem, o agente do SCCM é instalado num dispositivo gerido pelo MDM para administrar determinados aspetos.

Uma vez associado ao Azure AD dispositivos não estiverem ligados ao Active Directory no local, as políticas de grupo não são suportadas.


Se estiver a utilizar as políticas de grupo, avaliar a paridade de política MDM, utilizando o [ferramenta de análise de migração de MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Reveja as políticas de suportados e não suportadas para determinar se pode utilizar uma solução MDM, em vez de políticas de grupo. Para as políticas não suportadas, considere o seguinte:

- As políticas não suportadas são necessários para associado ao Azure AD dispositivos ou utilizadores?

- São aplicáveis as políticas não suportadas numa nuvem controlado por implementação?

Se a sua solução de MDM não está disponível através da Galeria de aplicações do Azure AD, adicione-o conforme o processo descrito em [integração do Azure Active Directory com o MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Por meio de cogestão, pode usar o SCCM para gerir determinados aspetos dos seus dispositivos, enquanto as políticas são fornecidas por meio de sua plataforma MDM. O Microsoft Intune permite a cogestão no SCCM. Para obter mais informações, consulte [cogestão para dispositivos Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Se usar um produto MDM que não seja o Intune, consulte o seu fornecedor de MDM em cenários de cogestão aplicável.

**Recomendação:** Considere MDM, só os dispositivos associados ao gerenciamento para o Azure AD.



## <a name="understand-considerations-for-applications-and-resources"></a>Compreenda as considerações para aplicações e recursos

Recomendamos que Migre aplicações no local para a cloud para um utilizador de uma melhor experiência e controlo de acesso. No entanto, associado ao Azure AD dispositivos perfeitamente podem fornecer acesso a ambos, no local e na nuvem aplicativos. Para obter mais informações, consulte [dispositivos associados ao como SSO para funciona de recursos no local no Azure AD](azuread-join-sso.md).

As secções seguintes listam as considerações para diferentes tipos de aplicações e recursos.

### <a name="cloud-based-applications"></a>Aplicativos baseados na nuvem

Se um aplicativo é adicionado à Galeria de aplicações do Azure AD, os utilizadores obtêm SSO através de dispositivos associados ao Azure AD. É necessária nenhuma configuração adicional. Os utilizadores obtêm SSO, Microsoft Edge tanto nos browsers do Chrome. Para o Chrome, o que necessita para implementar o [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Todos os aplicativos do Win32 que:

- Confie no Gestor de conta da Web (WAM) para pedidos de token também obtém SSO em dispositivos associados ao Azure AD. 

- Não se baseie em WAM pode pedir aos utilizadores a autenticação. 


### <a name="on-premises-web-applications"></a>Aplicações web no local

Se as suas aplicações são personalizadas criadas e/ou alojadas no local, terá de adicioná-los para sites fidedignos do seu browser para:

- Ativar a autenticação integrada do Windows funcionar 
- Fornece uma experiência SSO de linha de comandos não aos utilizadores. 

Se utilizar o AD FS, veja [Certifique-se e gerir o início de sessão único com o AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação:** Considere o alojamento na cloud (por exemplo, o Azure) e a integração com o Azure AD para uma melhor experiência.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplicações contando com protocolos legados no local

Os utilizadores get SSO do Azure AD associado a um dispositivos se o dispositivo tiver acesso a um controlador de domínio. 

**Recomendação:** Implementar [proxy de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para permitir o acesso seguro para estas aplicações.


### <a name="on-premises-network-shares"></a>Partilhas de rede no local

Os utilizadores terem SSO partir de dispositivos associados ao Azure AD quando um dispositivo tem acesso a um controlador de domínio no local.

### <a name="printers"></a>Impressoras

Para impressoras, precisa implantar [impressa de cloud híbrida](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para detetar impressoras no Azure AD dispositivos associados ao. 

Embora impressoras não puderem ser detetadas automaticamente num ambiente de cloud única, os utilizadores também podem utilizar caminho UNC dos impressoras diretamente adicioná-los. 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>Contando com autenticação de máquina de aplicações no local

Dispositivos do Azure AD associado não suportam aplicações no local, contando com autenticação de máquina. 

**Recomendação:** Considere estas aplicações a extinguir e mover a respetivas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços de Ambiente de Trabalho Remoto

Ligação de ambiente de trabalho remoto para um dispositivos associados ao Azure AD requer a máquina de host para ser de qualquer associados ao Azure AD ou Azure AD híbrido. Ambiente de trabalho remoto de um dispositivo retirado ou não Windows não é suportada. Para obter mais informações, consulte [pc associado a ligar ao remoto do Azure AD](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>Compreender as opções de aprovisionamento

Pode aprovisionar a associação do Azure AD com as seguintes abordagens:

- **Self-service no/definições de OOBE** – o modo de self-service, os utilizadores go por meio da associação do Azure AD processar a Windows fora do Box Experience (OOBE) ou durante a partir do Windows, as definições. Para obter mais informações, consulte [associar o seu dispositivo de trabalho para a rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 

- **Windows Autopilot** -Windows Autopilot permite pré-configuração de dispositivos para uma experiência mais suave no OOBE para executar uma associação do Azure AD. Para obter mais informações, consulte a [descrição geral do Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 

- **Inscrição em massa** -inscrição em massa permite uma associação do Azure AD de administrador controlado por através de uma em massa aprovisionamento ferramenta para configurar os dispositivos. Para obter mais informações, consulte [inscrição em massa para dispositivos Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
 


Aqui está uma comparação dessas três abordagens 

 
||Configuração de self-service|Windows Autopilot|Inscrição em massa|
|---|---|---|---|
|Exigir a interação do usuário para configurar|Sim|Sim|Não|
|Exigir esforço IT|Não|Sim|Sim|
|Fluxos aplicável|OOBE & definições|OOBE apenas|OOBE apenas|
|Direitos de administrador local para o utilizador primário|Sim, por predefinição|Configurável|Não|
|Precisam de suporte do OEM do dispositivo|Não|Sim|Não|
|Versões suportadas|1511+|1709+|1703+|
 
Escolha a abordagem de implantação ou abordagens ao rever a tabela acima e rever as seguintes considerações para a adoção de qualquer uma das abordagens:  

- São sua tecnologia de usuários experientes para ir até a configuração propriamente ditas? 

    - Self-Service pode funcionam melhor para estes utilizadores. Considere o Windows Autopilot para melhorar a experiência de utilizador.  

- São os utilizadores remotos ou dentro de locais empresariais? 

    - Self-Service ou trabalho Autopilot melhor para usuários remotos para uma configuração sem preocupações. 
 
- Prefere um controlada pelo usuário ou uma configuração de administrador gerido? 

    - Inscrição em massa funciona melhor para administração controlado por implementação configurar dispositivos antes de entregar aos utilizadores.     

- Comprar o dispositivos a partir de 1 a 2 OEMS, ou tem uma distribuição ampla de dispositivos de OEM?  

    - Se comprar da limitado OEMs que também suportam Autopilot, pode beneficiar de uma integração maior com Autopilot. 
 

## <a name="configure-your-device-settings"></a>Configurar definições do dispositivo

O portal do Azure permite-lhe controlar a implementação do Azure AD associado a um dispositivos na sua organização. Para configurar as definições relacionadas, sobre o **página do Azure Active Directory**, selecione `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Os utilizadores podem associar dispositivos ao Azure AD

Defina esta opção como **todos os** ou **selecionados** com base no âmbito da sua implementação e que o dispositivo que pretende permitir para configurar um Azure AD associado. 

![Os utilizadores podem associar dispositivos ao Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locais adicionais nos dispositivos associados do Azure AD

Escolher **selecionados** e seleciona os utilizadores que pretende adicionar ao grupo de administradores local em dispositivos de todos os Azure AD associado. 

![Administradores locais adicionais nos dispositivos associados do Azure AD](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>Exigir autenticação multifator associar dispositivos

Selecione **"Sim** se exigir que os utilizadores a executar o MFA durante a associação de dispositivos para o Azure AD. Para os usuários a associar dispositivos ao Azure AD através do MFA, o próprio dispositivo se torna um fator 2nd.

![Exigir autenticação multifator associar dispositivos](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>Configurar as definições de mobilidade

Antes de poder configurar as definições de mobilidade, poderá ter de adicionar um fornecedor MDM, pela primeira vez.

**Para adicionar um fornecedor MDM**:

1. Sobre o **página do Azure Active Directory**, na **gerir** secção, clique em `Mobility (MDM and MAM)`. 

2. Clique em **Adicionar aplicação**.

3. Selecione o seu fornecedor de MDM da lista.

    ![Adicionar uma aplicação](./media/azureadjoin-plan/04.png)

Selecione o seu fornecedor MDM para configurar as definições relacionadas. 

### <a name="mdm-user-scope"></a>Âmbito do utilizador MDM

Selecione **alguns** ou **todos os** com base no âmbito da sua implementação. 

![Âmbito do utilizador MDM](./media/azureadjoin-plan/05.png)

Com base no seu âmbito, um dos seguintes acontece: 

- **Utilizador está no âmbito do MDM**: Se tiver uma subscrição do Azure AD Premium, a inscrição MDM é automatizada, juntamente com a associação do Azure AD. Todos os utilizadores de âmbito tem de ter uma licença adequada para a MDM. Se a inscrição MDM falhar neste cenário, associação do Azure AD será também revertida.
    
- **Utilizador não está no âmbito do MDM**: Se os utilizadores não estiverem no âmbito MDM, a associação do Azure AD é concluída sem qualquer inscrição MDM. Isso resulta num dispositivo não gerido.


### <a name="mdm-urls"></a>URLs MDM

Existem três URLs que estão relacionados com a configuração de MDM:

- URL dos termos de utilização do MDM

- URL de deteção de MDM 

- URL de conformidade do MDM


![Adicionar uma aplicação](./media/azureadjoin-plan/06.png)


Cada URL tem um valor predefinido padrão. Se estes campos estiverem vazios, entre em contato com seu fornecedor de MDM para obter mais informações.

### <a name="mam-settings"></a>Definições de MAM

MAM não é aplicável a associação do Azure AD. 


## <a name="configure-enterprise-state-roaming"></a>Configurar o roaming de estado empresarial

Se pretender ativar o roaming de estado para o Azure AD para que os utilizadores podem sincronizar suas configurações em vários dispositivos, veja [ativar Enterprise Estado Roaming no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Recomendação**: Ative esta definição, mesmo para dispositivos de associados ao Azure AD híbrido.


## <a name="configure-conditional-access"></a>Configurar o acesso condicional

Se tiver dispositivos associados de um fornecedor MDM configurado para o Azure AD, o fornecedor sinaliza o dispositivo como estando em conformidade, assim que o dispositivo estiver sob gestão. 

![Dispositivo conforme](./media/azureadjoin-plan/46.png)

Pode utilizar esta implementação para [exigir que os dispositivos geridos para aceder à aplicação de cloud com o acesso condicional](../conditional-access/require-managed-devices.md).




## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Junte-se a um novo dispositivo Windows 10 com o Azure AD durante uma primeira](azuread-joined-devices-frx.md)
> [associar o seu dispositivo de trabalho para a rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
