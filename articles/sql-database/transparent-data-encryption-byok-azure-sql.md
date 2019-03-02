---
title: TDE - chave do Azure integração do cofre ou Bring Your Own Key (BYOK) - base de dados SQL do Azure | Documentos da Microsoft
description: Leve o suporte de Your Own Key (BYOK) para dados de encriptação transparente (TDE) com o Azure Key Vault para a base de dados SQL e o armazém de dados. TDE com BYOK descrição geral, benefícios, como ele funciona, considerações e recomendações.
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
ms.date: 02/20/2019
ms.openlocfilehash: bccf79cea88890d02e2e1bfeb952ca9e754705cb
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217381"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-keys-in-azure-key-vault-bring-your-own-key-support"></a>O Azure SQL encriptação de dados transparente com chaves geridas pelo cliente no Azure Key Vault: Oferecer suporte a sua própria chave

[Encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) com o Azure Key Vault permite a integração para encriptar a chave de encriptação da base de dados (DEK) com uma chave assimétrica gerida pelo cliente chamada Protetor de TDE. Isso também geralmente é chamado para suportar a como Bring Your Own Key (BYOK) para encriptação de dados transparente.  No cenário BYOK, o Protetor de TDE é armazenado numa propriedade de cliente e geridos [do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), sistema de gestão chave externa baseada na nuvem do Azure. Pode ser o Protetor de TDE [gerado](https://docs.microsoft.com/en-us/azure/key-vault/about-keys-secrets-and-certificates) pelo Cofre de chaves ou [transferidos](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) ao Cofre de chaves de um dispositivo HSM no local. O DEK TDE, que é armazenado na página de arranque de uma base de dados, é encriptado e é desencriptado pelo Protetor TDE armazenados no Azure Key Vault, que nunca sai.  Base de dados SQL tem de ser concedidas permissões para o Cofre de chaves pertencentes ao cliente para desencriptar e encriptar o DEK. Se as permissões do servidor SQL lógico para o Cofre de chaves são revogadas, uma base de dados ficarão inacessível e todos os dados são encriptados. Para a base de dados SQL do Azure, o protetor de TDE está definido ao nível do servidor lógico da SQL e é herdado por todas as bases de dados associados a esse servidor. Para [instância gerida do SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), o protetor de TDE está definido ao nível da instância e esta é herdada por todos os *encriptados* bases de dados nessa instância. O termo *servidor* refere-se tanto ao servidor e a instância em todo este documento, a menos que indicado de forma diferente.

Com TDE com a integração do Azure Key Vault, os utilizadores podem controlar as tarefas de gestão de chaves, incluindo rotações de chave, as permissões do Cofre de chaves, as cópias de segurança de chaves e ativar a auditoria/relatórios sobre todos os protetores de TDE com a funcionalidade do Azure Key Vault. Key Vault fornece gestão de chaves central, tira partido de módulos de segurança de rigidamente monitorizados de hardware (HSMs) e permite a separação de funções entre a gestão de chaves e dados para o ajudar a cumprir a conformidade com as políticas de segurança.  

TDE com a integração do Azure Key Vault fornece as seguintes vantagens:

- Maior transparência e um controle granular com a capacidade de Self-gerenciar o protetor de TDE
- Capacidade para revogar permissões em qualquer altura para processar a base de dados inacessível
- Gerenciamento central de protetores de TDE (juntamente com outras chaves e segredos utilizados nos outros serviços do Azure) que hospedam o-los no Cofre de chaves
- Separação de responsabilidades de gerenciamento de chave e os dados dentro da organização, para oferecer suporte a separação de funções
- Maior confiança de seus próprios clientes, uma vez que o Key Vault foi concebido para que a Microsoft não veja ou extraia as chaves de encriptação.
- Suporte para a rotação de chaves

> [!IMPORTANT]
> Para quem utiliza o TDE de gerida pelo serviço de mensagens em fila que gostaria de começar a utilizar o Cofre de chaves, o TDE permanece ativada durante o processo de mudança para um protetor de TDE no Cofre de chaves. Não existe nenhum tempo de inatividade nem nova criptografia de arquivos de banco de dados. Trocar de uma chave gerida pelo serviço para uma chave do Key Vault requer apenas nova criptografia de chave de encriptação de base de dados (DEK), que é uma operação rápida e online.

## <a name="how-does-tde-with-azure-key-vault-integration-support-work"></a>Como o TDE com a integração do Azure Key Vault suporta o trabalho

![Autenticação do servidor para o Cofre de chaves](./media/transparent-data-encryption-byok-azure-sql/tde-byok-server-authentication-flow.PNG)

Quando a TDE primeiro estiver configurado para utilizar um protetor de TDE do Key Vault, o servidor envia o DEK de cada base de dados habilitados para TDE para o Key Vault para um pedido de chave de moldagem. Key Vault devolve a chave de encriptação de banco de dados criptografado, o que é armazenada na base de dados do utilizador.  

> [!IMPORTANT]
> É importante ter em conta que **assim que um Protetor de TDE está armazenado no Azure Key Vault, nunca sai do Azure Key Vault**. O servidor só pode enviar solicitações de operação de chave para o material de chave de protetor de TDE dentro do Cofre de chaves, e **nunca acede ou armazena em cache o protetor de TDE**. O administrador do Cofre de chaves tem o direito de revogar as permissões do Cofre de chaves do servidor a qualquer momento, caso em que todas as ligações ao servidor serão cortados.

## <a name="guidelines-for-configuring-tde-with-azure-key-vault"></a>Diretrizes para configurar o TDE com o Azure Key Vault

### <a name="general-guidelines"></a>Orientações Gerais

- Certifique-se de que o Cofre de chaves do Azure e Azure SQL da base de dados/instância gerida vai estar no mesmo inquilino.  Interações de cofre e o servidor de chaves entre inquilinos **não são suportadas**.
- Decidir quais as subscrições que vão ser utilizados para os recursos necessários – uma nova configuração do TDE com BYOKs necessita de mover o servidor entre subscrições mais tarde. Saiba mais sobre [mover recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- Ao configurar o TDE com o Azure Key Vault, é importante levar em consideração a carga colocada no Cofre de chaves por repetidas moldar/anular moldagem de operações. Por exemplo, uma vez que todas as bases de dados associados a um servidor de base de dados SQL utilizam o mesmo protetor de TDE, irá acionar uma ativação pós-falha desse servidor como muitas operações-chave em relação a quantos do cofre são bases de dados no servidor. Com base em nossa experiência e documentadas [limites de serviço do Cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits), recomendamos que a associação de no máximo, 500 Standard / fins gerais ou 200 Premium / bases de dados críticas para a empresa com um Azure Key Vault numa única subscrição para garantir consistentemente elevada disponibilidade ao acessar o protetor de TDE no cofre.
- Recomendado: Manter uma cópia do protetor de TDE no local.  Isso exige um dispositivo HSM para criar um Protetor de TDE localmente e um sistema de caução de chaves para armazenar uma cópia local do Protetor de TDE.  Saiba mais [como transferir uma chave de um HSM local ao Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

### <a name="guidelines-for-configuring-azure-key-vault"></a>Diretrizes para configurar o Azure Key Vault

- Criar um cofre de chaves com [recuperável](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) ativada para proteger contra eliminação de perda em caso de chave acidental – ou o Cofre de chaves – de dados.  Tem de utilizar [PowerShell para ativar a propriedade de "eliminação de forma recuperável"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) no Cofre de chaves (esta opção não está disponível no AKV Portal ainda – mas exigidas pelo SQL do Azure):  
  - Recursos de eliminado de forma recuperável são retidos durante um determinado período de tempo, 90 dias, a menos que eles são recuperados ou removidos.
  - O **recuperar** e **remover** ações têm suas próprias permissões associadas a uma política de acesso do Cofre de chaves.
- Defina um bloqueio de recursos no Cofre de chaves para controlar quem pode eliminar este recurso crítico e ajudar a impedir a eliminação acidental ou não autorizada.  [Saiba mais sobre bloqueios de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)

- Conceda o acesso ao servidor da base de dados SQL para o Cofre de chaves utilizando a respetiva identidade do Azure Active Directory (Azure AD).  Ao utilizar a IU do Portal, a identidade do Azure AD é criada automaticamente e as permissões de acesso do Cofre de chaves são concedidas para o servidor.  Utilizar o PowerShell para configurar o TDE com o BYOK, a identidade do Azure AD tem de ser criada e a conclusão deve ser verificada. Ver [TDE configurar com o BYOK](transparent-data-encryption-byok-azure-sql-configure.md) e [configurar TDE com o BYOK para a instância gerida](http://aka.ms/sqlmibyoktdepowershell) para obter instruções passo a passo detalhadas ao utilizar o PowerShell.

  > [!NOTE]
  > Se o Azure AD Identity **é acidentalmente eliminado ou permissões do servidor são revogadas** através da política de acesso do Cofre de chaves, o servidor perde o acesso ao Cofre de chaves e bases de dados do TDE encriptado estão inacessíveis dentro de 24 horas.

- Ao utilizar firewalls e redes virtuais com o Azure Key Vault, tem de configurar o seguinte: - permitir que os serviços Microsoft fidedignos contornem esta firewall – escolher Sim

 > [!NOTE]
 > Se o TDE encriptados bases de dados SQL perdem o acesso ao Cofre de chaves, porque eles não consigam ignorar a firewall, as bases de dados estão inacessíveis dentro de 24 horas.

- Ative a auditoria e relatórios sobre todas as chaves de encriptação: O Key Vault proporciona registos que são fáceis de injetar em outras informações de segurança e as ferramentas de gestão (SIEM) de eventos. Operations Management Suite (OMS) [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) é um exemplo de um serviço que já está integrado.
- Para garantir a elevada disponibilidade de bases de dados encriptados, configure cada servidor de base de dados SQL com dois cofres de chaves do Azure que residem em diferentes regiões.

### <a name="guidelines-for-configuring-the-tde-protector-asymmetric-key"></a>Diretrizes para configurar o Protetor de TDE (chave assimétrica)

- Crie a sua chave de encriptação localmente num dispositivo HSM local. Certifique-se de que esta é uma chave assimétrica, do RSA 2048 para que seja storable no Azure Key Vault.
- Caução de chave num sistema de caução de chaves.  
- Importe o ficheiro de chave de encriptação (. pfx,. byok ou .backup) para o Azure Key Vault.

   > [!NOTE]
   > Para fins de teste, é possível criar uma chave com o Azure Key Vault, no entanto, esta chave não pode ser colocadas em caução em, porque a chave privada nunca pode deixar o Cofre de chaves.  Sempre faça backup e a caução de chaves utilizadas para encriptar os dados de produção, como a perda da chave (a eliminação acidental na chave do cofre, validade etc.) resulta em perda de dados permanente.

- Utilizar uma chave sem data de expiração – e não definir uma data de expiração numa chave já em utilização: **assim que a chave expira, as bases de dados encriptados perder o acesso ao respetivo Protetor de TDE e estão inacessíveis dentro de 24 horas**.
- Certifique-se de que a chave está ativada e tem permissões para efetuar *Obtenha*, *moldar chave*, e *anular a moldagem de chave* operações.
- Crie uma cópia de segurança de chave do Azure Key Vault antes de utilizar a chave no Cofre de chaves do Azure pela primeira vez. Saiba mais sobre o [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) comando.
- Criar uma nova cópia de segurança, sempre que são efetuadas quaisquer alterações à chave (por exemplo, adicionar ACLs, adicionar etiquetas, adicionar atributos principais).
- **Manter versões anteriores** da chave no Cofre de chaves quando alternar chaves, portanto, mais antigos backups de banco de dados podem ser restauradas. Quando o Protetor de TDE for alterado para uma base de dados, cópias de segurança antigas da base de dados **não são atualizadas** para utilizar o Protetor de TDE mais recente.  Cada cópia de segurança tem do Protetor de TDE foi criado com durante o restauro. Rotações de chave podem ser efetuadas a seguir as instruções em [girar o Protetor de encriptação de dados usando o PowerShell transparente](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Mantenha todas as chaves utilizadas anteriormente no Azure Key Vault após a alteração novamente para chaves geridas pelo serviço.  Isto garante que cópias de segurança da base de dados podem ser restauradas com os protetores de TDE armazenados no Azure Key Vault.  Protetores de TDE criados com o Azure Key Vault tem de ser mantidos até que todas as cópias de segurança armazenadas foram criadas com chaves geridas pelo serviço.  
- Fazer cópias de segurança recuperáveis destas chaves utilizando [Backup-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1).
- Para remover uma chave potencialmente comprometida durante um incidente de segurança sem o risco de perda de dados, siga os passos indicados em [remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).

## <a name="high-availability-geo-replication-and-backup--restore"></a>Elevada disponibilidade, Georreplicação e cópia de segurança / restauro

### <a name="high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre

Como configurar a elevada disponibilidade com o Azure Key Vault depende da configuração da sua base de dados e o servidor de base de dados SQL e, aqui estão as configurações recomendadas para dois casos distintos.  O primeiro caso é um banco de dados autónomo ou um servidor de base de dados SQL com nenhuma georredundância configurado.  O segundo caso é uma base de dados ou servidor de base de dados SQL configurada com grupos de ativação pós-falha ou georredundância, onde deve ser garantida que cada cópia georredundante tem um Azure Key Vault local dentro do grupo de ativação pós-falha para garantir que o trabalho de geo-as ativações pós-falha.

No primeiro caso, se necessitar de elevada disponibilidade de uma base de dados e o servidor de base de dados SQL com nenhuma georredundância configurada, recomenda-se elevada para configurar o servidor para utilizar dois cofres de chaves diferentes em duas regiões diferentes, com o mesmo material de chave. Isto pode ser conseguido através da criação de um TDE protetor a utilizar o Cofre de chave primária colocalizados na mesma região que o servidor de base de dados SQL e clonagem a chave para um cofre de chaves numa região diferente do Azure, para que o servidor tem acesso a um cofre de chaves segundo deve primário Cofre de chaves sofrer um período de indisponibilidade enquanto a base de dados está em execução. Utilize o cmdlet Backup-AzureKeyVaultKey para obter a chave no formato encriptado do Cofre de chaves primário e, em seguida, utilize o cmdlet Restore-AzureKeyVaultKey e especificar um cofre de chaves na segunda região.

![HA do servidor único e não geo-dr](./media/transparent-data-encryption-byok-azure-sql/SingleServer_HA_Config.PNG)

## <a name="how-to-configure-geo-dr-with-azure-key-vault"></a>Como configurar Geo-DR com o Azure Key Vault

Para manter uma elevada disponibilidade de Protetores de TDE para bases de dados encriptados, é necessário configurar cofres de chaves do Azure redundante, com base nos grupos de ativação pós-falha de base de dados SQL existentes ou pretendidos ou instâncias de georreplicação ativa.  Cada servidor georreplicado necessita de um cofre de chaves separado, que tem de estar localizado conjuntamente com o servidor na mesma região do Azure. Deve uma base de dados primária ficar inacessível devido a uma falha numa única região e é acionada uma ativação pós-falha, a base de dados secundária é capaz de assumir o controlo com o Cofre de chaves secundário.

Bases de dados SQL do Azure de Geo-Replicated, é necessária a seguinte configuração de Cofre de chaves do Azure:

- Uma base de dados primária com um cofre de chaves na região e uma base de dados secundária com um cofre de chaves na região.
- Pelo menos uma secundária não é necessária, são suportados até quatro bases de dados secundárias.
- Bases de dados secundárias de bases de dados secundárias (encadeamento) não são suportadas.

A secção seguinte irá abordar os passos de instalação e configuração mais detalhadamente.

### <a name="azure-key-vault-configuration-steps"></a>Passos de configuração do Cofre de chaves do Azure

- Instalar [PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-5.6.0)
- Criar cofres de chaves do Azure duas em duas regiões diferentes usando [PowerShell para ativar a propriedade de "eliminação de forma recuperável"](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) em cofres de chaves (esta opção não está disponível no AKV Portal ainda –, mas é necessário pelo SQL).
- Ambos os cofres de chaves do Azure têm de estar localizados em duas regiões disponíveis da mesma geo do Azure na ordem de backup e restauração das chaves para trabalhar.  Se precisar de cofres de chaves dois estejam localizadas nas áreas geográficas diferentes para atender aos requisitos de SQL Geo-DR, siga os [BYOK processo](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) que permite que as chaves para ser importados a partir de um HSM no local.
- Crie uma nova chave no Cofre de chaves primeiro:  
  - RSA/RSA-HSA 2048 key
  - Sem data de validade
  - Chave está ativada e tem permissões para executar o get, moldar chave e anular a moldagem de operações de chave
- Criar cópias de segurança a chave primária e restaurar a chave para o Cofre de chaves segundo.  Ver [BackupAzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-5.1.1) e [restauro-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-5.5.0).

### <a name="azure-sql-database-configuration-steps"></a>Passos de configuração de base de dados SQL do Azure

Os seguintes passos de configuração diferem, quer esteja a começar com uma nova implementação de SQL ou se a trabalhar com uma implementação de SQL Geo-DR já existente.  Vamos descrever as etapas de configuração para uma nova implementação primeiro e, depois, explicar como atribuir Protetores de TDE armazenados no Azure Key Vault para uma implementação existente que já tenha uma ligação de Geo-DR estabelecida.

**Passos de uma nova implementação**:

- Crie os dois servidores de base de dados SQL nas mesmas dois regiões como cofres de chaves criados anteriormente.
- Selecione o painel TDE do servidor de base de dados SQL e para cada servidor de base de dados SQL:  
  - Selecione o AKV na mesma região
  - Selecione a chave a utilizar como o Protetor de TDE – cada servidor irá utilizar a cópia local do Protetor de TDE.
  - Fazer isso no Portal do irá criar uma [AppID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para o servidor de base de dados SQL, que é usado para atribuir as permissões de servidor de base de dados SQL para aceder ao Cofre de chaves – não eliminar esta identidade. Acesso pode ser revogado, removendo as permissões no Cofre de chaves do Azure em vez disso, para o servidor de base de dados SQL, que é usado para atribuir as permissões de servidor de base de dados SQL para aceder ao Cofre de chaves.
- Crie base de dados primária.
- Siga os [orientações de georreplicação ativa](sql-database-geo-replication-overview.md) para concluir o cenário, este passo irá criar a base de dados secundário.

![Grupos de ativação pós-falha e geo-dr](./media/transparent-data-encryption-byok-azure-sql/Geo_DR_Config.PNG)

> [!NOTE]
> É importante certificar-se de que os Protetores de TDE mesmo estão presentes em ambos os cofres de chaves, antes de continuar para estabelecer a ligação de georreplicação entre as bases de dados.

**Passos para uma base de dados SQL com a implementação de Geo-DR**:

Como os servidores de base de dados SQL já existir, e as bases de dados primárias e secundárias já estiverem atribuídos, os passos para configurar o Azure Key Vault tem de ser executados na seguinte ordem:

- Começar com o servidor de base de dados SQL que aloja a base de dados secundária:
  - Atribuir o Cofre de chaves, localizado na mesma região
  - Atribuir o Protetor de TDE
- Agora, para o servidor de base de dados SQL que aloja a base de dados primária:
  - Selecione o mesmo Protetor de TDE como utilizado para o banco de dados secundário

![Grupos de ativação pós-falha e geo-dr](./media/transparent-data-encryption-byok-azure-sql/geo_DR_ex_config.PNG)

>[!NOTE]
>Ao atribuir o Cofre de chaves para o servidor, é importante começar com o servidor secundário.  A ligação de Geo-DR do segundo passo atribuir a chave de cofre para o servidor primário e atualizar o Protetor de TDE, continuará a ter trabalhar porque neste momento o Protetor de TDE utilizado pela base de dados replicado está disponível para os dois servidores.

Antes de ativar a TDE com o cliente chaves geridas no Azure Key Vault para um cenário de SQL da base de dados Geo-DR, é importante criar e manter dois cofres de chaves do Azure com conteúdos idênticos nas mesmas regiões que serão utilizados para georreplicação de base de dados SQL.  "Conteúdo idêntico" significa especificamente que ambos os cofres de chaves tem de conter cópias do mesmo Protector(s) TDE para que ambos os servidores têm acesso a utilização de Protetores de TDE, todas as bases de dados.  Daqui em diante, é necessário para manter a ambos os cofres de chaves em sincronia, que significa que tem de conter as mesmo cópias do TDE Protetores depois rotação de chaves, manter as versões antigas das chaves utilizadas para ficheiros de registo ou cópias de segurança, Protetores de TDE tem de manter as mesmas propriedades de chave e a chave cofres tem de manter as mesmas permissões de acesso para o SQL.  

Siga os passos em [descrição geral da georreplicação ativa](sql-database-geo-replication-overview.md) para testar e acionar uma ativação pós-falha, que deve ser feito em intervalos regulares para confirmar as permissões de acesso para o SQL para ambos os cofres de chaves tenha sido mantida.

### <a name="backup-and-restore"></a>Cópia de Segurança e Restauro

Depois de uma base de dados é encriptado com TDE com uma chave do Key Vault, quaisquer cópias de segurança geradas também são encriptadas com o Protetor de TDE mesmo.

Para restaurar uma cópia de segurança encriptada com um Protetor de TDE do Key Vault, certifique-se de que o material de chave é ainda no cofre original sob o nome da chave original. Quando o Protetor de TDE for alterado para uma base de dados, cópias de segurança antigas da base de dados **não são** atualizado para utilizar o Protetor de TDE mais recente. Por conseguinte, recomendamos que mantenha todas as versões antigas do Protetor de TDE no Key Vault, para que as cópias de segurança da base de dados podem ser restauradas.

Se uma chave que pode ser necessários para restaurar uma cópia de segurança não se encontra no seu Cofre de chaves original, é devolvida a seguinte mensagem de erro: "Servidor de destino `<Servername>` não tem acesso a todos os Uris de AKV criada entre < Timestamp n. º 1 > e < Timestamp n. º 2 >. Repita a operação depois de restaurar todos os Uris de AKV."

Para atenuar isso, execute o [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) cmdlet devolver a lista de chaves do Key Vault, que foram adicionados ao servidor (a menos que eles foram eliminados por um utilizador). Para garantir que todas as cópias de segurança podem ser restauradas, certificar-se de que o servidor de destino para a cópia de segurança tem acesso a todas essas chaves.

```powershell
Get-AzureRmSqlServerKeyVaultKey `
  -ServerName <LogicalServerName> `
  -ResourceGroup <SQLDatabaseResourceGroupName>
```

Para saber mais sobre a recuperação de cópia de segurança da base de dados SQL, veja [recuperar uma base de dados SQL do Azure](sql-database-recovery-using-backups.md). Para saber mais sobre a recuperação de cópia de segurança para o SQL Data Warehouse, veja [recuperar um Azure SQL Data Warehouse](../sql-data-warehouse/backup-and-restore.md).

Considerações adicionais para cópia de segurança ficheiros de registo: Backup log ficheiros permanecem encriptados com o encriptador de TDE original, mesmo que o Protetor de TDE foi girado e a base de dados está agora a utilizar um novo Protetor de TDE.  Durante o restauro, ambas as chaves serão necessários para restaurar a base de dados.  Se o ficheiro de registo está a utilizar um Protetor de TDE armazenados no Azure Key Vault, esta chave será necessária durante o restauro, mesmo que a base de dados foi alterado para utilizar o TDE gerida pelo serviço nesse meio tempo.
