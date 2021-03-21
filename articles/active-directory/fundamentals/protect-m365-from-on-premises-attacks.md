---
title: Proteger a Microsoft 365 de ataques no local
description: As orientações sobre como garantir um ataque no local não afetam o Microsoft 365.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d548f4d792d8980e2aa5040b09530eaf7868c4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609911"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Proteger a Microsoft 365 de ataques no local

Muitos clientes ligam as suas redes corporativas privadas à Microsoft 365 para beneficiar os seus utilizadores, dispositivos e aplicações. No entanto, estas redes privadas podem ser comprometidas de muitas formas bem documentadas. Como a Microsoft 365 funciona como uma espécie de sistema nervoso para muitas organizações, é fundamental protegê-lo de infraestruturas comprometidas no local.

Este artigo mostra-lhe como configurar os seus sistemas para proteger o seu ambiente em nuvem Microsoft 365 contra o compromisso no local. Focamo-nos principalmente em: 

- Definições de configuração do Azure Ative Directory (Azure AD).
- Como os inquilinos da AD AZure podem ser conectados com segurança aos sistemas no local.
- As compensações necessárias para operar os seus sistemas de forma a proteger os seus sistemas de nuvem de compromissos no local.

Recomendamos vivamente que implemente esta orientação para garantir o seu ambiente em nuvem Microsoft 365.
> [!NOTE]
> Este artigo foi inicialmente publicado como uma publicação de blogue. Foi transferido para a sua localização atual para longevidade e manutenção.
>
> Para criar uma versão offline deste artigo, use a funcionalidade de impressão-para-PDF do seu navegador. Consulte aqui com frequência para obter atualizações.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Vetores de ameaça primária de ambientes comprometidos no local


O seu ambiente em nuvem Microsoft 365 beneficia de uma extensa infraestrutura de monitorização e segurança. Utilizando machine learning e inteligência humana, a Microsoft 365 olha para todo o tráfego mundial. Pode detetar rapidamente ataques e permitir-lhe reconfigurar quase em tempo real. 

Em implementações híbridas que ligam a infraestrutura no local à Microsoft 365, muitas organizações delegam confiança em componentes no local para decisões críticas de autenticação e gestão de objetos de diretório.
Infelizmente, se o ambiente no local estiver comprometido, estas relações de confiança tornam-se oportunidades de um intruso para comprometer o seu ambiente Microsoft 365.

Os dois vetores de ameaça primária são *as relações de confiança da federação* e *a sincronização de contas.* Ambos os vetores podem conceder a um intruso acesso administrativo à sua nuvem.

* **As relações de confiança federadas**, como a autenticação SAML, são usadas para autenticar o Microsoft 365 através da sua infraestrutura de identidade no local. Se um certificado de assinatura de ficha SAML for comprometido, a federação permite que qualquer pessoa que tenha esse certificado se faça passar por qualquer utilizador na sua nuvem. *Recomendamos que desative as relações de confiança da federação para a autenticação para o Microsoft 365, sempre que possível.*

* **A sincronização de conta** pode ser usada para modificar utilizadores privilegiados (incluindo as suas credenciais) ou grupos que tenham privilégios administrativos na Microsoft 365. *Recomendamos que certifique-se de que os objetos sincronizados não possuem privilégios além de um utilizador na Microsoft 365,* quer diretamente quer através da inclusão em papéis ou grupos fidedignos. Certifique-se de que estes objetos não têm nenhuma atribuição direta ou aninhada em papéis ou grupos de nuvem fidedigna.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Proteger a Microsoft 365 do compromisso no local


Para abordar os vetores de ameaça delineados anteriormente, recomendamos que cumpra os princípios ilustrados no seguinte diagrama:

![Arquitetura de referência para proteger a Microsoft 365.](media/protect-m365/protect-m365-principles.png)

1. **Isole totalmente as suas contas de administrador Microsoft 365.** Deviam ser:

    * Mestre em Azure AD.

     * Autenticado utilizando a autenticação multifactor.

     *  Protegido por Azure AD Acesso Condicional.

     *  Acedido apenas utilizando estações de trabalho geridas pela Azure.

    Estas contas de administrador são contas de utilização restrita. *Nenhuma conta no local deve ter privilégios administrativos na Microsoft 365.* 

    Para mais informações, consulte a [visão geral das funções de administrador da Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles). Ver também [Funções para a Microsoft 365 em Azure AD](../roles/m365-workload-docs.md).

