---
title: TDE-integração de Azure Key Vault ou Bring Your Own Key (BYOK) – banco de dados SQL do Azure | Microsoft Docs
description: Suporte a Bring Your Own Key (BYOK) para Transparent Data Encryption (TDE) com Azure Key Vault para banco de dados SQL e data warehouse. TDE com BYOK visão geral, benefícios, como funciona, considerações e recomendações.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 07/18/2019
ms.openlocfilehash: cdd5e29fcc01639c03da70614f53ac648ee6620c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318571"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>Azure SQL Transparent Data Encryption com chaves gerenciadas pelo cliente no Azure Key Vault: Suporte a Bring Your Own Key

[Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) com a integração Azure Key Vault permite criptografar a DEK (chave de criptografia de banco de dados) com uma chave assimétrica gerenciada pelo cliente chamada protetor TDE. Isso também é conhecido como suporte a Bring Your Own Key (BYOK) para Transparent Data Encryption.  No cenário BYOK, o protetor de TDE é armazenado em um [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)gerenciado e de Propriedade do cliente, o sistema de gerenciamento de chaves externas baseado em nuvem do Azure. O protetor de TDE pode ser [gerado](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates) pelo cofre de chaves ou [transferido](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) para o cofre de chaves de um dispositivo HSM local. O TDE DEK, que é armazenado na página de inicialização de um banco de dados, é criptografado e descriptografado pelo protetor TDE armazenado em Azure Key Vault, que nunca deixa.  O banco de dados SQL precisa receber permissões para o cofre de chaves de Propriedade do cliente para descriptografar e criptografar o DEK. Se as permissões do SQL Server lógico para o cofre de chaves forem revogadas, um banco de dados ficará inacessível, as conexões serão negadas e todos os dados serão criptografados. Para o banco de dados SQL do Azure, o protetor TDE é definido no nível lógico do SQL Server e é herdado por todos os bancos de dados associados a esse servidor. Para o [Azure SQL instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), o protetor de TDE é definido no nível de instância e é herdado  por todos os bancos de dados criptografados nessa instância. O termo *servidor* se refere ao servidor e à instância em todo este documento, a menos que indicado de forma diferente.

> [!NOTE]
> Transparent Data Encryption com integração de Azure Key Vault (Bring Your Own Key) para Instância Gerenciada do Banco de Dados SQL do Azure está em versão prévia.


Com a integração do TDE com o Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chaves, permissões do Key Vault, backups de chave e habilitar a auditoria/relatórios em todos os protetores do TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento de chaves central, aproveita os HSMs (módulos de segurança de hardware) monitorados e permite a separação de tarefas entre o gerenciamento de chaves e dados para ajudar a atender às políticas de segurança.  

O TDE com integração Azure Key Vault oferece os seguintes benefícios:

- Maior transparência e controle granular com a capacidade de gerenciar o protetor de TDE automaticamente
- Capacidade de revogar permissões a qualquer momento para renderizar o banco de dados inacessível
- Gerenciamento central de protetores TDE (juntamente com outras chaves e segredos usados em outros serviços do Azure) hospedando-os em Key Vault
- Separação das responsabilidades de gerenciamento de dados e de chave dentro da organização, para dar suporte à separação de tarefas
- Maior confiança de seus próprios clientes, pois Key Vault foi projetada para que a Microsoft não veja nem Extraia nenhuma chave de criptografia.
- Suporte para rotação de chaves

> [!IMPORTANT]
> Para aqueles que usam TDE gerenciados por serviço que gostariam de começar a usar Key Vault, o TDE permanece habilitado durante o processo de alternar para um protetor TDE no Key Vault. Não há nenhum tempo de inatividade nem nova criptografia dos arquivos de banco de dados. Alternar de uma chave gerenciada por serviço para uma chave de Key Vault requer apenas uma nova criptografia da DEK (chave de criptografia de banco de dados), que é uma operação rápida e online.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Como funciona o TDE com suporte à integração Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

