---
title: Detalhes da conformidade regulamentar para HIPAA HITRUST 9.2
description: Detalhes da iniciativa hipaa HITRUST 9.2 Conformidade Regulamentar integrada. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 01/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: a3b43ee64be49d9e71c709f8472d9c56435f8e0e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683638"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa HIPAA HITRUST 9.2 Regulatória Compliance

O artigo que se segue detalha como a definição de iniciativa integrada da Azure Policy Regulatory Compliance para **os domínios** e **controlos** de conformidade no HIPAA HITRUST 9.2.
Para obter mais informações sobre esta norma de conformidade, consulte [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html). Para compreender _a Propriedade_, consulte [a definição de política de Azure](../concepts/definition-structure.md#type) e [a responsabilidade partilhada na nuvem.](../../../security/fundamentals/shared-responsibility.md)

Os seguintes mapeamentos são para os controlos **HIPAA HITRUST 9.2.** Utilize a navegação no direito de saltar diretamente para um domínio de **conformidade** específico . Muitos dos controlos são implementados com uma definição de iniciativa [política Azure.](../overview.md) Para rever a definição completa de iniciativa, abra **a Política** no portal Azure e selecione a página **Definições.**
Em seguida, encontre e selecione a definição de iniciativa incorporada **hitrust/HIPAA** Conformidade Regulamentar.

Esta iniciativa incorporada é implementada como parte da amostra de [planta HIPAA HITRUST 9.2](../../blueprints/samples/hipaa-hitrust-9-2.md).

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../overview.md)
> Estas políticas podem ajudá-lo a avaliar o [cumprimento](../how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a conformidade com** a política Azure refere-se apenas às próprias definições políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre domínios de conformidade, controlos e definições de Política Azure para esta norma de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json).

## <a name="privilege-management"></a>Gestão de Privilégios

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>O acesso a funções de gestão ou consolas administrativas para sistemas que acolhem sistemas virtualizados limita-se ao pessoal com base no princípio do menor privilégio e suportado através de controlos técnicos.

**ID**: 11180.01c3System.6 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>Os privilégios são formalmente autorizados e controlados, atribuídos aos utilizadores numa base de necessidade de utilização e evento-a-evento para o seu papel funcional (por exemplo, utilizador ou administrador), e documentados para cada produto/elemento do sistema.

**ID**: 1143.01c1System.123 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão devem ser fechadas nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Portas de gestão remota abertas estão expondo o seu VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam forçar credenciais brutas para obter acesso administrativo à máquina. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>A organização autoriza explicitamente o acesso a funções específicas relevantes para a segurança (implementadas em hardware, software e firmware) e informações relevantes para a segurança.

**ID**: 1144.01c1System.4 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de 3 proprietários deve ser designado para a sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>O controlo de acesso baseado em funções é implementado e capaz de mapear cada utilizador para uma ou mais funções, e cada papel para uma ou mais funções do sistema.

**ID**: 1145.01c2System.1 - 01.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais do que um proprietário de subscrição para ter o administrador a ter acesso a despedimentos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>A organização promove o desenvolvimento e utilização de programas que evitem a necessidade de executar com privilégios elevados e rotinas do sistema para evitar a necessidade de conceder privilégios aos utilizadores.

**ID**: 1146.01c2System.23 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas externas com permissões do proprietário devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |As contas externas com permissões do proprietário devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>Privilégios elevados são atribuídos a um ID de utilizador diferente daqueles utilizados para uso normal do negócio, todos os utilizadores acedem a serviços privilegiados numa única função, e esse acesso privilegiado é minimizado.

**ID**: 1147.01c2System.456 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |As contas pregridas com permissões do proprietário devem ser removidas da sua subscrição.  Contas pregridas são contas que foram impedidas de iniciar sessão. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>A organização restringe o acesso a funções privilegiadas e a todas as informações relevantes para a segurança.

**ID**: 1148.01c2System.78 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções incorporadas como "Proprietário, Contribuidor, Leitor" em vez de funções personalizadas de RBAC, que são propensas a erros. A utilização de funções personalizadas é tratada como uma exceção e requer uma revisão rigorosa e modelação de ameaças |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Máquinas windows devem satisfazer requisitos para "Opções de Segurança - Contas"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Contas" para limitar a utilização de conta local de palavras-passe em branco e o estado da conta de hóspedes. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>A organização facilita a partilha de informação, permitindo aos utilizadores autorizados determinar o acesso de um parceiro de negócio quando a discrição é permitida como definida pela organização e utilizando processos manuais ou mecanismos automatizados para ajudar os utilizadores a tomar decisões de partilha/colaboração de informação.

**ID**: 1149.01c2System.9 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular sobre as ações que os utilizadores podem executar, utilize Role-Based Access Control (RBAC) para gerir permissões em Clusters de Serviços Kubernetes e configurar políticas de autorização relevantes. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>O sistema de controlo de acesso para os componentes do sistema que armazenam, processam ou transmitem informações cobertas é definido com uma &quot; definição de negação por &quot; defeito.

**ID**: 1150.01c2System.10 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão devem ser fechadas nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Portas de gestão remota abertas estão expondo o seu VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam forçar credenciais brutas para obter acesso administrativo à máquina. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>A organização limita a autorização a contas privilegiadas em sistemas de informação a um subconjunto pré-definido de utilizadores.

**ID**: 1151.01c3System.1 - 01.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de 3 proprietários deve ser designado para a sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>A organização audita a execução de funções privilegiadas em sistemas de informação e garante que os sistemas de informação impedem os utilizadores não privilegiados de executar funções privilegiadas.

**ID**: 1152.01c3System.2 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais do que um proprietário de subscrição para ter o administrador a ter acesso a despedimentos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>Todo o acesso ao sistema de ficheiros não expressamente necessário é desativado, e apenas os utilizadores autorizados têm acesso apenas ao que é expressamente necessário para o desempenho das funções de trabalho dos utilizadores.

**ID**: 1153.01c3System.35 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular sobre as ações que os utilizadores podem executar, utilize Role-Based Access Control (RBAC) para gerir permissões em Clusters de Serviços Kubernetes e configurar políticas de autorização relevantes. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>Os empreiteiros só são fornecidos com sistema mínimo e acesso físico depois de a organização avaliar a capacidade do empreiteiro de cumprir os seus requisitos de segurança e o empreiteiro concordar em cumprir.

**ID**: 1154.01c3System.4 - 01.c **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de 3 proprietários deve ser designado para a sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>Autenticação do utilizador para ligações externas

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>São implementados métodos de autenticação fortes como multi-factor, Radius ou Kerberos (para acesso privilegiado) e CHAP (para encriptação de credenciais para métodos de acesso intermitente) para todas as ligações externas à rede de organizações.

**ID**: 1116.01j1Organização.145 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>O acesso remoto por parte de fornecedores e parceiros de negócios (por exemplo, para manutenção remota) é desativado/desativado quando não está a ser utilizado.

**ID**: 1117.01j1Organização.23 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>As organizações implementam encriptação (por exemplo, &nbsp; soluções VPN ou linhas privadas) e registam o acesso remoto à rede da organização por funcionários, empreiteiros ou terceiros (por exemplo, fornecedores).

**ID**: 1118.01j2Organização.124 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>O equipamento de rede é verificado para obter capacidades de ligação inesperadas.

**ID**: 1119.01j2Organização.3 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>As sessões de administração remota são autorizadas, encriptadas e empregam medidas de segurança acrescidas.

**ID**: 1121.01j3Organização.2 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>Se a encriptação não for utilizada para ligações de acesso ao acesso, o CIO ou o seu representante designado fornece uma autorização escrita específica.

**ID**: 1173.01j1Organização.6 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>A organização protege o acesso sem fios a sistemas que contenham informações sensíveis, autenticando tanto os utilizadores como os dispositivos.

**ID**: 1174.01j1Organização.7 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>O acesso remoto à informação empresarial através das redes públicas só ocorre após identificação e autenticação bem sucedidas.

**ID**: 1175.01j1Organização.8 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>A organização requer uma capacidade de retorno com reautorização para verificar ligações de acesso a locais autorizados.

**ID**: 1176.01j2Organização.5 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>Os IDs de utilizador atribuídos aos fornecedores são revistos de acordo com a política de revisão de acesso da organização, no mínimo anualmente.

**ID**: 1177.01j2Organização.6 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>A autenticação do nó, incluindo técnicas criptográficas (por exemplo, certificados de máquina), serve como um meio alternativo de autenticação de grupos de utilizadores remotos onde estão ligados a uma instalação de computador partilhada e segura.

