---
title: Controles de exemplo de plano de referência do CIS Microsoft Azure Foundations
description: Mapeamento de recomendação do exemplo de plano de referência do CIS Microsoft Azure Foundations para Azure Policy.
ms.date: 10/01/2019
ms.topic: sample
ms.openlocfilehash: 55abac9f7479f0ee7d1adddea64cb81a1c7cf2b5
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74544533"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapeamento de recomendação da amostra Blueprint de benchmark do Microsoft Azure Foundations do CIS

O artigo a seguir fornece detalhes sobre como a amostra do plano gráfico de benchmark do Azure Microsoft Azure Foundations do CIS é mapeada para as recomendações de benchmark Microsoft Azure Foundations. Para obter mais informações sobre as recomendações, consulte [CIS Microsoft Azure Foundations benchmark](https://www.cisecurity.org/benchmark/azure/).

Os mapeamentos a seguir são para as recomendações de **benchmark v 1.1.0 do Microsoft Azure Foundations do CIS** . Use a navegação à direita para ir diretamente para um mapeamento de recomendação específico.
Muitas das recomendações mapeadas são implementadas com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione as **recomendações de\[visualização\] Audit CIS Microsoft Azure Foundations benchmark v 1.1.0 e implante extensões de VM específicas para dar suporte** à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 garantir que a autenticação multifator esteja habilitada para todos os usuários privilegiados

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que ajudam a monitorar quando a autenticação multifator não está habilitada em contas com privilégios de Azure Active Directory.

- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada em contas com permissões de gravação em sua assinatura

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 garantir que a autenticação multifator esteja habilitada para todos os usuários sem privilégios

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar quando a autenticação multifator não está habilitada em contas de Azure Active Directory sem privilégios.

- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Verifique se não há usuários convidados

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que ajudam a monitorar as contas de convidado que podem precisar ser removidas.

- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de leitura devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 garantir que o tipo de preço Standard esteja selecionado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar redes e máquinas virtuais em que a camada Standard da central de segurança não está habilitada.

 - A camada de preços Standard da central de segurança deve ser selecionada

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 Verifique se ' provisionamento automático do agente de monitoramento ' está definido como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o provisionamento automático do agente de log Analytics esteja habilitado.

- O provisionamento automático do agente de monitoramento de Log Analytics deve ser habilitado em sua assinatura

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Verifique se a configuração de política padrão ASC "monitorar atualizações do sistema" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as atualizações do sistema sejam instaladas em máquinas virtuais.

- As atualizações do sistema devem ser instaladas em seus computadores

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Verifique se a configuração de política padrão ASC "monitorar vulnerabilidades do sistema operacional" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar vulnerabilidades de máquina virtual unremediated.

- Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Verifique se a configuração de política padrão ASC "monitor Endpoint Protection" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Endpoint Protection esteja habilitado em máquinas virtuais.

- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Verifique se a configuração de política padrão ASC "monitorar criptografia de disco" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os discos de máquina virtual sejam criptografados.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 Verifique se a configuração de política padrão ASC "monitorar grupos de segurança de rede" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a proteger máquinas virtuais voltadas para a Internet.

- As regras do grupo de segurança de rede para máquinas virtuais voltadas para a Internet devem ser protegidas

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Verifique se a configuração de política padrão ASC "monitorar o Firewall do aplicativo Web" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a proteger máquinas virtuais que executam aplicativos Web.

- As regras de NSGs para aplicativos Web em IaaS devem ser protegidas

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 Verifique se a configuração de política padrão ASC "Habilitar monitoramento de NGFW (firewall de próxima geração)" não está "desabilitada"

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que ajudam a proteger sub-redes e máquinas virtuais contra ameaças, restringindo o acesso. A política da central de segurança referenciada por esta recomendação de benchmark do Microsoft Azure Foundations foi substituída por duas novas recomendações. As políticas referenciadas abaixo abordam as novas recomendações.

- As sub-redes devem ser associadas a um grupo de segurança de rede
- As máquinas virtuais devem ser associadas a um grupo de segurança de rede

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 Verifique se a configuração de política padrão ASC "monitorar avaliação de vulnerabilidade" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as vulnerabilidades sejam detectadas e corrigidas.

- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 Verifique se a configuração de política padrão ASC "monitorar criptografia de blob de armazenamento" não está "desabilitada"

A criptografia de armazenamento do Azure está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada. (Esse é um recurso padrão do Azure; não há nenhuma atribuição de política.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Verifique se a configuração de política padrão ASC "monitorar acesso à rede JIT" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a controlar o acesso às máquinas virtuais.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 Verifique se a configuração de política padrão ASC "monitorar lista de permissões de aplicativo adaptável" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os controles de aplicativo adaptáveis estejam habilitados em máquinas virtuais.

- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 Verifique se a configuração de política padrão ASC "monitorar auditoria de SQL" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria do SQL Server esteja habilitada.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 Verifique se a configuração de política padrão ASC "monitorar criptografia de SQL" não está "desabilitada"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que Transparent Data Encryption esteja habilitado em bancos de dados SQL.

- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 Verifique se ' email de contato de segurança ' está definido

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam corretamente habilitadas

- Um endereço de email de contato de segurança deve ser fornecido para sua assinatura

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 garantir que o contato de segurança ' número de telefone ' esteja definido

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam corretamente habilitadas

- Um número de telefone de contato de segurança deve ser fornecido para sua assinatura

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 Verifique se ' enviar notificação por email para alertas de severidade alta ' está definido como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam corretamente habilitadas

- A notificação por email para alertas de severidade alta deve ser habilitada

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 Verifique se "enviar email também para proprietários de assinatura" está definido como "ativado"

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança estejam corretamente habilitadas

- A notificação por email para o proprietário da assinatura para alertas de alta gravidade deve ser habilitada

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Verifique se ' transferência segura necessária ' está definido como ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar contas de armazenamento que permitem conexões não seguras.

- A transferência segura para contas de armazenamento deve ser habilitada

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Verifique se a regra de acesso de rede padrão para contas de armazenamento está definida como negar

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar contas de armazenamento que permitem acesso irrestrito.

- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 garantir que ' serviços confiáveis da Microsoft ' esteja habilitado para acesso à conta de armazenamento

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar contas de armazenamento que não permitem o acesso de serviços confiáveis da Microsoft.

- As contas de armazenamento devem permitir o acesso de serviços confiáveis da Microsoft

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Verifique se a ' auditoria ' está definida como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria do SQL Server esteja habilitada. 

- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Verifique se "AuditActionGroups" na política de "auditoria" de um SQL Server está definido corretamente

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a auditoria do SQL Server esteja configurada corretamente.

- As configurações de auditoria do SQL devem ter grupos de ações configurados para capturar atividades críticas

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 garantir que a retenção de ' auditoria ' seja ' maior que 90 dias '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs do SQL Server sejam retidos por pelo menos 90 dias.

- Os SQL Servers devem ser configurados com dias de retenção de auditoria maiores que 90 dias.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Verifique se a ' segurança de dados avançada ' em um SQL Server está definida como ' on '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a segurança de dados avançada esteja habilitada em servidores SQL e instâncias gerenciadas do SQL.

- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas do SQL
- A segurança de dados avançada deve estar ativada nos seus servidores SQL

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 Verifique se ' tipos de detecção de ameaças ' está definido como ' todos '

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que ajudam a garantir que a proteção avançada contra ameaças esteja configurada corretamente em servidores SQL e instâncias gerenciadas do SQL.

- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' nas configurações de segurança de dados avançadas do SQL Server
- Os tipos de proteção avançada contra ameaças devem ser definidos como ' todos ' na instância gerenciada do SQL configurações de segurança de dados avançadas

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Verifique se ' enviar alertas para ' está definido

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança de dados avançadas estejam habilitadas corretamente.

- As configurações de segurança de dados avançadas para a instância gerenciada do SQL devem conter um endereço de email para receber alertas de segurança
- As configurações avançadas de segurança de dados para o SQL Server devem conter um endereço de email para receber alertas de segurança

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 Verifique se ' serviço de email e coadministradores ' está ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as notificações de segurança de dados avançadas estejam habilitadas corretamente.

- As notificações por email para administradores e proprietários de assinatura devem ser habilitadas na instância gerenciada do SQL configurações de segurança de dados avançadas
- As notificações por e-mail destinadas aos administradores e proprietários de subscrições devem estar ativadas nas definições da segurança de dados avançada no servidor SQL

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 Verifique se o administrador do Azure Active Directory está configurado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que um administrador de Azure Active Directory seja provisionado para servidores SQL.

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Verifique se a ' criptografia de dados ' está definida como ' on ' em um banco de dados SQL

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que Transparent Data Encryption esteja habilitado em bancos de dados SQL.

- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 garantir que o protetor de TDE do SQL Server seja criptografado com BYOK (Use sua própria chave)

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que ajudam a garantir que o protetor de dados criptografados transparente para SQL Servers e instâncias gerenciadas do SQL seja criptografado com sua própria chave.

- O protetor TDE da instância gerenciada do SQL deve ser criptografado com sua própria chave
- O protetor de TDE do servidor SQL deve estar encriptado com a sua própria chave

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 Verifique se ' impor conexão SSL ' está definido como ' habilitado ' para o servidor de banco de dados MySQL

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os servidores de banco de dados MySQL imponham conexões SSL.

- Impor a conexão SSL deve ser habilitada para servidores de banco de dados MySQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 Verifique se ' impor conexão SSL ' está definido como ' habilitado ' para o servidor de banco de dados PostgreSQL

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os servidores de banco de dados PostgreSQL imponham conexões SSL.

- Impor a conexão SSL deve ser habilitada para servidores de banco de dados PostgreSQL

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 Verifique se o parâmetro de servidor ' connection_throttling ' está definido como ' ON ' para o servidor de banco de dados PostgreSQL

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a reduzir ataques de força bruta em servidores de banco de dados PostgreSQL.

- A limitação de conexão deve ser habilitada para servidores de banco de dados PostgreSQL

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 Verifique se o administrador do Azure Active Directory está configurado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que um administrador de Azure Active Directory seja provisionado para servidores SQL. O parâmetro de comparação do CIS Microsoft Azure Foundations inclui essa recomendação; no entanto, é uma duplicata da [recomendação 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 garantir que exista um perfil de log

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que exista um perfil de log para todas as assinaturas do Azure. 

- As assinaturas do Azure devem ter um perfil de log para o log de atividades

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Verifique se a retenção do log de atividades está definida em 365 dias ou mais

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs de atividades sejam mantidos por pelo menos um ano.

- O log de atividades deve ser retido por pelo menos um ano

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 garantir que o perfil de auditoria Capture todas as atividades

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o perfil de log esteja configurado corretamente.

- Azure Monitor perfil de log deve coletar logs para as categorias ' gravar ', ' ' excluir ' e ' ação '

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 garantir que o perfil de log Capture logs de atividade para todas as regiões, incluindo global

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o perfil de log esteja configurado corretamente.

- Azure Monitor deve coletar logs de atividade de todas as regiões

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 garantir que o log do Azure keyvault esteja ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os logs de diagnóstico estejam habilitados para cofres de chaves.

- Os logs de diagnóstico no Key Vault devem ser habilitados

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 Verifique se o observador de rede está ' habilitado '

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o observador de rede esteja habilitado para todas as regiões em que os recursos são implantados. Essa política requer uma matriz de parâmetros que especifica todas as regiões aplicáveis. O valor padrão nesta definição de iniciativa de política é ' eastus '.

- O observador de rede deve estar habilitado

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 garantir que o ' disco do sistema operacional ' esteja criptografado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a criptografia de disco esteja habilitada em máquinas virtuais.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 garantir que os ' discos de dados ' estejam criptografados

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que a criptografia de disco esteja habilitada em máquinas virtuais.

- A criptografia de disco deve ser aplicada em máquinas virtuais

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 garantir que os ' discos desanexados ' sejam criptografados

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que discos desconectados sejam criptografados.

- Discos desanexados devem ser criptografados

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 garantir que apenas as extensões aprovadas sejam instaladas

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que apenas as extensões de máquina virtual aprovadas sejam instaladas. Essa política requer uma matriz de parâmetros que especifica todas as extensões de máquina virtual aprovadas. Essa definição de iniciativa de política contém padrões sugeridos que os clientes devem validar. 

 - Somente extensões de VM aprovadas devem ser instaladas

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 garantir que os patches do sistema operacional mais recentes para todas as máquinas virtuais sejam aplicados

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que as atualizações do sistema sejam instaladas em máquinas virtuais.

- As atualizações do sistema devem ser instaladas em seus computadores

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 garantir que o Endpoint Protection para todas as máquinas virtuais esteja instalado

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o Endpoint Protection esteja habilitado em máquinas virtuais.

- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 garantir que o cofre de chaves seja recuperável

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os objetos do cofre de chaves sejam recuperáveis no caso de exclusão acidental.

- Os objetos Key Vault devem ser recuperáveis

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 habilitar o RBAC (controle de acesso baseado em função) nos serviços Kubernetess do Azure

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que o controle de acesso baseado em função seja usado para permissões gerenciadas em clusters do serviço kubernetes

- \[visualização\]: o RBAC (controle de acesso baseado em função) deve ser usado nos serviços Kubernetess

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 garantir que o aplicativo Web Redirecione todo o tráfego HTTP para HTTPS no serviço Azure App

Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a garantir que os aplicativos Web sejam acessíveis somente em conexões seguras.

- Aplicação Web só deve estar acessível através de HTTPS

## <a name="next-steps"></a>Passos Seguintes

Agora que você analisou o mapeamento de controle do plano de referência do CIS Microsoft Azure Foundations, visite os artigos a seguir para saber mais sobre o plano gráfico ou visite Azure Policy no portal do Azure para atribuir a iniciativa:

> [!div class="nextstepaction"]
> [Cis Microsoft Azure Foundations plano de referência Blueprint-visão geral](./index.md)
> [cis Microsoft Azure fundamentos de benchmark-etapas de implantação](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).