---
title: Detalhes da conformidade regulamentar para cis Microsoft Azure Foundations Benchmark
description: Detalhes da iniciativa integrada cis Microsoft Azure Foundations Benchmark Regulatory Compliance. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 02/09/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 02976da72027f9e5c5396148cd15e361aaf20ab0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099783"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa integrada cis Microsoft Azure Foundations Benchmark Regulatory Compliance

O artigo que se segue detalha como a definição de iniciativa incorporada da Azure Policy Regulatory Compliance mapeia para **domínios** de conformidade e **controlos** no CIS Microsoft Azure Foundations Benchmark.
Para obter mais informações sobre esta norma de conformidade, consulte [o CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/). Para compreender _a Propriedade_, consulte [a definição de política de Azure](../concepts/definition-structure.md#type) e [a responsabilidade partilhada na nuvem.](../../../security/fundamentals/shared-responsibility.md)

Os seguintes mapeamentos são para os controlos de benchmark da **CIS Microsoft Azure Foundations.** Utilize a navegação no direito de saltar diretamente para um domínio de **conformidade** específico . Muitos dos controlos são implementados com uma definição de iniciativa [política Azure.](../overview.md) Para rever a definição completa de iniciativa, abra **a Política** no portal Azure e selecione a página **Definições.**
Em seguida, encontre e selecione a definição de iniciativa de referência de conformidade integrada **cis Microsoft Azure Foundations 1.1.0.**

Esta iniciativa incorporada é implementada como parte da amostra de projeto benchmark da [CIS Microsoft Azure Foundations.](../../blueprints/samples/cis-azure-1-1-0.md)

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../overview.md)
> Estas políticas podem ajudá-lo a avaliar o [cumprimento](../how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a conformidade com** a política Azure refere-se apenas às próprias definições políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre domínios de conformidade, controlos e definições de Política Azure para esta norma de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json).

## <a name="identity-and-access-management"></a>Gestão de Acesso e Identidades

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Certifique-se de que a autenticação de vários fatores está ativada para todos os utilizadores privilegiados

**ID**: CIS Azure 1.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Certifique-se de que a autenticação de vários fatores está ativada para todos os utilizadores não privilegiados

**ID**: CIS Azure 1.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>Certifique-se de que não há utilizadores convidados