**ID**: 1178.01j2Organização.7 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>O sistema de informação monitoriza e controla métodos de acesso remoto.

**ID**: 1179.01j3Organização.1 - 01.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Proteção remota da porta de diagnóstico e configuração

### <a name="access-to-network-equipment-is-physically-protected"></a>O acesso a equipamentos de rede está fisicamente protegido.

**ID**: 1192.01l1Organização.1 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>Os controlos para o acesso às portas de diagnóstico e configuração incluem a utilização de um bloqueio de chave e a implementação de procedimentos de apoio para controlar o acesso físico à porta.

**ID**: 1193.01l2Organização.13 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão devem ser fechadas nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |Portas de gestão remota abertas estão expondo o seu VM a um alto nível de risco de ataques baseados na Internet. Estes ataques tentam forçar credenciais brutas para obter acesso administrativo à máquina. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>Portas, serviços e aplicações similares instaladas num computador ou sistemas de rede, que não são especificamente necessárias para a funcionalidade do negócio, são desativadas ou removidas.

**ID**: 1194.01l2Organização.2 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para aplicações web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A depuragem remota requer que as portas de entrada sejam abertas numa aplicação web. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>A organização revê o sistema de informação em cada trezentos e sessenta e cinco (365) dias para identificar e desativar funções desnecessárias e não seguras, portos, protocolos e/ou serviços.

**ID**: 1195.01l3Organização.1 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para apps de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuragem remota requer que as portas de entrada sejam abertas em aplicações de função. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>A organização identifica software não autorizado (na lista negra) no sistema de informação, impede a execução do programa de acordo com uma lista de programas de software não autorizados (na lista negra), emprega uma política de autorização, negação por exceção para proibir a execução de software conhecido (lista negra) e analisa e atualiza a lista de programas de software não autorizados (listas negras) anualmente.

**ID**: 1196.01l3Organização.24 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para apps da API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A depuragem remota requer a abertura de portas de entrada em aplicações API. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>A organização desativa os protocolos de rede Bluetooth e peer-to-peer dentro do sistema de informação determinado como desnecessários ou não seguros.

**ID**: 1197.01l3Organização.3 - 01.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>Segregação em Redes

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>Os gateways de segurança da organização (por exemplo, firewalls) aplicam políticas de segurança e estão configurados para filtrar o tráfego entre domínios, bloquear o acesso não autorizado, e são usados para manter a segregação entre segmentos internos de rede com fios, sem fios e externos (por exemplo, a Internet) incluindo DMZs e impor políticas de controlo de acesso para cada um dos domínios.