![Autenticação do servidor para o Key Vault](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quando o TDE é configurado pela primeira vez para usar um protetor de TDE de Key Vault, o servidor envia a DEK de cada banco de dados habilitado para TDE para Key Vault para uma solicitação de chave de encapsulamento. Key Vault retorna a chave de criptografia de banco de dados criptografada, que é armazenada no banco de dados do usuário.  

> [!IMPORTANT]
> É importante observar que **, uma vez que um protetor de TDE é armazenado em Azure Key Vault, ele nunca deixa o Azure Key Vault**. O servidor só pode enviar solicitações de operação de chave para o material da chave do protetor TDE dentro do Key Vault e **nunca acessa ou armazena em cache o protetor TDE**. O administrador de Key Vault tem o direito de revogar Key Vault permissões do servidor a qualquer momento; nesse caso, todas as conexões com o banco de dados são negadas.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Diretrizes para configurar o TDE com Azure Key Vault

### <a name="general-guidelines"></a>Orientações Gerais

- Verifique se Azure Key Vault e o banco de dados SQL/Instância Gerenciada do Azure estarão no mesmo locatário.  **Não há suporte para**interações de servidor e cofre de chaves entre locatários.
- Se você estiver planejando uma movimentação de locatário, TDE com AKV precisará ser reconfigurado, saiba mais sobre como [mover recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).
- Ao configurar o TDE com Azure Key Vault, é importante considerar a carga colocada no cofre de chaves por operações repetidas de encapsulamento/desencapsulamento. Por exemplo, uma vez que todos os bancos de dados associados a um servidor SQL Database usam o mesmo protetor de TDE, um failover desse servidor irá disparar tantas operações de chave no cofre quanto houver bancos de dados no servidor. Com base em nossa experiência e nos limites de serviço documentados do [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), é recomendável associar no máximo 500 bancos de dados Standard/Uso Geral ou 200 Premium/comercialmente crítico com um Azure Key Vault em uma única assinatura para garantir consistentemente alto disponibilidade ao acessar o protetor de TDE no cofre.
- Aconselhável Mantenha uma cópia do protetor de TDE local.  Isso requer que um dispositivo HSM crie um protetor de TDE localmente e um sistema de caução de chave para armazenar uma cópia local do protetor de TDE.  Saiba [como transferir uma chave de um HSM local para Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).


### <a name="guidelines-for-configuring-azure-key-vault"></a>Diretrizes para configurar Azure Key Vault

- Crie um cofre de chaves com a proteção de [exclusão](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) e limpeza reversível habilitada para proteger contra perda de dados em caso de chave acidental – ou cofre de chaves – exclusão.  Você deve usar [o PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) no cofre de chaves (essa opção ainda não está disponível no portal do akv – mas exigida pelo SQL do Azure):  
  - Os recursos com exclusão reversível são retidos por um determinado período de tempo, 90 dias, a menos que sejam recuperados ou limpos.
  - As ações de **recuperação** e **limpeza** têm suas próprias permissões associadas em uma política de acesso do cofre de chaves.
- Defina um bloqueio de recurso no cofre de chaves para controlar quem pode excluir esse recurso crítico e ajudar a evitar a exclusão acidental ou não autorizada.  [Saiba mais sobre bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Conceda ao servidor do banco de dados SQL acesso ao cofre de chaves usando sua identidade Azure Active Directory (Azure AD).  Ao usar a interface do usuário do portal, a identidade do Azure AD é criada automaticamente e as permissões de acesso do cofre de chaves são concedidas ao servidor.  Usando o PowerShell para configurar o TDE com o BYOK, a identidade do Azure AD deve ser criada e a conclusão deve ser verificada. Consulte [Configurar TDE com BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [Configurar o TDE com BYOK para instância gerenciada](https://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao usar o PowerShell.

   > [!NOTE]
   > Se a identidade do Azure AD **for excluída acidentalmente ou se as permissões do servidor forem revogadas** usando a política de acesso do cofre de chaves ou inadvertidamente movendo o servidor para um locatário diferente, o servidor perderá o acesso ao cofre de chaves e TDEá os bancos de dados criptografados estarão inacessíveis e logons serão negados até que a identidade e as permissões do servidor lógico do Azure AD tenham sido restauradas.  

- Ao usar firewalls e redes virtuais com Azure Key Vault, você deve permitir que serviços confiáveis da Microsoft ignorem esse firewall. Escolha Sim.

   > [!NOTE]
   > Se os bancos de dados SQL criptografados do TDE perderem o acesso ao cofre de chaves porque não podem ignorar o firewall, os bancos de dados estarão inacessíveis e logons serão negados até que as permissões de bypass do firewall tenham sido restauradas.

- Habilitar auditoria e relatórios em todas as chaves de criptografia: Key Vault fornece logs que são fáceis de injetar em outras ferramentas de SIEM (gerenciamento de eventos e informações de segurança). O OMS (Operations Management Suite) [log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) é um exemplo de um serviço que já está integrado.
- Para garantir a alta disponibilidade de bancos de dados criptografados, configure cada servidor de banco de dados SQL com dois cofres de chaves do Azure que residem em regiões diferentes.


### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Diretrizes para configurar o protetor de TDE (chave assimétrica)

- Crie sua chave de criptografia localmente em um dispositivo HSM local. Certifique-se de que essa seja uma chave RSA 2048 assimétrica para que ela seja armazenadada em Azure Key Vault.
- Caução a chave em um sistema de caução de chave.  
- Importe o arquivo de chave de criptografia (. pfx,. byok ou. Backup) para Azure Key Vault.

   > [!NOTE]
   > Para fins de teste, é possível criar uma chave com Azure Key Vault, no entanto, essa chave não pode ser caução, porque a chave privada nunca pode deixar o cofre de chaves.  Sempre faça backup e as chaves de caução usadas para criptografar dados de produção, pois a perda da chave (exclusão acidental no cofre de chaves, expiração etc.) resulta em perda de dados permanente.

- Se você usar uma chave com uma data de expiração – implemente um sistema de aviso de expiração para girar a chave antes de ela expirar: **depois que a chave expirar, os bancos de dados criptografados perderão o acesso ao seu protetor TDE e** ficarão inacessíveis e todos os logons serão negados até a chave foi girada para uma nova chave.
- Verifique se a chave está habilitada e tem permissões para executar as operações *Get*, *Wrap Key*e Unwrap *Key* .
- Crie um backup de chave de Azure Key Vault antes de usar a chave em Azure Key Vault pela primeira vez. Saiba mais sobre o comando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) .
- Crie um novo backup sempre que qualquer alteração for feita à chave (por exemplo, adicionar ACLs, adicionar marcas, adicionar atributos de chave).
- **Mantenha as versões anteriores** da chave no cofre de chaves ao girar as chaves, para que os backups de banco de dados mais antigos possam ser restaurados. Quando o protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são atualizados** para usar o protetor de TDE mais recente.  Cada backup precisa do protetor de TDE com o qual foi criado no momento da restauração. As rotações de chave podem ser executadas seguindo as instruções em [girar o protetor de Transparent Data Encryption usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Mantenha todas as chaves usadas anteriormente em Azure Key Vault depois de voltar para chaves gerenciadas por serviço.  Isso garante que os backups de banco de dados possam ser restaurados com os protetores de TDE armazenados em Azure Key Vault.  Os protetores de TDE criados com Azure Key Vault precisam ser mantidos até que todos os backups armazenados tenham sido criados com chaves gerenciadas pelo serviço.  
- Faça cópias de backup recuperáveis dessas chaves usando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey).
- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga as etapas em [remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

### <a name="guidelines-for-monitoring-the-tde-with-azure-key-vault-configuration"></a>Diretrizes para monitorar o TDE com a configuração Azure Key Vault

Se o SQL Server lógico perder o acesso ao protetor de TDE gerenciado pelo cliente no Azure Key Vault, o banco de dados negará todas as conexões e aparecerá inacessível na portal do Azure.  As causas mais comuns para isso são:
- Key Vault excluído acidentalmente ou atrás de um firewall
- Chave do Key Vault excluída acidentalmente ou expirada
- A AppId da instância de SQL Server lógica excluída acidentalmente
- Permissões de chave específicas para a instância de SQL Server lógica AppId revogada

 > [!NOTE]
 > O banco de dados será reparado e se tornará online automaticamente se o acesso ao protetor de TDE gerenciado pelo cliente for restaurado dentro de 48 horas.  Se o banco de dados estiver inacessível devido a uma interrupção intermitente da rede, não haverá nenhuma ação necessária e os bancos de dados voltarão a ficar online automaticamente.
  
- Para obter mais informações sobre como solucionar problemas de configurações existentes, consulte [solucionar problemas do TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde)

- Para monitorar o estado do banco de dados e habilitar o alerta para perda de acesso ao protetor do TDE, configure os seguintes recursos do Azure:
    - [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). Um banco de dados inacessível que perdeu o acesso ao protetor de TDE aparecerá como "indisponível" após a negação da primeira conexão com o banco de dados.
    - [Log de atividades](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) quando o acesso ao protetor de TDE no cofre de chaves gerenciado pelo cliente falha, as entradas são adicionadas ao log de atividades.  A criação de alertas para esses eventos permitirá que você reinstale o acesso assim que possível.
    - Os [grupos de ações](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) podem ser definidos para enviar notificações e alertas com base em suas preferências, por exemplo, email/SMS/Push/voz, aplicativo lógico, webhook, ITSM ou runbook de automação.
    

## <a name="high-availability-geo-replication-and-backup--restore"></a>Alta disponibilidade, replicação geográfica e backup/restauração

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

Como configurar a alta disponibilidade com o Azure Key Vault depende da configuração do banco de dados e do servidor do banco de dados SQL, e aqui estão as configurações recomendadas para dois casos distintos.  O primeiro caso é um banco de dados autônomo ou um servidor de banco de dados SQL sem redundância geográfica configurada.  O segundo caso é um banco de dados ou servidor de banco de dados SQL configurado com grupos de failover ou redundância geográfica, em que deve ser garantido que cada cópia com redundância geográfica tenha um Azure Key Vault local dentro do grupo de failover para garantir que os failovers geográficos funcionem.

No primeiro caso, se você precisar de alta disponibilidade de um banco de dados e de um servidor de banco de dados SQL sem redundância geográfica configurada, é altamente recomendável configurar o servidor para usar dois cofres de chaves diferentes em duas regiões diferentes com o mesmo material de chave. Isso pode ser feito criando um protetor de TDE usando o Key Vault primário colocalizado na mesma região que o servidor do banco de dados SQL e clonando a chave em um cofre de chaves em uma região diferente do Azure, para que o servidor tenha acesso a um segundo cofre de chaves se o primário o Key Vault experimenta uma interrupção enquanto o banco de dados está em execução. Use o cmdlet backup-AzKeyVaultKey para recuperar a chave no formato criptografado do cofre de chaves primárias e, em seguida, use o cmdlet Restore-AzKeyVaultKey e especifique um cofre de chaves na segunda região.

![HA de servidor único e sem Dr geográfica](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Como configurar a recuperação de desastres geograficamente com o Azure Key Vault

Para manter a alta disponibilidade de protetores TDE para bancos de dados criptografados, é necessário configurar cofres de chaves do Azure redundantes com base nos grupos de failover de banco de dados SQL existentes ou desejados ou instâncias de replicação geográfica ativas.  Cada servidor com replicação geográfica requer um cofre de chaves separado, que deve ser colocalizado com o servidor na mesma região do Azure. Se um banco de dados primário se tornar inacessível devido a uma interrupção em uma região e um failover for disparado, o banco de dados secundário será capaz de assumir o uso do cofre de chaves secundário.

Para bancos de dados SQL do Azure replicados geograficamente, a seguinte configuração de Azure Key Vault é necessária:

- Um banco de dados primário com um cofre de chaves na região e um banco de dados secundário com um cofre de chaves na região.
- Pelo menos um secundário é necessário, há suporte para até quatro secundários.
- Não há suporte para os secundários de secundários (encadeamento).

A seção a seguir abordará as etapas de instalação e configuração em mais detalhes.

### <a name="azure-key-vault-configuration-steps"></a>Azure Key Vault etapas de configuração

- Instalar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Crie dois cofres de chaves do Azure em duas regiões diferentes usando o [PowerShell para habilitar a propriedade "exclusão reversível"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) nos cofres de chaves (essa opção ainda não está disponível no portal do akv – mas exigida pelo SQL).
- Ambos os cofres de chaves do Azure devem estar localizados nas duas regiões disponíveis na mesma Geografia do Azure para que o backup e a restauração de chaves funcionem.  Se você precisar que os dois cofres de chaves estejam localizados em diferentes áreas geográficas para atender aos requisitos de SQL geo-DR, siga o [processo BYOK](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) que permite que as chaves sejam importadas de um HSM local.
- Crie uma nova chave no primeiro cofre de chaves:  
  - Chave RSA/RSA-HSA 2048
  - Nenhuma data de expiração
  - A chave está habilitada e tem permissões para executar as operações obter, encapsular chave e desencapsular chave
- Faça backup da chave primária e restaure a chave para o segundo cofre de chaves.  Consulte [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) e [Restore-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey).

### <a name="azure-sql-database-configuration-steps"></a>Etapas de configuração do banco de dados SQL do Azure

As etapas de configuração a seguir são diferentes se começarem com uma nova implantação do SQL ou se estiverem trabalhando com uma implantação de SQL geo-DR já existente.  Descrevemos as etapas de configuração para uma nova implantação primeiro e explicamos como atribuir TDE protetores armazenados em Azure Key Vault a uma implantação existente que já tem um link de DR geográfica estabelecido.

**Etapas para uma nova implantação**:

- Crie os dois servidores de banco de dados SQL nas mesmas duas regiões que os cofres de chaves criados anteriormente.
- Selecione o painel TDE do servidor do banco de dados SQL e, para cada servidor do banco de dados SQL:  
  - Selecione o AKV na mesma região
  - Selecione a chave a ser usada como protetor de TDE – cada servidor usará a cópia local do protetor de TDE.
  - Fazer isso no portal criará um [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para o servidor do banco de dados SQL, que é usado para atribuir as permissões do servidor do banco de dados SQL para acessar o cofre de chaves – não exclua essa identidade. O acesso pode ser revogado removendo as permissões em Azure Key Vault em vez do servidor do banco de dados SQL, que é usado para atribuir as permissões do servidor do banco de dados SQL para acessar o cofre de chaves.
- Crie o banco de dados primário.
- Siga as [diretrizes de replicação geográfica ativa](sql-database-geo-replication-overview.md) para concluir o cenário, esta etapa criará o banco de dados secundário.

![Grupos de failover e Dr geográfica](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> É importante garantir que os mesmos protetores de TDE estejam presentes em ambos os cofres de chaves, antes de prosseguir para estabelecer o vínculo geográfico entre os bancos de dados.

**Etapas para um banco de BD SQL existente com implantação de Dr geográfica**:

Como os servidores do banco de dados SQL já existem e os bancos de dados primário e secundário já estão atribuídos, as etapas para configurar Azure Key Vault devem ser executadas na seguinte ordem:

- Comece com o servidor do banco de dados SQL que hospeda o banco de dados secundário:
  - Atribuir o cofre de chaves localizado na mesma região
  - Atribuir o protetor de TDE
- Agora, acesse o servidor do banco de dados SQL que hospeda o banco de dados primário:
  - Selecione o mesmo protetor de TDE usado para o banco de BD secundário

![Grupos de failover e Dr geográfica](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

> [!NOTE]
> Ao atribuir o cofre de chaves ao servidor, é importante começar com o servidor secundário.  Na segunda etapa, atribua o cofre de chaves ao servidor primário e atualize o protetor de TDE, o link de DR geográfica continuará funcionando porque, nesse ponto, o protetor de TDE usado pelo banco de dados replicado está disponível para ambos os servidores.

Antes de habilitar o TDE com chaves gerenciadas pelo cliente no Azure Key Vault para um cenário de DR-RD do banco de dados SQL, é importante criar e manter dois cofres de chaves do Azure com conteúdos idênticos nas mesmas regiões que serão usadas para a replicação geográfica do banco de dados SQL.  O "conteúdo idêntico" significa especificamente que ambos os cofres de chaves devem conter cópias do mesmo (s) protetor (es) TDE, de modo que ambos os servidores tenham acesso aos protetores de TDE usados por todos os bancos de dados.  No futuro, é necessário manter os dois cofres de chaves em sincronia, o que significa que eles devem conter as mesmas cópias de protetores de TDE após a rotação de chaves, manter versões antigas de chaves usadas para backup ou arquivos de log, os protetores de TDE devem manter as mesmas propriedades de chave e a chave os cofres devem manter as mesmas permissões de acesso para SQL.  

Siga as etapas em [visão geral da replicação geográfica ativa](sql-database-geo-replication-overview.md) para testar e disparar um failover, que deve ser feito regularmente para confirmar se as permissões de acesso do SQL para os dois cofres de chaves foram mantidas.

### <a name="backup-and-restore"></a>Cópia de Segurança e Restauro

Depois que um banco de dados é criptografado com TDE usando uma chave de Key Vault, todos os backups gerados também são criptografados com o mesmo protetor de TDE.

Para restaurar um backup criptografado com um protetor TDE do Key Vault, verifique se o material da chave ainda está no cofre original sob o nome da chave original. Quando o protetor de TDE é alterado para um banco de dados, os backups antigos do banco de dados **não são** atualizados para usar o protetor de TDE mais recente. Portanto, recomendamos que você mantenha todas as versões antigas do protetor TDE em Key Vault, para que os backups de banco de dados possam ser restaurados.

Se uma chave que pode ser necessária para restaurar um backup não estiver mais em seu cofre de chaves original, a seguinte mensagem de erro será retornada: "O servidor `<Servername>` de destino não tem acesso a todos os URIs akv \<criados entre carimbo de \<data/hora #1 > e carimbo de data/hora #2 >. Repita a operação após restaurar todos os URIs de AKV. "

Para atenuar isso, execute o cmdlet [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) para retornar a lista de chaves de Key Vault que foram adicionadas ao servidor (a menos que elas tenham sido excluídas por um usuário). Para garantir que todos os backups possam ser restaurados, verifique se o servidor de destino para o backup tem acesso a todas essas chaves.

```powershell
Get-AzSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Para saber mais sobre a recuperação de backup para o banco de dados SQL, consulte [recuperar um banco de dados SQL do Azure](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de backup para SQL Data Warehouse, consulte [recuperar uma SQL data warehouse do Azure](../sql-data-warehouse/backup-and-restore.md).

Considerações adicionais para arquivos de log de backup: Os arquivos de log de backup permanecem criptografados com o criptografador TDE original, mesmo que o protetor de TDE fosse girado e o banco de dados agora está usando um novo protetor TDE.  No momento da restauração, ambas as chaves serão necessárias para restaurar o banco de dados.  Se o arquivo de log estiver usando um protetor de TDE armazenado em Azure Key Vault, essa chave será necessária no momento da restauração, mesmo que o banco de dados tenha sido alterado para usar o TDE gerenciado por serviço enquanto isso.
