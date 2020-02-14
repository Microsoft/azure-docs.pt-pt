---
title: TDE (Transparent Data Encryption) gerenciada pelo cliente
description: Suporte a Bring Your Own Key (BYOK) para Transparent Data Encryption (TDE) com Azure Key Vault para banco de dados SQL e data warehouse. TDE com BYOK visão geral, benefícios, como funciona, considerações e recomendações.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: 8e91bb9223f3e6ccd4c76614d75db8591dbed045
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201526"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Transparent Data Encryption do SQL do Azure com chave gerenciada pelo cliente

A Encriptação transparente de [dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) azure SQL com chave gerida pelo cliente permite trazer a sua própria chave (BYOK) para proteção de dados em repouso, e permite que as organizações implementem a separação de deveres na gestão de chaves e dados. Com a Transparent Data Encryption gerenciada pelo cliente, o cliente é responsável por e em um controle total de um gerenciamento de ciclo de vida de chave (criação de chave, carregamento, rotação, exclusão), permissões de uso de chave e auditoria de operações em chaves.

Neste cenário, a chave utilizada para encriptação da Chave de Encriptação de Base de Dados (DEK), chamada protectorTDE, é uma chave assimétrica gerida pelo cliente armazenada num Cofre de [Chaves Azure (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)gerido pelo cliente e gerido pelo cliente, um sistema de gestão de chaves externas baseado na nuvem. O Key Vault é um armazenamento seguro escalonável e altamente disponível para chaves de criptografia RSA, opcionalmente apoiado por HSMs (módulos de segurança de hardware) validados pelo FIPS 140-2 nível 2. Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia/descriptografia usando a chave para as entidades autorizadas. A chave pode ser gerada pelo cofre chave, importado ou [transferido para o cofre chave a partir de um dispositivo HSM on-prem](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Para o banco de dados SQL do Azure e o Azure SQL Data Warehouse, o protetor TDE é definido no nível do servidor lógico e é herdado por todos os bancos de dados criptografados associados a esse servidor. Para o Azure SQL Instância Gerenciada, o protetor de TDE é definido no nível de instância e é herdado por todos os bancos de dados criptografados nessa instância. O *servidor* de termo refere-se tanto ao servidor lógico sQL Database como ao caso gerido ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Para aqueles que usam TDE gerenciados por serviços que gostariam de começar a usar o TDE gerenciado pelo cliente, os dados permanecem criptografados durante o processo de troca e não há nenhum tempo de inatividade nem nova criptografia dos arquivos de banco de dados. Alternar de uma chave gerenciada por serviço para uma chave gerenciada pelo cliente requer apenas uma nova criptografia do DEK, que é uma operação rápida e online.

## <a name="benefits-of-the-customer-managed-tde"></a>Benefícios do TDE gerenciado pelo cliente

O TDE gerenciado pelo cliente fornece os seguintes benefícios para o cliente:

- Controle completo e granular sobre o uso e o gerenciamento do protetor de TDE;

- Transparência do uso do protetor TDE;

- Capacidade de implementar a separação de tarefas no gerenciamento de chaves e dados dentro da organização;

- Key Vault administrador pode revogar as permissões de acesso à chave para tornar o banco de dados criptografado inacessível;

- Gerenciamento central de chaves no AKV;

- Maior confiança dos clientes finais, já que o AKV foi projetado de modo que a Microsoft não possa ver nem extrair as chaves de criptografia;

## <a name="how-customer-managed-tde-works"></a>Como funciona o TDE gerenciado pelo cliente

![Configuração e funcionamento do TDE gerenciado pelo cliente](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Para que o servidor possa usar o TDE protector armazenado em AKV para criptografia do DEK, o administrador do Key Vault precisa conceder os seguintes direitos de acesso ao servidor usando sua identidade exclusiva do AAD:

- **obter** - para recuperar a parte pública e propriedades da chave no Cofre chave

- **wrapKey** - para ser capaz de proteger (encriptar) DEK

- **desembrulharKey** - para ser capaz de desproteger (desencriptar) DEK

O administrador do cofre chave também pode [permitir o registo de eventos de auditoria do cofre chave,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)para que possam ser auditados mais tarde.

Quando o servidor é configurado para usar um protetor de TDE de AKV, o servidor envia o DEK de cada banco de dados habilitado para TDE para o cofre de chaves para criptografia. O Key Vault retorna o DEK criptografado, que é armazenado no banco de dados do usuário.

Quando necessário, o servidor envia DEK protegidas para o cofre de chaves para descriptografia.

Os auditores podem usar Azure Monitor para examinar os logs de AuditEvent do cofre de chaves, se o registro em log estiver habilitado.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisitos para configurar o TDE gerenciado pelo cliente

### <a name="requirements-for-configuring-akv"></a>Requisitos para configurar AKV

- O Key Vault e o banco de dados SQL/instância gerenciada devem pertencer ao mesmo locatário Azure Active Directory. Não há suporte para interações de servidor e cofre de chaves entre locatários. Para mover os recursos posteriormente, o TDE com AKV precisará ser reconfigurado. Saiba mais sobre [recursos móveis.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)

- A função [de eliminação suave](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ativada no cofre da chave, para proteger contra a eliminação da chave acidental (ou cofre chave) de perda de dados. Os recursos eliminados suavemente são retidos durante 90 dias, a menos que recuperados ou purgados pelo cliente entretanto. As ações de *recuperação* e *purga* têm as suas próprias permissões associadas a uma política chave de acesso ao cofre. A função soft-delete está desligada por defeito e pode ser ativada através de [Powershell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) ou [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Não pode ser ativado através do portal Azure.  

- Conceda ao servidor de banco de dados SQL ou à instância gerenciada acesso ao cofre de chaves (Get, wrapKey, unwrapKey) usando sua identidade de Azure Active Directory. Ao usar portal do Azure, a identidade do Azure AD é criada automaticamente. Ao usar o PowerShell ou a CLI, a identidade do Azure AD deve ser explicitamente criada e a conclusão deve ser verificada. Consulte [o Configure TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configure TDE com BYOK para Managed Instance](https://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao utilizar o PowerShell.

- Ao utilizar firewall com AKV, tem de ativar a opção *Permitir que os serviços fidedignos da Microsoft contornem a firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Requisitos para configurar o protetor de TDE

- O protetor de TDE só pode ser assimétrico, RSA 2048 ou RSA HSM 2048 Key.

- A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.

- A chave deve estar no estado *ativado.*

- Se você estiver importando a chave existente para o cofre de chaves, certifique-se de fornecê-la nos formatos de arquivo com suporte (. pfx,. byok ou. Backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Recomendações ao configurar o TDE gerenciado pelo cliente

### <a name="recommendations-when-configuring-akv"></a>Recomendações ao configurar o AKV

- Associe no máximo 500 Uso Geral ou 200 Comercialmente Crítico bancos de dados no total com um cofre de chaves em uma única assinatura para garantir a alta disponibilidade quando o servidor acessa o protetor de TDE no cofre de chaves. Estes números baseiam-se na experiência e documentados nos [principais limites](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)de serviço do cofre. A intenção aqui é evitar problemas após o failover do servidor, pois ele irá disparar tantas operações de chave no cofre quanto há bancos de dados nesse servidor.

- Defina um bloqueio de recurso no cofre de chaves para controlar quem pode excluir esse recurso crítico e impedir a exclusão acidental ou não autorizada. Saiba mais sobre bloqueios de [recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Habilitar auditoria e relatórios em todas as chaves de criptografia: o Key Vault fornece logs que são fáceis de injetar em outras informações de segurança e ferramentas de gerenciamento de eventos. O [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) de Gestão de Operações é um exemplo de um serviço que já está integrado.

- Vincule cada servidor com dois cofres de chaves que residem em regiões diferentes e mantenha o mesmo material de chave, para garantir a alta disponibilidade de bancos de dados criptografados. Marque somente a chave do cofre de chaves na mesma região que um protetor de TDE. O sistema usará

### <a name="recommendations-when-configuring-tde-protector"></a>Recomendações ao configurar o protetor de TDE
- Mantenha uma cópia do protetor de TDE em um local seguro ou a caução para o serviço de caução.

- Se a chave for gerada no cofre de chaves, crie um backup de chave antes de usar a chave em AKV pela primeira vez. A cópia de segurança só pode ser restaurada num Cofre de Chaves Azure. Saiba mais sobre o comando [Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Crie um novo backup sempre que qualquer alteração for feita na chave (por exemplo, atributos de chave, marcas, ACLs).

- **Guarde versões anteriores** da chave no cofre da chave quando rodar as teclas, para que as cópias de dados mais antigas possam ser restauradas. Quando o protetor TDE é alterado para uma base de dados, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE. No momento da restauração, cada backup precisa do protetor de TDE em que foi criptografado no momento da criação. As rotações das teclas podem ser efetuadas seguindo as instruções no Rotate o Protetor transparente de [encriptação de dados utilizando powershell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Mantenha todas as chaves usadas anteriormente em AKV mesmo depois de alternar para chaves gerenciadas pelo serviço. Ele garante que os backups de banco de dados possam ser restaurados com os protetores de TDE armazenados no AKV.  Os protetores de TDE criados com Azure Key Vault precisam ser mantidos até que todos os backups armazenados restantes tenham sido criados com chaves gerenciadas pelo serviço. Faça cópias de backup recuperáveis destas teclas utilizando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga os passos da [remoção de uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protetor de TDE inacessível

Quando a Transparent Data Encryption é configurada para usar uma chave gerenciada pelo cliente, o acesso contínuo ao protetor de TDE é necessário para que o banco de dados permaneça online. Se o servidor perder o acesso ao protetor TDE gerido pelo cliente no AKV, em até 10 minutos uma base de dados começará a negar todas as ligações com a mensagem de erro correspondente e mudar o seu estado para *Inacessível*. A única ação permitida numa base de dados no estado inacessível é a sua demissão.

> [!NOTE]
> Se o banco de dados estiver inacessível devido a uma interrupção intermitente da rede, não haverá nenhuma ação necessária e os bancos de dados voltarão a ficar online automaticamente.

Depois que o acesso à chave for restaurado, colocar o banco de dados online novamente exigirá tempo e etapas adicionais, o que pode variar com base no tempo decorrido sem acesso à chave e o tamanho dos dados no banco de dado:

- Se o acesso à chave for restaurado dentro de 8 horas, o banco de dados será reparado automaticamente na próxima hora.

- Se o acesso chave for restaurado após mais de 8 horas, a cicatrização automática não é possível e a recolocação da base de dados requer passos adicionais no portal e pode demorar um tempo significativo dependendo do tamanho da base de dados. Uma vez que a base de dados esteja novamente on-line, configurações de nível de servidor previamente configuradas, tais como configuração do [grupo failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) histórico de restauração de pontono e etiquetas **serão perdidas**. Portanto, é recomendável implementar um sistema de notificação que permita que você identifique e resolva os problemas de acesso de chave subjacente dentro de 8 horas.

### <a name="accidental-tde-protector-access-revocation"></a>Revogação de acesso de protetor de TDE acidental

Pode acontecer que alguém com direitos de acesso suficientes ao cofre de chaves desabilite acidentalmente o acesso do servidor à chave por:

- revogando o cofre chave *get*, *wrapKey*, *desembrulharAutorizações* chave do servidor

- apagar a chave

- excluindo o cofre de chaves

- alterando as regras de firewall do Key Vault

- apagar a identidade gerida do servidor no Diretório Ativo Azure

Saiba mais sobre [as causas comuns para](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)que a base de dados fique inacessível .

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitoramento do TDE gerenciado pelo cliente

Para monitorizar o estado da base de dados e para permitir o alerta para a perda de acesso protetor TDE, configure as seguintes funcionalidades do Azure:
- [Saúde dos Recursos Azure.](https://docs.microsoft.com/azure/service-health/resource-health-overview) Um banco de dados inacessível que perdeu o acesso ao protetor de TDE aparecerá como "indisponível" após a negação da primeira conexão com o banco de dados.
- [Registo de atividade](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) saqueado quando o acesso ao protetor TDE no cofre de chaves gerido pelo cliente falha, as entradas são adicionadas ao registo de atividade.  A criação de alertas para estes eventos permitir-lhe-á restabelecer o acesso o mais rapidamente possível.
- [Os Grupos de Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) podem ser definidos para lhe enviar notificações e alertas com base nas suas preferências, por exemplo, e-mail/SMS/Push/Voice, Logic App, Webhook, ITSM ou Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup e restauração de banco de dados com TDE gerenciados pelo cliente

Depois que um banco de dados é criptografado com TDE usando uma chave de Key Vault, todos os backups gerados recentemente também são criptografados com o mesmo protetor de TDE. Quando o protetor TDE é alterado, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE.

Para restaurar um backup criptografado com um protetor TDE do Key Vault, verifique se o material da chave está disponível para o servidor de destino. Portanto, recomendamos que você mantenha todas as versões antigas do protetor de TDE no Key Vault, para que os backups de banco de dados possam ser restaurados.

> [!IMPORTANT]
> A qualquer momento, não pode haver mais de um conjunto de protetor TDE para um servidor. É a chave marcada com "tornar a chave o protetor de TDE padrão" na folha portal do Azure. No entanto, várias chaves adicionais podem ser vinculadas a um servidor sem marcá-las como um protetor de TDE. Essas chaves não são usadas para proteger DEK, mas podem ser usadas durante a restauração de um backup, se o arquivo de backup for criptografado com a chave com a impressão digital correspondente.

Se a chave necessária para restaurar uma cópia de segurança já não estiver disponível para o servidor-alvo, a seguinte mensagem de erro é devolvida na tentativa de restauro: "O servidor-alvo `<Servername>` não tem acesso a todos os URIs AKV criados entre \<Timestamp #1> e \<Timestamp #2>. Repita a operação após restaurar todos os URIs de AKV. "

Para mitigar, execute o [get-AzSqlServerKeyKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet para o servidor lógico De base de dados SQL alvo ou [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) para a instância gerida pelo alvo para devolver a lista de chaves disponíveis e identificar as que faltam. Para garantir que todos os backups possam ser restaurados, verifique se o servidor de destino da restauração tem acesso a todas as chaves necessárias. Essas chaves não precisam ser marcadas como protetor de TDE.

Para saber mais sobre a recuperação de cópias de segurança para a Base de Dados SQL, consulte Recuperar uma base de [dados Azure SQL](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de backup para o SQL Data Warehouse, consulte Recuperar um Armazém de [Dados Azure SQL](../sql-data-warehouse/backup-and-restore.md). Para a cópia de segurança/restauro nativo do SQL Server com instância gerida, consulte [Quickstart: Restaurar uma base de dados para uma instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Considerações adicionais para arquivos de log: Arquivos de log de backup permanecem criptografados com o protetor de TDE original, mesmo que ele tenha sido girado e o banco de dados agora esteja usando um novo protetor de TDE.  No momento da restauração, ambas as chaves serão necessárias para restaurar o banco de dados.  Se o arquivo de log estiver usando um protetor de TDE armazenado em Azure Key Vault, essa chave será necessária no momento da restauração, mesmo que o banco de dados tenha sido alterado para usar o TDE gerenciado por serviço enquanto isso.

## <a name="high-availability-with-customer-managed-tde"></a>Alta disponibilidade com TDE gerenciados pelo cliente

Mesmo em casos em que não haja redundância geográfica configurada para o servidor, é altamente recomendável configurar o servidor para usar dois cofres de chaves diferentes em duas regiões diferentes com o mesmo material de chave. Ele pode ser feito criando um protetor de TDE usando o cofre de chaves primário colocalizado na mesma região que o servidor e clonando a chave em um cofre de chaves em uma região diferente do Azure, para que o servidor tenha acesso a um segundo cofre de chaves caso o cofre de chaves primário exper ience uma interrupção enquanto o banco de dados está em execução.

Use o cmdlet backup-AzKeyVaultKey para recuperar a chave no formato criptografado do cofre de chaves primárias e, em seguida, use o cmdlet Restore-AzKeyVaultKey e especifique um cofre de chaves na segunda região para clonar a chave. Como alternativa, use portal do Azure para fazer backup e restaurar a chave. A chave no cofre de chaves secundários no se em outra região não deve ser marcada como um protetor TDE e nem mesmo permitida.

 Se houver uma interrupção afetando o cofre de chaves primárias e, somente em seguida, o sistema alternará automaticamente para a outra chave vinculada com a mesma impressão digital no cofre de chaves secundário, se existir. Observe que essa opção não ocorrerá se o protetor de TDE estiver inacessível devido a direitos de acesso revogados ou porque a chave ou o cofre de chaves é excluído, pois pode indicar que o cliente queria intencionalmente restringir o acesso do servidor à chave.

![HA de servidor único](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>TDE de recuperação geográfica e gerenciada pelo cliente

Em ambos os cenários [de geo-replicação ativa](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) e [de grupos failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) cada servidor envolvido requer um cofre chave separado, que deve ser co-localizado com o servidor na mesma região do Azure. O cliente é responsável por manter o material de chave entre os cofres de chaves consistentes, para que o secundário geográfico esteja em sincronia e possa assumir o uso da mesma chave de seu cofre de chaves local se o primário se tornar inacessível devido a uma interrupção na região e um failover for disparado . Até quatro secundários podem ser configurados, e o encadeamento (secundários de secundários) não tem suporte.

Para evitar problemas ao estabelecer ou durante a replicação geográfica devido ao material de chave incompleto, é importante seguir estas regras ao configurar o TDE gerenciado pelo cliente:

- Todos os cofres de chaves envolvidos devem ter as mesmas propriedades e os mesmos direitos de acesso para os respectivos servidores.

- Todos os cofres de chaves envolvidos devem conter material de chave idêntico. Ele se aplica não apenas ao protetor de TDE atual, mas a todos os protetores de TDE anteriores que podem ser usados nos arquivos de backup.

- Tanto a configuração inicial quanto a rotação do protetor TDE devem ser feitas no secundário primeiro e, em seguida, no primário.

![Grupos de failover e Dr geográfica](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Para testar uma falha, siga os passos na visão geral da [geo-replicação ativa](sql-database-geo-replication-overview.md). Isso deve ser feito regularmente para confirmar se as permissões de acesso do SQL para os dois cofres de chaves foram mantidas.

## <a name="next-steps"></a>Passos seguintes

Você também pode querer verificar os seguintes scripts de exemplo do PowerShell para as operações comuns com o TDE gerenciado pelo cliente:

- [Rode o protetor transparente de encriptação de dados para base de dados SQL usando powershell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Remova um protetor transparente de encriptação de dados (TDE) para base de dados SQL utilizando powerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Gerencie a encriptação de dados transparente numa instância gerida com a sua própria chave usando powerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