**ID**: 0805.01m1Organização.12 - 01.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Esta política audita qualquer Serviço de Aplicações não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Esta política audita qualquer DB cosmos não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[O Event Hub deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Esta política audita qualquer Centro de Eventos não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Esta política nega se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. A atribuição de um grupo de segurança de rede a uma sub-rede de gateway fará com que o portal deixe de funcionar. |negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Esta política audita qualquer Cofre chave não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Esta política audita qualquer Service Bus não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Esta política audita qualquer Servidor SQL não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As contas de armazenamento devem usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Esta política audita qualquer Conta de Armazenamento não configurada para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>A rede de organizações é segmentada logicamente e fisicamente com um perímetro de segurança definido e um conjunto graduado de controlos, incluindo sub-redes para componentes do sistema acessíveis ao público que são logicamente separados da rede interna, com base em requisitos organizacionais; e o tráfego é controlado com base na funcionalidade necessária e na classificação dos dados/sistemas com base numa avaliação de risco e nos respetivos requisitos de segurança.

**ID**: 0806.01m2Organização.12356 - 01.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Esta política audita qualquer Serviço de Aplicações não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Esta política audita qualquer DB cosmos não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[O Event Hub deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Esta política audita qualquer Centro de Eventos não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Esta política nega se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. A atribuição de um grupo de segurança de rede a uma sub-rede de gateway fará com que o portal deixe de funcionar. |negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Esta política audita qualquer Cofre chave não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Esta política audita qualquer Service Bus não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Esta política audita qualquer Servidor SQL não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As contas de armazenamento devem usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Esta política audita qualquer Conta de Armazenamento não configurada para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>As redes são separadas de redes de nível de produção ao migrar servidores físicos, aplicações ou dados para servidores virtualizados.

**ID**: 0894.01m2Organização.7 - 01.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Esta política audita qualquer Serviço de Aplicações não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Esta política audita qualquer DB cosmos não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Implementar o observador de rede quando as redes virtuais forem criadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |Esta política cria um recurso de observadores de rede em regiões com redes virtuais. É necessário garantir a existência de um grupo de recursos chamado networkWatcherRG, que será utilizado para implementar instâncias de observadores de rede. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[O Event Hub deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Esta política audita qualquer Centro de Eventos não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[As sub-redes gateway não devem ser configuradas com um grupo de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |Esta política nega se uma sub-rede de gateway estiver configurada com um grupo de segurança de rede. A atribuição de um grupo de segurança de rede a uma sub-rede de gateway fará com que o portal deixe de funcionar. |negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[O Key Vault deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Esta política audita qualquer Cofre chave não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Esta política audita qualquer Service Bus não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[O SQL Server deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Esta política audita qualquer Servidor SQL não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[As contas de armazenamento devem usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Esta política audita qualquer Conta de Armazenamento não configurada para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>Controlo de Ligação à Rede

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>O tráfego de rede é controlado de acordo com a política de controlo de acesso das organizações através de firewall e outras restrições relacionadas com a rede para cada ponto de acesso à rede ou interface gerida pelo serviço de telecomunicações externo.

**ID**: 0809.01n2Organização.1234 - 01.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>A informação transmitida é segura e, no mínimo, encriptada através de redes públicas abertas.

**ID**: 0810.01n2Organização.5 - 01.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>As exceções à política de fluxos de tráfego são documentadas com uma necessidade de missão/empresa de apoio, duração da exceção e revista pelo menos anualmente; as exceções à política de fluxos de tráfego são eliminadas quando já não são apoiadas por uma necessidade explícita de missão/empresa.

**ID**: 0811.01n2Organização.6 - 01.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>Os dispositivos remotos que estabelecem uma ligação não remota não são autorizados a comunicar com recursos externos (remotos).

**ID**: 0812.01n2Organização.8 - 01.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>A capacidade de os utilizadores se conectarem à rede interna é restrita utilizando uma política de negação por defeito e de permitir por exceção em interfaces geridas de acordo com a política de controlo de acesso e os requisitos das aplicações clínicas e empresariais.

**ID**: 0814.01n1Organização.12 - 01.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[Máquinas virtuais devem ser ligadas a uma rede virtual aprovada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |Esta política audita qualquer máquina virtual ligada a uma rede virtual que não seja aprovada. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>Identificação e Autenticação do Utilizador

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>A organização garante que os IDs de utilizador redundantes não são emitidos para outros utilizadores e que todos os utilizadores são identificados e autenticados exclusivamente para o acesso local e remoto aos sistemas de informação.

**ID**: 11109.01q1Organização.57 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>Os utilizadores não organizacionais (todos os utilizadores de sistemas de informação que não sejam utilizadores organizacionais, como pacientes, clientes, empreiteiros ou estrangeiros), ou processos que atuam em nome de utilizadores não organizacionais, determinados a precisar de acesso à informação que reside nos sistemas de informação da organização, são identificados e autenticados de forma única.

**ID**: 11110.01q1Organização.6 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>Quando a autenticação baseada em PKI é utilizada, o sistema de informação valida os certificados construindo e verificando uma via de certificação para uma âncora fiduciária aceite, incluindo a verificação das informações sobre o estado do certificado; impõe o acesso à chave privada correspondente; mapeia a identidade para a conta correspondente do indivíduo ou grupo; e implementa uma cache local de dados de revogação para apoiar a descoberta e validação do caminho em caso de incapacidade de acesso à informação de revogação através da rede.

**ID**: 11111.01q2System.4 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>O sistema de informação emprega mecanismos de autenticação resistentes à repetição, tais como nonce, senhas únicas ou carimbos de tempo para garantir o acesso à rede para contas privilegiadas; e, para a autenticação baseada em hardware, emprega mecanismos que satisfaçam os requisitos mínimos de fichas discutidos no NIST SP 800-63-2, Electronic Authentication Guideline.

**ID**: 11112.01q2Organização.67 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um máximo de 3 proprietários deve ser designado para a sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |Recomenda-se designar até 3 proprietários de subscrições para reduzir o potencial de violação por parte de um proprietário comprometido. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>A organização exige que as assinaturas electrónicas, exclusivas de um indivíduo, não possam ser reutilizadas por, ou reatribuídas a qualquer outra pessoa.

**ID**: 11208.01q1Organização.8 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve haver mais de um proprietário atribuído à sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |Recomenda-se designar mais do que um proprietário de subscrição para ter o administrador a ter acesso a despedimentos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>As assinaturas electrónicas e as assinaturas manuscritas executadas a registos eletrónicos devem estar ligadas aos respetivos registos eletrónicos.

**ID**: 11210.01q2Organização.10 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows que têm os membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais contiver um ou mais dos membros listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>Os registos eletrónicos assinados devem conter informações associadas à assinatura em formato legível pelo homem.

**ID**: 11211.01q2Organização.11 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Máquinas de auditoria do Windows em falta de qualquer um dos membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais não contiver um ou mais membros que estão listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>Os utilizadores que desempenharam funções privilegiadas (por exemplo, administração do sistema) utilizam contas separadas no desempenho dessas funções privilegiadas.

**ID**: 1123.01q1System.2 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows que têm contas extra no grupo de Administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais contiver membros que não estão listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>Os métodos de autenticação de vários fatores são utilizados de acordo com a política organizacional (por exemplo, para acesso remoto à rede).

**ID**: 1125.01q2System.1 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows que têm os membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais contiver um ou mais dos membros listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>Quando são fornecidas fichas para a autenticação de vários fatores, é necessária uma verificação presencial antes da concessão de acesso.

**ID**: 1127.01q2System.3 - 01.q **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Máquinas de auditoria do Windows em falta de qualquer um dos membros especificados no grupo de administradores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o grupo de administradores locais não contiver um ou mais membros que estão listados no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>Identificação dos riscos relacionados com partes externas

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>O acesso às organizações de informação e sistemas por parte de partes externas não é permitido até que sejam realizadas as devidas diligências, os controlos adequados foram executados, e um contrato/acordo que reflita os requisitos de segurança é assinado reconhecendo que compreendem e aceitam as suas obrigações.

**ID**: 1401.05i1Organização.1239 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>As ligações de acesso remoto entre a organização e as partes externas são encriptadas.

**ID**: 1402.05i1Organização.45 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>O acesso concedido a partes externas limita-se ao mínimo necessário e concedido apenas durante a duração exigida.

**ID**: 1403.05i1Organização.67 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>A devida diligência do partido externo inclui entrevistas, revisão de documentos, listas de verificação, avaliações de certificação (por exemplo, HITRUST) &nbsp; ou outros meios remotos.

**ID**: 1404.05i2Organização.1 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>A identificação dos riscos relacionados com o acesso de partes externas tem em conta um conjunto mínimo de questões especificamente definidas.

**ID**: 1418.05i1Organização.8 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>A organização obtém garantias satisfatórias de que existe uma segurança de informação razoável em toda a sua cadeia de fornecimento de informação através da realização de uma revisão anual, que inclui todos os parceiros/terceiros-fornecedores de que depende a sua cadeia de fornecimento de informação.

**ID**: 1450.05i2Organização.2 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>Os fornecedores de serviços em nuvem concebem e implementam controlos para mitigar e conter riscos de segurança de dados através da separação adequada de direitos, acesso baseado em funções e acesso de menor privilégio para todo o pessoal dentro da sua cadeia de fornecimento.

**ID**: 1451.05iCSPOrganização.2 - 05.i **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>Registo de Auditoria

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>É criado um registo de auditoria seguro para todas as atividades do sistema (criar, ler, atualizar, excluir) envolvendo informações cobertas.

**ID**: 1202.09aa1System.1 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico na Azure Data Lake Store devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Audite se existem atualizações de segurança do sistema em falta e atualizações críticas que devem ser instaladas para garantir que os conjuntos de escala de máquinas virtuais Windows e Linux estão seguros. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>Os registos de auditoria incluem o ID exclusivo do utilizador, o ID exclusivo do assunto dos dados, a função executada e a data/hora em que o evento foi realizado.

**ID**: 1203.09aa1System.2 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico em Aplicações Lógicas devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>As atividades de utilizadores privilegiados (administradores, operadores, etc.) incluem o sucesso/falha do evento, a hora em que ocorreu o evento, a conta envolvida, os processos envolvidos e informações adicionais sobre o evento.

**ID**: 1204.09aa1System.3 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico no IoT Hub devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>Os registos de mensagens enviadas e recebidas são mantidos, incluindo a data, hora, origem e destino da mensagem, mas não o seu conteúdo.

**ID**: 1205.09aa2System.1 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico nas contas do Lote devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>A auditoria está sempre disponível enquanto o sistema estiver ativo e rastreie eventos-chave, acesso de dados de sucesso/falha, alterações na configuração da segurança do sistema, utilização privilegiada ou de utilidade, quaisquer alarmes levantados, &nbsp; ativação e desativação de sistemas de proteção (por exemplo, A/V e IDS), ativação e desativação de mecanismos de identificação e autenticação, e criação e eliminação de objetos de nível de sistema.

**ID**: 1206.09aa2System.23 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico em conjuntos de escala de máquina virtual devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Recomenda-se que os Registos possam ser recriados quando são necessárias investigações em caso de incidente ou de compromisso. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>Os registos de auditoria são mantidos por 90 dias e os registos de auditoria mais antigos são arquivados por um ano.

**ID**: 1207.09aa2System.4 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico no Azure Stream Analytics devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Os registos de diagnóstico no Centro de Eventos devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>Os registos de auditoria são mantidos para atividades de gestão, arranque/paragem/paragem/erros de aplicação, alterações de ficheiros e alterações na política de segurança.

**ID**: 1208.09aa3System.1 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico nos serviços de pesquisa devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Os registos de diagnóstico no Service Bus devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>O sistema de informação gera registos de auditoria contendo as seguintes informações detalhadas: (i) nome de ficheiros acedido; (ii) programa ou comando utilizado para iniciar o evento; e (iii) endereços de origem e destino.

**ID**: 1209.09aa3System.2 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Auditoria habilitando registos de diagnóstico na aplicação. Isto permite-lhe recriar pistas de atividade para fins de investigação se ocorrer um incidente de segurança ou se a sua rede estiver comprometida |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>Todas as divulgações de informações cobertas dentro ou fora da organização são registadas, incluindo tipo de divulgação, data/hora do evento, destinatário e remetente.

**ID**: 1210.09aa3System.3 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Definição de diagnóstico de auditoria](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |Definição de diagnóstico de auditoria para tipos de recursos selecionados |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Os registos de diagnóstico em Data Lake Analytics devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>A organização verifica a cada noventa (90) dias por cada extrato de informação coberta registrado que os dados são apagados ou a sua utilização ainda é necessária.

**ID**: 1211.09aa3System.4 - 09.aa **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no servidor SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no seu SQL Server deve ser ativada para rastrear as atividades de base de dados em todas as bases de dados do servidor e guardá-las num registo de auditoria. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Os registos de diagnóstico no Cofre de Chaves devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditoria habilitando registos de diagnóstico. Isto permite-lhe recriar pistas de atividade para uso para fins de investigação quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>Utilização do sistema de monitorização

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>As ligações remotas não autorizadas aos sistemas de informação são monitorizadas e revistas pelo menos trimestralmente, e são tomadas medidas adequadas se for descoberta uma ligação não autorizada.

**ID**: 1120.09ab3System.9 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve recolher registos de atividade de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Esta política audita o perfil de registo do Azure Monitor que não exporta atividades de todas as regiões apoiadas pelo Azure, incluindo a nível global. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>A organização monitoriza o sistema de informação para identificar irregularidades ou anomalias que sejam indicadores de uma avaria ou compromisso do sistema e ajude a confirmar que o sistema está funcionando em um estado ideal, resiliente e seguro.

**ID**: 12100.09ab2System.15 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente Log Analytics deve ser instalado em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Esta política audita quaisquer máquinas virtuais Windows/Linux se o agente Log Analytics não estiver instalado. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>A organização especifica a frequência com que os registos de auditoria são revistos, como as revisões são documentadas, e as funções e responsabilidades específicas do pessoal que realiza as revisões, incluindo as certificações profissionais ou outras qualificações necessárias.

**ID**: 12101.09ab1Organização.3 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente Log Analytics deve ser instalado em conjuntos de escala de máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Esta política audita quaisquer conjuntos de balanças de máquinas virtuais Windows/Linux se o agente Log Analytics não estiver instalado. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>A organização testará periodicamente os seus processos de monitorização e deteção, remediará deficiências e melhorará os seus processos.

**ID**: 12102.09ab1Organização.4 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows nas quais o agente Log Analytics não está ligado como esperado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o agente não estiver instalado, ou se for instalado, mas o agente de objetos COMConfigManager.MgmtSvcCfg retorna que está registado num espaço de trabalho diferente do ID especificado no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>Todos os requisitos legais aplicáveis relacionados com a monitorização do acesso autorizado e tentativas de acesso não autorizados são cumpridos.

**ID**: 1212.09ab1System.1 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Esta política garante que um perfil de registo recolhe registos para categorias de "escrever", "excluir" e "ação" |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>Os sistemas automatizados implantados em todo o ambiente da organização são utilizados para monitorizar eventos-chave e atividade anómala, e analisar registos de sistemas, dos quais os resultados são revistos regularmente.

**ID**: 1213.09ab2System.128 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O fornecimento automático do agente Log Analytics deve ser ativado na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center recolhe dados das suas máquinas virtuais Azure. Os dados são recolhidos pelo agente Log Analytics, anteriormente conhecido como O Agente de Monitorização da Microsoft (MMA), que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho Log Analytics para análise. Recomendamos que o fornecimento automático de automóveis implemente automaticamente o agente em todos os VMs Azure suportados e quaisquer novos que sejam criados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>A monitorização inclui operações privilegiadas, acesso autorizado ou tentativas de acesso não autorizadas, incluindo tentativas de acesso a contas desativadas e alertas ou falhas do sistema.

**ID**: 1214.09ab2System.3456 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve recolher registos de atividade de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Esta política audita o perfil de registo do Azure Monitor que não exporta atividades de todas as regiões apoiadas pelo Azure, incluindo a nível global. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>Sistemas de auditoria e monitorização utilizados pela organização apoiam a redução da auditoria e a geração de relatórios.

**ID**: 1215.09ab2System.7 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente Log Analytics deve ser instalado em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |Esta política audita quaisquer máquinas virtuais Windows/Linux se o agente Log Analytics não estiver instalado. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>Os sistemas automatizados são utilizados para rever as atividades de monitorização dos sistemas de segurança (por exemplo, IPS/IDS) e registos do sistema diariamente, e identificar e documentar anomalias.

**ID**: 1216.09ab3System.12 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente Log Analytics deve ser instalado em conjuntos de escala de máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |Esta política audita quaisquer conjuntos de balanças de máquinas virtuais Windows/Linux se o agente Log Analytics não estiver instalado. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>São gerados alertas para que o pessoal técnico analise e investigue atividades suspeitas ou suspeitas de violações.

**ID**: 1217.09ab3System.3 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows nas quais o agente Log Analytics não está ligado como esperado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se o agente não estiver instalado, ou se for instalado, mas o agente de objetos COMConfigManager.MgmtSvcCfg retorna que está registado num espaço de trabalho diferente do ID especificado no parâmetro da política. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>O sistema de informação é capaz de processar automaticamente os registos de auditoria para eventos de interesse com base em critérios selecionáveis.

**ID**: 1219.09ab3System.10 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Esta política garante que um perfil de registo recolhe registos para categorias de "escrever", "excluir" e "ação" |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>A monitorização inclui comunicações de entrada e saída e monitorização da integridade dos ficheiros.

**ID**: 1220.09ab3System.56 - 09.ab **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O fornecimento automático do agente Log Analytics deve ser ativado na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center recolhe dados das suas máquinas virtuais Azure. Os dados são recolhidos pelo agente Log Analytics, anteriormente conhecido como O Agente de Monitorização da Microsoft (MMA), que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho Log Analytics para análise. Recomendamos que o fornecimento automático de automóveis implemente automaticamente o agente em todos os VMs Azure suportados e quaisquer novos que sejam criados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>Registos de administrador e operador

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>A organização garante que a exploração madeireira adequada é ativada para auditar atividades de administrador; e revê regularmente os registos do administrador do sistema e do operador.

**ID**: 1270.09ad1System.12 - 09.ad **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>Um sistema de deteção de intrusões gerido fora do controlo de administradores de sistemas e de rede é utilizado para monitorizar as atividades de administração de sistemas e redes para o cumprimento.

**ID**: 1271.09ad1System.1 - 09.ad **Ownership**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>Segregação de Deveres

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>A separação de direitos é utilizada para limitar o risco de modificação não autorizada ou não intencional de informações e sistemas.

**ID**: 1229.09c1Organização.1 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular sobre as ações que os utilizadores podem executar, utilize Role-Based Access Control (RBAC) para gerir permissões em Clusters de Serviços Kubernetes e configurar políticas de autorização relevantes. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>Nenhuma pessoa é capaz de aceder, modificar ou usar sistemas de informação sem autorização ou deteção.

**ID**: 1230.09c2Organização.1 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar o uso das regras personalizadas do RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Auditar funções incorporadas como "Proprietário, Contribuidor, Leitor" em vez de funções personalizadas de RBAC, que são propensas a erros. A utilização de funções personalizadas é tratada como uma exceção e requer uma revisão rigorosa e modelação de ameaças |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>O acesso aos indivíduos responsáveis pela gestão dos controlos de &nbsp; acesso limita-se ao mínimo necessário com base no papel e responsabilidades de cada utilizador e estes indivíduos não podem aceder a funções de auditoria relacionadas com esses controlos.

**ID**: 1232.09c3Organização.12 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para a "Atribuição de Direitos de Utilizador"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Atribuição de Direitos de Utilizador" para permitir o registo local, RDP, acesso a partir da rede e muitas outras atividades do utilizador. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>As atividades de auditoria de segurança são independentes.

**ID**: 1276.09c2Organização.2 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Esta política garante que não existem funções de proprietário de subscrição personalizadas. |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>O início de um evento é separado da sua autorização para reduzir a possibilidade de conluio.

**ID**: 1277.09c2Organização.4 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Opções de Segurança - Controlo de Contas de Utilizador"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Controlo de Conta de Utilizador" para o modo para administradores, comportamento de elevação rápida e virtualização de falhas de registo e ficheiros e registos. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>A organização identifica deveres que exigem a separação e define autorizações de acesso ao sistema de informação para apoiar a separação de direitos; e os deveres incompatíveis são segregados em vários utilizadores para minimizar a oportunidade de uso indevido ou fraude.

**ID**: 1278.09c2Organização.56 - 09.c **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Esta política garante que não existem funções de proprietário de subscrição personalizadas. |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>Controlos contra código malicioso

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>Os antivírus e anti-spyware são instalados, operativos e atualizados em todos os dispositivos do utilizador final para realizar análises periódicas dos sistemas para identificar e remover software não autorizado. Ambientes de servidor para os quais o desenvolvedor de software do servidor recomenda especificamente que não instale software antivírus e anti-spyware baseado no hospedeiro podem resolver o requisito através de uma solução de deteção de malware baseada na rede (NBMD).

**ID**: 0201.09j1Organização.124 - 09.j **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Implementar extensão padrão do Microsoft IaaSAntimalware para o Windows Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |Esta política implementa uma extensão Microsoft IaaSAntimalware com uma configuração padrão quando um VM não está configurado com a extensão antimalware. |implementarIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |Audite a existência e a saúde de uma solução de proteção de pontos finais nos conjuntos de escala de máquinas virtuais, para protegê-las de ameaças e vulnerabilidades. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[O Microsoft Antimalware para Azure deve ser configurado para atualizar automaticamente as assinaturas de proteção](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |Esta política audita qualquer máquina virtual do Windows não configurada com a atualização automática das assinaturas de proteção antimalware do Microsoft. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Monitor que falta proteção de ponto final no Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Os servidores sem um agente instalado de Proteção de Pontos Finais serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[As atualizações de sistema devem ser instaladas nos seus computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>Back-up

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>Cópias de segurança de informações e software são feitas e os testes dos meios de comunicação e procedimentos de restauração são regularmente realizados em intervalos apropriados.

**ID**: 1616.09l1Organização.16 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Esta política audita qualquer Base de Dados Azure SQL com cópia de segurança geo-redundante a longo prazo não ativada. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>Uma definição formal do nível de cópia de segurança necessária para cada sistema é definida e documentada, incluindo a forma como cada sistema será restaurado, o âmbito dos dados a ser imagem, a frequência da imagem e a duração da retenção com base nos requisitos contratuais, legais, regulamentares e empresariais relevantes.

**ID**: 1617.09l1Organização.23 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |A Azure Database for MySQL permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>As cópias de segurança são armazenadas num local remoto fisicamente seguro, a uma distância suficiente para torná-los razoavelmente imunes aos danos causados aos dados no local primário, e estão em vigor controlos físicos e ambientais razoáveis para garantir a sua proteção no local remoto.

**ID**: 1618.09l1Organização.45 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |A Azure Database for PostgreSQL permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>Os registos de inventário das cópias de segurança, incluindo o conteúdo e a localização atual, são mantidos.

**ID**: 1619.09l1Organização.7 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |A Azure Database for MariaDB permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>Quando o serviço de cópia de segurança é entregue por terceiros, o acordo de nível de serviço inclui as proteções detalhadas para controlar a confidencialidade, integridade e disponibilidade das informações de backup.

**ID**: 1620.09l1Organização.8 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garantir a proteção das suas Máquinas Virtuais Azure, ativando a cópia de segurança do Azure. O Azure Backup é uma solução segura e eficaz de proteção de dados para o Azure. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>As ferramentas automatizadas são usadas para rastrear todas as cópias de segurança.

**ID**: 1621.09l2Organização.1 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |Esta política audita qualquer Base de Dados Azure SQL com cópia de segurança geo-redundante a longo prazo não ativada. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>A integridade e segurança das cópias de cópias de segurança são mantidas para garantir a disponibilidade futura, e quaisquer potenciais problemas de acessibilidade com as cópias de cópias de cópias de reserva são identificados e atenuados em caso de desastre em toda a área.

**ID**: 1622.09l2Organização.23 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database para MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |A Azure Database for MySQL permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>As informações cobertas são apoiadas num formato encriptado para garantir a confidencialidade.

**ID**: 1623.09l2Organização.4 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |A Azure Database for PostgreSQL permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>A organização realiza backups incrementais ou diferenciais diariamente e cópias de segurança completas semanalmente para separar os meios de comunicação.

**ID**: 1624.09l3Organização.12 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |A Azure Database for MariaDB permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>Três (3) gerações de backups (full plus all related incremental or diferencial backups) são armazenadas fora do local, e tanto as cópias de segurança no local como fora do local são registadas com nome, data, hora e ação.

**ID**: 1625.09l3Organização.34 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garantir a proteção das suas Máquinas Virtuais Azure, ativando a cópia de segurança do Azure. O Azure Backup é uma solução segura e eficaz de proteção de dados para o Azure. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>A organização garante que uma cópia atual e recuperável de informações cobertas está disponível antes do movimento dos servidores.

**ID**: 1626.09l3Organização.5 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |A Azure Database for PostgreSQL permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>A organização testa informações de backup após cada cópia de segurança para verificar a fiabilidade dos meios de comunicação e a integridade da informação, e pelo menos anualmente depois.

**ID**: 1627.09l3Organização.6 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Backup geo-redundante deve ser ativado para Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |A Azure Database for MariaDB permite-lhe escolher a opção de redundância para o seu servidor de base de dados. Pode ser configurado para um armazenamento de backup geo-redundante no qual os dados não são apenas armazenados dentro da região em que o seu servidor está hospedado, mas também é replicado para uma região emparelhada para fornecer opção de recuperação em caso de falha na região. Configurar o armazenamento geo-redundante para cópia de segurança só é permitido durante a criação do servidor. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>As funções e responsabilidades dos membros da força de trabalho no processo de backup de dados são identificadas e comunicadas à força de trabalho; em particular, os utilizadores do Bring Your Own Device (BYOD) são obrigados a realizar cópias de segurança dos dados organizacionais e/ou do cliente nos seus dispositivos.

**ID**: 1699.09l1Organização.10 - 09.l **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A azure Backup deve ser ativado para máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Garantir a proteção das suas Máquinas Virtuais Azure, ativando a cópia de segurança do Azure. O Azure Backup é uma solução segura e eficaz de proteção de dados para o Azure. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>Controlos de rede

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>A organização monitoriza para todo o acesso sem fios autorizado e não autorizado ao sistema de informação e proíbe a instalação de pontos de acesso sem fios (WAPs), a menos que explicitamente autorizados por escrito pelo CIO ou pelo seu representante designado.

**ID**: 0858.09m1Organização.4 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Todas as portas de rede devem ser restringidas em grupos de segurança de rede associados à sua máquina virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |O Centro de Segurança Azure identificou as regras de entrada de alguns dos seus grupos de segurança de rede como demasiado permissivas. As regras de entrada não devem permitir o acesso a partir das gamas "Qualquer" ou "Internet". Isto pode potencialmente permitir que os atacantes direcionem os seus recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[As máquinas windows devem satisfazer os requisitos para 'Windows Firewall Properties'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Windows Firewall Properties" para estado de firewall, ligações, gestão de regras e notificações. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>A organização garante a segurança da informação nas redes, a disponibilidade de serviços de rede e serviços de informação utilizando a rede e a proteção de serviços conectados de acesso não autorizado.

**ID**: 0859.09m1Organização.78 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>A organização gere formalmente equipamentos na rede, incluindo equipamentos em áreas de utilizador.

**ID**: 0860.09m1Organização.9 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Implementar definições de diagnóstico para grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |Esta política implementa automaticamente as definições de diagnóstico para grupos de segurança de rede. Uma conta de armazenamento com o nome '{storagePrefixParameter}{NSGLocation}' será criada automaticamente. |implementarIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Esta política audita qualquer Service Bus não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>Para identificar e autenticar dispositivos em redes locais e/ou de vasta área, incluindo redes sem fios, &nbsp; o sistema de informação utiliza uma (i) solução de informação conhecida partilhada ou (ii) uma solução de autenticação organizacional, da qual a seleção e a força exatas dependem da categorização de segurança do sistema de informação.

**ID**: 0861.09m2Organização.67 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Serviço de Aplicações deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |Esta política audita qualquer Serviço de Aplicações não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[As máquinas windows devem satisfazer os requisitos para "Opções de Segurança - Acesso à Rede"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Acesso à Rede" para incluir acesso a utilizadores anónimos, contas locais e acesso remoto ao registo. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>A organização garante que os sistemas de informação protejam a confidencialidade e integridade das informações transmitidas, incluindo durante a preparação para a transmissão e durante a receção.

**ID**: 0862.09m2Organização.8 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O SQL Server deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |Esta política audita qualquer Servidor SQL não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>A organização constrói uma configuração de firewall que restringe as ligações entre redes não confiáveis e quaisquer componentes do sistema no ambiente de informação coberto; e quaisquer alterações na configuração da firewall são atualizadas no diagrama da rede.

**ID**: 0863.09m2Organização.910 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Event Hub deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |Esta política audita qualquer Centro de Eventos não configurado para utilizar um ponto final de serviço de rede virtual. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>As restrições de utilização e orientação de implementação são formalmente definidas para o VoIP, incluindo a autorização e monitorização do serviço.

**ID**: 0864.09m2Organização.12 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Esta política audita qualquer DB cosmos não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>A organização (i) autoriza ligações do sistema de informação a outros sistemas de informação fora da organização através da utilização de acordos de segurança de interconexão ou outro acordo formal; ii documentar cada ligação, as características da interface, os requisitos de segurança e a natureza das informações comunicadas; iii utilize uma política de negação, permitindo, por exceção, permitir ligações do sistema de informação a outros sistemas de informação fora da organização; e (iv) aplica uma regra de negação padrão que deixa cair todo o tráfego através de firewalls ou ferramentas de filtragem de portas nos seus pontos finais (estações de trabalho, servidores, etc.), exceto os serviços e portas que são explicitamente permitidos.

**ID**: 0865.09m2Organização.13 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Key Vault deve usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |Esta política audita qualquer Cofre chave não configurado para usar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>A organização descreve os grupos, funções e responsabilidades pela gestão lógica das componentes da rede e assegura a coordenação e consistência nos elementos da infraestrutura de rede.

**ID**: 0866.09m3Organização.1516 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso à rede às contas de armazenamento deve ser restringido. Configure as regras de rede para que apenas aplicações de redes permitidas possam aceder à conta de armazenamento. Para permitir ligações de clientes específicos da Internet ou no local, o acesso pode ser concedido ao tráfego a partir de redes virtuais específicas do Azure ou de intervalos de endereços IP da Internet pública |Auditoria, Negar, Deficientes |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>Os pontos de acesso sem fios são colocados em áreas seguras e desligados quando não estão a ser utilizados (por exemplo, noites, fins de semana).

**ID**: 0867.09m3Organização.17 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem usar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |Esta política audita qualquer Conta de Armazenamento não configurada para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>A organização constrói uma configuração de firewall para restringir o tráfego de entrada e saída ao que é necessário para o ambiente de dados coberto.

**ID**: 0868.09m3Organização.18 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>Os ficheiros de configuração do router estão protegidos e sincronizados.

**ID**: 0869.09m3Organização.19 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>O acesso a todos os proxies é negado, com exceção dos anfitriões, portos e serviços que são explicitamente necessários.

**ID**: 0870.09m3Organização.20 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>Os servidores DNS autoritários estão segregados em funções internas e externas.

**ID**: 0871.09m3Organização.22 - 09.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Registo de Contentores deve utilizar um ponto final de serviço de rede virtual](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |Esta política audita qualquer registo de contentores não configurado para utilizar um ponto final de serviço de rede virtual. |Auditoria, Deficientes |[1.0.0-pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Segurança dos Serviços de Rede

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>Os serviços acordados prestados por um prestador/gestor de serviços de rede são formalmente geridos e monitorizados para garantir que são fornecidos de forma segura.

**ID**: 0835.09n1Organização.1 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |O Security Center utiliza o agente da Microsoft Dependency para recolher dados de tráfego de rede das suas máquinas virtuais Azure para permitir funcionalidades avançadas de proteção da rede, tais como visualização de tráfego no mapa de rede, recomendações de endurecimento da rede e ameaças específicas de rede. |AuditIfNotExists, Desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |Utilize o novo Gestor de Recursos Azure para as suas máquinas virtuais para fornecer melhorias de segurança tais como: controlo de acesso mais forte (RBAC), melhor auditoria, implementação e governação baseada em recursos do Azure, acesso a identidades geridas, acesso a cofre chave para segredos, autenticação baseada em Azure e suporte para tags e grupos de recursos para uma gestão de segurança mais fácil |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>A organização autoriza formalmente e documenta as características de cada ligação de um sistema de informação a outros sistemas de informação fora da organização.

**ID**: 0836.09.n2Organização.1 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |O Security Center utiliza o agente da Microsoft Dependency para recolher dados de tráfego de rede das suas máquinas virtuais Azure para permitir funcionalidades avançadas de proteção da rede, tais como visualização de tráfego no mapa de rede, recomendações de endurecimento da rede e ameaças específicas de rede. |AuditIfNotExists, Desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>Os acordos formais com fornecedores de sistemas de informação externos incluem obrigações específicas para a segurança e a privacidade.

**ID**: 0837.09.n2Organização.2 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>A organização analisa e atualiza os acordos de segurança de interconexão numa base contínua, verificando a aplicação dos requisitos de segurança.

**ID**: 0885.09n2Organização.3 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |O Security Center utiliza o agente da Microsoft Dependency para recolher dados de tráfego de rede das suas máquinas virtuais Azure para permitir funcionalidades avançadas de proteção da rede, tais como visualização de tráfego no mapa de rede, recomendações de endurecimento da rede e ameaças específicas de rede. |AuditIfNotExists, Desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>A organização emprega e documenta num acordo formal ou noutro documento, quer i) permitir tudo, negar por exceção, ou, ii) negar tudo, permitir autorização por exceção (preferencialmente), política para permitir que sistemas de informação específicos se conectem a sistemas de informação externos.

**ID**: 0886.09n2Organização.4 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>A organização exige que os prestadores de serviços externos/subcontratados identifiquem as funções específicas, portas e protocolos utilizados na prestação dos serviços externos/externos.

**ID**: 0887.09n2Organização.5 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O agente de recolha de dados de tráfego de rede deve ser instalado em máquinas virtuais do Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |O Security Center utiliza o agente da Microsoft Dependency para recolher dados de tráfego de rede das suas máquinas virtuais Azure para permitir funcionalidades avançadas de proteção da rede, tais como visualização de tráfego no mapa de rede, recomendações de endurecimento da rede e ameaças específicas de rede. |AuditIfNotExists, Desativado |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>O contrato com o prestador de serviços externo/subcontratado inclui a especificação de que o prestador de serviços é responsável pela proteção das informações cobertas partilhadas.

**ID**: 0888.09n2Organização.6 - 09.n **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>Gestão de Mídia Amovível

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>A organização, com base no nível de classificação de dados, regista meios de comunicação (incluindo portáteis) antes da utilização, coloca restrições razoáveis sobre a forma como esses meios de comunicação são utilizados e fornece um nível adequado de proteção física e lógica (incluindo encriptação) para os meios que contenham informações cobertas até serem devidamente destruídos ou higienizados.

**ID**: 0301.09o1Organização.123 - 09.o **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação transparente de dados nas bases de dados SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e cumprir os requisitos de conformidade |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>A organização protege e controla os meios de comunicação que contêm informações sensíveis durante o transporte fora das áreas controladas.

**ID**: 0302.09o2Organização.1 - 09.o **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação do disco deve ser aplicada em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem encriptação de disco ativada serão monitorizadas pelo Azure Security Center como recomendações. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>São identificados meios digitais e não digitais que exijam uma utilização restrita e as salvaguardas específicas utilizadas para restringir a sua utilização.

**ID**: 0303.09o2Organização.2 - 09.o **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os discos não ligados devem ser encriptados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Esta política audita qualquer disco não ligado sem encriptação ativada. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>A organização restringe o uso de meios removíveis e de meios de comunicação amovíveis pessoalmente em sistemas organizacionais.

**ID**: 0304.09o3Organização.1 - 09.o **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Exigir encriptação nas contas da Data Lake Store](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |Esta política garante que a encriptação está ativada em todas as contas da Data Lake Store |negar |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona-lhe uma maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os servidores SQL devem usar as chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona uma maior transparência e controlo sobre o Protetor TDE, uma maior segurança com um serviço externo apoiado pelo HSM e a promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>Políticas e procedimentos de intercâmbio de informações

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>Os fornecedores de serviços em nuvem utilizam uma plataforma de virtualização reconhecida pela indústria e formatos de virtualização padrão (por exemplo, Open Virtualization Format, OVF) para ajudar a garantir a interoperabilidade, e documentaram alterações personalizadas feitas a qualquer hipervisor em uso e todos os ganchos de virtualização específicos da solução disponíveis para revisão do cliente.

**ID**: 0662.09sCSPOrganização.2 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>A organização aborda formalmente múltiplas salvaguardas antes de permitir a utilização de sistemas de informação para troca de informações.

**ID**: 0901.09s1Organização.1 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos tenham acesso às suas Aplicações Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação web. Permitir que apenas os domínios necessários interajam com a sua aplicação web. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>O acesso remoto (externo) aos ativos de informação da organização e o acesso a ativos de informação externos (para os quais a organização não tem controlo) baseia-se em termos e condições claramente definidos.

**ID**: 0902.09s2Organização.13 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acedam às suas Apps de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação Function. Permitir que apenas os domínios necessários interajam com a sua aplicação Função. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>A organização estabelece termos e condições, consistentes com qualquer relação de confiança estabelecida com outras organizações proprietárias, operacionais e/ou mantendo sistemas de informação externos, permitindo que indivíduos autorizados acedam ao sistema de informação a partir de sistemas de informação externos; e (ii) processar, armazenar ou transmitir informações controladas pela organização utilizando sistemas de informação externos.

**ID**: 0911.09s1Organização.2 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acedam à sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação API. Permitir que apenas os domínios necessários interajam com a sua aplicação API. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>A criptografia é usada para proteger a confidencialidade e integridade das sessões de acesso remoto à rede interna e aos sistemas externos.

**ID**: 0912.09s1Organização.4 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para aplicações web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |A depuragem remota requer que as portas de entrada sejam abertas numa aplicação web. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>Protocolos de criptografia fortes são utilizados para salvaguardar informações cobertas durante a transmissão sobre redes públicas menos confiáveis/abertas.

**ID**: 0913.09s1Organização.5 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para apps de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuragem remota requer que as portas de entrada sejam abertas em aplicações de função. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>A organização garante que os requisitos de proteção da comunicação, incluindo a segurança do intercâmbio de informações, são objeto de auditorias de desenvolvimento de políticas e conformidade.

**ID**: 0914.09s1Organização.6 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para apps da API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |A depuragem remota requer a abertura de portas de entrada em aplicações API. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>A organização limita o uso de suportes de armazenamento portáteis controlados pela organização por indivíduos autorizados em sistemas de informação externos.

**ID**: 0915.09s2Organização.2 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>O sistema de informação proíbe a ativação remota de dispositivos de computação colaborativa e fornece uma indicação explícita de utilização aos utilizadores fisicamente presentes nos dispositivos.

**ID**: 0916.09s2Organização.4 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos tenham acesso às suas Aplicações Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação web. Permitir que apenas os domínios necessários interajam com a sua aplicação web. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>Os prestadores de serviços em nuvem utilizam protocolos de rede standardizados seguros (por exemplo, textos não claros e autenticados) para a importação e exportação de dados e para gerir o serviço, e disponibilizam um documento aos consumidores (inquilinos) que detalham as normas de interoperabilidade e portabilidade relevantes envolvidas.

**ID**: 0960.09sCSPOrganização.1 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O CORS não deve permitir que todos os recursos acedam às suas Apps de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |A Partilha de Recursos de Origem Cruzada (CORS) não deve permitir que todos os domínios acedam à sua aplicação Function. Permitir que apenas os domínios necessários interajam com a sua aplicação Função. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>O pessoal é devidamente treinado sobre princípios e práticas principais para todos os tipos de intercâmbio de informações (oral, papel e electrónica).

**ID**: 1325.09s1Organização.3 - 09.s **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Depuragem remota deve ser desligada para apps de funções](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |A depuragem remota requer que as portas de entrada sejam abertas em aplicações de função. A depuragem remota deve ser desligada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>Transações on-line

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>Os dados envolvidos no comércio eletrónico e nas transações online são verificados para determinar se contém informações cobertas.

**ID**: 0943.09y1Organização.1 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>Os protocolos utilizados para comunicar entre todas as partes envolvidas são garantidos utilizando técnicas criptográficas (por exemplo, SSL).

**ID**: 0945.09y1Organização.3 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas Windows que não contenham os certificados especificados em Raiz Fidedigna](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |Requer que os pré-requisitos sejam aplicados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. As máquinas não são compatíveis se a máquina Trusted Root certificate store (Cert:\LocalMachine\Root) não conter um ou mais dos certificados listados pelo parâmetro da política. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>A organização exige o uso da encriptação entre, e o uso de assinaturas eletrónicas por cada uma das partes envolvidas na transação.

**ID**: 0946.09y2Organização.14 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Auditoria que permite apenas ligações via SSL a Azure Cache para Redis. A utilização de ligações seguras garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>A organização garante que o armazenamento dos detalhes da transação está localizado fora de quaisquer ambientes acessíveis ao público (por exemplo, numa plataforma de armazenamento existente na intranet da organização) e não retido e exposto num suporte de armazenamento diretamente acessível a partir da Internet.

**ID**: 0947.09y2Organização.2 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>Sempre que seja utilizada uma autoridade de confiança (por exemplo, para efeitos de emissão e manutenção de assinaturas digitais e/ou certificados digitais), a segurança é integrada e incorporada em todo o processo de gestão de certificados/assinaturas de ponta a ponta.

**ID**: 0948.09y2Organização.3 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>Os protocolos utilizados para as comunicações são reforçados para resolver qualquer nova vulnerabilidade, e as versões atualizadas dos protocolos são adotadas o mais rapidamente possível.

**ID**: 0949.09y2Organização.5 - 09.y **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A API App só deve estar acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[A App de função só deve estar acessível através do HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>Controlo de Software Operacional

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>Apenas os administradores autorizados são autorizados a implementar atualizações aprovadas para software, aplicações e bibliotecas de programas, com base nos requisitos de negócio e nas implicações de segurança da libertação.

**ID**: 0605.10h1System.12 - 10.h **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não satisfaçam a linha de base configurada serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Máquinas Windows devem satisfazer requisitos para "Opções de Segurança - Auditoria"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Auditoria" para forçar a subcategoria da política de auditoria e desligar se não puder registar auditorias de segurança. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Máquinas Windows devem satisfazer requisitos para "Políticas de Auditoria do Sistema - Gestão de Contas"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Gestão de Contas" para auditoria de aplicações, segurança e gestão de grupos de utilizadores, e outros eventos de gestão. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>As aplicações e os sistemas operativos são testados com sucesso para a usabilidade, segurança e impacto antes da produção.

**ID**: 0606.10h2System.1 - 10.h **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditar vulnerabilidades na configuração de segurança em máquinas com Docker instalado e exibido como recomendações no Azure Security Center. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>A organização utiliza o seu programa de controlo de configuração para manter o controlo de todo o software implementado e da documentação do sistema e arquivar versões anteriores de software implementado e documentação do sistema associada.

**ID**: 0607.10h2System.23 - 10.h **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Audite as vulnerabilidades do SO nos conjuntos de escala de máquinas virtuais para protegê-los de ataques. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>Alterar procedimentos de controlo

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>Os gestores responsáveis pelos sistemas de aplicação são também responsáveis pelo controlo rigoroso (segurança) do projeto ou ambiente de apoio e asseguram que todas as alterações propostas do sistema sejam revistas para verificar se não comprometem a segurança do sistema ou do ambiente operacional.

**ID**: 0635.10k1Organização.12 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>A organização aborda formalmente o propósito, âmbito, funções, responsabilidades, compromisso de gestão, coordenação entre entidades organizacionais, e cumprimento da gestão de configuração (por exemplo, através de políticas, normas, processos).

**ID**: 0636.10k2Organização.1 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>A organização desenvolveu, documentou e implementou um plano de gestão de configuração para o sistema de informação.

**ID**: 0637.10k2Organização.2 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>As alterações são formalmente controladas, documentadas e aplicadas de forma a minimizar a corrupção dos sistemas de informação.

**ID**: 0638.10k2Organização.34569 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>As listas de verificação de instalação e as verificações de vulnerabilidade são utilizadas para validar a configuração de servidores, estações de trabalho, dispositivos e aparelhos e garantir que a configuração cumpre os padrões mínimos.

**ID**: 0639.10k2Organização.78 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>Quando o desenvolvimento é subcontratado, os procedimentos de controlo de alteração para resolver a segurança são incluídos no(s) contrato(s) e especificamente exigem que o desenvolvedor rastreie falhas de segurança e resolução de falhas dentro do sistema, componente ou serviço e reporte resultados a pessoal ou funções definidos pela organização.

**ID**: 0640.10k2Organização.1012 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>A organização não utiliza atualizações automatizadas em sistemas críticos.

**ID**: 0641.10k2Organização.11 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>A organização desenvolve, documenta e mantém, sob controlo de configuração, uma configuração de base atual do sistema de informação, e analisa e atualiza a linha de base conforme necessário.

**ID**: 0642.10k3Organização.12 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>A organização (i) estabelece e documenta configurações obrigatórias de configuração para produtos de tecnologias da informação empregues dentro do sistema de informação utilizando as linhas de base de configuração de segurança mais recentes; ii identifique, documente e aprove exceções das definições de configuração estabelecidas obrigatórias para componentes individuais com base em requisitos operacionais explícitos; e (iii) monitoriza e controla as alterações às definições de configuração de acordo com as políticas e procedimentos organizacionais.

**ID**: 0643.10k3Organização.3 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>A organização emprega mecanismos automatizados para (i) gerir, aplicar e verificar definições de configuração; ii responder a alterações não autorizadas nas definições de configuração relacionadas com a segurança da rede e do sistema; e (iii) impor restrições de acesso e auditoria das ações de execução.

**ID**: 0644.10k3Organização.4 - 10.k **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas windows devem satisfazer os requisitos para "Políticas de Auditoria do Sistema - Rastreio Detalhado"](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |As máquinas Windows devem ter as definições de Política de Grupo especificadas na categoria "Políticas de Auditoria do Sistema - Rastreio Detalhado" para auditoria ao DPAPI, criação/rescisão de processos, eventos de RPC e atividade de PNP. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>Controlo de Vulnerabilidades Técnicas

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>As vulnerabilidades técnicas são identificadas, avaliadas para o risco e corrigidas em tempo útil.

**ID**: 0709.10m1Organização.1 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidades deve ser ativada nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita máquinas virtuais para detetar se estão a executar uma solução de avaliação de vulnerabilidade suportada. Um componente central de cada programa de risco e segurança cibernético é a identificação e análise de vulnerabilidades. O nível de preços padrão do Azure Security Center inclui a verificação de vulnerabilidades para as suas máquinas virtuais sem custos adicionais. Além disso, o Security Center pode implantar automaticamente esta ferramenta para si. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditar vulnerabilidades na configuração de segurança em máquinas com Docker instalado e exibido como recomendações no Azure Security Center. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não satisfaçam a linha de base configurada serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Audite as vulnerabilidades do SO nos conjuntos de escala de máquinas virtuais para protegê-los de ataques. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[As vulnerabilidades nas suas bases de dados SQL devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore resultados de avaliação de vulnerabilidades e recomendações para como remediar vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoriza as vulnerabilidades detetadas pela solução de Avaliação de Vulnerabilidades e VMs sem uma solução de Avaliação de Vulnerabilidades no Centro de Segurança Azure como recomendações. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |
|[A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditar cada SQL Managed Instance que não tenha verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditar servidores SQL do Azure que não têm verificações de avaliação de vulnerabilidade recorrentes ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[As máquinas windows devem satisfazer os requisitos para 'Opções de Segurança - Microsoft Network Server'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Microsoft Network Server" para desativar o servidor SMB v1. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>Existe um padrão de configuração endurecido para todos os componentes do sistema e da rede.

**ID**: 0710.10m2Organização.1 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditar cada SQL Managed Instance que não tenha verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>Está em vigor um programa de gestão de vulnerabilidades técnicas para monitorizar, avaliar, classificar e remediar vulnerabilidades identificadas nos sistemas.

**ID**: 0711.10m2Organização.23 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidades deve ser ativada nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita máquinas virtuais para detetar se estão a executar uma solução de avaliação de vulnerabilidade suportada. Um componente central de cada programa de risco e segurança cibernético é a identificação e análise de vulnerabilidades. O nível de preços padrão do Azure Security Center inclui a verificação de vulnerabilidades para as suas máquinas virtuais sem custos adicionais. Além disso, o Security Center pode implantar automaticamente esta ferramenta para si. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="internal-and-external-vulnerability-assessments-of-covered-information-systems-virtualized-environments-and-networked-environments-including-both-network--and-application-layer-tests-are-performed-by-a-qualified-individual-on-a-quarterly-basis-or-after-significant-changes"></a>As avaliações internas e externas de vulnerabilidades de sistemas de informação cobertos, ambientes virtualizados e ambientes em rede, incluindo testes de rede e camadas de aplicação, são efetuadas por um indivíduo qualificado numa base trimestral ou após alterações significativas.

**ID**: 0712.10m2Organização.4 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Monitoriza as vulnerabilidades detetadas pela solução de Avaliação de Vulnerabilidades e VMs sem uma solução de Avaliação de Vulnerabilidades no Centro de Segurança Azure como recomendações. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>As correções são testadas e avaliadas antes de serem instaladas.

**ID**: 0713.10m2Organização.5 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não satisfaçam a linha de base configurada serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>O programa de gestão de vulnerabilidades técnicas é avaliado trimestralmente.

**ID**: 0714.10m2Organização.7 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Audite as vulnerabilidades do SO nos conjuntos de escala de máquinas virtuais para protegê-los de ataques. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>Os sistemas são devidamente endurecidos (por exemplo, configurados apenas com serviços necessários e seguros, portas e protocolos ativados).

**ID**: 0715.10m2Organização.8 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Auditar vulnerabilidades na configuração de segurança em máquinas com Docker instalado e exibido como recomendações no Azure Security Center. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>A organização realiza uma revisão da postura de segurança da empresa conforme necessário, mas não menos do que uma vez em cada trezentos e cinco (365) dias, de acordo com os procedimentos organizacionais do EI.

**ID**: 0716.10m3Organização.1 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades nas suas bases de dados SQL devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |Monitore resultados de avaliação de vulnerabilidades e recomendações para como remediar vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>As ferramentas de digitalização de vulnerabilidades incluem a capacidade de atualizar prontamente as vulnerabilidades do sistema de informação digitalizadas.

**ID**: 0717.10m3Organização.2 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |Audite as vulnerabilidades do SO nos conjuntos de escala de máquinas virtuais para protegê-los de ataques. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>A organização procura vulnerabilidades no sistema de informação e acolhia aplicações para determinar o estado de remediação mensal (automaticamente) e novamente (manual ou automática) quando novas vulnerabilidades que podem afetar os sistemas e ambientes em rede são identificadas e reportadas.

**ID**: 0718.10m3Organização.34 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não satisfaçam a linha de base configurada serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>A organização atualiza a lista de vulnerabilidades do sistema de informação digitalizadas dentro de 30 (30) dias ou quando novas vulnerabilidades são identificadas e reportadas.

**ID**: 0719.10m3Organização.5 - 10.m **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditar cada SQL Managed Instance que não tenha verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>Continuidade do negócio e Avaliação de Riscos

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>A organização identifica os processos de negócio críticos que exigem continuidade do negócio.

**ID**: 1634.12b1Organização.1 - 12.b **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem recuperação de desastres configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais que não tenham a recuperação de desastres configurada. Para saber mais sobre a recuperação de desastres, [https://aka.ms/asr-doc](https://aka.ms/asr-doc) visite. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>Os aspetos de segurança da informação da continuidade do negócio são (i) baseados na identificação de eventos (ou sequência de eventos) que podem causar interrupções nos processos comerciais críticos da organização (por exemplo, falha de equipamentos, erros humanos, roubo, incêndio, atos de catástrofes naturais de terrorismo); ii seguida de uma avaliação dos riscos para determinar a probabilidade e o impacto dessas interrupções, em termos de tempo, escala de danos e período de recuperação; iii Com base nos resultados da avaliação dos riscos, é desenvolvida uma estratégia de continuidade das empresas para identificar a abordagem global da continuidade das empresas; e (iv) uma vez criada esta estratégia, o apoio é fornecido pela administração, e um plano criado e endossado para implementar esta estratégia.

**ID**: 1635.12b1Organização.2 - 12.b **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os cofres-chave devem ter proteção de purga ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A eliminação maliciosa de um cofre chave pode levar à perda permanente de dados. Um infiltrado malicioso na sua organização pode potencialmente apagar e purgar cofres de chaves. A proteção de purga protege-o de ataques de infiltrados, impondo um período de retenção obrigatório para cofres-chave apagados suaves. Ninguém dentro da sua organização ou microsoft será capaz de limpar os seus cofres chave durante o período de retenção de eliminação suave. |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>A análise de impacto do negócio é usada para avaliar as consequências de desastres, falhas de segurança, perda de serviço e disponibilidade de serviços.

**ID**: 1637.12b2Organização.2 - 12.b **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As máquinas Windows devem cumprir os requisitos para 'Opções de Segurança - Consola de recuperação'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |As máquinas windows devem ter as definições de Política de Grupo especificadas na categoria "Opções de Segurança - Consola de recuperação" para permitir a cópia floppy e o acesso a todas as unidades e pastas. Esta política requer que os pré-requisitos de Configuração de Convidados tenham sido implementados no âmbito de atribuição de políticas. Para mais detalhes, [https://aka.ms/gcpol](https://aka.ms/gcpol) visite. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>As avaliações de risco de continuidade do negócio (i) são realizadas anualmente com total envolvimento dos proprietários de recursos e processos empresariais; (ii) considerar todos os processos empresariais e não se limitar aos ativos de informação, mas inclui os resultados específicos da segurança da informação; e (iii) identifica, quantifica e prioriza riscos contra os principais objetivos e critérios de negócio relevantes para a organização, incluindo recursos críticos, impactos de perturbações, tempos de interrupção admissíveis e prioridades de recuperação.

**ID**: 1638.12b2Organização.345 - 12.b **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar máquinas virtuais sem recuperação de desastres configurada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |Audite máquinas virtuais que não tenham a recuperação de desastres configurada. Para saber mais sobre a recuperação de desastres, [https://aka.ms/asr-doc](https://aka.ms/asr-doc) visite. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre a Política Azure:

- [Visão geral da conformidade](../concepts/regulatory-compliance.md) regulamentar.
- Consulte a [estrutura de definição de iniciativa.](../concepts/initiative-definition-structure.md)
- Reveja outros exemplos nas [amostras da Azure Policy](./index.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
