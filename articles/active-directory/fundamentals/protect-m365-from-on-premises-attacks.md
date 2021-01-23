---
title: Proteger a Microsoft 365 de ataques no local
description: Orientações sobre como garantir um ataque no local não afetam a Microsoft 365
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
ms.openlocfilehash: ecddb950c06c9f8e61f31e104051f5e3b3640ae5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725015"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>Proteger a Microsoft 365 de ataques no local

Muitos clientes ligam as suas redes corporativas privadas à Microsoft 365 para beneficiar os seus utilizadores, dispositivos e aplicações. No entanto, existem muitas formas bem documentadas de estas redes privadas poderem ser comprometidas. Como a Microsoft 365 funciona como o "sistema nervoso" para muitas organizações, é fundamental protegê-lo de infraestruturas comprometidas no local.

Este artigo mostra-lhe como configurar os seus sistemas para proteger o seu ambiente em nuvem Microsoft 365 contra o compromisso no local. Focamo-nos principalmente nas configurações de configuração do inquilino AZure AD, nas formas como os inquilinos da AZure AD podem estar ligados com segurança aos sistemas no local, e nas compensações necessárias para operar os seus sistemas de forma a proteger os seus sistemas de nuvem de compromissos no local.

Recomendamos vivamente que implemente esta orientação para garantir o seu ambiente em nuvem Microsoft 365.
> [!NOTE]
> Este artigo foi inicialmente publicado como uma publicação de blogue. Foi movido para cá para longevidade e manutenção. <br>
Para criar uma versão offline deste artigo, use a impressão digital do seu navegador para a funcionalidade PDF. Consulte aqui com frequência para obter atualizações.

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>Vetores de ameaça primária de ambientes comprometidos no local


O seu ambiente em nuvem Microsoft 365 beneficia de uma extensa infraestrutura de monitorização e segurança. A utilização de machine learning e inteligência humana que olha através do tráfego mundial pode rapidamente detetar ataques e permitir-lhe reconfigurar em tempo quase real. Em implementações híbridas que ligam a infraestrutura no local à Microsoft 365, muitas organizações delegam confiança em componentes no local para decisões críticas de autenticação e gestão de objetos de diretório.
Infelizmente, se o ambiente no local estiver comprometido, estas relações de confiança resultam em oportunidades de os atacantes comprometerem o seu ambiente Microsoft 365.

Os dois vetores de ameaça primária são **as relações de confiança da federação** e **a sincronização de contas.** Ambos os vetores podem conceder a um intruso acesso administrativo à sua nuvem.

* **As relações de confiança federadas**, como a autenticação SAML, são usadas para autenticar o Microsoft 365 através da sua Infraestrutura de Identidade no local. Se um certificado de assinatura de ficha SAML for comprometido, a federação permitirá que qualquer pessoa com esse certificado se personibile qualquer utilizador na sua nuvem. **Recomendamos que desative as relações de confiança da federação para a autenticação para o Microsoft 365, sempre que possível.**

* **A sincronização de conta** pode ser usada para modificar utilizadores privilegiados (incluindo as suas credenciais) ou grupos que concedem privilégios administrativos na Microsoft 365. **Recomendamos que certifique-se de que os objetos sincronizados não possuem privilégios além de um utilizador na Microsoft 365,** quer diretamente quer através da inclusão em papéis ou grupos fidedignos. Certifique-se de que estes objetos não têm nenhuma atribuição direta ou aninhada em papéis ou grupos de nuvem fidedigna.

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>Proteger a Microsoft 365 do compromisso no local


Para abordar os vetores de ameaça acima descritos, recomendamos que cumpra os princípios abaixo ilustrados:

![Arquitetura de referência para proteger a Microsoft 365 ](media/protect-m365/protect-m365-principles.png)

*  **Isole totalmente as suas contas de administrador Microsoft 365.** Deviam estar.

    * Mestre em Azure AD.

     * Autenticado com autenticação multi-factor (MFA).

     *  Seguro por Azure AD acesso condicional.

     *  Acedido apenas utilizando estações de trabalho geridas aZure.

Estas são contas de utilização restrita. **Não deve haver contas no local com privilégios administrativos na Microsoft 365.** Para mais informações, consulte esta [visão geral das funções de administrador da Microsoft 365](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).
Ver também [Funções para a Microsoft 365 no Azure Ative Directory](../roles/m365-workload-docs.md).

