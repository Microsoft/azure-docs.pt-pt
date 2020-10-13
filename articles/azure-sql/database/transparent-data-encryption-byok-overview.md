---
title: Encriptação de dados transparentes gerida pelo cliente (TDE)
description: Traga o suporte da sua própria chave (BYOK) para encriptação de dados transparentes (TDE) com cofre de chave Azure para base de dados SQL e Azure Synapse Analytics. TDE com visão geral byok, benefícios, como funciona, considerações e recomendações.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/18/2020
ms.openlocfilehash: 4e17af8289c68ded282a9c4a9ca2d400d31ca30d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602674"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Encriptação de Dados Transparente do SQL do Azure com chave gerida pelo cliente
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

A Azure SQL [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) com chave gerida pelo cliente permite trazer a sua própria chave (BYOK) para proteção de dados em repouso, e permite que as organizações implementem a separação de deveres na gestão de chaves e dados. Com a encriptação de dados transparente gerida pelo cliente, o cliente é responsável por e em total controlo de uma gestão chave do ciclo de vida (criação chave, upload, rotação, eliminação), permissões de utilização chave e auditoria de operações em chaves.

Neste cenário, a chave utilizada para a encriptação da Chave de Encriptação da Base de Dados (DEK), chamada protetor TDE, é uma chave assimétrica gerida pelo cliente armazenada num Cofre chave [Azure (AKV)](../../key-vault/general/secure-your-key-vault.md)de propriedade do cliente e gerido pelo cliente, um sistema de gestão de chaves externo baseado na nuvem. O Key Vault está altamente disponível e um armazenamento seguro escalável para chaves criptográficas RSA, opcionalmente apoiados por módulos de segurança de hardware validados FIPS 140-2 Nível 2 (HSMs). Não permite o acesso direto a uma chave armazenada, mas fornece serviços de encriptação/desencriptação utilizando a chave para as entidades autorizadas. A chave pode ser gerada pelo cofre-chave, importado ou [transferido para o cofre-chave a partir de um dispositivo HSM on-prem](../../key-vault/keys/hsm-protected-keys.md).

Para a Azure SQL Database e Azure Synapse Analytics, o protetor TDE é definido ao nível do servidor e é herdado por todas as bases de dados encriptadas associadas a esse servidor. Para Azure SQL Managed Instance, o protetor TDE é definido ao nível de instância e é herdado por todas as bases de dados encriptadas nesse caso. O *servidor* de termo refere-se tanto a um servidor na BASE de Dados SQL como a Azure Synapse e a um caso gerido em SQL Managed Instance ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Para aqueles que usam O TDE gerido pelo serviço que gostariam de começar a usar o TDE gerido pelo cliente, os dados permanecem encriptados durante o processo de comutação, e não há tempo de inatividade nem reencriminação dos ficheiros da base de dados. Mudar de uma chave gerida pelo serviço para uma chave gerida pelo cliente só requer a reencriminação do DEK, que é uma operação rápida e online.

> [!NOTE]
> Para fornecer aos clientes do Azure SQL duas camadas de encriptação de dados em repouso, a encriptação da infraestrutura (utilizando o algoritmo de encriptação AES-256) com as teclas geridas pela plataforma está a ser lançada. Isto fornece uma camada de encriptação de adição em repouso juntamente com TDE com chaves geridas pelo cliente, que já está disponível. Neste momento, os clientes devem solicitar acesso a esta capacidade. Se estiver interessado nesta capacidade, AzureSQLDoubleEncryptionAtRest@service.microsoft.com contacte.

## <a name="benefits-of-the-customer-managed-tde"></a>Benefícios do TDE gerido pelo cliente

O TDE gerido pelo cliente proporciona os seguintes benefícios ao cliente:

- Controlo total e granular sobre a utilização e gestão do protetor TDE;

- Transparência da utilização do protetor TDE;

- Capacidade de implementar a separação de deveres na gestão de chaves e dados dentro da organização;

- O administrador do Key Vault pode revogar permissões de acesso à chave para tornar a base de dados encriptada inacessível;

- Gestão central das chaves em AKV;

- Uma maior confiança dos seus clientes finais, uma vez que a AKV foi concebida de modo a que a Microsoft não possa ver nem extrair chaves de encriptação;

## <a name="how-customer-managed-tde-works"></a>Como funciona o TDE gerido pelo cliente