1. **Gerencie os dispositivos da Microsoft 365.** Utilize a aderente Azure AD e a gestão de dispositivos móveis baseados na nuvem (MDM) para eliminar as dependências da sua infraestrutura de gestão de dispositivos no local. Estas dependências podem comprometer os controlos de dispositivos e de segurança.

1. **Certifique-se de que nenhuma conta no local tem privilégios elevados para a Microsoft 365.**
    Algumas contas acedem a aplicações no local que requerem a autenticação NTLM, LDAP ou Kerberos. Estas contas devem estar na infraestrutura de identidade da organização no local. Certifique-se de que estas contas, incluindo contas de serviço, não estão incluídas em papéis ou grupos privilegiados na nuvem. Também certifique-se de que as alterações a estas contas não podem afetar a integridade do seu ambiente em nuvem. O software privilegiado no local não deve ser capaz de afetar contas ou funções privilegiadas da Microsoft 365.

1. **Utilize a autenticação em nuvem Azure** para eliminar as dependências das suas credenciais no local. Utilize sempre a autenticação forte, como a autenticação multifactor Windows Hello, FIDO, Microsoft Authenticator ou Azure AD.

## <a name="specific-security-recommendations"></a>Recomendações de segurança específicas


As secções seguintes fornecem orientações específicas sobre como implementar os princípios descritos anteriormente.

### <a name="isolate-privileged-identities"></a>Isolar identidades privilegiadas


No Azure AD, os utilizadores que têm funções privilegiadas, como administradores, são a raiz da confiança para construir e gerir o resto do ambiente. Implementar as seguintes práticas para minimizar os efeitos de um compromisso.

* Utilize contas apenas em nuvem para funções privilegiadas AZure AD e Microsoft 365.

