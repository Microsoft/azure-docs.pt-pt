---
title: CIS Microsoft Azure Foundations Benchmark controlos de amostras de projeto
description: Mapeamento de recomendações da amostra de projeto de referência da CIS Microsoft Azure Foundations para a Política Azure.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: ea61ae4ea05b34c785485cbb5fd39c8a772565e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656971"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapeamento de recomendações da amostra de projeto de referência da CIS Microsoft Azure Foundations

O seguinte artigo detalha como o Azure Blueprints CIS Microsoft Azure Foundations Benchmark mapeia os mapas da amostra de referência do CIS Microsoft Azure Foundations Benchmark. Para obter mais informações sobre as recomendações, consulte o [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Os seguintes mapeamentos são para as recomendações **de Referência v1.1.0 da CIS Microsoft Azure Foundations.** Utilize a navegação à direita para saltar diretamente para um mapeamento de recomendação específico.
Muitas das recomendações mapeadas são implementadas com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione as ** \[recomendações de\] Referência de Referência cIS Microsoft Azure Foundations Benchmark v1.1.0 e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Certifique-se de que a autenticação multifactor está ativada para todos os utilizadores privilegiados

Este projeto atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a monitorizar quando a autenticação de vários fatores não está ativada em contas privilegiadas do Diretório Ativo Azure.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de escrita na sua subscrição

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Certifique-se de que a autenticação multifactor está ativada para todos os utilizadores não privilegiados

Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar quando a autenticação de vários fatores não está ativada em contas de Diretório Ativo Azure não privilegiadas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Certifique-se de que não há utilizadores convidados

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a monitorizar as contas dos hóspedes que podem necessitar de ser removidas.

- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Certifique-se de que não são criadas funções personalizadas do proprietário da subscrição

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a monitorizar para funções personalizadas do proprietário de subscrição que possam necessitar de ser removidas.

- Funções personalizadas do proprietário da subscrição não devem existir

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Certifique-se de que o nível de preços padrão é selecionado

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar redes e máquinas virtuais onde o nível padrão do Centro de Segurança não está ativado.

 - Nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Assegurar que o "fornecimento automático do agente de monitorização" está definido para 'On'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que o fornecimento automático do agente Log Analytics está ativado.

- O fornecimento automático do agente de monitorização Log Analytics deve ser ativado na sua subscrição

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Garantir que a definição da definição da política padrão ASC "Monitor System Updates" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as atualizações do sistema são instaladas em máquinas virtuais.

- As atualizações de sistema devem ser instaladas nos seus computadores

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Garantir que a definição da política padrão ASC "Monitor os as vulnerabilidades" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar vulnerabilidades de máquinas virtuais não remediadas.

- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Garantir que a definição da definição da definição de padrão ASC "Monitor Endpoint Protection" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a proteção do ponto final está ativada em máquinas virtuais.

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Garantir que a definição da definição de definição de padrão ASC "Monitor Disk Crypton" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os discos de máquinas virtuais são encriptados.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Garantir que a definição da definição da política padrão ASC "Monitor Network Security Groups" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a proteger as máquinas virtuais viradas para a Internet.

- As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Garantir que a definição da definição da política padrão ASC "Firewall de aplicação web do monitor" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a proteger as máquinas virtuais que executam aplicações web.

- As regras do NSGs para aplicações web em IaaS devem ser endurecidas

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Garantir que a definição da política padrão ASC "Enable Next Generation Firewall (NGFW) Monitoring" não é "Desativada"

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que ajudam a proteger as subredes e as máquinas virtuais de ameaças, restringindo o acesso. A política do Security Center referenciada por esta recomendação de referência do CIS Microsoft Azure Foundations foi substituída por duas novas recomendações. As políticas abaixo referidas abordam as novas recomendações.

- As subredes devem ser associadas a um Grupo de Segurança da Rede
- As máquinas virtuais devem ser associadas a um Grupo de Segurança da Rede

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Garantir que a definição da política padrão ASC "Monitor Vulnerability Assessment" não é "Deficiente"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as vulnerabilidades são detetadas e remediadas.

- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Garantir que a definição da política padrão ASC "Monitor Storage Blob Crypton" não é "Desativada"

A encriptação do Armazenamento do Microsoft Azure está ativada para todas as contas de armazenamento novas e já existentes e não pode ser desativada. (Esta é uma capacidade azure padrão; não há atribuição de políticas.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Garantir que a definição da política padrão ASC "Monitor JIT Network Access" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a controlar o acesso a máquinas virtuais.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Garantir que a definição da política padrão ASC "Monitor Adaptive Application Whitelisting" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os controlos de aplicação adaptáveis estão ativados em máquinas virtuais.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Garantir que a definição da política padrão ASC "Monitor SQL Auditing" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que ajuda a garantir que a auditoria do servidor SQL está ativada.

- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Garantir que a definição de definição de definição de padrão ASC "Monitor SQL Encryption" não é "Desativada"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação de dados transparente está ativada nas bases de dados SQL.

- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Certifique-se de que estão definidos 'Emails de contacto de segurança'

Este plano atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- Um endereço de e-mail de contacto de segurança deve ser fornecido para a sua subscrição

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Certifique-se de que o contacto de segurança 'Número de telefone' está definido

Este plano atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- Um número de telefone de contato de segurança deve ser fornecido para a sua subscrição

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Certifique-se de que 'Enviar notificação por e-mail para alertas de alta gravidade' está definido para 'On'

Este plano atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- A notificação por e-mail para alertas de alta gravidade deve ser ativada

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Certifique-se de que 'Enviar e-mail também para os proprietários de subscrições' está definido para 'On'

Este plano atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- Notificação por e-mail ao proprietário de subscrição para alertas de alta gravidade deve ser ativada

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Certifique-se de que a "transferência segura necessária" está definida para 'Activada'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que permitem ligações inseguras.

- A transferência segura para contas de armazenamento deve ser ativada

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Garantir que a regra de acesso à rede padrão para contas de armazenamento está definida para negar

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que permitem um acesso sem restrições.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Garantir que os 'Serviços Microsoft Fidedignos' estão habilitados para acesso à conta de armazenamento

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que não permitem o acesso a partir de serviços confiáveis da Microsoft.

- As contas de armazenamento devem permitir o acesso a partir de serviços fidedignos da Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Certifique-se de que a 'Auditoria' está definida para 'On'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que ajuda a garantir que a auditoria do servidor SQL está ativada. 

- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Certifique-se de que a política de "AuditActionGroups" na política de "auditoria" para um servidor SQL é corretamente definida

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a auditoria do servidor SQL está devidamente configurada.

- As definições de auditoria sQL devem ter grupos de ação configurados para capturar atividades críticas

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Garantir que a retenção de 'Auditoria' seja "superior a 90 dias"

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de servidores SQL sejam retidos durante pelo menos 90 dias.

- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Certifique-se de que a 'Segurança avançada de dados' num servidor SQL está definida para 'On'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a segurança avançada de dados está ativada em servidores SQL e instâncias geridas pela SQL.

- A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL
- A segurança avançada de dados deve ser ativada nos seus servidores SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Certifique-se de que os "tipos de deteção de ameaças" estão definidos para 'All'

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que a proteção avançada de ameaças está devidamente configurada em servidores SQL e instâncias geridas pela SQL.

- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' em definições avançadas de segurança de dados geridas pela SQL

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Certifique-se de que 'Enviar alertas para' está definido

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que notificações avançadas de segurança de dados estão devidamente ativadas.

- As definições avançadas de segurança de dados para a instância gerida pela SQL devem conter um endereço de e-mail para receber alertas de segurança
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Certifique-se de que o 'serviço de e-mail e os co-administradores' estão 'habilitados'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que notificações avançadas de segurança de dados estão devidamente ativadas.

- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados geridas pela SQL
- As notificações de e-mail para administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados do servidor SQL

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Certifique-se de que o Administrador de Diretório Ativo Azure está configurado

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que um administrador de Diretório Ativo Azure é aprovisionado para servidores SQL.

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Certifique-se de que 'Encriptação de dados' está definida para 'On' numa base de dados SQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação de dados transparente está ativada nas bases de dados SQL.

- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Certifique-se de que o protetor TDE do servidor SQL está encriptado com BYOK (Utilize a sua própria chave)

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que o protetor encriptado de dados transparente para servidores SQL e instâncias geridas pela SQL é encriptado com a sua própria chave.

- SQL gerido exemplo TDE protetor deve ser encriptado com a sua própria chave
- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Certifique-se de que a 'ligação SSL' está definida como 'ACTIVADA' para o Servidor de Base de Dados MySQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados MySQL aplicam as ligações TLS/SSL.

- Impor a ligação SSL deve ser ativada para servidores de base de dados MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Certifique-se de que o parâmetro do servidor 'log_checkpoints' está definido para 'ON' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de dados PostgreSQL saiam postos de verificação.

- Os postos de verificação de registo devem ser ativados para servidores de base de dados PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Certifique-se de que a 'ligação SSL' está definida como 'ACTIVADA' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL apliquem ligações TLS/SSL.

- Impor a ligação SSL deve ser ativada para servidores de base de dados PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Certifique-se de que o parâmetro do servidor 'log_connections' está definido para 'ON' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL slogem ligações.

- As ligações de log devem ser ativadas para servidores de base de dados PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Certifique-se de que o parâmetro do servidor 'log_disconnections' está definido para 'ON' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL slogem desconexões.

- As desconexões devem ser registadas nos servidores de base de dados PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Certifique-se de que o parâmetro do servidor 'log_duration' está definido para 'ON' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL registam a duração das declarações concluídas.

- A duração do registo deve ser ativada para servidores de base de dados PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Certifique-se de que o parâmetro do servidor 'connection_throttling' está definido para 'ON' para o Servidor de Base de Dados PostgreSQL

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a mitigar ataques de força bruta em servidores de base de dados PostgreSQL.

- O estrangulamento da ligação deve ser ativado para servidores de base de dados PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Certifique-se de que o Administrador de Diretório Ativo Azure está configurado

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que um administrador de Diretório Ativo Azure é aprovisionado para servidores SQL. O CIS Microsoft Azure Foundations Benchmark inclui esta recomendação; no entanto, trata-se de uma duplicação da [recomendação 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Certifique-se de que existe um perfil de registo

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que existe um perfil de registo para todas as subscrições do Azure. 

- As subscrições do Azure devem ter um perfil de registo para o Registo de Atividades

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Certifique-se de que a retenção de registo de atividade está definida 365 dias ou mais

Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de atividade são mantidos durante pelo menos um ano.

- O registo de atividade deve ser conservado por pelo menos um ano

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Garantir que o perfil de auditoria capture todas as atividades

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que o perfil de registo está corretamente configurado.

- O perfil de registo do Monitor Azure deve recolher registos para categorias 'escrever', 'excluir' e 'agir'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Certifique-se de que o perfil de registo captura registos de atividade para todas as regiões, incluindo globais

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que o perfil de registo está corretamente configurado.

- O Monitor Azure deve recolher registos de atividade sacar registos de atividades de todas as regiões

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Certifique-se de que a exploração madeireira para o Azure KeyVault está 'activada'

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de diagnóstico estão ativados para cofres chave.

- Os registos de diagnóstico no Cofre-Chave devem ser ativados

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Certifique-se de que o Observador da Rede está 'Activado'

Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que o Observador de Rede está habilitado para todas as regiões onde os recursos são implantados. Esta política requer um conjunto de parâmetros que especifique todas as regiões aplicáveis. O valor predefinido nesta definição de iniciativa política é "leste".

- O Observador da Rede deve ser ativado

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Certifique-se de que o 'disco OS' está encriptado

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação do disco está ativada em máquinas virtuais.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Certifique-se de que os 'discos de dados' estão encriptados

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação do disco está ativada em máquinas virtuais.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Certifique-se de que os discos não ligados estão encriptados

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os discos não ligados são encriptados.

- Os discos não ligados devem ser encriptados

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Certifique-se de que só são instaladas extensões aprovadas

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que apenas as extensões de máquinas virtuais aprovadas são instaladas. Esta política requer uma matriz de parâmetros que especifica todas as extensões de máquinas virtuais aprovadas. Esta definição de iniciativa política contém incumprimentos sugeridos que os clientes devem validar. 

 - Só devem ser instaladas extensões VM aprovadas

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Certifique-se de que são aplicados os mais recentes Patches de OS para todas as Máquinas Virtuais

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as atualizações do sistema são instaladas em máquinas virtuais.

- As atualizações de sistema devem ser instaladas nos seus computadores

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Certifique-se de que a proteção do ponto final para todas as máquinas virtuais está instalada

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que a proteção do ponto final está ativada em máquinas virtuais.

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Certifique-se de que o cofre chave está recuperável

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que os objetos chave do cofre são recuperáveis em caso de eliminação acidental.

- Objetos do cofre da chave devem ser recuperáveis

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Permitir o controlo de acesso baseado em funções (RBAC) nos serviços da Azure Kubernetes

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que o controlo de acesso baseado em papéis é usado para permissões geridas em clusters de serviços Kubernetes

- \[Pré-visualização\]: O Controlo de Acesso baseado em funções (RBAC) deve ser utilizado nos serviços kubernetes

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Garantir que a aplicação web redireciona todo o tráfego HTTP para HTTPS no Serviço de Aplicações Azure

Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a garantir que as aplicações web só são acessíveis através de ligações seguras.

- Aplicação Web só deve ser acessível em HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Certifique-se de que a aplicação web está a usar a versão mais recente da encriptação TLS

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão Mais recente do TLS.

- A versão TLS mais recente deve ser utilizada na sua app API
- A versão TLS mais recente deve ser usada na sua App de Funções
- A versão TLS mais recente deve ser utilizada na sua Web App

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Certifique-se de que a aplicação web tem 'Certificados de cliente (certificados de cliente de entrada)' definidos para 'On'

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que apenas os clientes com certificados válidos são capazes de chegar a uma aplicação web.

- Garantir que a aplicação DaPI tem 'Certificados de cliente (certificados de cliente de entrada)' definidos para 'On'
- Garantir a aplicação 'Certificar-se' tem 'Certificados de Cliente (Certificados de Cliente De entrada)' definidos para 'On'
- Certifique-se de que a aplicação WEB tem 'Certificados de cliente (certificados de cliente de entrada)' definidos para 'On'

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Certifique-se de que o Registo com Diretório Ativo Azure está ativado no Serviço de Aplicações

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão usando uma identidade gerida.

- Certifique-se de que o Register with Azure Ative Directory está ativado na aplicação API
- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na App de Funções
- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na Web App

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Certifique-se de que a versão '.Net Framework' é a mais recente, se utilizada como parte da aplicação web

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do .Net Framework.

- Certifique-se de que a versão '.Net Framework' é a mais recente, se utilizada como parte da aplicação DaPI
- Certifique-se de que a versão '.Net Framework' é a mais recente, se utilizada como parte da App de Funções
- Certifique-se de que a versão '.Net Framework' é a mais recente, se utilizada como parte da aplicação Web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Certifique-se de que 'versão PHP' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do PHP.

- Certifique-se de que 'versão PHP' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'versão PHP' é a mais recente, se utilizada como parte da aplicação Fun
- Certifique-se de que 'versão PHP' é a mais recente, se usada como parte da aplicação WEB

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Certifique-se de que 'Versão Python' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente da Python.

- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Fun
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Certifique-se de que 'versão Java' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente de Java.

- Certifique-se de que 'versão Java' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Funtion
- Certifique-se de que 'versão Java' é a mais recente, se usada como parte da aplicação Web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do HTTP.

- Certifique-se de que 'VERSÃO HTTP' é o mais recente, se usado para executar a app Api
- Certifique-se de que 'VERSÃO HTTP' é o mais recente, se usado para executar a aplicação Função
- Certifique-se de que 'VERSÃO HTTP' é o mais recente, se usado para executar a aplicação Web


## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto de referência da CIS Microsoft Azure Foundations Benchmark, visite os seguintes artigos para conhecer o projeto ou visite a Política Azure no portal Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark blueprint - Visão geral](./index.md)
> [CIS Microsoft Azure Foundations Benchmark blueprint - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).