![Configuração e funcionamento do TDE gerido pelo cliente](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Para que o servidor possa utilizar o protetor TDE armazenado em AKV para encriptação do DEK, o administrador do cofre de chaves precisa de dar os seguintes direitos de acesso ao servidor utilizando a sua identidade única Azure Ative Directory (Azure AD):

- **obter** - para recuperar a parte pública e propriedades da chave no Cofre chave

- **wrapKey** - para ser capaz de proteger (encriptar) DEK

- **desembrulhoKey** - para ser capaz de desprotegir (desencriptar) DEK

O administrador-chave do cofre também pode [permitir o registo de eventos de auditoria de cofre chave,](../../azure-monitor/insights/key-vault-insights-overview.md)para que possam ser auditados mais tarde.

Quando o servidor está configurado para utilizar um protetor TDE a partir de AKV, o servidor envia o DEK de cada base de dados ativada por TDE para o cofre de chaves para encriptação. O cofre de chaves devolve o DEK encriptado, que é depois armazenado na base de dados do utilizador.

Quando necessário, o servidor envia DEK protegido para o cofre de chaves para desencriptação.

Os auditores podem utilizar o Azure Monitor para rever os registos auditevent do cofre de chaves, se o registo de registo estiver ativado.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Requisitos para configurar o TDE gerido pelo cliente

### <a name="requirements-for-configuring-akv"></a>Requisitos para configurar a AKV

- O cofre-chave e a base de dados SQL/instância gerida devem pertencer ao mesmo inquilino do Azure Ative Directory. O cofre de chaves de inquilino cruzado e as interações do servidor não são suportadas. Para mover recursos depois, tDE com AKV terá que ser reconfigurado. Saiba mais sobre [a movimentação de recursos.](../../azure-resource-manager/management/move-resource-group-and-subscription.md)

- A função [de eliminação suave](../../key-vault/general/soft-delete-overview.md) deve ser ativada no cofre da chave, para proteger contra a perda de dados a eliminação acidental da chave (ou cofre de chaves). Os recursos de soft-deleted são retidos por 90 dias, a menos que entretanto sejam recuperados ou purgados pelo cliente. As ações *de recuperação* e *purga* têm as suas próprias permissões associadas a uma política de acesso ao cofre chave. A função de eliminação suave está desligada por predefinição e pode ser ativada através do [PowerShell](../../key-vault/general/soft-delete-powershell.md#enabling-soft-delete) ou [do CLI](../../key-vault/general/soft-delete-cli.md#enabling-soft-delete). Não pode ser ativado através do portal Azure.  

- Conceda ao servidor ou ao acesso de instância gerida ao cofre de chaves (obter, wrapKey, desembrulhar aKey) utilizando a sua identidade de Diretório Ativo Azure. Ao utilizar o portal Azure, a identidade Azure AD é criada automaticamente. Ao utilizar o PowerShell ou o CLI, a identidade AZure AD deve ser explicitamente criada e a conclusão deve ser verificada. Consulte [o Configure TDE com BYOK](transparent-data-encryption-byok-configure.md) e [Configure TDE com BYOK para SQL Managed Instance](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) para instruções detalhadas passo a passo ao utilizar o PowerShell.

- Ao utilizar firewall com AKV, deve ativar a opção *Permitir que os serviços fidedignos da Microsoft contornem a firewall*.

### <a name="requirements-for-configuring-tde-protector"></a>Requisitos para configurar o protetor TDE

- O protetor TDE só pode ser assimétrico, rsa ou rsa HSM. Os comprimentos-chave suportados são 2048 e 3072 bytes.

- A data de ativação da chave (se definida) deve ser uma data e hora no passado. A data de validade (se definida) deve ser uma data e hora futuras.

- A chave deve estar no estado *ativado.*

- Se estiver a importar a chave existente para o cofre da chave, certifique-se de que a fornece nos formatos de ficheiro suportados (.pfx, .byok ou .backup).

> [!NOTE]
> O Azure SQL suporta agora a utilização de uma chave RSA armazenada num HSM gerido como Protetor TDE. Esta funcionalidade está em **pré-visualização pública.** Azure Key Vault Managed HSM é um serviço de nuvem totalmente gerido, altamente disponível, de inquilino único, que permite proteger chaves criptográficas para as suas aplicações em nuvem, utilizando HSMs validados FIPS 140-2 Nível 3. Saiba mais sobre [HSMs geridos.](https://aka.ms/mhsm)


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Recomendações ao configurar o TDE gerido pelo cliente

### <a name="recommendations-when-configuring-akv"></a>Recomendações ao configurar a AKV

- Associe no máximo 500 bases de dados De Propósito Geral ou 200 Business Critical no total com um cofre chave numa única subscrição para garantir alta disponibilidade quando o servidor acede ao protetor TDE no cofre de chaves. Estes números baseiam-se na experiência e documentados nos [principais limites](../../key-vault/general/service-limits.md)de serviço do cofre. A intenção aqui é evitar problemas após a falha do servidor, uma vez que irá desencadear tantas operações chave contra o cofre como existem bases de dados nesse servidor.

- Coloque um bloqueio de recursos no cofre da chave para controlar quem pode eliminar este recurso crítico e evitar a eliminação acidental ou não autorizada. Saiba mais sobre [as fechaduras de recursos.](../../azure-resource-manager/management/lock-resources.md)

- Ativar a auditoria e a reportagem em todas as chaves de encriptação: O cofre de chaves fornece registos fáceis de injetar noutras informações de segurança e ferramentas de gestão de eventos. Operations Management Suite [Log Analytics](../../azure-monitor/insights/azure-key-vault.md) é um exemplo de um serviço que já está integrado.

- Ligue cada servidor a dois cofres-chave que residem em diferentes regiões e possuam o mesmo material chave, para garantir uma elevada disponibilidade de bases de dados encriptadas. Marque apenas a chave do cofre chave na mesma região que um protetor TDE. O sistema mudará automaticamente para o cofre de chaves na região remota se houver uma falha que afete o cofre chave na mesma região.

### <a name="recommendations-when-configuring-tde-protector"></a>Recomendações ao configurar o protetor TDE

- Guarde uma cópia do protetor TDE num local seguro ou guarde-a para o serviço de caução.

- Se a chave for gerada no cofre da chave, crie uma cópia de segurança antes de utilizar a chave em AKV pela primeira vez. A cópia de segurança pode ser restaurada apenas para um Cofre de Chaves Azure. Saiba mais sobre o comando [Backup-AzKeyVaultKey.](/powershell/module/az.keyvault/backup-azkeyvaultkey)

- Crie uma nova cópia de segurança sempre que forem feitas alterações à tecla (por exemplo, atributos chave, tags, ACLs).

- **Mantenha as versões anteriores** da chave no cofre quando estiver a rodar as chaves, para que as cópias de segurança da base de dados mais antigas possam ser restauradas. Quando o protetor TDE é alterado para uma base de dados, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE. No momento da restauração, cada backup precisa do protetor TDE com o que foi encriptado na hora da criação. As rotações das chaves podem ser efetuadas seguindo as instruções da [Rotação do Protetor de Encriptação de Dados Transparente utilizando o PowerShell](transparent-data-encryption-byok-key-rotation.md).

- Mantenha todas as teclas anteriormente utilizadas em AKV mesmo depois de mudar para teclas geridas pelo serviço. Garante que as cópias de segurança da base de dados podem ser restauradas com os protetores TDE armazenados em AKV.  Os protetores TDE criados com O Cofre da Chave Azure têm de ser mantidos até que todas as cópias de segurança restantes sejam criadas com teclas geridas por serviço. Faça cópias de cópias de segurança recuperáveis destas teclas utilizando [o Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey).

- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga os passos da [Chave Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-remove-tde-protector.md).

## <a name="inaccessible-tde-protector"></a>Protetor TDE inacessível

Quando a encriptação de dados transparente é configurada para utilizar uma chave gerida pelo cliente, é necessário um acesso contínuo ao protetor TDE para que a base de dados se mantenha on-line. Se o servidor perder o acesso ao protetor TDE gerido pelo cliente em AKV, em até 10 minutos uma base de dados começará a negar todas as ligações com a mensagem de erro correspondente e a alterar o seu estado para *Inacessível*. A única ação permitida numa base de dados no estado inacessível é apagá-la.

> [!NOTE]
> Se a base de dados estiver inacessível devido a uma falha intermitente de rede, não é necessária nenhuma ação e as bases de dados voltarão a funcionar automaticamente.

Após o acesso à chave ser restaurado, a tomada de base de dados online requer tempo e passos adicionais, que podem variar em função do tempo decorrido sem acesso à chave e do tamanho dos dados na base de dados:

- Se o acesso à chave for restaurado dentro de 8 horas, a base de dados curar-se-á automaticamente dentro de uma hora.

- Se o acesso à chave for restaurado mais de 8 horas depois, não será possível realizar a autorrecuperação. Além disso, recuperar a base de dados requer passos adicionais no portal e pode demorar bastante tempo, dependendo do tamanho da base de dados. Uma vez que a base de dados esteja novamente on-line, configuradas previamente configuradas configurações de nível do servidor, tais como configuração [do grupo de failover,](auto-failover-group-overview.md) histórico de restauração pontual e tags **serão perdidas**. Por isso, recomenda-se a implementação de um sistema de notificação que lhe permita identificar e resolver os principais problemas de acesso subjacentes dentro de 8 horas.

Abaixo está uma visão dos passos adicionais necessários no portal para colocar uma base de dados inacessível de volta on-line.

![TDE BYOK Base de Dados Inacessível](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Revogação acidental do acesso ao protetor TDE

Pode acontecer que alguém com direitos de acesso suficientes ao cofre de chaves desative acidentalmente o acesso do servidor à chave:

- revogando o cofre de chaves *obter*, *wrapKey*, *desembrulhar* permissões Do servidor

- excluir a chave

- excluindo o cofre chave

- alterando as regras de firewall do cofre chave

- eliminando a identidade gerida do servidor no Azure Ative Directory

Saiba mais sobre [as causas comuns para que a base de dados se torne inacessível.](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)

## <a name="monitoring-of-the-customer-managed-tde"></a>Monitorização do TDE gerido pelo cliente

Para monitorizar o estado da base de dados e permitir o alerta para a perda de acesso ao protetor TDE, configuure as seguintes funcionalidades Azure:

- [Saúde dos Recursos Azure.](../../service-health/resource-health-overview.md) Uma base de dados inacessível que tenha perdido o acesso ao protetor TDE mostrará como "Indisponível" depois de ter sido negada a primeira ligação à base de dados.
- [Registo de atividades](../../service-health/alerts-activity-log-service-notifications.md) quando o acesso ao protetor TDE no cofre de chaves gerido pelo cliente falha, as entradas são adicionadas ao registo de atividade.  A criação de alertas para estes eventos permitir-lhe-á restabelecer o acesso o mais rapidamente possível.
- [Os Grupos de Ação](../../azure-monitor/platform/action-groups.md) podem ser definidos para enviar-lhe notificações e alertas com base nas suas preferências, por exemplo, Email/SMS/Push/Voice, Logic App, Webhook, ITSM ou Automation Runbook.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Backup de base de dados e restauro com TDE gerido pelo cliente

Uma vez que uma base de dados é encriptada com O TDE usando uma chave do Key Vault, quaisquer cópias de segurança recentemente geradas também são encriptadas com o mesmo protetor TDE. Quando o protetor TDE é alterado, as cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o mais recente protetor TDE.

Para restaurar uma cópia de segurança encriptada com um protetor TDE do Key Vault, certifique-se de que o material chave está disponível para o servidor alvo. Por isso, recomendamos que mantenha todas as versões antigas do protetor TDE no cofre de chaves, para que as cópias de segurança da base de dados possam ser restauradas.

> [!IMPORTANT]
> A qualquer momento, não pode haver mais do que um conjunto de protetor TDE para um servidor. É a chave marcada com "Faça da chave o protetor TDE predefinido" na lâmina do portal Azure. No entanto, várias teclas adicionais podem ser ligadas a um servidor sem as marcar como um protetor TDE. Estas teclas não são utilizadas para proteger o DEK, mas podem ser utilizadas durante a restauração de uma cópia de segurança, se o ficheiro de cópia de segurança for encriptado com a chave com a impressão digital correspondente.

Se a chave necessária para restaurar uma cópia de segurança já não estiver disponível para o servidor alvo, a seguinte mensagem de erro é devolvida na tentativa de restauro: "O servidor-alvo `<Servername>` não tem acesso a todos os URIs AKV criados entre e \<Timestamp #1> \<Timestamp #2> . Por favor, relembra a operação depois de restaurar todos os URIs AKV."

Para o mitigar, execute o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para o servidor alvo ou [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) para o caso gerido pelo alvo para devolver a lista das chaves disponíveis e identificar as que faltam. Para garantir que todas as cópias de segurança podem ser restauradas, certifique-se de que o servidor alvo para a restauração tem acesso a todas as teclas necessárias. Estas chaves não precisam de ser marcadas como protetora TDE.

Para saber mais sobre a recuperação de backup para a Base de Dados SQL, consulte [recuperar uma base de dados na Base de Dados SQL.](recovery-using-backups.md) Para saber mais sobre a recuperação de backup para a SQL Pool, consulte [Recuperar uma Piscina SQL.](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md) Para a cópia de segurança/restauro nativo do SQL Server com sql Managed Instance, consulte [Quickstart: Restaurar uma base de dados para SQL Managed Instance](../managed-instance/restore-sample-database-quickstart.md)

Consideração adicional para ficheiros de registo: Os ficheiros de registo com o fundo permanecem encriptados com o protetor TDE original, mesmo que tenha sido rodado e a base de dados esteja agora a utilizar um novo protetor TDE.  No momento de restauração, ambas as chaves serão necessárias para restaurar a base de dados.  Se o ficheiro de registo estiver a utilizar um protetor TDE armazenado no Cofre da Chave Azure, esta chave será necessária no momento de restauração, mesmo que a base de dados tenha sido alterada para utilizar o TDE gerido pelo serviço entretanto.

## <a name="high-availability-with-customer-managed-tde"></a>Alta disponibilidade com TDE gerido pelo cliente

Mesmo nos casos em que não há geo-redundância configurada para o servidor, é altamente recomendado configurar o servidor para usar dois cofres-chave diferentes em duas regiões diferentes com o mesmo material chave. Pode ser realizado criando um protetor TDE usando o cofre principal co-localizado na mesma região que o servidor e clonando a chave em um cofre chave em uma região de Azure diferente, de modo que o servidor tenha acesso a um segundo cofre chave se o cofre principal experimentar uma falha enquanto a base de dados está em funcionamento.

Utilize o Backup-AzKeyVaultKey cmdlet para recuperar a chave em formato encriptado a partir do cofre de tecla primária e, em seguida, use o Restore-AzKeyVaultKey cmdlet e especifique um cofre chave na segunda região para clonar a chave. Em alternativa, utilize o portal Azure para fazer recuar e restaurar a chave. A chave no cofre de chaves secundárias da outra região não deve ser marcada como protetor tde, e nem sequer é permitida.

Se houver uma falha que afete o cofre principal da chave, e só então, o sistema mudará automaticamente para a outra chave ligada com a mesma impressão digital no cofre da chave secundária, se existir. Note que esse interruptor não acontecerá se o protetor TDE estiver inacessível devido a direitos de acesso revogados, ou porque a chave ou o cofre chave são eliminados, pois pode indicar que o cliente intencionalmente queria restringir o servidor de aceder à chave.

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>TDE geo-DR e gerido pelo cliente

Em ambos os cenários [de geo-replicação ativa](active-geo-replication-overview.md) e [grupos de failover,](auto-failover-group-overview.md) cada servidor envolvido requer um cofre de chaves separado, que deve ser co-localizado com o servidor na mesma região de Azure. O cliente é responsável por manter o material chave através dos cofres-chave consistente, de modo que o geo-secundário está sincronizado e pode assumir usando a mesma chave do seu cofre de chaves local se o primário se tornar inacessível devido a uma falha na região e uma falha é desencadeada. Até quatro secundários podem ser configurados, e acorrentamento (secundários de secundários) não é suportado.

Para evitar problemas durante o estabelecimento ou durante a geo-replicação devido a material chave incompleto, é importante seguir estas regras ao configurar o TDE gerido pelo cliente:

- Todos os cofres-chave envolvidos devem ter as mesmas propriedades e os mesmos direitos de acesso para os respetivos servidores.

- Todos os cofres-chave envolvidos devem conter material de chave idêntico. Aplica-se não só ao atual protetor TDE, mas a todos os protetores TDE anteriores que podem ser utilizados nos ficheiros de backup.

- Tanto a configuração inicial como a rotação do protetor TDE devem ser feitas primeiro no secundário e, em seguida, na primária.

![Grupos de failover e geo-dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Para testar uma falha, siga os passos na [visão geral da geo-replicação ativa](active-geo-replication-overview.md). Os testes de failover devem ser feitos regularmente para validar que a Base de Dados SQL manteve a permissão de acesso a ambos os cofres-chave.

## <a name="next-steps"></a>Passos seguintes

Também pode querer verificar os seguintes scripts de amostra powerShell para as operações comuns com TDE gerido pelo cliente:

- [Rode o Protetor de Encriptação de Dados Transparente para base de dados SQL usando powerShell](transparent-data-encryption-byok-key-rotation.md)

- [Remova um protetor transparente de encriptação de dados (TDE) para base de dados SQL usando PowerShell](transparent-data-encryption-byok-remove-tde-protector.md)

- [Gerir encriptação de dados transparentes em SQL Managed Instance com a sua própria chave usando PowerShell](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)