*  **Gerencie os dispositivos da Microsoft 365.** Utilize o Azure AD Join e a gestão de dispositivos móveis baseados na nuvem (MDM) para eliminar as dependências da sua infraestrutura de gestão de dispositivos no local, o que pode comprometer os controlos de dispositivos e segurança.

* **Nenhuma conta no local elevou os privilégios à Microsoft 365.**
    As contas que acedem a aplicações no local que requerem a autenticação NTLM, LDAP ou Kerberos precisam de uma conta na infraestrutura de identidade da organização no local. Certifique-se de que estas contas, incluindo contas de serviço, não estão incluídas em funções ou grupos privilegiados na nuvem e que as alterações a estas contas não podem afetar a integridade do seu ambiente na nuvem. O software privilegiado no local não deve ser capaz de impactar contas ou funções privilegiadas da Microsoft 365.

*  **Utilize a autenticação em nuvem Azure** para eliminar as dependências das suas credenciais no local. Utilize sempre uma autenticação forte, como o Windows Hello, FIDO, o Microsoft Authenticator ou o Azure AD MFA.

## <a name="specific-recommendations"></a>Recomendações específicas


As secções seguintes fornecem orientações específicas sobre a forma de aplicar os princípios acima descritos.

### <a name="isolate-privileged-identities"></a>Isolar identidades privilegiadas


No Azure AD, os utilizadores com funções privilegiadas, como administradores, são a raiz da confiança para construir e gerir o resto do ambiente. Implementar as seguintes práticas para minimizar o impacto de um compromisso.

* Use contas apenas em nuvem para Azure AD e Microsoft 365 funções privilegiadas.d