**ID**: CIS Azure 1.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas externas com permissões do proprietário devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |As contas externas com permissões do proprietário devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[As contas externas com permissões de leitura devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |As contas externas com privilégios de leitura devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[As contas externas com permissões de escrita devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |As contas externas com privilégios de escrita devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Certifique-se de que não são criadas funções personalizadas de proprietário de subscrição

**ID**: CIS Azure 1.23 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Esta política garante que não existem funções de proprietário de subscrição personalizadas. |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Centro de Segurança

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Certifique-se de que o nível de preços padrão é selecionado

**ID**: CIS Azure 2.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O nível de preços padrão do Centro de Segurança deve ser selecionado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1181c5f-672a-477a-979a-7d58aa086233) |O nível de preços padrão permite a deteção de ameaças para redes e máquinas virtuais, fornecendo inteligência de ameaças, deteção de anomalias e análise de comportamento no Azure Security Center |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Certifique-se de que o "provisionamento automático do agente de monitorização" está definido para "On"

**ID**: CIS Azure 2.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O fornecimento automático do agente Log Analytics deve ser ativado na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center recolhe dados das suas máquinas virtuais Azure. Os dados são recolhidos pelo agente Log Analytics, anteriormente conhecido como O Agente de Monitorização da Microsoft (MMA), que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho Log Analytics para análise. Recomendamos que o fornecimento automático de automóveis implemente automaticamente o agente em todos os VMs Azure suportados e quaisquer novos que sejam criados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>Certifique-se de que a definição de política por defeito da ASC "Monitor System Updates" não é "desativada"

**ID**: CIS Azure 2.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As atualizações de sistema devem ser instaladas nos seus computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>Certifique-se de que a definição de política de predefinição da ASC "Monitorizar as vulnerabilidades dos OS" não é "Desativada"

**ID**: CIS Azure 2.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Os servidores que não satisfaçam a linha de base configurada serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>Certifique-se de que a definição de política por defeito da ASC "Monitor Endpoint Protection" não é "Desativada"

**ID**: CIS Azure 2.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitor que falta proteção de ponto final no Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Os servidores sem um agente instalado de Proteção de Pontos Finais serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>Certifique-se de que a definição de política padrão da ASC "Monitor Disk Encryption" não é "Desativada"

**ID**: CIS Azure 2.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação do disco deve ser aplicada em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem encriptação de disco ativada serão monitorizadas pelo Azure Security Center como recomendações. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>Certifique-se de que a definição de política por defeito da ASC "Monitor Network Security Groups" não é "Desativada"

**ID**: CIS Azure 2.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Recomendações de endurecimento de rede adaptativas devem ser aplicadas na internet face a máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do Grupo de Segurança da Rede que reduzem a potencial superfície de ataque |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>Certifique-se de que a definição de política padrão da ASC "Enable Next Generation Firewall (NGFW) Monitoring" não é "Desativada"

**ID**: CIS Azure 2.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |Proteja as suas máquinas virtuais de potenciais ameaças, restringindo-lhes o acesso a grupos de segurança de rede (NSG). Saiba mais sobre o controlo do tráfego com os NSGs em [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[As subnetas devem ser associadas a um Grupo de Segurança de Rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |Proteja a sua sub-rede de ameaças potenciais restringindo-lhe o acesso a um Grupo de Segurança de Rede (NSG). Os NSGs contêm uma lista de regras da Lista de Controlo de Acesso (ACL) que permitem ou negam o tráfego de rede à sua sub-rede. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>Garantir que a definição de política de predefinição da ASC "Monitor Vulnerability Assessment" não é "Desativada"

**ID**: CIS Azure 2.10 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Uma solução de avaliação de vulnerabilidades deve ser ativada nas suas máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |Audita máquinas virtuais para detetar se estão a executar uma solução de avaliação de vulnerabilidade suportada. Um componente central de cada programa de risco e segurança cibernético é a identificação e análise de vulnerabilidades. O nível de preços padrão do Azure Security Center inclui a verificação de vulnerabilidades para as suas máquinas virtuais sem custos adicionais. Além disso, o Security Center pode implantar automaticamente esta ferramenta para si. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>Certifique-se de que a definição de política padrão asc "Monitor JIT Network Access" não é "Desativada"

**ID**: CIS Azure 2.12 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Possível rede Just In Time (JIT) o acesso será monitorizado pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>Certifique-se de que a definição de política por defeito da ASC "Monitor Adaptive Application Whitelisting" não é "Desativada"

**ID**: CIS Azure 2.13 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |Ative os controlos de aplicação para definir a lista de aplicações seguras conhecidas em execução nas suas máquinas e alerte-o quando outras aplicações são executadas. Isto ajuda a endurecer as suas máquinas contra malware. Para simplificar o processo de configuração e manutenção das suas regras, o Security Center utiliza machine learning para analisar as aplicações em funcionamento em cada máquina e sugerir a lista de aplicações conhecidas e seguras. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>Certifique-se de que a definição de política por defeito da ASC "Monitor SQL Auditing" não é "Desativada"

**ID**: CIS Azure 2.14 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no servidor SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no seu SQL Server deve ser ativada para rastrear as atividades de base de dados em todas as bases de dados do servidor e guardá-las num registo de auditoria. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>Certifique-se de que a definição de política padrão ASC "Monitor SQL Encryption" não é "Desativada"

**ID**: CIS Azure 2.15 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação transparente de dados nas bases de dados SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e cumprir os requisitos de conformidade |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>Certifique-se de que 'Emails de contacto de segurança' está definido

**ID**: CIS Azure 2.16 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As subscrições devem ter um endereço de e-mail de contacto para questões de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Para garantir que as pessoas relevantes da sua organização são notificadas quando há uma potencial falha de segurança numa das suas subscrições, desaça um contacto de segurança para receber notificações de e-mail do Security Center. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>Certifique-se de que 'Enviar notificação por e-mail para alertas de alta gravidade' está definido para 'On'

**ID**: CIS Azure 2.18 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A notificação por e-mail para alertas de alta gravidade deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Para garantir que as pessoas relevantes da sua organização são notificadas quando há uma potencial falha de segurança numa das suas subscrições, ativar notificações de e-mail para alertas de alta gravidade no Centro de Segurança. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>Certifique-se de que 'Enviar e-mail também para os proprietários de subscrição' está definido para 'On'

**ID**: CIS Azure 2.19 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |Para garantir que os seus proprietários de subscrição são notificados quando houver uma potencial falha de segurança na sua subscrição, desemote notificações de email aos proprietários de subscrições para alertas de alta gravidade no Security Center. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>Contas de Armazenamento

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Certifique-se de que a 'transferência segura necessária' está definida para 'Activado'

**ID**: CIS Azure 3.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Certifique-se de que o "nível de acesso público" está definido para Privado para recipientes blob

**ID**: CIS Azure 3.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público à conta de armazenamento deve ser proibido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O público anónimo lê o acesso a contentores e bolhas no Azure Storage é uma forma conveniente de partilhar dados, mas pode apresentar riscos de segurança. Para evitar violações de dados causadas por acesso anónimo não gasto, a Microsoft recomenda impedir o acesso do público a uma conta de armazenamento, a menos que o seu cenário o exija. |auditoria, negação, desativado |[2.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Garantir que a regra de acesso à rede padrão para contas de armazenamento está definida para negar

**ID**: CIS Azure 3.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso à rede às contas de armazenamento deve ser restringido. Configure as regras de rede para que apenas aplicações de redes permitidas possam aceder à conta de armazenamento. Para permitir ligações de clientes específicos da Internet ou no local, o acesso pode ser concedido ao tráfego a partir de redes virtuais específicas do Azure ou de intervalos de endereços IP da Internet pública |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Certifique-se de que 'Serviços Microsoft Fidedignos' está ativado para acesso à conta de armazenamento

**ID**: CIS Azure 3.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Alguns serviços da Microsoft que interagem com contas de armazenamento operam a partir de redes que não podem ser autorizadas a ter acesso através das regras de rede. Para ajudar este tipo de serviço a funcionar conforme pretendido, permita que o conjunto de serviços confiáveis da Microsoft ignore as regras da rede. Estes serviços utilizarão então uma autenticação forte para aceder à conta de armazenamento. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>Serviços de Base de Dados

### <a name="ensure-that-auditing-is-set-to-on"></a>Certifique-se de que a 'Auditoria' está definida para 'On'

**ID**: CIS Azure 4.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no servidor SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no seu SQL Server deve ser ativada para rastrear as atividades de base de dados em todas as bases de dados do servidor e guardá-las num registo de auditoria. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>Certifique-se de que os "AuditActionGroups" na política de "auditoria" para um servidor SQL são definidos corretamente

**ID**: CIS Azure 4.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As definições de auditoria do SQL devem ter Action-Groups configuradas para capturar atividades críticas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |A propriedade AuditActionsAndGroups deve conter pelo menos SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP, FAILED_DATABASE_AUTHENTICATION_GROUP, BATCH_COMPLETED_GROUP para garantir uma auditoria completa |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Garantir que a retenção de "auditoria" seja "superior a 90 dias"

**ID**: CIS Azure 4.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL devem ser configurados com retenção de auditoria de 90 dias ou superior](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Os servidores SQL devem ser configurados com retenção de auditoria de 90 dias ou superior. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>Certifique-se de que a 'Segurança Avançada de Dados' num servidor SQL está definida para 'On'

**ID**: CIS Azure 4.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A segurança avançada dos dados deve ser ativada em SqL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada SQL Managed Instance sem segurança avançada de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A segurança avançada dos dados deve ser ativada nos seus servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança Avançada de Dados |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Certifique-se de que o Azure Ative Directory Admin está configurado

**ID**: CIS Azure 4.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Fornecimento de auditoria de um administrador do Azure Ative Directory para o seu servidor SQL para permitir a autenticação Azure AD. A autenticação AZURE AD permite a gestão simplificada de permissões e a gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Certifique-se de que a 'encriptação de dados' está definida para 'On' numa Base de Dados SQL

**ID**: CIS Azure 4.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação transparente de dados nas bases de dados SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e cumprir os requisitos de conformidade |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>Certifique-se de que o protetor TDE do servidor SQL está encriptado com BYOK (Use a sua própria chave)

**ID**: CIS Azure 4.10 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona-lhe uma maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os servidores SQL devem usar as chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona uma maior transparência e controlo sobre o Protetor TDE, uma maior segurança com um serviço externo apoiado pelo HSM e a promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Certifique-se de que a 'ligação SSL' está definida para 'ENABLED' para o MySQL Database Server

**ID**: CIS Azure 4.11 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_checkpoints' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.12 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os pontos de verificação de registo devem ser ativados para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_checkpoints definição ativada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Certifique-se de que a 'ligação SSL' está definida para 'ENABLED' para o Servidor de Base de Dados Pós-SQL

**ID**: CIS Azure 4.13 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_connections' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.14 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As ligações de registo devem ser ativadas para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_connections definição ativada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_disconnections' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.15 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As desconexões devem ser registadas para servidores de base de dados PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_disconnections ativadas. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'connection_throttling' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.17 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O estrangulamento de ligação deve ser ativado para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem o estrangulamento de Ligação ativado. Esta definição permite o estrangulamento temporário da ligação por IP para demasiadas falhas de início de senha inválidas. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

### <a name="ensure-that-a-log-profile-exists"></a>Certifique-se de que existe um perfil de registo

**ID**: CIS Azure 5.1.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As subscrições do Azure devem ter um perfil de registo de registo de atividades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |Esta política garante se for ativado um perfil de registo para a exportação de registos de atividades. Audita se não houver um perfil de registo criado para exportar os registos para uma conta de armazenamento ou para um centro de eventos. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>Certifique-se de que a retenção de registo de atividade está definida em 365 dias ou mais

**ID**: CIS Azure 5.1.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O registo de atividade deve ser mantido por pelo menos um ano](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |Esta política audita o registo de atividade se a retenção não for definida durante 365 dias ou para sempre (dias de retenção definidos para 0). |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>Garantir que o perfil de auditoria captura todas as atividades

**ID**: CIS Azure 5.1.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |Esta política garante que um perfil de registo recolhe registos para categorias de "escrever", "excluir" e "ação" |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>Certifique-se de que o perfil de registo captura registos de atividade para todas as regiões, incluindo global

**ID**: CIS Azure 5.1.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Monitor deve recolher registos de atividade de todas as regiões](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |Esta política audita o perfil de registo do Azure Monitor que não exporta atividades de todas as regiões apoiadas pelo Azure, incluindo a nível global. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Certifique-se de que o recipiente de armazenamento que armazena os registos de atividade não é acessível ao público

**ID**: CIS Azure 5.1.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público à conta de armazenamento deve ser proibido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O público anónimo lê o acesso a contentores e bolhas no Azure Storage é uma forma conveniente de partilhar dados, mas pode apresentar riscos de segurança. Para evitar violações de dados causadas por acesso anónimo não gasto, a Microsoft recomenda impedir o acesso do público a uma conta de armazenamento, a menos que o seu cenário o exija. |auditoria, negação, desativado |[2.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Certifique-se de que a conta de armazenamento que contém o recipiente com registos de atividade é encriptada com BYOK (Use a sua própria chave)

**ID**: CIS Azure 5.1.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A conta de armazenamento que contenha o contentor com registos de atividade deve ser encriptada com a BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Esta política audita se a conta de Armazenamento que contém o contentor com registos de atividade for encriptada com a BYOK. A apólice só funciona se a conta de armazenamento estiver na mesma subscrição que os registos de atividade por design. Mais informações sobre a encriptação do Armazenamento Azure em repouso podem ser encontradas [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) aqui.  |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Certifique-se de que a sessão de registo para Azure KeyVault está 'Activada'

**ID**: CIS Azure 5.1.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de recursos no Cofre de Chaves devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar pistas de atividade para uso para fins de investigação quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Certifique-se de que existe um alerta de registo de atividade para criar a atribuição de políticas

**ID**: CIS Azure 5.2.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações políticas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Esta política audita operações políticas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar grupo de segurança de rede

**ID**: CIS Azure 5.2.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Certifique-se de que existe um alerta de registo de atividade para eliminar o Grupo de Segurança da Rede

**ID**: CIS Azure 5.2.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a regra do grupo de segurança da rede

**ID**: CIS Azure 5.2.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Certifique-se de que existe um alerta de registo de atividade para a Regra do Grupo de Segurança da Rede de Eliminação

**ID**: CIS Azure 5.2.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a solução de segurança

**ID**: CIS Azure 5.2.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Esta política audita operações de segurança específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Certifique-se de que existe um alerta de registo de atividade para eliminar a solução de segurança

**ID**: CIS Azure 5.2.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Esta política audita operações de segurança específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar ou eliminar a regra de firewall do servidor SQL

**ID**: CIS Azure 5.2.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>Certifique-se de que existe um alerta de registo de atividade para a política de segurança de atualização

**ID**: CIS Azure 5.2.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Esta política audita operações de segurança específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>Rede

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Certifique-se de que o acesso rdp é restrito a partir da internet

**ID**: CIS Azure 6.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso do PDR da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Esta política audita qualquer regra de segurança de rede que permita o acesso rdp a partir da Internet |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Certifique-se de que o acesso ao SSH é restrito a partir da internet

**ID**: CIS Azure 6.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso ao SSH a partir da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Esta política audita qualquer regra de segurança de rede que permita o acesso ao SSH a partir da Internet |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Certifique-se de que o Observador de Rede está 'Activado'

**ID**: CIS Azure 6.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Máquinas Virtuais

### <a name="ensure-that-os-disk-are-encrypted"></a>Certifique-se de que o 'disco DE' está encriptado

**ID**: CIS Azure 7.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação do disco deve ser aplicada em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem encriptação de disco ativada serão monitorizadas pelo Azure Security Center como recomendações. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>Certifique-se de que os "discos de dados" estão encriptados

**ID**: CIS Azure 7.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação do disco deve ser aplicada em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem encriptação de disco ativada serão monitorizadas pelo Azure Security Center como recomendações. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>Certifique-se de que os "discos não ligados" são encriptados

**ID**: CIS Azure 7.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os discos não ligados devem ser encriptados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Esta política audita qualquer disco não ligado sem encriptação ativada. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Certifique-se de que apenas as extensões aprovadas são instaladas

**ID**: CIS Azure 7.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Só devem ser instaladas extensões VM aprovadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Esta política rege as extensões de máquinas virtuais que não são aprovadas. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Certifique-se de que os mais recentes patches de OS para todas as Máquinas Virtuais são aplicados

**ID**: CIS Azure 7.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As atualizações de sistema devem ser instaladas nos seus computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Certifique-se de que a proteção do ponto final para todas as Máquinas Virtuais está instalada

**ID**: CIS Azure 7.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitor que falta proteção de ponto final no Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Os servidores sem um agente instalado de Proteção de Pontos Finais serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Outras Considerações de Segurança

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Certifique-se de que a data de validade está definida em todas as teclas

**ID**: CIS Azure 8.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As chaves do cofre devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |As teclas criptográficas devem ter uma data de validade definida e não ser permanentes. As chaves que são válidas para sempre proporcionam a um potencial intruso mais tempo para comprometer a chave. É uma prática de segurança recomendada para definir datas de validade em teclas criptográficas. |Auditoria, Negar, Deficientes |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Certifique-se de que a data de validade está definida em todos os Segredos

**ID**: CIS Azure 8.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Segredos chave do cofre devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Os segredos devem ter uma data de validade definida e não ser permanentes. Segredos que são válidos para sempre fornecem a um potencial atacante mais tempo para comprometê-los. É uma prática de segurança recomendada para definir datas de validade em segredos. |Auditoria, Negar, Deficientes |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Certifique-se de que o cofre da chave é recuperável

**ID**: CIS Azure 8.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os cofres-chave devem ter proteção de purga ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A eliminação maliciosa de um cofre chave pode levar à perda permanente de dados. Um infiltrado malicioso na sua organização pode potencialmente apagar e purgar cofres de chaves. A proteção de purga protege-o de ataques de infiltrados, impondo um período de retenção obrigatório para cofres-chave apagados suaves. Ninguém dentro da sua organização ou microsoft será capaz de limpar os seus cofres chave durante o período de retenção de eliminação suave. |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Permitir o controlo de acesso baseado em funções (RBAC) nos serviços Azure Kubernetes

**ID**: CIS Azure 8.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular sobre as ações que os utilizadores podem executar, utilize Role-Based Access Control (RBAC) para gerir permissões em Clusters de Serviços Kubernetes e configurar políticas de autorização relevantes. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Certifique-se de que a autenticação do serviço de aplicações está definida no Serviço de Aplicações Azure

**ID**: CIS Azure 9.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A autenticação deve ser ativada na sua aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação API, ou autenticar aqueles que têm fichas antes de chegarem à app API |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A autenticação deve ser ativada na sua aplicação Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação Function, ou autenticar aqueles que têm fichas antes de chegarem à aplicação Função |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A autenticação deve ser ativada na sua aplicação web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação web, ou autenticar aqueles que têm fichas antes de chegarem à aplicação web |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Garantir que aplicativo web redireciona todo o tráfego HTTPS para HTTPS no Azure App Service

**ID**: CIS Azure 9.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Garanta que a web app está a usar a versão mais recente da encriptação TLS

**ID**: CIS Azure 9.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Certifique-se de que a aplicação web tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'

**ID**: CIS Azure 9.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que a aplicação da API tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[As aplicações de função devem ter "Certificados de Cliente (certificados de cliente incoming)" ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes com certificados válidos poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Certifique-se de que o Registo com o Diretório Ativo Azure está ativado no Serviço de Aplicações

**ID**: CIS Azure 9.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A identidade gerida deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[A identidade gerida deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A identidade gerida deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que a 'versão PHP' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Periodicamente, as versões mais recentes são lançadas para software PHP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomenda-se a utilização da versão PHP mais recente para aplicações API, de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Periodicamente, as versões mais recentes são lançadas para software PHP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomenda-se a utilização da versão PHP mais recente para aplicações web, de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'Python version' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações API é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Function](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações function é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Certifique-se de que 'Python version' é a mais recente, se usada como parte da aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações web é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'Versão Java' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Periodicamente, as versões mais recentes são lançadas para a Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações API é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Periodicamente, as versões mais recentes são lançadas para o software Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão java mais recente para aplicações function é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Periodicamente, as versões mais recentes são lançadas para o software Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão java mais recente para aplicações web é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.10 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Description |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Certifique-se de que 'versão HTTP' é a mais recente, se usada para executar a aplicação 'Função'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre a Política Azure:

- [Visão geral da conformidade](../concepts/regulatory-compliance.md) regulamentar.
- Consulte a [estrutura de definição de iniciativa.](../concepts/initiative-definition-structure.md)
- Reveja outros exemplos nas [amostras da Azure Policy](./index.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
