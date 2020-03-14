---
title: Encriptação transparente de dados gerida pelo cliente (TDE)
description: Traga o suporte da sua própria chave (BYOK) para encriptação de dados transparente (TDE) com cofre de chave Azure para base de dados SQL e Synapse Azure. TDE com visão geral byok, benefícios, como funciona, considerações e recomendações.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: a29466ad5b261e1e2ce818d7b4a18260e35caaec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255655"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Encriptação de dados transparente sql Azure com chave gerida pelo cliente

A Encriptação transparente de [dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) azure SQL com chave gerida pelo cliente permite trazer a sua própria chave (BYOK) para proteção de dados em repouso, e permite que as organizações implementem a separação de deveres na gestão de chaves e dados. Com encriptação transparente de dados gerida pelo cliente, o cliente é responsável e em pleno controlo de uma gestão chave do ciclo de vida (criação chave, upload, rotação, eliminação), permissões de utilização chave e auditoria de operações em teclas.

Neste cenário, a chave utilizada para encriptação da Chave de Encriptação de Base de Dados (DEK), chamada protectorTDE, é uma chave assimétrica gerida pelo cliente armazenada num Cofre de [Chaves Azure (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)gerido pelo cliente e gerido pelo cliente, um sistema de gestão de chaves externas baseado na nuvem. Key Vault é um armazenamento seguro altamente disponível e escalável para chaves criptográficas RSA, opcionalmente apoiadopor módulos de segurança de hardware validados de nível 2 (HSMs). Não permite o acesso direto a uma chave armazenada, mas fornece serviços de encriptação/desencriptação usando a chave para as entidades autorizadas. A chave pode ser gerada pelo cofre chave, importado ou [transferido para o cofre chave a partir de um dispositivo HSM on-prem](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Para a Base de Dados Azure SQL e azure Synapse, o protetor TDE é definido ao nível lógico do servidor e é herdado por todas as bases de dados encriptadas associadas a esse servidor. Para o Azure SQL Managed Instance, o protetor TDE é definido ao nível da instância e é herdado por todas as bases de dados encriptadas nessa instância. O *servidor* de termo refere-se tanto ao servidor lógico sQL Database como ao caso gerido ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Para aqueles que usam O TDE gerido pelo serviço que gostaria de começar a usar O TDE gerido pelo cliente, os dados permanecem encriptados durante o processo de transição, e não há tempo de inatividade nem reencriptação dos ficheiros de base de dados. Mudar de uma chave gerida pelo serviço para uma chave gerida pelo cliente apenas requer a reencriptação do DEK, que é uma operação rápida e online.

## <a name="benefits-of-the-customer-managed-tde"></a>Benefícios do TDE gerido pelo cliente

O TDE gerido pelo cliente proporciona os seguintes benefícios ao cliente:

- Controlo total e granular sobre a utilização e gestão do protetor TDE;

- Transparência da utilização protetora do TDE;

- Capacidade de implementação da separação de funções na gestão de chaves e dados dentro da organização;

- O administrador do Key Vault pode revogar as permissões de acesso chave para tornar a base de dados encriptada inacessível;

- Gestão central das chaves no AKV;

- Maior confiança dos seus clientes finais, uma vez que o AKV foi concebido de tal forma que a Microsoft não consegue ver nem extrair chaves de encriptação;

## <a name="how-customer-managed-tde-works"></a>Como funciona o TDE gerido pelo cliente

![Configuração e funcionamento do TDE gerido pelo cliente](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Para que o servidor possa utilizar o protetor TDE armazenado no AKV para encriptação do DEK, o administrador de cofre chave precisa de dar os seguintes direitos de acesso ao servidor utilizando a sua identidade AAD única:

- **obter** - para recuperar a parte pública e propriedades da chave no Cofre chave

- **wrapKey** - para ser capaz de proteger (encriptar) DEK

- **desembrulharKey** - para ser capaz de desproteger (desencriptar) DEK

O administrador do cofre chave também pode [permitir o registo de eventos de auditoria do cofre chave,](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)para que possam ser auditados mais tarde.

Quando o servidor está configurado para utilizar um protetor TDE do AKV, o servidor envia o DEK de cada base de dados ativada pelo TDE para o cofre chave para encriptação. O cofre chave devolve o DEK encriptado, que é depois armazenado na base de dados do utilizador.

Quando necessário, o servidor envia O DEK protegido para o cofre da chave para desencriptação.

Os auditores podem usar o Monitor Azure para rever os registos do cofre de chaves AuditEvent, se o registo estiver ativado.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisitos para configurar TDE gerido pelo cliente

### <a name="requirements-for-configuring-akv"></a>Requisitos para configurar AKV

- O cofre chave e a Base de Dados SQL/instância gerida devem pertencer ao mesmo inquilino do Diretório Ativo Azure. O cofre de chaves de inquilinos cruzados e as interações do servidor não são suportadas. Para movimentar recursos depois, o TDE com AKV terá de ser reconfigurado. Saiba mais sobre [recursos móveis.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)

- A função [de eliminação suave](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) deve ser ativada no cofre da chave, para proteger contra a eliminação da chave acidental (ou cofre chave) de perda de dados. Os recursos eliminados suavemente são retidos durante 90 dias, a menos que recuperados ou purgados pelo cliente entretanto. As ações de *recuperação* e *purga* têm as suas próprias permissões associadas a uma política chave de acesso ao cofre. A função soft-delete está desligada por defeito e pode ser ativada através de [Powershell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) ou [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete). Não pode ser ativado através do portal Azure.  

- Conceda ao servidor de base de dados SQL ou acesso de instância gerido ao cofre chave (get, wrapKey, unwrapKey) utilizando a sua identidade azure Ative Directory. Ao utilizar o portal Azure, a identidade Azure AD é criada automaticamente. Ao utilizar o PowerShell ou o CLI, a identidade Azure AD deve ser explicitamente criada e a conclusão deve ser verificada. Consulte [o Configure TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configure TDE com BYOK para Managed Instance](https://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao utilizar o PowerShell.

- Ao utilizar firewall com AKV, tem de ativar a opção *Permitir que os serviços fidedignos da Microsoft contornem a firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Requisitos para configurar o protetor TDE

- O protetor TDE pode ser apenas assimétrico, rSA 2048 ou rSA HSM 2048.

- A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.

- A chave deve estar no estado *ativado.*

- Se estiver a importar a chave existente para o cofre chave, certifique-se de que a fornece nos formatos de ficheiros suportados (.pfx, .byok ou .backup).

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Recomendações ao configurar TDE gerido pelo cliente

### <a name="recommendations-when-configuring-akv"></a>Recomendações ao configurar AKV

- Associe-se no máximo 500 bases de dados De Propósito Geral ou 200 Business Critical no total com um cofre chave numa única subscrição para garantir uma alta disponibilidade quando o servidor acede ao protetor TDE no cofre chave. Estes números baseiam-se na experiência e documentados nos [principais limites](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)de serviço do cofre. A intenção aqui é prevenir problemas após falha do servidor, uma vez que irá desencadear tantas operações chave contra o cofre como há bases de dados nesse servidor.

- Coloque um bloqueio de recursos no cofre da chave para controlar quem pode eliminar este recurso crítico e evitar a eliminação acidental ou não autorizada. Saiba mais sobre bloqueios de [recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Ativar auditoria e reportagem em todas as chaves de encriptação: O cofre chave fornece registos que são fáceis de injetar em outras ferramentas de informação de segurança e gestão de eventos. O [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) de Gestão de Operações é um exemplo de um serviço que já está integrado.

- Ligue cada servidor a dois cofres chave que residem em diferentes regiões e possuam o mesmo material chave, para garantir uma alta disponibilidade de bases de dados encriptadas. Marque apenas a chave do cofre chave na mesma região que um protetor TDE. O sistema vai usar

### <a name="recommendations-when-configuring-tde-protector"></a>Recomendações ao configurar o protetor TDE
- Guarde uma cópia do protetor TDE num local seguro ou deposite-a no serviço de caução.

- Se a chave for gerada no cofre da chave, crie uma chave de reserva antes de utilizar a chave no AKV pela primeira vez. A cópia de segurança só pode ser restaurada num Cofre de Chaves Azure. Saiba mais sobre o comando [Backup-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Crie uma nova cópia de segurança sempre que sejam feitas alterações na chave (por exemplo, atributos-chave, tags, ACLs).

- **Guarde versões anteriores** da chave no cofre da chave quando rodar as teclas, para que as cópias de dados mais antigas possam ser restauradas. Quando o protetor TDE é alterado para uma base de dados, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE. No momento de restauro, cada backup precisa do protetor TDE com o que foi encriptado no momento da criação. As rotações das teclas podem ser efetuadas seguindo as instruções no Rotate o Protetor transparente de [encriptação de dados utilizando powershell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- Guarde todas as teclas usadas anteriormente no AKV mesmo depois de mudar para as chaves geridas pelo serviço. Garante que as cópias de segurança da base de dados podem ser restauradas com os protetores TDE armazenados no AKV.  Os protetores TDE criados com o Azure Key Vault têm de ser mantidos até que todas as restantes cópias de segurança armazenadas tenham sido criadas com chaves geridas pelo serviço. Faça cópias de backup recuperáveis destas teclas utilizando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga os passos da [remoção de uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protetor TDE inacessível

Quando a encriptação transparente de dados é configurada para usar uma chave gerida pelo cliente, é necessário um acesso contínuo ao protetor TDE para que a base de dados permaneça on-line. Se o servidor perder o acesso ao protetor TDE gerido pelo cliente no AKV, em até 10 minutos uma base de dados começará a negar todas as ligações com a mensagem de erro correspondente e mudar o seu estado para *Inacessível*. A única ação permitida numa base de dados no estado inacessível é a sua demissão.

> [!NOTE]
> Se a base de dados estiver inacessível devido a uma falha intermitente de rede, não é necessária qualquer ação e as bases de dados voltarão automaticamente a funcionar.

Após o acesso à chave ser restaurado, retomar a base de dados online requer tempo e passos adicionais, que podem variar em função do tempo decorrido sem acesso à chave e do tamanho dos dados na base de dados:

- Se o acesso da chave for restaurado dentro de 8 horas, a base de dados irá sarar automaticamente dentro de uma hora.

- Se o acesso chave for restaurado após mais de 8 horas, a cicatrização automática não é possível e a recolocação da base de dados requer passos adicionais no portal e pode demorar um tempo significativo dependendo do tamanho da base de dados. Uma vez que a base de dados esteja novamente on-line, configurações de nível de servidor previamente configuradas, tais como configuração do [grupo failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) histórico de restauração de pontono e etiquetas **serão perdidas**. Por isso, recomenda-se a implementação de um sistema de notificação que lhe permita identificar e abordar os principais problemas de acesso subjacentes dentro de 8 horas.

### <a name="accidental-tde-protector-access-revocation"></a>Revogação acidental do acesso ao protetor TDE

Pode acontecer que alguém com direitos de acesso suficientes para o cofre chave desativa acidentalmente o acesso do servidor à chave por:

- revogando o cofre chave *get*, *wrapKey*, *desembrulharAutorizações* chave do servidor

- apagar a chave

- apagar o cofre chave

- alterando as regras de firewall do cofre chave

- apagar a identidade gerida do servidor no Diretório Ativo Azure

Saiba mais sobre [as causas comuns para](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)que a base de dados fique inacessível .

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorização do TDE gerido pelo cliente

Para monitorizar o estado da base de dados e para permitir o alerta para a perda de acesso protetor TDE, configure as seguintes funcionalidades do Azure:
- [Saúde dos Recursos Azure.](https://docs.microsoft.com/azure/service-health/resource-health-overview) Uma base de dados inacessível que tenha perdido o acesso ao protetor TDE mostrará como "Indisponível" depois de ter sido negada a primeira ligação à base de dados.
- [Registo de atividade](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) saqueado quando o acesso ao protetor TDE no cofre de chaves gerido pelo cliente falha, as entradas são adicionadas ao registo de atividade.  A criação de alertas para estes eventos permitir-lhe-á restabelecer o acesso o mais rapidamente possível.
- [Os Grupos de Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) podem ser definidos para lhe enviar notificações e alertas com base nas suas preferências, por exemplo, e-mail/SMS/Push/Voice, Logic App, Webhook, ITSM ou Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup de base de dados e restauro com TDE gerido pelo cliente

Uma vez que uma base de dados é encriptada com TDE usando uma chave do Key Vault, quaisquer cópias de segurança recentemente geradas também são encriptadas com o mesmo protetor TDE. Quando o protetor TDE é alterado, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE.

Para restaurar uma cópia de segurança encriptada com um protetor TDE do Key Vault, certifique-se de que o material chave está disponível para o servidor alvo. Por isso, recomendamos que mantenha todas as versões antigas do protetor TDE no cofre da chave, para que as cópias de dados possam ser restauradas.

> [!IMPORTANT]
> A qualquer momento não pode haver mais do que um conjunto de protetor TDE para um servidor. É a chave marcada com "Faça da chave o protetor TDE padrão" na lâmina do portal Azure. No entanto, várias teclas adicionais podem ser ligadas a um servidor sem as marcar como protetora TDE. Estas teclas não são utilizadas para proteger o DEK, mas podem ser utilizadas durante a restauração a partir de uma cópia de segurança, se o ficheiro de reserva for encriptado com a chave com a impressão digital correspondente.

Se a chave necessária para restaurar uma cópia de segurança já não estiver disponível para o servidor-alvo, a seguinte mensagem de erro é devolvida na tentativa de restauro: "O servidor-alvo `<Servername>` não tem acesso a todos os URIs AKV criados entre \<Timestamp #1> e \<Timestamp #2>. Por favor, retente a operação depois de restaurar todos os URIs AKV."

Para mitigar, execute o [get-AzSqlServerKeyKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet para o servidor lógico De base de dados SQL alvo ou [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) para a instância gerida pelo alvo para devolver a lista de chaves disponíveis e identificar as que faltam. Para garantir que todas as cópias de segurança podem ser restauradas, certifique-se de que o servidor-alvo para a restauração tem acesso a todas as teclas necessárias. Estas chaves não precisam de ser marcadas como protetora tde.

Para saber mais sobre a recuperação de cópias de segurança para a Base de Dados SQL, consulte Recuperar uma base de [dados Azure SQL](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de backup para o SQL Pool, consulte [Recuperar um SQL Pool](../sql-data-warehouse/backup-and-restore.md). Para a cópia de segurança/restauro nativo do SQL Server com instância gerida, consulte [Quickstart: Restaurar uma base de dados para uma instância gerida](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore)

Consideração adicional para ficheiros de registo: Os ficheiros de registo apoiados permanecem encriptados com o protetor TDE original, mesmo que tenha sido rodado e a base de dados esteja agora a utilizar um novo protetor TDE.  No momento de restauro, ambas as chaves serão necessárias para restaurar a base de dados.  Se o ficheiro de registo estiver a utilizar um protetor TDE armazenado no Cofre de Chaves Azure, esta chave será necessária no momento de reestabelecimento, mesmo que a base de dados tenha sido alterada para utilizar o TDE gerido pelo serviço entretanto.

## <a name="high-availability-with-customer-managed-tde"></a>Alta disponibilidade com TDE gerido pelo cliente

Mesmo nos casos em que não há geo-redundância configurada para o servidor, é altamente recomendado configurar o servidor para usar dois cofres chave diferentes em duas regiões diferentes com o mesmo material chave. Pode ser realizado criando um protetor TDE usando o cofre principal co-localizado na mesma região que o servidor e clonando a chave em um cofre chave em uma região azure diferente, de modo que o servidor tem acesso a um segundo cofre chave se o cofre principal exper ience uma paragem enquanto a base de dados está em funcionamento.

Utilize o cmdlet Backup-AzKeyVaultKey para recuperar a chave em formato encriptado a partir do cofre principal da chave e, em seguida, utilize o cmdlet Restore-AzKeyVaultKey e especifique um cofre chave na segunda região para clonar a chave. Em alternativa, utilize o portal Azure para fazer backup e restaurar a chave. A chave no cofre secundário em se outra região não deve ser marcada como protetorta TDE, e nem sequer é permitida.

 Se houver uma falha que afete o cofre primário da chave, e só então, o sistema mudará automaticamente para a outra tecla ligada com a mesma impressão digital no cofre de chaves secundário, se existir. Note que o interruptor não acontecerá se o protetor TDE estiver inacessível devido aos direitos de acesso revogados, ou porque o cofre chave ou chave é eliminado, pois pode indicar que o cliente intencionalmente quis restringir o servidor de aceder à chave.

![HA de um servidor único](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>TDE geo-DR e gerido pelo cliente

Em ambos os cenários [de geo-replicação ativa](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) e [de grupos failover,](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) cada servidor envolvido requer um cofre chave separado, que deve ser co-localizado com o servidor na mesma região do Azure. O cliente é responsável por manter o material chave através dos cofres-chave consistente, de modo que o geo-secundário está sincronizado e pode assumir o uso da mesma chave do seu cofre chave local se o primário ficar inacessível devido a uma falha na região e uma falha é desencadeada . Até quatro secundários podem ser configurados, e a corrente (secundárias de secundários) não é suportada.

Para evitar problemas durante o estabelecimento ou durante a geo-replicação devido a material chave incompleto, é importante seguir estas regras ao configurar o TDE gerido pelo cliente:

- Todos os cofres-chave envolvidos devem ter as mesmas propriedades, e os mesmos direitos de acesso para os respetivos servidores.

- Todos os cofres-chave envolvidos devem conter material chave idêntico. Aplica-se não só ao protetor TDE atual, mas também a todos os protetores TDE anteriores que podem ser utilizados nos ficheiros de backup.

- Tanto a configuração inicial como a rotação do protetor TDE devem ser feitas primeiro no secundário e, em seguida, na primária.

![Grupos failover e geo-dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Para testar uma falha, siga os passos na visão geral da [geo-replicação ativa](sql-database-geo-replication-overview.md). Deve ser feito regularmente para confirmar as permissões de acesso da SQL a ambos os cofres-chave.

## <a name="next-steps"></a>Passos seguintes

Também pode querer verificar os seguintes scripts de amostra PowerShell para as operações comuns com o TDE gerido pelo cliente:

- [Rode o protetor transparente de encriptação de dados para base de dados SQL usando powershell](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [Remova um protetor transparente de encriptação de dados (TDE) para base de dados SQL utilizando powerShell](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Gerencie a encriptação de dados transparente numa instância gerida com a sua própria chave usando powerShell](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
