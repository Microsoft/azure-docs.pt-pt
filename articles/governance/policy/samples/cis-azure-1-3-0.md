---
title: Detalhes da conformidade regulamentar para cis Microsoft Azure Foundations Benchmark 1.3.0
description: Detalhes da cis Microsoft Azure Foundations Benchmark 1.3.0 Iniciativa De Conformidade Regulamentar integrada. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 03/05/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 1d4a2520131babdc9d94dce530dc8acfe737c3c2
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435727"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Detalhes da iniciativa cis Microsoft Azure Foundations Benchmark 1.3.0 Regulatory Compliance

O artigo que se segue detalha como a definição de iniciativa integrada na conformidade da política Azure mapeia **os domínios** e **controlos** de conformidade no CIS Microsoft Azure Foundations Benchmark 1.3.0.
Para obter mais informações sobre esta norma de conformidade, consulte [o CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/). Para compreender _a Propriedade_, consulte [a definição de política de Azure](../concepts/definition-structure.md#type) e [a responsabilidade partilhada na nuvem.](../../../security/fundamentals/shared-responsibility.md)

Os seguintes mapeamentos são para os **controlos CIS Microsoft Azure Foundations Benchmark 1.3.0.** Utilize a navegação no direito de saltar diretamente para um domínio de **conformidade** específico . Muitos dos controlos são implementados com uma definição de iniciativa [política Azure.](../overview.md) Para rever a definição completa de iniciativa, abra **a Política** no portal Azure e selecione a página **Definições.**
Em seguida, encontre e selecione a definição de iniciativa de referência de conformidade integrada **cis Microsoft Azure Foundations 1.3.0.**

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../overview.md)
> Estas políticas podem ajudá-lo a avaliar o [cumprimento](../how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a conformidade com** a política Azure refere-se apenas às próprias definições políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre domínios de conformidade, controlos e definições de Política Azure para esta norma de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Gestão de Acesso e Identidades

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Certifique-se de que a autenticação de vários fatores está ativada para todos os utilizadores privilegiados

**ID**: CIS Azure 1.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A MFA deve ser ativada em contas com permissões de escrita na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de escrita para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com permissões do proprietário para evitar uma violação de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Certifique-se de que a autenticação de vários fatores está ativada para todos os utilizadores não privilegiados

**ID**: CIS Azure 1.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MFA deve ser ativado em contas com permissões de leitura na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |A Autenticação Multi-Factor (MFA) deve ser ativada para todas as contas de subscrição com privilégios de leitura para evitar uma quebra de contas ou recursos. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Garantir que os utilizadores dos hóspedes são revistos mensalmente

**ID**: CIS Azure 1.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas externas com permissões do proprietário devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |As contas externas com permissões do proprietário devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[As contas externas com permissões de leitura devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |As contas externas com privilégios de leitura devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[As contas externas com permissões de escrita devem ser removidas da sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |As contas externas com privilégios de escrita devem ser removidas da sua subscrição de forma a evitar o acesso não monitorizado. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Certifique-se de que não são criadas funções personalizadas de proprietário de subscrição

**ID**: CIS Azure 1.21 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As funções de proprietário de subscrição personalizada não devem existir](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Esta política garante que não existem funções de proprietário de subscrição personalizadas. |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Centro de Segurança

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Certifique-se de que o Azure Defender está definido para os servidores

**ID**: CIS Azure 2.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Defender para servidores deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |O Azure Defender para servidores fornece proteção contra ameaças em tempo real para cargas de trabalho do servidor e gera recomendações de endurecimento, bem como alertas sobre atividades suspeitas. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Certifique-se de que o Azure Defender está definido para o Serviço de Aplicações

**ID**: CIS Azure 2.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |O Azure Defender for App Service aproveita a escala da nuvem e a visibilidade que o Azure tem como fornecedor de nuvem, para monitorizar os ataques comuns de aplicações web. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Certifique-se de que o Azure Defender está definido para os servidores de base de dados Azure SQL

**ID**: CIS Azure 2.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Defender para os servidores da Base de Dados Azure SQL deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |O Azure Defender for SQL fornece funcionalidades para aacessibilização e mitigação de potenciais vulnerabilidades de base de dados, detetando atividades anómalas que possam indicar ameaças às bases de dados SQL e descobrir e classificar dados sensíveis. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Certifique-se de que o Azure Defender está definido para os servidores SQL em máquinas

**ID**: CIS Azure 2.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Azure Defender para servidores SQL em máquinas deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |O Azure Defender for SQL fornece funcionalidades para aacessibilização e mitigação de potenciais vulnerabilidades de base de dados, detetando atividades anómalas que possam indicar ameaças às bases de dados SQL e descobrir e classificar dados sensíveis. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Certifique-se de que o Azure Defender está definido para o On para armazenamento

**ID**: CIS Azure 2.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender para armazenamento deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |O Azure Defender for Storage fornece deteções de tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Certifique-se de que o Azure Defender está pronto para o On for Kubernetes

**ID**: CIS Azure 2.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for Kubernetes deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |O Azure Defender for Kubernetes fornece proteção contra ameaças em tempo real para ambientes contentorizados e gera alertas para atividades suspeitas. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Certifique-se de que o Azure Defender está definido para registos de contentores

**ID**: CIS Azure 2.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender para registos de contentores deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |O Azure Defender para registos de contentores fornece uma verificação de vulnerabilidade de quaisquer imagens retiradas nos últimos 30 dias, empurradas para o seu registo, ou importadas, e expõe resultados detalhados por imagem. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Certifique-se de que o Azure Defender está pronto para o Cofre de Chaves

**ID**: CIS Azure 2.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender para o Cofre de Chaves deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |O Azure Defender for Key Vault fornece uma camada adicional de inteligência de proteção e segurança, detetando tentativas incomuns e potencialmente nocivas de aceder ou explorar contas-chave do cofre. |AuditIfNotExists, Desativado |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Certifique-se de que o "provisionamento automático do agente de monitorização" está definido para "On"

**ID**: CIS Azure 2.11 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O fornecimento automático do agente Log Analytics deve ser ativado na sua subscrição](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Para monitorizar vulnerabilidades e ameaças de segurança, o Azure Security Center recolhe dados das suas máquinas virtuais Azure. Os dados são recolhidos pelo agente Log Analytics, anteriormente conhecido como O Agente de Monitorização da Microsoft (MMA), que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho Log Analytics para análise. Recomendamos que o fornecimento automático de automóveis implemente automaticamente o agente em todos os VMs Azure suportados e quaisquer novos que sejam criados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Certifique-se de que os "endereços de e-mail adicionais" estão configurados com um e-mail de contacto de segurança

**ID**: CIS Azure 2.13 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As subscrições devem ter um endereço de e-mail de contacto para questões de segurança](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Para garantir que as pessoas relevantes da sua organização são notificadas quando há uma potencial falha de segurança numa das suas subscrições, desaça um contacto de segurança para receber notificações de e-mail do Security Center. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Certifique-se de que 'Notifique sobre alertas com a seguinte gravidade' está definido para 'Alto'

**ID**: CIS Azure 2.14 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A notificação por e-mail para alertas de alta gravidade deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Para garantir que as pessoas relevantes da sua organização são notificadas quando há uma potencial falha de segurança numa das suas subscrições, ativar notificações de e-mail para alertas de alta gravidade no Centro de Segurança. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Contas de Armazenamento

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Certifique-se de que a 'transferência segura necessária' está definida para 'Activado'

**ID**: CIS Azure 3.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A transferência segura para contas de armazenamento deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito de auditoria de transferência Segura na sua conta de armazenamento. A transferência segura é uma opção que obriga a sua conta de armazenamento a aceitar pedidos apenas a partir de ligações seguras (HTTPS). A utilização do HTTPS garante a autenticação entre o servidor e o serviço e protege os dados em trânsito de ataques de camadas de rede, tais como o homem no meio, a escuta e o sequestro de sessão |Auditoria, Negar, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Certifique-se de que o "nível de acesso público" está definido para Privado para recipientes blob

**ID**: CIS Azure 3.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público à conta de armazenamento deve ser proibido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O público anónimo lê o acesso a contentores e bolhas no Azure Storage é uma forma conveniente de partilhar dados, mas pode apresentar riscos de segurança. Para evitar violações de dados causadas por acesso anónimo não gasto, a Microsoft recomenda impedir o acesso do público a uma conta de armazenamento, a menos que o seu cenário o exija. |auditoria, negação, desativado |[2.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Garantir que a regra de acesso à rede padrão para contas de armazenamento está definida para negar

**ID**: CIS Azure 3.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem restringir o acesso à rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |O acesso à rede às contas de armazenamento deve ser restringido. Configure as regras de rede para que apenas aplicações de redes permitidas possam aceder à conta de armazenamento. Para permitir ligações de clientes específicos da Internet ou no local, o acesso pode ser concedido ao tráfego a partir de redes virtuais específicas do Azure ou de intervalos de endereços IP da Internet pública |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[As contas de armazenamento devem restringir o acesso à rede usando regras de rede virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Proteja as suas contas de armazenamento de ameaças potenciais usando regras de rede virtuais como método preferido em vez de filtragem baseada em IP. A desativação da filtragem baseada em IP impede que os IPs públicos acedam às suas contas de armazenamento. |Auditoria, Negar, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Certifique-se de que 'Serviços Microsoft Fidedignos' está ativado para acesso à conta de armazenamento

**ID**: CIS Azure 3.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Alguns serviços da Microsoft que interagem com contas de armazenamento operam a partir de redes que não podem ser autorizadas a ter acesso através das regras de rede. Para ajudar este tipo de serviço a funcionar conforme pretendido, permita que o conjunto de serviços confiáveis da Microsoft ignore as regras da rede. Estes serviços utilizarão então uma autenticação forte para aceder à conta de armazenamento. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Garantir que o armazenamento de dados críticos é encriptado com chave gerida pelo cliente

**ID**: CIS Azure 3.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As contas de armazenamento devem usar a chave gerida pelo cliente para encriptação](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Proteja a sua conta de armazenamento com maior flexibilidade utilizando as chaves geridas pelo cliente. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. A utilização de teclas geridas pelo cliente fornece capacidades adicionais para controlar a rotação da chave de encriptação ou apagar os dados criptograficamente. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Serviços de Base de Dados

### <a name="ensure-that-auditing-is-set-to-on"></a>Certifique-se de que a 'Auditoria' está definida para 'On'

**ID**: CIS Azure 4.1.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A auditoria no servidor SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |A auditoria no seu SQL Server deve ser ativada para rastrear as atividades de base de dados em todas as bases de dados do servidor e guardá-las num registo de auditoria. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Certifique-se de que a 'encriptação de dados' está definida para 'On' numa Base de Dados SQL

**ID**: CIS Azure 4.1.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação transparente de dados nas bases de dados SQL deve ser ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |A encriptação transparente de dados deve ser ativada para proteger os dados em repouso e cumprir os requisitos de conformidade |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Garantir que a retenção de "auditoria" seja "superior a 90 dias"

**ID**: CIS Azure 4.1.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os servidores SQL devem ser configurados com retenção de auditoria de 90 dias ou superior](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Os servidores SQL devem ser configurados com retenção de auditoria de 90 dias ou superior. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Certifique-se de que a Proteção Avançada de Ameaças (ATP) num servidor SQL está definida para 'Activado'

**ID**: CIS Azure 4.2.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A segurança avançada dos dados deve ser ativada em SqL Managed Instance](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Audite cada SQL Managed Instance sem segurança avançada de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[A segurança avançada dos dados deve ser ativada nos seus servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Auditar servidores SQL sem Segurança Avançada de Dados |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Certifique-se de que a Avaliação de Vulnerabilidade (VA) está ativada num servidor SQL, definindo uma Conta de Armazenamento

**ID**: CIS Azure 4.2.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A avaliação da vulnerabilidade deve ser ativada em casos geridos pela SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Auditar cada SQL Managed Instance que não tenha verificações recorrentes de avaliação de vulnerabilidade ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Auditar servidores SQL do Azure que não têm verificações de avaliação de vulnerabilidade recorrentes ativadas. A avaliação de vulnerabilidades pode descobrir, rastrear e ajudá-lo a corrigir potenciais vulnerabilidades de base de dados. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Certifique-se de que a definição de VA Enviar relatórios de digitalização está configurado para um servidor SQL

**ID**: CIS Azure 4.2.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As definições de Avaliação de Vulnerabilidade para servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Certifique-se de que é fornecido um endereço de e-mail para o campo 'Enviar relatórios de digitalização' nas definições de Avaliação de Vulnerabilidade. Este endereço de e-mail recebe o resumo do resultado da verificação depois de uma verificação periódica em servidores SQL. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Certifique-se de que a 'ligação SSL' está definida para 'ENABLED' para o Servidor de Base de Dados Pós-SQL

**ID**: CIS Azure 4.3.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |A Azure Database for MySQL suporta ligar a sua Base de Dados Azure para o servidor MySQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Certifique-se de que a 'ligação SSL' está definida para 'ENABLED' para o MySQL Database Server

**ID**: CIS Azure 4.3.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |A Azure Database for PostgreSQL suporta ligar a sua Base de Dados Azure para servidor PostgreSQL a aplicações de clientes utilizando a Camada de Tomadas Seguras (SSL). A aplicação de ligações SSL entre o servidor da base de dados e as aplicações do seu cliente ajuda a proteger contra ataques de "homem no meio", encriptando o fluxo de dados entre o servidor e a sua aplicação. Esta configuração impõe que o SSL esteja sempre ativado para aceder ao servidor de base de dados. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_checkpoints' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.3.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os pontos de verificação de registo devem ser ativados para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_checkpoints definição ativada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_connections' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.3.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As ligações de registo devem ser ativadas para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_connections definição ativada. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'log_disconnections' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.3.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As desconexões devem ser registadas para servidores de base de dados PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem log_disconnections ativadas. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Certifique-se de que o parâmetro do servidor 'connection_throttling' está definido para 'ON' para o Servidor de Base de Dados pós-SQL

**ID**: CIS Azure 4.3.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O estrangulamento de ligação deve ser ativado para servidores de base de dados PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Esta política ajuda a auditar quaisquer bases de dados PostgreSQL no seu ambiente sem o estrangulamento de Ligação ativado. Esta definição permite o estrangulamento temporário da ligação por IP para demasiadas falhas de início de senha inválidas. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Certifique-se de que o Azure Ative Directory Admin está configurado

**ID**: CIS Azure 4.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Fornecimento de auditoria de um administrador do Azure Ative Directory para o seu servidor SQL para permitir a autenticação Azure AD. A autenticação AZURE AD permite a gestão simplificada de permissões e a gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Certifique-se de que o protetor TDE do servidor SQL está encriptado com chave gerida pelo Cliente

**ID**: CIS Azure 4.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As instâncias geridas pela SQL devem usar chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona-lhe uma maior transparência e controlo sobre o Protetor TDE, maior segurança com um serviço externo apoiado pelo HSM e promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[Os servidores SQL devem usar as chaves geridas pelo cliente para encriptar dados em repouso](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |Implementar a Encriptação de Dados Transparentes (TDE) com a sua própria chave proporciona uma maior transparência e controlo sobre o Protetor TDE, uma maior segurança com um serviço externo apoiado pelo HSM e a promoção da separação de direitos. Esta recomendação aplica-se a organizações com um requisito de conformidade relacionado. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Início de sessão e Monitorização

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Certifique-se de que o recipiente de armazenamento que armazena os registos de atividade não é acessível ao público

**ID**: CIS Azure 5.1.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso público à conta de armazenamento deve ser proibido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |O público anónimo lê o acesso a contentores e bolhas no Azure Storage é uma forma conveniente de partilhar dados, mas pode apresentar riscos de segurança. Para evitar violações de dados causadas por acesso anónimo não gasto, a Microsoft recomenda impedir o acesso do público a uma conta de armazenamento, a menos que o seu cenário o exija. |auditoria, negação, desativado |[2.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Certifique-se de que a conta de armazenamento que contém o recipiente com registos de atividade é encriptada com BYOK (Use a sua própria chave)

**ID**: CIS Azure 5.1.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A conta de armazenamento que contenha o contentor com registos de atividade deve ser encriptada com a BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Esta política audita se a conta de Armazenamento que contém o contentor com registos de atividade for encriptada com a BYOK. A apólice só funciona se a conta de armazenamento estiver na mesma subscrição que os registos de atividade por design. Mais informações sobre a encriptação do Armazenamento Azure em repouso podem ser encontradas [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok) aqui.  |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Certifique-se de que a sessão de registo para Azure KeyVault está 'Activada'

**ID**: CIS Azure 5.1.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de recursos no Cofre de Chaves devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar pistas de atividade para uso para fins de investigação quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Certifique-se de que existe um alerta de registo de atividade para criar a atribuição de políticas

**ID**: CIS Azure 5.2.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações políticas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Esta política audita operações políticas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Certifique-se de que existe um alerta de registo de atividade para eliminar a atribuição de políticas

**ID**: CIS Azure 5.2.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações políticas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Esta política audita operações políticas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar grupo de segurança de rede

**ID**: CIS Azure 5.2.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Certifique-se de que existe um alerta de registo de atividade para eliminar o Grupo de Segurança da Rede

**ID**: CIS Azure 5.2.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a regra do grupo de segurança da rede

**ID**: CIS Azure 5.2.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Certifique-se de que existe um alerta de registo de atividade para a Regra do Grupo de Segurança da Rede de Eliminação

**ID**: CIS Azure 5.2.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a solução de segurança

**ID**: CIS Azure 5.2.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Esta política audita operações de segurança específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Certifique-se de que existe um alerta de registo de atividade para eliminar a solução de segurança

**ID**: CIS Azure 5.2.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações de segurança específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Esta política audita operações de segurança específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar ou eliminar a regra de firewall do servidor SQL

**ID**: CIS Azure 5.2.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Deve existir um alerta de registo de atividade para operações administrativas específicas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Esta política audita operações administrativas específicas sem alertas de registo de atividade configurados. |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Certifique-se de que os Registos de Diagnóstico estão ativados para todos os serviços que o suportam.

**ID**: CIS Azure 5.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os registos de diagnóstico nos Serviços de Aplicações devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Auditoria habilitando registos de diagnóstico na aplicação. Isto permite-lhe recriar pistas de atividade para fins de investigação se ocorrer um incidente de segurança ou se a sua rede estiver comprometida |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[Os registos de recursos na Azure Data Lake Store devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos no Azure Stream Analytics devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos nas contas do Lote devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos em Data Lake Analytics devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos no Event Hub devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos no IoT Hub devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos no Cofre de Chaves devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar pistas de atividade para uso para fins de investigação quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos nas Aplicações Lógicas devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos nos serviços de pesquisa devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos no Service Bus devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Auditoria habilitação de registos de recursos. Isto permite-lhe recriar percursos de atividade para uso para fins de investigação; quando ocorre um incidente de segurança ou quando a sua rede está comprometida |AuditIfNotExists, Desativado |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Os registos de recursos em conjuntos de escala de máquinas virtuais devem ser ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |Recomenda-se que os Registos possam ser recriados quando são necessárias investigações em caso de incidente ou de compromisso. |AuditIfNotExists, Desativado |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Rede

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Certifique-se de que o acesso rdp é restrito a partir da internet

**ID**: CIS Azure 6.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso do PDR da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Esta política audita qualquer regra de segurança de rede que permita o acesso rdp a partir da Internet |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Certifique-se de que o acesso ao SSH é restrito a partir da internet

**ID**: CIS Azure 6.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O acesso ao SSH a partir da Internet deve ser bloqueado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Esta política audita qualquer regra de segurança de rede que permita o acesso ao SSH a partir da Internet |Auditoria, Deficientes |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Certifique-se de que o Observador de Rede está 'Activado'

**ID**: CIS Azure 6.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Observador de Rede deve ser ativado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |O Network Watcher é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de cenário de rede dentro, e a partir de Azure. A monitorização do nível do cenário permite diagnosticar problemas no final da vista do nível da rede. As ferramentas de diagnóstico e visualização da rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e a obter informações sobre a sua rede em Azure. |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Máquinas Virtuais

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Certifique-se de que as máquinas virtuais estão a utilizar Discos Geridos

**ID**: CIS Azure 7.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VMs de auditoria que não usam discos geridos](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Esta política audita VMs que não usam discos geridos |auditoria |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Certifique-se de que os discos 'OS e Dados' são encriptados com CMK

**ID**: CIS Azure 7.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A encriptação do disco deve ser aplicada em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |As máquinas virtuais sem encriptação de disco ativada serão monitorizadas pelo Azure Security Center como recomendações. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Certifique-se de que os "discos não ligados" são encriptados com CMK

**ID**: CIS Azure 7.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os discos não ligados devem ser encriptados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Esta política audita qualquer disco não ligado sem encriptação ativada. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Certifique-se de que apenas as extensões aprovadas são instaladas

**ID**: CIS Azure 7.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Só devem ser instaladas extensões VM aprovadas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Esta política rege as extensões de máquinas virtuais que não são aprovadas. |Auditoria, Negar, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Certifique-se de que os mais recentes patches de OS para todas as Máquinas Virtuais são aplicados

**ID**: CIS Azure 7.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As atualizações de sistema devem ser instaladas nos seus computadores](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |As atualizações do sistema de segurança em falta nos seus servidores serão monitorizadas pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Certifique-se de que a proteção do ponto final para todas as Máquinas Virtuais está instalada

**ID**: CIS Azure 7.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitor que falta proteção de ponto final no Centro de Segurança Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Os servidores sem um agente instalado de Proteção de Pontos Finais serão monitorizados pelo Azure Security Center como recomendações |AuditIfNotExists, Desativado |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Outras Considerações de Segurança

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Certifique-se de que a data de validade está definida em todas as teclas

**ID**: CIS Azure 8.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[As chaves do cofre devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |As teclas criptográficas devem ter uma data de validade definida e não ser permanentes. As chaves que são válidas para sempre proporcionam a um potencial intruso mais tempo para comprometer a chave. É uma prática de segurança recomendada para definir datas de validade em teclas criptográficas. |Auditoria, Negar, Deficientes |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Certifique-se de que a data de validade está definida em todos os Segredos

**ID**: CIS Azure 8.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Segredos chave do cofre devem ter uma data de validade](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |Os segredos devem ter uma data de validade definida e não ser permanentes. Segredos que são válidos para sempre fornecem a um potencial atacante mais tempo para comprometê-los. É uma prática de segurança recomendada para definir datas de validade em segredos. |Auditoria, Negar, Deficientes |[1.0.1 pré-visualização](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Certifique-se de que o cofre da chave é recuperável

**ID**: CIS Azure 8.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os cofres-chave devem ter proteção de purga ativada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |A eliminação maliciosa de um cofre chave pode levar à perda permanente de dados. Um infiltrado malicioso na sua organização pode potencialmente apagar e purgar cofres de chaves. A proteção de purga protege-o de ataques de infiltrados, impondo um período de retenção obrigatório para cofres-chave apagados suaves. Ninguém dentro da sua organização ou microsoft será capaz de limpar os seus cofres chave durante o período de retenção de eliminação suave. |Auditoria, Negar, Deficientes |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Permitir o controlo de acesso baseado em funções (RBAC) nos serviços Azure Kubernetes

**ID**: CIS Azure 8.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Para fornecer filtragem granular sobre as ações que os utilizadores podem executar, utilize Role-Based Access Control (RBAC) para gerir permissões em Clusters de Serviços Kubernetes e configurar políticas de autorização relevantes. |Auditoria, Deficientes |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>Serviço de Aplicações

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Certifique-se de que a autenticação do serviço de aplicações está definida no Serviço de Aplicações Azure

**ID**: CIS Azure 9.1 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A autenticação deve ser ativada na sua aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação API, ou autenticar aqueles que têm fichas antes de chegarem à app API |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[A autenticação deve ser ativada na sua aplicação Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação Function, ou autenticar aqueles que têm fichas antes de chegarem à aplicação Função |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[A autenticação deve ser ativada na sua aplicação web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |A Azure App Service Authentication é uma funcionalidade que pode impedir que pedidos anónimos de HTTP cheguem à aplicação web, ou autenticar aqueles que têm fichas antes de chegarem à aplicação web |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Garantir que aplicativo web redireciona todo o tráfego HTTPS para HTTPS no Azure App Service

**ID**: CIS Azure 9.2 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A Aplicação Web só deve ser acessível em HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |A utilização do HTTPS garante a autenticação do servidor/serviço e protege os dados em trânsito contra ataques de escutas de camadas de rede. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Garanta que a web app está a usar a versão mais recente da encriptação TLS

**ID**: CIS Azure 9.3 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A versão TLS mais recente deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[A versão TLS mais recente deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[A versão mais recente do TLS deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Upgrade para a versão mais recente do TLS |AuditIfNotExists, Desativado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Certifique-se de que a aplicação web tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'

**ID**: CIS Azure 9.4 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Garantir que a aplicação da API tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes que tenham um certificado válido poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[As aplicações de função devem ter "Certificados de Cliente (certificados de cliente incoming)" ativados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |Os certificados de cliente permitem que a app solicite um certificado para pedidos de entrada. Apenas os clientes com certificados válidos poderão chegar à aplicação. |Auditoria, Deficientes |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Certifique-se de que o Registo com o Diretório Ativo Azure está ativado no Serviço de Aplicações

**ID**: CIS Azure 9.5 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[A identidade gerida deve ser usada na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[A identidade gerida deve ser usada na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[A identidade gerida deve ser usada na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Utilize uma identidade gerida para uma segurança de autenticação reforçada |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que a 'versão PHP' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.6 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Periodicamente, as versões mais recentes são lançadas para software PHP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomenda-se a utilização da versão PHP mais recente para aplicações API, de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Periodicamente, as versões mais recentes são lançadas para software PHP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Recomenda-se a utilização da versão PHP mais recente para aplicações web, de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'Python version' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.7 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que a 'versão Python' é a mais recente, se usada como parte da aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações API é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Function](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações function é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Certifique-se de que 'Python version' é a mais recente, se usada como parte da aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, as versões mais recentes são lançadas para o software Python, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações web é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'Versão Java' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.8 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Periodicamente, as versões mais recentes são lançadas para a Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão python mais recente para aplicações API é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Periodicamente, as versões mais recentes são lançadas para o software Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão java mais recente para aplicações function é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Periodicamente, as versões mais recentes são lançadas para o software Java, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. A utilização da versão java mais recente para aplicações web é recomendada de forma a tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação web

**ID**: CIS Azure 9.9 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Certifique-se de que 'versão HTTP' é a mais recente, se usada para executar a aplicação 'Função'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Periodicamente, as versões mais recentes são lançadas para HTTP, seja devido a falhas de segurança ou para incluir funcionalidades adicionais. Utilizando a versão HTTP mais recente para aplicações web para tirar partido das correções de segurança, caso existam, e/ou novas funcionalidades da versão mais recente. Atualmente, esta política aplica-se apenas às aplicações web do Linux. |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Garantir que as implementações ftp são desativadas

**ID**: CIS Azure 9.10 **Propriedade**: Cliente

|Name<br /><sub>(Portal Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[FTPS só deve ser exigido na sua App API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Permitir a aplicação do FTPS para uma maior segurança |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[FTPS só deve ser exigido na sua App de Função](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Permitir a aplicação do FTPS para uma maior segurança |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[FTPS deve ser necessário na sua Web App](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Permitir a aplicação do FTPS para uma maior segurança |AuditIfNotExists, Desativado |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre a Política Azure:

- [Visão geral da conformidade](../concepts/regulatory-compliance.md) regulamentar.
- Consulte a [estrutura de definição de iniciativa.](../concepts/initiative-definition-structure.md)
- Reveja outros exemplos nas [amostras da Azure Policy](./index.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