* Implemente [dispositivos de acesso privilegiados](/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerir o Microsoft 365 e a AZure AD.

*  Implementar [Azure AD Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) (PIM) para acesso just-in-time (JIT) a todas as contas humanas que tenham funções privilegiadas. Requerem uma autenticação forte para ativar funções.

* Proporcionar funções administrativas que permitam o [menor privilégio necessário para fazer as tarefas necessárias](../roles/delegate-by-task.md).

* Para permitir uma experiência de atribuição de papéis ricas que inclua delegação e múltiplas funções ao mesmo tempo, considere usar grupos de segurança AD AZure ou Grupos Microsoft 365. Estes grupos são coletivamente *chamados grupos de nuvens.* Também [habilitado o controlo de acesso baseado em funções.](../roles/groups-assign-role.md) Pode utilizar [unidades administrativas](../roles/administrative-units.md) para restringir o âmbito das funções a uma parte da organização.

* Implementar [contas de acesso de emergência](../roles/security-emergency-access.md). *Não* utilize cofres de senha no local para armazenar credenciais.

Para mais informações, consulte [Garantir o acesso privilegiado.](/security/compass/overview) Consulte também [práticas de acesso segura para administradores em Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Use a autenticação em nuvem 

Credenciais são um vetor de ataque primário. Implementar as seguintes práticas para tornar as credenciais mais seguras:

* [Implementar a autenticação sem palavras-passe](../authentication/howto-authentication-passwordless-deployment.md). Reduza o uso de palavras-passe o máximo possível através da implementação de credenciais sem palavras-passe. Estas credenciais são geridas e validadas nativamente na nuvem. Escolha entre estes métodos de autenticação:

   * [Windows Hello para negócios](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [A aplicação Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chaves de segurança FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Implementar a autenticação multifactor](../authentication/howto-mfa-getstarted.md). Fornecimento [de múltiplas credenciais fortes utilizando a autenticação multifactor Azure AD](../fundamentals/resilience-in-credentials.md). Dessa forma, o acesso aos recursos em nuvem exigirá uma credencial que é gerida em Azure AD, além de uma senha no local que pode ser manipulada. Para obter mais informações, consulte [Criar uma estratégia de gestão de controlo de acesso resiliente utilizando a Azure AD.](./resilience-overview.md)

### <a name="limitations-and-tradeoffs"></a>Limitações e compensações

* A gestão de passwords de conta híbrida requer componentes híbridos, tais como agentes de proteção de palavras-passe e agentes de writeback de palavras-passe. Se a sua infraestrutura no local estiver comprometida, os atacantes podem controlar as máquinas em que estes agentes residem. Esta vulnerabilidade não compromete a sua infraestrutura em nuvem. Mas as suas contas na nuvem não protegem estes componentes do compromisso no local.

*  As contas no local sincronizadas com o Ative Directory estão marcadas para nunca expirar em Azure AD. Esta definição é geralmente atenuada pelas definições de palavra-passe do Ative Directory no local. No entanto, se a sua instância no local de Ative Directory estiver comprometida e a sincronização for desativada, tem de definir a opção [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) para forçar alterações de senha.

## <a name="provision-user-access-from-the-cloud"></a>Fornecimento de acesso ao utilizador a partir da nuvem

*O provisionamento* refere-se à criação de contas de utilizador e grupos em aplicações ou fornecedores de identidade.

![Diagrama de arquitetura de provisionamento.](media/protect-m365/protect-m365-provision.png)

Recomendamos os seguintes métodos de provisionamento:

* **Fornecimento de aplicativos hr em nuvem para Azure AD**: Este provisionamento permite isolar um compromisso no local, sem perturbar o seu ciclo deder-de-mudanças de marceneiro das suas aplicações de RH em nuvem para Azure AD.

* **Aplicações em nuvem**: Sempre que possível, implemente [o provisionamento de aplicações Azure AD](../app-provisioning/user-provisioning.md) em oposição a soluções de provisionamento no local. Este método protege algumas das suas aplicações de software como um serviço (SaaS) de serem afetadas por perfis de hackers maliciosos em violações no local. 

* **Identidades externas**: Utilize [a colaboração Azure AD B2B](../external-identities/what-is-b2b.md).
    Este método reduz a dependência das contas no local para a colaboração externa com parceiros, clientes e fornecedores. Avalie cuidadosamente qualquer federação direta com outros fornecedores de identidade. Recomendamos limitar as contas de hóspedes B2B das seguintes formas:

   *  Limite o acesso dos hóspedes a grupos de navegação e outras propriedades no diretório. Use as definições de colaboração externa para restringir a capacidade dos hóspedes de lerem grupos de que não são membros. 

    *   Bloqueie o acesso ao portal Azure. Pode fazer raras exceções necessárias.  Crie uma política de Acesso Condicional que inclua todos os hóspedes e utilizadores externos. Em seguida, [implementar uma política para bloquear o acesso](../../role-based-access-control/conditional-access-azure-management.md). 

* **Florestas desligadas**: Utilize [o provisionamento de nuvens Azure AD](../cloud-sync/what-is-cloud-sync.md). Este método permite ligar-se a florestas desligadas, eliminando a necessidade de estabelecer conectividade ou fidedignidades florestais, que podem alargar o efeito de uma violação no local. 
 
### <a name="limitations-and-tradeoffs"></a>Limitações e compensações

Quando utilizado para a prestação de contas híbridas, o sistema Azure-AD-from-cloud-HR baseia-se na sincronização no local para completar o fluxo de dados do Ative Directory para a Azure AD. Se a sincronização for interrompida, novos registos de empregados não estarão disponíveis no Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Use grupos de nuvem para colaboração e acesso

Os grupos cloud permitem-lhe dissociar a sua colaboração e acesso a partir da sua infraestrutura no local.

* **Colaboração**: Utilize os Grupos Microsoft 365 e as Equipas Microsoft para uma colaboração moderna. Desativar as listas de distribuição no local e atualizar as listas de [distribuição para os Grupos Microsoft 365 no Outlook.](/office365/admin/manage/upgrade-distribution-lists)

* **Acesso**: Utilize grupos de segurança AZure AD ou Grupos Microsoft 365 para autorizar o acesso a aplicações em Azure AD.
* **Licenciamento do Office 365**: Utilize o licenciamento baseado em grupo para provisão ao Office 365 utilizando grupos apenas na nuvem. Este método dissocia o controlo da adesão ao grupo a partir de infraestruturas no local.

Os proprietários de grupos utilizados para o acesso devem ser considerados identidades privilegiadas para evitar a tomada de posse de membros num compromisso no local.
Uma aquisição incluiria a manipulação direta da adesão ao grupo no local ou a manipulação de atributos no local que podem afetar a adesão dinâmica do grupo na Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gerir dispositivos a partir da nuvem


Utilize as capacidades AD do Azure para gerir de forma segura os dispositivos.

-   **Utilizar estações de trabalho do Windows 10**: Implementar dispositivos [azure AD com](../devices/azureadjoin-plan.md) políticas DED. Ativar [o Windows Autopilot](/mem/autopilot/windows-autopilot) para uma experiência de provisionamento totalmente automatizada.

    -   Deprecar máquinas que executam o Windows 8.1 e mais cedo.

    -   Não implemente as máquinas de sistema operativo do servidor como estações de trabalho.

    -   Utilize o [Microsoft Intune](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/microsoft-intune) como fonte de autoridade para todas as cargas de trabalho de gestão de dispositivos.

-   [**Implementar dispositivos de acesso privilegiados**](/security/compass/privileged-access-devices#device-roles-and-profiles): Utilize acesso privilegiado para gerir o Microsoft 365 e o AD AZure.

## <a name="workloads-applications-and-resources"></a>Cargas de trabalho, aplicações e recursos 

-   **Sistemas de assinatura única (SSO) no local** 

    Depreciar qualquer federação no local e infraestruturas de gestão de acessos web. Configure aplicações para usar Azure AD.  

-   **Aplicações saaS e line-of-business (LOB) que suportam protocolos modernos de autenticação** 

    [Utilize Azure AD para SSO](../manage-apps/what-is-single-sign-on.md). Quanto mais aplicações configurar para usar a Azure AD para autenticação, menor é o risco num compromisso no local.


* **Aplicações antigas** 

   * Pode ativar a autenticação, autorização e acesso remoto a aplicações antigas que não suportam a autenticação moderna. Utilize [o Azure Ad Application Proxy](../manage-apps/application-proxy.md). Também pode capacitá-los através de uma solução de controlador de rede ou de entrega de aplicações utilizando [integrações de parceiros de acesso híbridos seguros.](../manage-apps/secure-hybrid-access.md)   

   * Escolha um fornecedor VPN que suporte a autenticação moderna. Integre a sua autenticação com a Azure AD. Num compromisso no local, pode utilizar a Azure AD para desativar ou bloquear o acesso, desativando a VPN.

*  **Servidores de aplicações e carga de trabalho**

   * As aplicações ou recursos que exijam servidores podem ser migrados para a infraestrutura Azure como um serviço (IaaS). Utilize [os Serviços de Domínio Azure AD](../../active-directory-domain-services/overview.md) (Azure AD DS) para dissociar a confiança e a dependência de casos no local do Ative Directory. Para conseguir esta dissociação, certifique-se de que as redes virtuais utilizadas para a Azure AD DS não têm uma ligação com redes corporativas.

   * Siga as orientações para o [nível credencial](/security/compass/privileged-access-access-model#ADATM_BM). Os servidores de aplicações são normalmente considerados ativos de nível 1.

## <a name="conditional-access-policies"></a>Políticas de Acesso Condicional

Utilize o Acesso Condicional Azure AD para interpretar sinais e usá-los para tomar decisões de autenticação. Para mais informações, consulte o [plano de implementação do Acesso Condicional.](../conditional-access/plan-conditional-access.md)

* Utilizar o Acesso Condicional para [bloquear protocolos de autenticação de legados](../conditional-access/howto-conditional-access-policy-block-legacy.md) sempre que possível. Além disso, desative os protocolos de autenticação de legados ao nível da aplicação utilizando uma configuração específica para aplicações.

   Para mais informações, consulte [os protocolos de autenticação legacy.](../fundamentals/auth-sync-overview.md) Ou consulte detalhes específicos para [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e [SharePoint Online.](/powershell/module/sharepoint-online/set-spotenant)

* Implementar as [configurações recomendadas](/microsoft-365/security/office-365-security/identity-access-policies)de identidade e acesso ao dispositivo .

* Se estiver a utilizar uma versão do AD Azure que não inclua acesso condicional, certifique-se de que está a utilizar as [falhas de segurança Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   Para obter mais informações sobre o licenciamento de recursos Azure AD, consulte o [guia de preços AD Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitor"></a>Monitor 

Depois de configurar o seu ambiente para proteger o seu Microsoft 365 de um compromisso no local, [monitorize proativamente](../reports-monitoring/overview-monitoring.md) o ambiente.
### <a name="scenarios-to-monitor"></a>Cenários a monitorizar

Monitorize os seguintes cenários-chave, além de quaisquer cenários específicos da sua organização. Por exemplo, deve monitorizar proativamente o acesso às aplicações e recursos críticos do seu negócio.

* **Atividade suspeita** 

    Monitorize todos os eventos de [risco Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) para atividades suspeitas. [A Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) é integrada nativamente com o Azure Security Center.

    Defina os [locais nomeados](../reports-monitoring/quickstart-configure-named-locations.md) para evitar deteções ruidosas em sinais baseados na localização. 
*  **Alertas de Análise Comportamental do Utilizador e entidade (UEBA)** 

    Use a UEBA para obter informações sobre a deteção de anomalias.
    * O Microsoft Cloud App Security (MCAS) fornece [a UEBA na nuvem.](/cloud-app-security/tutorial-ueba)

    * Pode [integrar no local a UEBA da Azure Advanced Threat Protection (ATP)](/defender-for-identity/install-step2). MCAS lê sinais da Azure AD Identity Protection. 

* **Atividade de contas de acesso de emergência** 

    Monitorize qualquer acesso que utilize [contas de acesso de emergência.](../roles/security-emergency-access.md) Criar alertas para investigações. Este controlo deve incluir: 

   * Inscrições. 

   * Gestão credencial. 

   * Quaisquer atualizações sobre membros do grupo. 

   * Atribuição de candidaturas. 
* **Atividade de função privilegiada**

    Configure e reveja os alertas de segurança [gerados pela Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitorize a atribuição direta de funções privilegiadas fora da PIM gerando alertas sempre que um utilizador é atribuído diretamente.

* **Configurações para inquilinos Azure AD**

    Qualquer alteração nas configurações em todo o inquilino deve gerar alertas no sistema. Estas alterações incluem, mas não se limitam a:

  *  Domínios personalizados atualizados.  

  * Azure AD B2B alterações para listas e listas de bloqueio.

  * Azure AD B2B alterações aos fornecedores de identidade autorizados (fornecedores de identidade SAML através de federação direta ou de acessos sociais).  

  * Alterações na política de acesso condicional ou risco. 

* **Objetos de aplicação e de principal de serviço**
    
   * Novas aplicações ou diretores de serviço que podem necessitar de políticas de acesso condicional. 

   * Credenciais adicionadas aos diretores de serviço.
   * Atividade de consentimento de pedido. 

* **Funções personalizadas**
   * Atualizações para as definições de funções personalizadas. 

   * Papéis personalizados recém-criados. 

### <a name="log-management"></a>Gestão de registos

Defina uma estratégia de armazenamento e retenção de registos, design e implementação para facilitar um conjunto de ferramentas consistente. Por exemplo, você poderia considerar sistemas de informação de segurança e gestão de eventos (SIEM) como Azure Sentinel, consultas comuns, e investigação e livros de jogadas forenses.

* **Registos AD AZure**: Ingerir registos e sinais gerados seguindo consistentemente as melhores práticas para configurações como diagnósticos, retenção de log e ingestão de SIEM. 

    A estratégia de registo deve incluir os seguintes registos AD Azure:
   * Atividade de início de sessão 

   * Registos de auditoria 

   * Eventos de risco 

    A Azure AD proporciona [integração do Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) para os registos de atividades de login e registos de auditoria. Os eventos de risco podem ser ingeridos através da [Microsoft Graph API](/graph/api/resources/identityriskevent). Pode [transmitir registos AD do Azure para os registos do Monitor Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Registos de segurança da infraestrutura híbrida OS**: Todos os registos de infraestruturas de identidade híbrida os registos oss devem ser arquivados e cuidadosamente monitorizados como um sistema de nível 0, devido às implicações na superfície. Incluir os seguintes elementos: 

   *  Azure AD Connect. [A Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) deve ser implementado para monitorizar a sincronização de identidade.

   *  Agentes de procuração de aplicação 


   * Agentes de writeback de palavra-passe 

   * Máquinas de gateway de proteção de palavras-passe  

   * Servidores de política de rede (NPSs) que têm a extensão RADIUS de autenticação multifactor Azure AD 

## <a name="next-steps"></a>Passos seguintes
* [Construa resiliência na gestão de identidade e acesso utilizando a Azure AD](resilience-overview.md)

* [Proteger o acesso externo aos recursos](secure-external-access-resources.md) 
* [Integre todas as suas aplicações com Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)