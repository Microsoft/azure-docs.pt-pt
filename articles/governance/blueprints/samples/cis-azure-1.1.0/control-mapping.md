---
title: CIS Microsoft Azure Foundations Benchmark controlos de amostras de projeto
description: Mapeamento de recomendação da amostra de projeto benchmark da CIS Microsoft Azure Foundations para a Azure Policy.
ms.date: 05/12/2020
ms.topic: sample
ms.openlocfilehash: b6029e147af49cfb91078c6228615c32ad2db5fe
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167235"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Amostra de projeto de projeto de benchmark da CIS Microsoft Azure Foundations

O artigo seguinte detalha como o Azure Blueprints CIS Microsoft Azure Foundations Benchmark mapes de amostra de amostra para as recomendações de benchmark da CIS Microsoft Azure Foundations Benchmark. Para obter mais informações sobre as recomendações, consulte [o CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Os seguintes mapeamentos são para as recomendações **cis Microsoft Azure Foundations Benchmark v1.1.0.** Utilize a navegação no direito de saltar diretamente para um mapeamento de recomendação específico.
Muitas das recomendações mapeadas são implementadas com uma iniciativa [política do Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione as ** \[ \] recomendações de benchmark das Fundações CIS Microsoft Azure Foundations v1.1.0 e implemente extensões VM específicas para apoiar os requisitos** de auditoria integrados na iniciativa política.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Certifique-se de que a autenticação multi-factor está ativada para todos os utilizadores privilegiados

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a monitorizar quando a autenticação de vários fatores não é ativada em contas privilegiadas do Azure Ative Directory.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Assegurar que a autenticação multi-factor está ativada para todos os utilizadores não privilegiados

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar quando a autenticação de vários fatores não é ativada em contas de Diretório Ativo Azure não privilegiadas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Certifique-se de que não existem utilizadores convidados

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a monitorizar as contas dos hóspedes que podem necessitar de ser removidas.

- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Certifique-se de que não são criadas funções de proprietário de subscrição personalizadas

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a monitorizar as funções personalizadas do proprietário da subscrição que podem necessitar de ser removidas.

- As funções de proprietário de subscrição personalizada não devem existir

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Certifique-se de que o nível de preços padrão é selecionado

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar redes e máquinas virtuais onde o nível padrão do Centro de Segurança não está ativado.

- O nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Assegurar que o "provisionamento automático do agente de controlo" seja definido como "Ligado"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o fornecimento automático do agente Log Analytics está ativado.

- O provisionamento automático do agente de monitorização Log Analytics deve ser ativado na sua subscrição

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Garantir que a definição de política por defeito da ASC "Monitor System Updates" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as atualizações do sistema são instaladas em máquinas virtuais.

- As atualizações de sistema devem ser instaladas nos seus computadores

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Garantir que a definição de política por defeito da ASC "Monitorizar as vulnerabilidades dos OS" não seja "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar vulnerabilidades de máquinas virtuais não mediadas.

- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Garantir que a definição de política por defeito da ASC "Monitor Endpoint Protection" não seja "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a proteção do ponto final é ativada em máquinas virtuais.

- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Garantir que a definição de política padrão asc "Monitor Disk Encryption" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os discos de máquina virtuais são encriptados.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Garantir que a definição de política por defeito da ASC "Monitor Network Security Groups" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a proteger máquinas virtuais viradas para a Internet.

- Recomendações de endurecimento de rede adaptativa devem ser aplicadas na internet face a máquinas virtuais

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Garantir que a definição de política por defeito da ASC "Enable Next Generation Firewall (NGFW) Monitoring" não é "Desativada"

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que ajudam a proteger sub-redes e máquinas virtuais de ameaças, restringindo o acesso. A política do Centro de Segurança referenciada por esta recomendação de benchmark da CIS Microsoft Azure Foundations foi substituída por duas novas recomendações. As políticas a seguir referidas abordam as novas recomendações.

- As subnetas devem ser associadas a um Grupo de Segurança de Rede
- Máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Garantir que a definição de política por defeito da ASC "Monitorização da Vulnerabilidade" não seja "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as vulnerabilidades são detetadas e remediadas.

- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Garantir que a definição de política padrão asc "Monitor Storage Blob Encryption" não é "Desativada"

A encriptação do Armazenamento do Microsoft Azure está ativada para todas as contas de armazenamento novas e já existentes e não pode ser desativada. (Esta é uma capacidade Azure padrão; não há nenhuma atribuição de política.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Garantir que a definição de política por defeito da ASC "Monitor JIT Network Access" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a controlar o acesso a máquinas virtuais.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Garantir que a definição de política por defeito da ASC "Monitor Adaptive Application Whitelisting" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os Controlos de Aplicações Adaptativos são ativados em máquinas virtuais.

- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Garantir que a definição de política por defeito da ASC "Monitor SQL Auditing" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que ajuda a garantir que a auditoria do servidor SQL está ativada.

- A auditoria no servidor SQL deve ser ativada

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Garantir que a definição de política por defeito da ASC "Monitor SQL Encryption" não é "Desativada"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a Encriptação de Dados Transparente está ativada nas bases de dados SQL.

- A encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Certifique-se de que os "e-mails de contacto de segurança" estão definidos

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- Um endereço de e-mail de contacto de segurança deve ser fornecido para a sua subscrição

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Certifique-se de que o contacto de segurança 'Número de telefone' está definido

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- Um número de telefone de contacto de segurança deve ser fornecido para a sua subscrição

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Certifique-se de que 'Enviar e-mail de notificação para alertas de alta gravidade' está definido para 'On'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- A notificação por e-mail para alertas de alta gravidade deve ser ativada

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Certifique-se de que 'Enviar e-mail também para os proprietários de assinaturas' está definido para 'On'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações de segurança estão devidamente ativadas

- A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Certifique-se de que a 'transferência segura necessária' está definida para 'Activado'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que permitem ligações inseguras.

- A transferência segura para contas de armazenamento deve ser ativada

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Garantir que a regra de acesso à rede padrão para contas de armazenamento está definida para negar

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que permitem o acesso sem restrições.

- Auditoria acesso ilimitado à rede a contas de armazenamento

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Garantir que os "Serviços De Confiança da Microsoft" estão ativados para o acesso à conta de armazenamento

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar contas de armazenamento que não permitem o acesso a serviços confiáveis da Microsoft.

- As contas de armazenamento devem permitir o acesso a partir de serviços confiáveis da Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Assegurar que a "auditoria" está definida para 'On'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que ajuda a garantir que a auditoria do servidor SQL está ativada. 

- A auditoria no servidor SQL deve ser ativada

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Certifique-se de que os "AuditActionGroups" na política de "auditoria" para um servidor SQL são corretamente definidos

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a auditoria do servidor SQL está devidamente configurada.

- As definições de auditoria sql devem ter grupos de ação configurados para capturar atividades críticas

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Assegurar que a retenção de "auditoria" seja "superior a 90 dias"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de servidores SQL são mantidos durante pelo menos 90 dias.

- Os servidores SQL devem ser configurados com dias de retenção de auditoria superiores a 90 dias.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Certifique-se de que a 'Segurança Avançada de Dados' num servidor SQL está definida para 'On'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a segurança avançada de dados é ativada nos servidores SQL Database e SQL Managed Instance.

- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas pelo SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Certifique-se de que os "tipos de deteção de ameaças" estão definidos para 'All'

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que a proteção avançada de ameaças está devidamente configurada nos servidores SQL Database e SQL Managed Instance.

- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Os tipos avançados de proteção de ameaças devem ser definidos para 'All' em definições de segurança de dados avançadas de casos

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Certifique-se de que 'Enviar alertas para' está definido

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações avançadas de segurança de dados estão devidamente ativadas.

- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- As definições avançadas de segurança de dados para a ocorrência gerida pelo SQL devem conter um endereço de e-mail para receber alertas de segurança

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Assegurar que o "serviço de e-mail e coadministradores" é 'Habilitado'

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as notificações avançadas de segurança de dados estão devidamente ativadas.

- As notificações por e-mail para administradores e proprietários de subscrições devem ser ativadas nas definições avançadas de segurança de dados do servidor SQL
- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados de casos DE SQL

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Certifique-se de que o Azure Ative Directory Admin está configurado

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que um administrador do Azure Ative Directory é a provisionado para servidores SQL.

- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Certifique-se de que a 'encriptação de dados' está definida para 'On' numa Base de Dados SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a Encriptação de Dados Transparente está ativada nas bases de dados SQL.

- A encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Certifique-se de que o protetor TDE do servidor SQL está encriptado com BYOK (Use a sua própria chave)

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que o protetor encriptado de dados transparente para os servidores SQL Database e SQL Managed Instance é encriptado com a sua própria chave.

- O protetor TDE do servidor SQL deve ser encriptado com a sua própria chave
- O protetor TDE de caso gerido pela SQL deve ser encriptado com a sua própria chave

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Certifique-se de que a 'ligação SSL' está definida para 'ENABLED' para o MySQL Database Server

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados MySQL aplicam ligações TLS/SSL.

- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Certifique-se de que o parâmetro do servidor 'log_checkpoints' está definido para 'ON' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL registam pontos de verificação.

- Os pontos de verificação de registo devem ser ativados para servidores de base de dados PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Certifique-se de que a "ligação SSL de execução" está definida como 'ENABLED' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL aplicam ligações TLS/SSL.

- A ligação SSL da Aplicação deve ser ativada para servidores de base de dados PostgreSQL

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Certifique-se de que o parâmetro do servidor 'log_connections' está definido para 'ON' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir ligações de registo de servidores de base postgresQL.

- As ligações de registo devem ser ativadas para servidores de base de dados PostgreSQL

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Certifique-se de que o parâmetro do servidor 'log_disconnections' está definido para 'ON' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL registam desconexões.

- As desconexões devem ser registadas para servidores de base de dados PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Certifique-se de que o parâmetro do servidor 'log_duration' está definido para 'ON' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os servidores de base de dados PostgreSQL registam a duração das declarações completas.

- A duração do registo deve ser ativada para servidores de base de dados PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Certifique-se de que o parâmetro do servidor 'connection_throttling' está definido para 'ON' para o Servidor de Base de Dados Pós-SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a mitigar ataques de força bruta em servidores de base de dados PostgreSQL.

- O estrangulamento de ligação deve ser ativado para servidores de base de dados PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Assegurar que o Azure Ative Directory Admin está configurado

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que um administrador do Azure Ative Directory é a provisionado para servidores SQL. O CIS Microsoft Azure Foundations Benchmark inclui esta recomendação; no entanto, trata-se de uma duplicação da [recomendação 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Certifique-se de que existe um perfil de registo

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir a existência de um perfil de registo para todas as subscrições do Azure. 

- As subscrições do Azure devem ter um perfil de registo de registo de atividades

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Certifique-se de que a retenção de registos de atividade é definida em 365 dias ou mais

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de atividade são mantidos por pelo menos um ano.

- O registo de atividade deve ser mantido por pelo menos um ano

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Garantir que o perfil de auditoria capta todas as atividades

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o perfil de registo está devidamente configurado.

- O perfil de registo do Azure Monitor deve recolher registos para categorias de 'escrever', 'excluir' e 'acção'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Certifique-se de que o perfil de registo capta registos de atividade para todas as regiões, incluindo a global

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o perfil de registo está devidamente configurado.

- O Azure Monitor deve recolher registos de atividade de todas as regiões

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 Certifique-se de que a conta de armazenamento que contém o recipiente com registos de atividade é encriptada com BYOK (Use a sua própria chave)

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as contas de armazenamento que contêm registos de atividade são encriptadas com BYOK.

- A conta de armazenamento que contenha o contentor com registos de atividade deve ser encriptada com a BYOK

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Certifique-se de que o registo de Azure KeyVault está "Ativado"

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os registos de diagnóstico estão ativados para cofres-chave.

- Os registos de diagnóstico no Cofre de Chaves devem ser ativados

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 Certifique-se de que existe um alerta de registo de atividade para criar a atribuição de políticas

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações políticas específicas

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar grupo de segurança da rede

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 Certifique-se de que existe um alerta de registo de atividade para eliminar o Grupo de Segurança da Rede

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a regra do grupo de segurança da rede

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 Certifique-se de que existe um alerta de registo de atividade para a Regra do Grupo de Segurança da Rede de Eliminação

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar a solução de segurança

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações de segurança específicas

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 Certifique-se de que existe um alerta de registo de atividade para eliminar a solução de segurança

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações de segurança específicas

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 Certifique-se de que existe um alerta de registo de atividade para criar ou atualizar ou eliminar a regra de firewall do servidor SQL

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações administrativas específicas

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 Assegurar que existe alerta de registo de atividade para a política de segurança de atualização

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que existem alertas específicos de registo de atividade.

- Deve existir um alerta de registo de atividade para operações de segurança específicas

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6.1 Garantir que o acesso rdp é restrito a partir da internet

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o acesso rdp é restrito.

- O acesso do PDR da Internet deve ser bloqueado

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6.2 Garantir que o acesso ao SSH é restrito a partir da internet

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o acesso ao SSH é restrito.

- O acesso ao SSH a partir da Internet deve ser bloqueado

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Certifique-se de que o Observador de Rede está 'Activado'

Este projeto atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o Network Watcher está ativado para todas as regiões onde os recursos são utilizados. Esta política requer um conjunto de parâmetros que especifique todas as regiões aplicáveis. O valor padrão nesta definição de iniciativa política é "leste".

- O Observador de Rede deve ser ativado

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Certifique-se de que o 'disco de OS' está encriptado

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação do disco é ativada em máquinas virtuais.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Certifique-se de que os "discos de dados" estão encriptados

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a encriptação do disco é ativada em máquinas virtuais.

- A encriptação do disco deve ser aplicada em máquinas virtuais

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Certifique-se de que os "discos não ligados" estão encriptados

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os discos não ligados são encriptados.

- Os discos não ligados devem ser encriptados

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Certifique-se de que só são instaladas extensões aprovadas

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que apenas as extensões de máquinas virtuais aprovadas são instaladas. Esta política requer um conjunto de parâmetros que especifica todas as extensões de máquinas virtuais aprovadas. Esta definição de iniciativa política contém incumprimentos sugeridos que os clientes devem validar. 

- Só devem ser instaladas extensões VM aprovadas

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Certifique-se de que são aplicados os mais recentes patches de OS para todas as Máquinas Virtuais

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as atualizações do sistema são instaladas em máquinas virtuais.

- As atualizações de sistema devem ser instaladas nos seus computadores

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Certifique-se de que a proteção do ponto final para todas as máquinas virtuais está instalada

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que a proteção do ponto final é ativada em máquinas virtuais.

- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Certifique-se de que o cofre da chave é recuperável

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os objetos do cofre chave são recuperáveis em caso de eliminação acidental.

- Os objetos do Cofre chave devem ser recuperáveis

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Permitir o controlo de acesso baseado em funções (RBAC) nos serviços Azure Kubernetes

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que o controlo de acesso baseado em funções é usado para obter permissões em clusters de serviços Kubernetes

- O Controlo de Acesso Baseado em Função (RBAC) deve ser utilizado nos serviços kubernetes

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9.1 Garantir que a autenticação do serviço de aplicações está definida no Serviço de Aplicações Azure

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que os pedidos para aplicações do Serviço de Aplicações são autenticados.

- A autenticação deve ser ativada na sua aplicação API
- A autenticação deve ser ativada na sua aplicação Função
- A autenticação deve ser ativada na sua aplicação web

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Garantir que a web app redireciona todo o tráfego HTTPS para HTTPS no Azure App Service

Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a garantir que as aplicações web são acessíveis apenas sobre ligações seguras.

- A Aplicação Web só deve ser acessível em HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Garanta que a web app está a usar a versão mais recente da encriptação TLS

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do TLS.

- A versão TLS mais recente deve ser usada na sua App API
- A versão TLS mais recente deve ser usada na sua App de Função
- A versão mais recente do TLS deve ser usada na sua Web App

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Certifique-se de que a aplicação web tem 'Certificados de Cliente (certificados de cliente incoming)' definidos para 'On'

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que apenas os clientes com certificados válidos são capazes de chegar a uma aplicação web.

- Garantir que a aplicação da API tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'
- Garantir que a aplicação 'Certificados de Cliente ' (certificados de cliente incoming)' definidos para 'On'
- Garantir que a aplicação WEB tem "Certificados de Cliente (Certificados de cliente incoming)" definidos para 'On'

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Certifique-se de que o Registo com o Azure Ative Directory está ativado no Serviço de Aplicações

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar uma identidade gerida.

- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na aplicação API
- Certifique-se de que o Registo com o Diretório Ativo Azure está ativado na App de Função
- Certifique-se de que o Registo com o Azure Ative Directory está ativado na Web App

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Certifique-se de que a versão '.Net Framework' é a mais recente, se utilizada como parte da aplicação web

Este projeto atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do .Net Framework.

- Certifique-se de que a versão '.Net Framework' é a mais recente, se usada como parte da aplicação API
- Certifique-se de que a versão '.Net Framework' é a mais recente, se usada como parte da App de Função
- Certifique-se de que a versão '.Net Framework' é a mais recente, se usada como parte da aplicação Web

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Certifique-se de que a 'versão PHP' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do PHP.

- Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação Api
- Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação Function
- Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Certifique-se de que a 'versão Python' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente do Python.

- Certifique-se de que 'Python version' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Function
- Certifique-se de que 'Python version' é a mais recente, se usada como parte da aplicação Web

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Certifique-se de que 'Versão Java' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente de Java.

- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação web

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que o ajudam a garantir que as aplicações web estão a usar a versão mais recente de HTTP.

- Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Api
- Certifique-se de que 'versão HTTP' é a mais recente, se usada para executar a aplicação 'Função'
- Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web

## <a name="next-steps"></a>Próximos passos

Agora que reviu o mapeamento de controlo do projeto de benchmark da CIS Microsoft Azure Foundations, visite os seguintes artigos para conhecer o projeto ou visite a Azure Policy no portal Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark blueprint - Visão geral](./index.md) 
>  [CIS Microsoft Azure Foundations Benchmark blueprint - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).