* Implemente [dispositivos de acesso privilegiados](/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerir o Microsoft 365 e a AZure AD.

*  Implementar [Azure AD Gestão de Identidade Privilegiada](../privileged-identity-management/pim-configure.md) (PIM) para o acesso a todas as contas humanas que tenham funções privilegiadas, e exigir uma autenticação forte para ativar funções.

* Proporcionar às funções administrativas o [menor privilégio possível para executar as suas tarefas](../roles/delegate-by-task.md).

* Para permitir uma experiência de atribuição de funções mais rica que inclua delegação e múltiplas funções ao mesmo tempo, considere utilizar grupos de segurança AD AZure ou Grupos Microsoft 365 (coletivamente "grupos de nuvem") e permitir o [controlo de acesso baseado em funções](../roles/groups-assign-role.md). Também pode utilizar [Unidades Administrativas](../roles/administrative-units.md) para restringir o âmbito de funções a uma parte da organização.

* Implemente [contas de acesso de emergência](../roles/security-emergency-access.md) e NÃO utilize cofres de senha no local para armazenar credenciais.

Para mais informações, consulte [a Garantia de acesso privilegiado,](/security/compass/overview)que tem orientações detalhadas sobre este tema. Consulte também [práticas de acesso Secure para administradores em Azure AD](../roles/security-planning.md).

### <a name="use-cloud-authentication"></a>Use a autenticação em nuvem 

Credenciais são um vetor de ataque primário. Implementar as seguintes práticas para tornar as credenciais mais seguras.

* [Implementar autenticação sem palavras-passe](../authentication/howto-authentication-passwordless-deployment.md): Reduza ao máximo a utilização de palavras-passe através da implementação de credenciais sem palavras-passe. Estas credenciais são geridas e validadas nativamente na nuvem. Escolha entre:

   * [Windows Hello para negócios](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Aplicação Authenticator](../authentication/howto-authentication-passwordless-phone.md)

   * [Chaves de segurança FIDO2](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Implementar autenticação multi-factor](../authentication/howto-mfa-getstarted.md): Providenciar [múltiplas credenciais fortes utilizando Azure AD MFA](../fundamentals/resilience-in-credentials.md). Desta forma, o acesso aos recursos em nuvem exigirá uma credencial que é gerida em Azure AD, além de uma senha no local que pode ser manipulada.

   * Para obter mais informações, consulte [Criar uma estratégia resiliente de gestão de controlo de acessos com o Diretório ativo da Azure.](./resilience-overview.md)

**Limitações e compensações**

* A gestão de passwords de conta híbrida requer componentes híbridos, tais como agentes de proteção de palavras-passe e agentes de writeback de palavras-passe. Se a sua infraestrutura no local estiver comprometida, os atacantes podem controlar as máquinas em que estes agentes residem. Embora isso não comprometa a sua infraestrutura em nuvem, as suas contas em nuvem não protegerão estes componentes do compromisso no local.

*  As contas no local sincronizadas com o Ative Directory estão marcadas para nunca expirar em Azure AD, com base no pressuposto de que as políticas de senha de AD no local irão mitigar isso. Se o seu AD no local estiver comprometido e a sincronização da ligação AD tiver de ser desativada, tem de definir a opção [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="provision-user-access-from-the-cloud"></a>Fornecimento de Acesso ao Utilizador a partir da Nuvem

O provisionamento refere-se à criação de contas de utilizador e grupos em aplicações ou fornecedores de identidade.

![Diagrama de arquitetura de provisionamento](media/protect-m365/protect-m365-provision.png)

* **Fornecimento de aplicativos hr em nuvem para Azure AD:** Isto permite que um compromisso no local seja isolado sem perturbar o seu ciclo Joiner-Mover-Leaver das suas aplicações de RH em nuvem para Azure AD.

* **Aplicações cloud:** Sempre que possível, implementar o Provisioning da [App AZure AD](../app-provisioning/user-provisioning.md) em oposição às soluções de provisionamento no local. Isto irá proteger algumas das suas aplicações SaaS de serem envenenadas com perfis de utilizador maliciosos devido a falhas no local. 

* **Identidades externas:** Utilize [a colaboração Azure AD B2B](../external-identities/what-is-b2b.md).
    Isto reduzirá a dependência das contas no local para a colaboração externa com parceiros, clientes e fornecedores. Avalie cuidadosamente qualquer federação direta com outros fornecedores de identidade. Recomendamos limitar as contas de hóspedes B2B das seguintes formas.

   *  Limite o acesso dos hóspedes a grupos de navegação e outras propriedades no diretório. Utilize as definições de colaboração externa para restringir a capacidade de leitura dos grupos de que não são membros. 

    *   Bloqueie o acesso ao portal Azure. Pode fazer raras exceções necessárias.  Crie uma política de Acesso Condicional que inclua todos os hóspedes e utilizadores externos e, em seguida, [implemente uma política para bloquear o acesso.](../../role-based-access-control/conditional-access-azure-management.md) 

* **Florestas desligadas:** Utilize o provisionamento da [nuvem ad Azure](../cloud-provisioning/what-is-cloud-provisioning.md). Isto permite-lhe conectar-se a florestas desligadas, eliminando a necessidade de estabelecer conectividade ou fidedignidades florestais, que podem alargar o impacto de uma violação no local. * 
 
**Limitações e compensações:**

* Quando usado para a prestação de contas híbridas, o AZure AD dos sistemas de RH em nuvem depende da sincronização no local para completar o fluxo de dados de AD para Azure AD. Se a sincronização for interrompida, novos registos de empregados não estarão disponíveis no Azure AD.

## <a name="use-cloud-groups-for-collaboration-and-access"></a>Use grupos de nuvem para colaboração e acesso

Os grupos cloud permitem-lhe dissociar a sua colaboração e acesso a partir da sua infraestrutura no local.

* **Colaboração:** Utilize os Grupos Microsoft 365 e as Equipas Microsoft para uma colaboração moderna. Desativar listas de distribuição no local e atualizar as listas de [distribuição para os Grupos Microsoft 365 no Outlook](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide).

* **Acesso:** Utilize grupos de segurança Azure AD ou Grupos Microsoft 365 para autorizar o acesso a aplicações em Azure AD.
* **Licenciamento do Escritório 365:** Utilize o licenciamento baseado em grupo para provisão ao Office 365 utilizando grupos apenas em nuvem. Isto separa o controlo da adesão ao grupo a partir de infraestruturas no local.

Os proprietários de grupos utilizados para o acesso devem ser considerados identidades privilegiadas para evitar a tomada de posse de membros do compromisso no local.
A tomada de posse inclui manipulação direta da adesão ao grupo no local ou manipulação de atributos no local que podem afetar a adesão dinâmica do grupo na Microsoft 365.

## <a name="manage-devices-from-the-cloud"></a>Gerir dispositivos a partir da nuvem


Utilize as capacidades AD do Azure para gerir de forma segura os dispositivos.

-   **Utilizar estações de trabalho do Windows 10:** Implementar dispositivos [AD AD Azure](../devices/azureadjoin-plan.md) com políticas DE MDM. Ativar [o Windows Autopilot](/mem/autopilot/windows-autopilot) para uma experiência de provisionamento totalmente automatizada.

    -   Depreciar o Windows 8.1 e as máquinas anteriores.

    -   Não implemente as máquinas de sistema operativo server como estações de trabalho.

    -   Utilize o [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) como fonte de autoridade de todas as cargas de trabalho de gestão de dispositivos.

-   [**Implemente dispositivos de acesso privilegiados**](/security/compass/privileged-access-devices#device-roles-and-profiles) para acesso privilegiado para gerir o Microsoft 365 e a AZure AD.

 ## <a name="workloads-applications-and-resources"></a>Cargas de trabalho, aplicações e recursos 

-   **Sistemas SSO no local:** Depreciar qualquer federação no local e infraestruturas de Gestão de Acesso Web e configurar aplicações para usar a Azure AD.  

-   **Aplicações SaaS e LOB que suportam protocolos modernos de autenticação:** [Use Azure AD para uma única solução](../manage-apps/what-is-single-sign-on.md). Quanto mais aplicações configurar para usar a Azure AD para autenticação, menor é o risco no caso de um compromisso no local.


* **Aplicações Legados** 

   * A autenticação, autorização e acesso remoto a aplicações antigas que não suportem a autenticação moderna podem ser ativadas através do [Azure AD Application Proxy](../manage-apps/application-proxy.md). Também podem ser ativados através de uma solução de controlador de rede ou de entrega de aplicações utilizando [integrações de parceiros de acesso híbridos seguros.](../manage-apps/secure-hybrid-access.md)   

   * Escolha um fornecedor VPN que suporte a autenticação moderna e integre a sua autenticação com a Azure AD. No caso de compromisso de instalações anon, você pode usar Azure AD para desativar ou bloquear o acesso desativando a VPN.

*  **Servidores de aplicações e carga de trabalho**

   * As aplicações ou recursos que exijam servidores podem ser migrados para Azure IaaS e utilizar [os Serviços de Domínio AD Azure](../../active-directory-domain-services/overview.md) (Azure AD DS) para dissociar a confiança e a dependência da AD no local. Para conseguir esta dissociação, as redes virtuais utilizadas para a Azure AD DS não devem ter ligação com redes corporativas.

   * Siga a orientação do [tiering credencial](/security/compass/privileged-access-access-model#ADATM_BM). Os Servidores de Aplicações são normalmente considerados ativos de Nível 1.

 ## <a name="conditional-access-policies"></a>Políticas de acesso condicional

Utilize o Acesso Condicional Azure AD para interpretar sinais e tomar decisões de autenticação com base neles. Para mais informações, consulte o [plano de implementação](../conditional-access/plan-conditional-access.md) do Acesso Condicional.

* [Protocolos de Autenticação Legacy](../fundamentals/auth-sync-overview.md): Utilize acesso condicional para bloquear protocolos [de autenticação legado](../conditional-access/howto-conditional-access-policy-block-legacy.md) sempre que possível. Adicionalmente, desative os protocolos de autenticação de legados ao nível da aplicação utilizando a configuração específica da aplicação.

   * Consulte detalhes específicos para [Exchange Online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) e [SharePoint Online.](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps)

* Implementar as [configurações recomendadas de acesso à identidade e ao dispositivo.](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* Se estiver a utilizar uma versão do AZure AD que não inclua acesso condicional, certifique-se de que está a utilizar as [falhas de segurança Azure AD](../fundamentals/concept-fundamentals-security-defaults.md).

   * Para obter mais informações sobre o licenciamento de recursos Azure AD, consulte o [guia de preços AZure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="monitoring"></a>Monitorização 

Depois de configurar o seu ambiente para proteger o Microsoft 365 de um compromisso no local, [monitorize proativamente](../reports-monitoring/overview-monitoring.md) o ambiente.
### <a name="scenarios-to-monitor"></a>Cenários para Monitorizar

Monitorize os seguintes cenários-chave, além de quaisquer cenários específicos da sua organização. Por exemplo, deve monitorizar proativamente o acesso às aplicações e recursos críticos do seu negócio.

* **Atividade suspeita**: Todos os eventos de [risco Azure AD](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) devem ser monitorizados para atividades suspeitas. [A Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) é integrada nativamente com o Azure Security Center.

   * Defina os [locais nomeados](../reports-monitoring/quickstart-configure-named-locations.md) para evitar deteções ruidosas em sinais baseados na localização. 
*  **Alertas de Análise Comportamental da Entidade utilizadora (UEBA)** Use a UEBA para obter informações sobre a deteção de anomalias.
   * O Microsoft Cloud App Discovery (MCAS) fornece [a UEBA na nuvem.](/cloud-app-security/tutorial-ueba)

   * Pode [integrar no local a UEBA a partir do Azure ATP](/defender-for-identity/install-step2). MCAS lê sinais da Azure AD Identity Protection. 

* **Atividade das contas de acesso de emergência**: Qualquer acesso que utilize contas de acesso de [emergência](../roles/security-emergency-access.md) deve ser monitorizado e alertas criados para investigações. Este controlo deve incluir: 

   * Inscrições. 

   * Gestão credencial. 

   * Quaisquer atualizações sobre membros do grupo. 

   *    Atribuição de Candidaturas. 
* **Atividade de função privilegiada**: Configurar e rever alertas de segurança [gerados pela Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts).
    Monitorize a atribuição direta de funções privilegiadas fora da PIM gerando alertas sempre que um utilizador é atribuído diretamente.
* **Configurações para o inquilino Azure AD**: Qualquer alteração às configurações em toda a parte do inquilino deve gerar alertas no sistema. Estes incluem, mas não se limitam a
  *  Atualizar domínios personalizados  

  * Azure AD B2B permite alterações na lista de permitir/bloquear.
  * O Azure AD B2B permitiu fornecedores de identidade (IDPs SAML através de federação direta ou logins sociais).  
  * Alterações da política de acesso condicional ou de risco 

* **Objetos principais de aplicação e serviço:**
   * Novas aplicações ou diretores de serviço que podem necessitar de políticas de acesso condicional. 

   * Credenciais adicionais adicionadas aos principais serviços.
   * Atividade de consentimento de pedido. 

* **Funções personalizadas:**
   * Atualizações das definições de funções personalizadas. 

   * Novas funções personalizadas criadas. 

### <a name="log-management"></a>Gestão de Registos

Defina uma estratégia de armazenamento e retenção de registos, design e implementação para facilitar um pacote de ferramentas consistentes como sistemas SIEM como O Azure Sentinel, consultas comuns e livros de investigação e perícia.

* **Diários AD Azure** Ingerir troncos e sinal produzidos seguindo as melhores práticas consistentes, incluindo configurações de diagnóstico, retenção de registos e ingestão de SIEM. A estratégia de registo deve incluir os seguintes registos AD Azure:
   * Atividade de início de sessão 

   * Registos de auditoria 

   * Eventos de risco 

A Azure AD proporciona [integração do Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md) para os registos de atividades de login e registos de auditoria. Os eventos de risco podem ser ingeridos através [da Microsoft Graph API](/graph/api/resources/identityriskevent). Pode [transmitir registos AD AZure para registos de monitores Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

* **Registos de segurança da infraestrutura híbrida.** Todos os registos de infraestruturas de identidade híbridas os registos de OS devem ser arquivados e cuidadosamente monitorizados como um <br>Sistema de nível 0, dadas as implicações da área da superfície. O que está incluído: 

   *  Azure AD Connect. [A Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) deve ser implementado para monitorizar a sincronização de identidade.

   *  Agentes de procuração de aplicação 


   * Agentes de desafins de palavra-passe 

   * Máquinas de gateway de proteção de palavras-passe  

   * NPS que têm a extensão Azure MFA RADIUS 

## <a name="next-steps"></a>Passos Seguintes
* [Incorporar resiliência na gestão de identidades e acessos com o Azure AD](resilience-overview.md)

* [Proteger o acesso externo aos recursos](secure-external-access-resources.md) 
* [Integre todas as suas aplicações com Azure AD](five-steps-to-full-application-integration-with-azure-ad.md)