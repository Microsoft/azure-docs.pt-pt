---
title: Gerenciar bancos de dados individuais e em pool após a migração – banco de dados SQL do Azure | Microsoft Docs
description: Saiba como gerenciar seu banco de dados após a migração para o banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: 2edd12435643f88a0923abf0927149993d49e424
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567811"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Novo DBA na nuvem – Gerenciando seus bancos de dados individuais e em pool no banco de dados SQL do Azure

A mudança do ambiente tradicional autogerenciado e autocontrolado para um ambiente de PaaS pode parecer um pouco difícil a princípio. Como desenvolvedor de aplicativos ou DBA, você desejaria conhecer os principais recursos da plataforma que o ajudarão a manter seu aplicativo disponível, com bom desempenho, seguro e resiliente sempre. Este artigo tem como objetivo fazer exatamente isso. O artigo organiza de forma sucinta os recursos e fornece algumas diretrizes sobre como usar melhor os principais recursos do banco de dados SQL com bancos de dados individuais e em pool para gerenciar e manter seu aplicativo em execução com eficiência e obter resultados ideais na nuvem. O público típico deste artigo seria aquele que:

- Estão avaliando a migração de seus aplicativos para o banco de dados SQL do Azure – modernizando seus aplicativos.
- Estão no processo de migração de seus aplicativos – cenário de migração em andamento.
- Concluíram recentemente a migração para o BD SQL do Azure – novo DBA na nuvem.

Este artigo discute algumas das principais características do banco de dados SQL do Azure como uma plataforma que você pode aproveitar prontamente ao trabalhar com bancos de dados individuais e bancos de dados em pool em pools elásticos. Eles são os seguintes:

- Monitorar o banco de dados usando o portal do Azure
- Continuidade dos negócios e recuperação de desastres (BCDR)
- Segurança e conformidade
- Monitoramento e manutenção de banco de dados inteligente
- O movimento de dados é suspenso.

> [!NOTE]
> Este artigo aplica-se às seguintes opções de implantação no banco de dados SQL do Azure: bancos de dados individuais e pools elásticos. Ele não se aplica à opção de implantação de instância gerenciada no banco de dados SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados com o portal do Azure

No [portal do Azure](https://portal.azure.com/), você pode monitorar uma utilização de bancos de dados individuais selecionando seu banco de dados e clicando no gráfico de **monitoramento** . É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

- Percentagem de CPU
- Percentagem de DTU
- Percentagem de ES de Dados
- Percentagem de tamanho da Base de Dados

Depois de adicionar essas métricas, você pode continuar a exibi-las no gráfico de **monitoramento** com mais informações na janela **métrica** . As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte os artigos [modelo de compra com base em DTU](sql-database-service-tiers-dtu.md) e modelo de [compra baseado em vCore](sql-database-service-tiers-vcore.md) para obter mais informações sobre as camadas de serviço.  

![Monitorização da camada de serviços do desempenho da base de dados.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Você pode usar isso como um aviso antecipado para descobrir quando pode ser necessário mudar para o próximo tamanho de computação mais alto.

As métricas de desempenho também podem ajudá-lo a determinar se você pode fazer downgrade para um tamanho de computação inferior. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, esteja atento às cargas de trabalho que se espicom ou flutuam antes de tomar a decisão de mudar para um tamanho de computação inferior.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Continuidade dos negócios e recuperação de desastres (BCDR)

A continuidade dos negócios e os recursos de recuperação de desastre permitem que você continue seus negócios, como de costume, em caso de desastre. O desastre pode ser um evento no nível do banco de dados (por exemplo, alguém descartava erroneamente uma tabela crucial) ou um evento de nível do centro de data (catástrofe regional, por exemplo, um tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como fazer criar e gerenciar backups no banco de dados SQL

Você não cria backups no BD SQL do Azure e isso ocorre porque você não precisa. O banco de dados SQL faz backup automaticamente de bancos de dados para você, portanto, você não deve mais se preocupar em agendar, fazer e gerenciar backups. A plataforma faz um backup completo toda semana, backup diferencial a cada poucas horas e um backup de log a cada 5 minutos para garantir que a recuperação de desastres seja eficiente e a perda mínima de dados. O primeiro backup completo ocorre assim que você cria um banco de dados. Esses backups estão disponíveis para você por um determinado período chamado de "período de retenção" e variam de acordo com a camada de serviço escolhida. O banco de dados SQL fornece a capacidade de restaurar para qualquer ponto no tempo dentro desse período de retenção usando [PITR (recuperação pontual)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Escalão do serviço|Período de retenção em dias|
|---|:---:|
|Básica|7|
|Standard|35|
|Premium|35|
|||

Além disso, o recurso de [retenção de longo prazo (EPD)](sql-database-long-term-retention.md) permite que você mantenha os arquivos de backup por um período muito mais longo especificamente, por até 10 anos, e restaure os dados desses backups em qualquer ponto dentro desse período. Além disso, os backups de banco de dados são mantidos no armazenamento replicado geograficamente para garantir a resiliência de catástrofe regional. Você também pode restaurar esses backups em qualquer região do Azure a qualquer momento dentro do período de retenção. Consulte [visão geral](sql-database-business-continuity.md)da continuidade de negócios.

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Como fazer garantir a continuidade dos negócios no caso de um desastre de nível de Datacenter ou de uma catástrofe regional

Como os backups de banco de dados são armazenados no armazenamento replicado geograficamente para garantir que, no caso de um desastre regional, você possa restaurar o backup para outra região do Azure. Isso é chamado de restauração geográfica. O RPO (objetivo de ponto de recuperação) para isso é geralmente < 1 hora e o ERT (tempo estimado de recuperação – alguns minutos a horas).

Para bancos de dados de missão crítica, o BD SQL do Azure oferece a replicação geográfica ativa. O que isso basicamente faz é que ele cria uma cópia secundária replicada geograficamente do seu banco de dados original em outra região. Por exemplo, se seu banco de dados estiver inicialmente hospedado na região do oeste dos EUA do Azure e você quiser resiliência de desastre regional. Você criaria uma réplica geográfica ativa do banco de dados no oeste dos EUA para dizer ao leste dos EUA. Quando o calamidade ocorre no oeste dos EUA, você pode fazer failover para a região leste dos EUA. Configurá-los em um grupo de failover automático é ainda melhor porque isso garante que o banco de dados faça failover automaticamente para o secundário no leste dos EUA em caso de desastre. O RPO para isso é < 5 segundos e o ERT < 30 segundos.

Se um grupo de failover automático não estiver configurado, o aplicativo precisará monitorar ativamente um desastre e iniciar um failover para o secundário. Você pode criar até quatro réplicas geográficas ativas em diferentes regiões do Azure. Ele fica ainda melhor. Você também pode acessar essas réplicas geográficas ativas secundárias para acesso somente leitura. Isso é muito útil para reduzir a latência de um cenário de aplicativo distribuído geograficamente.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Como meu plano de recuperação de desastres é alterado do local para o banco de dados SQL

Em resumo, a configuração tradicional de SQL Server local exigia que você gerencie ativamente sua disponibilidade usando recursos como clustering de failover, espelhamento de banco de dados, replicação de transação ou envio de logs e mantenha e gerencie backups para garantir Continuidade dos negócios. Com o banco de dados SQL, a plataforma gerencia essas informações para você, para que você possa se concentrar no desenvolvimento e na otimização de seu aplicativo de banco de dados e não se preocupe com o gerenciamento de desastres. Você pode ter planos de recuperação de desastre e backup configurados e trabalhando com apenas alguns cliques na portal do Azure (ou alguns comandos usando as APIs do PowerShell).

Para saber mais sobre a recuperação de desastre, consulte: [Recuperação de desastre do banco de BD SQL do Azure 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Segurança e conformidade

O banco de dados SQL usa segurança e privacidade muito seriamente. A segurança no banco de dados SQL está disponível no nível do banco de dados e no nível da plataforma e é melhor entendida quando categorizada em várias camadas. Em cada camada, você obtém controle e fornece segurança ideal para seu aplicativo. As camadas são:

- Autenticação de & de identidade (autenticação do[Windows/SQL e Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorando a atividade ([auditoria](sql-database-auditing.md) e [detecção de ameaças](sql-database-threat-detection.md)).
- Proteção de dados reais ([Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Always Encrypted [ae]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Controlando o acesso a dados sensíveis e privilegiados ([segurança em nível de linha](/sql/relational-databases/security/row-level-security) e [máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking)).

A [central de segurança do Azure](https://azure.microsoft.com/services/security-center/) oferece gerenciamento de segurança centralizado entre cargas de trabalho em execução no Azure, no local e em outras nuvens. Você pode exibir se a proteção essencial do banco de dados SQL, como [auditoria](sql-database-auditing.md) e [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) , está configurada em todos os recursos e criar políticas com base em seus próprios requisitos.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Quais métodos de autenticação de usuário são oferecidos no banco de dados SQL

Há [dois métodos de autenticação](sql-database-control-access.md#authentication) oferecidos no banco de dados SQL:

- [Autenticação Azure Active Directory](sql-database-aad-authentication.md)
- Autenticação do SQL

Não há suporte para a autenticação tradicional do Windows. O Azure Active Directory (AD) é um serviço de gerenciamento de identidade e acesso centralizado. Com isso, você pode fornecer um acesso de logon único (SSO) a todos os funcionários em sua organização. Isso significa que as credenciais são compartilhadas entre todos os serviços do Azure para autenticação mais simples. O AAD dá suporte à [MFA (autenticação multifator)](sql-database-ssms-mfa-authentication.md) e, com [alguns cliques](../active-directory/hybrid/how-to-connect-install-express.md) , o AAD pode ser integrado com o Windows Server Active Directory. A autenticação do SQL funciona exatamente como você estava usando no passado. Você fornece um nome de usuário/senha e pode autenticar usuários em qualquer banco de dados em um determinado servidor de banco de dados SQL. Isso também permite que o banco de dados SQL e o SQL Data Warehouse ofereçam autenticação multifator e contas de usuário convidado em um domínio do Azure AD. Se você já tiver um Active Directory local, poderá federar o diretório com Azure Active Directory para estender seu diretório para o Azure.

|**Se você...**|**Banco de dados SQL/SQL Data Warehouse**|
|---|---|
|Prefira não usar o Azure Active Directory (AD) no Azure|Usar [autenticação do SQL](sql-database-security-overview.md)|
|Usado o AD no SQL Server local|[Federar o AD com o Azure ad](../active-directory/hybrid/whatis-hybrid-identity.md)e usar a autenticação do Azure AD. Com isso, você pode usar o logon único.|
|Necessidade de impor a autenticação multifator (MFA)|Exija MFA como uma política por meio [do acesso condicional da Microsoft](sql-database-conditional-access.md)e use [a autenticação universal do Azure AD com suporte para MFA](sql-database-ssms-mfa-authentication.md).|
|Ter contas de convidado de contas da Microsoft (live.com, outlook.com) ou outros domínios (gmail.com)|Use a [autenticação universal do Azure ad](sql-database-ssms-mfa-authentication.md) no banco de dados SQL/data warehouse, que aproveita a [colaboração B2B do Azure ad](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Está conectado ao Windows usando suas credenciais do Azure AD de um domínio federado|Use a [autenticação integrada do Azure ad](sql-database-aad-authentication-configure.md).|
|Está conectado ao Windows usando credenciais de um domínio não federado com o Azure|Use a [autenticação integrada do Azure ad](sql-database-aad-authentication-configure.md).|
|Ter serviços de camada intermediária que precisam se conectar ao banco de dados SQL ou SQL Data Warehouse|Use a [autenticação integrada do Azure ad](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Como fazer limitar ou controlar o acesso de conectividade ao meu banco de dados

Há várias técnicas à sua disposição que você pode usar para obter uma organização de conectividade ideal para seu aplicativo.

- Regras da Firewall
- Pontos de extremidade de serviço de VNet
- IPs Reservados

#### <a name="firewall"></a>Firewall

Um Firewall impede o acesso ao seu servidor de uma entidade externa, permitindo que somente entidades específicas acessem o servidor do banco de dados SQL. Por padrão, todas as conexões e bancos de dados dentro do servidor do banco de dados SQL não são permitidas, exceto conexões provenientes de outros serviços do Azure. Com uma regra de firewall, você pode abrir o acesso ao seu servidor somente para entidades (por exemplo, um computador de desenvolvedor) que você aprovar, permitindo o endereço IP desse computador por meio do firewall. Ele também permite que você especifique um intervalo de IPs que deseja permitir o acesso ao servidor do banco de dados SQL. Por exemplo, os endereços IP do computador do desenvolvedor em sua organização podem ser adicionados ao mesmo tempo especificando um intervalo na página Configurações do firewall.

Você pode criar regras de firewall no nível do servidor ou no nível do banco de dados. As regras de firewall de IP de nível de servidor podem ser criadas usando o portal do Azure ou com o SSMS. Para saber mais sobre como definir uma regra de firewall no nível de servidor e de banco de dados, consulte: [Criar regras de firewall de IP no banco de dados SQL](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Pontos finais de serviço

Por padrão, o banco de dados SQL está configurado para "permitir que os serviços do Azure acessem o servidor", o que significa que qualquer máquina virtual no Azure pode tentar se conectar ao seu banco de dados. Essas tentativas ainda precisam ser autenticadas. No entanto, se você não quiser que seu banco de dados seja acessível por qualquer IPs do Azure, você pode desabilitar "permitir que os serviços do Azure acessem o servidor". Além disso, você pode configurar [pontos de extremidade de serviço de VNet](sql-database-vnet-service-endpoint-rule-overview.md).

Os pontos de extremidade de serviço (SE) permitem que você exponha seus recursos críticos do Azure somente para sua própria rede virtual privada no Azure. Ao fazer isso, você essencialmente elimina o acesso público aos seus recursos. O tráfego entre sua rede virtual para o Azure permanece na rede de backbone do Azure. Sem SE você obtiver o roteamento de pacotes de túnel forçado. Sua rede virtual força o tráfego de Internet para sua organização e o tráfego de serviço do Azure para passar pela mesma rota. Com os pontos de extremidade de serviço, você pode otimizar isso desde que os pacotes fluam diretamente da sua rede virtual para o serviço na rede de backbone do Azure.

![Pontos finais de serviço de VNet](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>IPs Reservados

Outra opção é provisionar [IPS reservados](../virtual-network/virtual-networks-reserved-public-ip.md) para suas VMs e listar os endereços IP específicos da VM nas configurações de firewall do servidor. Ao atribuir IPs reservados, você economiza o problema de ter que atualizar as regras de firewall com a alteração de endereços IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Em qual porta eu me conecto ao banco de dados SQL

Porta 1433. O banco de dados SQL se comunica por essa porta. Para se conectar de dentro de uma rede corporativa, você precisa adicionar uma regra de saída nas configurações de firewall da sua organização. Como diretriz, Evite expor a porta 1433 fora do limite do Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Como posso monitorar e regular a atividade em meu servidor e banco de dados no banco de dados SQL

#### <a name="sql-database-auditing"></a>Auditoria do banco de dados SQL

Com o banco de dados SQL, você pode ativar a auditoria para rastrear eventos de banco de dados. A [auditoria do banco de dados SQL](sql-database-auditing.md) registra eventos de banco de dados e os grava em um arquivo de log de auditoria em sua conta de armazenamento do Azure. A auditoria é especialmente útil se você pretende obter informações sobre possíveis violações de segurança e política, manter a conformidade regulatória etc. Ele permite que você defina e configure determinadas categorias de eventos que você imagina que precisam de auditoria e com base no que você pode obter relatórios pré-configurados e um painel para obter uma visão geral dos eventos que ocorrem no banco de dados. Você pode aplicar essas políticas de auditoria no nível do banco de dados ou no nível do servidor. Um guia sobre como ativar a auditoria para seu servidor/banco de dados, consulte: [Habilite a auditoria do banco de dados SQL](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Deteção de ameaças

Com a [detecção de ameaças](sql-database-threat-detection.md), você obtém a capacidade de agir sobre as violações de segurança ou política descobertas pela auditoria com muita facilidade. Você não precisa ser um especialista em segurança para lidar com possíveis ameaças ou violações em seu sistema. A detecção de ameaças também tem alguns recursos internos como detecção de injeção de SQL. A injeção de SQL é uma tentativa de alterar ou comprometer os dados e uma maneira bastante comum de atacar um aplicativo de banco de dado em geral. A detecção de ameaças executa vários conjuntos de algoritmos que detectam possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anormais de acesso ao banco de dados (como o acesso de um local incomum ou por uma entidade não familiar). Os responsáveis pela segurança ou outros administradores designados receberão uma notificação por email se uma ameaça for detectada no banco de dados. Cada notificação fornece detalhes sobre a atividade suspeita e recomendações sobre como investigar e atenuar a ameaça. Para saber como ativar a detecção de ameaças, consulte: [Habilitar a detecção de ameaças](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Como fazer proteger meus dados em geral no banco de dados SQL

A criptografia fornece um mecanismo forte para proteger e proteger seus dados confidenciais de intrusos. Seus dados criptografados não são usados para o invasor sem a chave de descriptografia. Portanto, ele adiciona uma camada extra de proteção sobre as camadas de segurança existentes criadas no banco de dados SQL. Há dois aspectos para proteger seus dados no banco de dado SQL:

- Seus dados que estão em repouso nos arquivos de dados e de log
- Seus dados em andamento

No banco de dados SQL, por padrão, os dados em repouso nos arquivos de log e de logs no subsistema de armazenamento são completamente e são criptografados via [Transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Os backups também são criptografados. Com o TDE, não há nenhuma alteração necessária no lado do aplicativo que está acessando esses dados. A criptografia e a descriptografia ocorrem de forma transparente; Portanto, o nome.
Para proteger seus dados confidenciais em andamento e em repouso, o banco de dado SQL fornece um recurso chamado [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). A AE é uma forma de criptografia do lado do cliente que criptografa colunas confidenciais em seu banco de dados (para que elas estejam em texto cifrado para administradores de banco de dados e usuários não autorizados). O servidor recebe os dados criptografados para começar. A chave para Always Encrypted também é armazenada no lado do cliente, de modo que somente os clientes autorizados podem descriptografar as colunas confidenciais. Os administradores de servidor e de dados não podem ver os dados confidenciais, já que as chaves de criptografia são armazenadas no cliente. AE criptografa as colunas confidenciais na tabela de ponta a ponta, de clientes não autorizados ao disco físico. A AE dá suporte a comparações de igualdade hoje, para que os DBAs possam continuar consultando colunas criptografadas como parte de seus comandos SQL. Always Encrypted pode ser usado com uma variedade de opções de repositório de chaves, como [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), repositório de certificados do Windows e módulos de segurança de hardware local.

|**Características**|**Always Encrypted**|**Encriptação de Dados Transparente**|
|---|---|---|
|**Extensão de criptografia**|Ponta a ponta|Dados em repouso|
|**O servidor de banco de dados pode acessar informações confidenciais**|Não|Sim, uma vez que a criptografia é para os dados em repouso|
|**Operações T-SQL permitidas**|Comparação de igualdade|Toda a área de superfície do T-SQL está disponível|
|**Alterações de aplicativo necessárias para usar o recurso**|Mínimo|Muito mínimo|
|**Granularidade da criptografia**|Nível de coluna|Nível do banco de dados|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Como posso limitar o acesso a dados confidenciais em meu banco

Cada aplicativo tem um determinado bit de dados confidenciais no banco de dados que precisa ser protegido contra visibilidade a todos. Certos funcionários dentro da organização precisam exibir esses dados, no entanto, outros não devem ser capazes de exibir esses dados. Um exemplo são os salários dos funcionários. Um gerente precisaria de acesso às informações salariais para seus subordinados diretos, no entanto, os membros individuais da equipe não deveriam ter acesso às informações salariais de seus colegas. Outro cenário são os desenvolvedores de dados que podem estar interagindo com dados confidenciais durante estágios de desenvolvimento ou testes, por exemplo, CPFs de clientes. Essas informações novamente não precisam ser expostas ao desenvolvedor. Nesses casos, os dados confidenciais precisam ser mascarados ou não ser expostos. O banco de dados SQL oferece duas abordagens para impedir que usuários não autorizados possam exibir dados confidenciais:

[Máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md) é um recurso de mascaramento de dados que permite limitar a exposição de dados confidenciais mascarando-os para usuários sem privilégios na camada de aplicativo. Você define uma regra de mascaramento que pode criar um padrão de mascaramento (por exemplo, para mostrar apenas os últimos quatro dígitos de um SSN da ID nacional: XXX-XX-0000 e marca a maior parte dele como xs) e identifica quais usuários devem ser excluídos da regra de mascaramento. O mascaramento ocorre instantaneamente e há várias funções de mascaramento disponíveis para várias categorias de dados. O mascaramento de dados dinâmicos permite que você detecte automaticamente os dados confidenciais em seu banco de dado e aplique o mascaramento a ele.

A [segurança em nível de linha](/sql/relational-databases/security/row-level-security) permite que você controle o acesso no nível de linha. Ou seja, determinadas linhas em uma tabela de banco de dados com base no usuário que executa a consulta (Associação de grupo ou contexto de execução) ficam ocultas. A restrição de acesso é feita na camada de banco de dados em vez de em uma camada de aplicativo, para simplificar a lógica do aplicativo. Você começa criando um predicado de filtro, filtrando as linhas que não são expostas e a política de segurança em seguida definindo quem tem acesso a essas linhas. Por fim, o usuário final executa sua consulta e, dependendo do privilégio do usuário, eles visualizam essas linhas restritas ou não conseguem vê-las.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Como fazer gerenciar chaves de criptografia na nuvem

Há opções de gerenciamento de chaves para Always Encrypted (criptografia do lado do cliente) e Transparent Data Encryption (criptografia em repouso). É recomendável que você gire regularmente as chaves de criptografia. A frequência de rotação deve alinhar-se com os regulamentos internos da organização e os requisitos de conformidade.

#### <a name="transparent-data-encryption-tde"></a>Encriptação de Dados Transparente (TDE)

Há uma hierarquia de duas chaves em TDE – os dados em cada banco de dado de usuário são criptografados por uma DEK (chave de criptografia de banco de dados exclusiva) do banco de dados do AES-256 assimétrico, que, por sua vez, é criptografada por uma chave mestra 2048 assimétrica exclusiva do servidor. A chave mestra pode ser gerenciada:

- Automaticamente pelo banco de dados de plataforma SQL.
- Ou por você usando [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) como o repositório de chaves.

Por padrão, a chave mestra para Transparent Data Encryption é gerenciada pelo serviço do banco de dados SQL para sua conveniência. Se sua organização quiser ter controle sobre a chave mestra, há uma opção para usar Azure Key Vault] (SQL-Database-Always-Encrypted-Azure-Key-vault.md) como o repositório de chaves. Usando Azure Key Vault, sua organização assume o controle sobre o provisionamento de chave, rotação e controles de permissão. [A rotação ou a alternância do tipo de uma chave mestra TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápida, pois ela apenas CRIPTOGRAFA a DEK novamente. Para organizações com separação de funções entre segurança e gerenciamento de dados, um administrador de segurança pode provisionar o material da chave para a chave mestra TDE em Azure Key Vault e fornecer um identificador de chave Azure Key Vault ao administrador de banco de dados a ser usado para criptografia em repouso em um servidor. A Key Vault é projetada de modo que a Microsoft não veja nem Extraia nenhuma chave de criptografia. Você também obtém um gerenciamento centralizado de chaves para sua organização.

#### <a name="always-encrypted"></a>Sempre Encriptado

Há também uma [hierarquia de duas chaves](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) no Always Encrypted-uma coluna de dados confidenciais é criptografada por uma CEK (chave de criptografia de coluna) 256 AES, que, por sua vez, é criptografada por uma chave mestra de coluna (CMK). Os drivers de cliente fornecidos para Always Encrypted não têm limitações quanto ao comprimento de CMKs. O valor criptografado do CEK é armazenado no banco de dados e o CMK é armazenado em um repositório de chaves confiável, como o repositório de certificados do Windows, Azure Key Vault ou um módulo de segurança de hardware.

- O [CEK e o CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) podem ser girados.
- A rotação CEK é um tamanho de operação de dados e pode ser de uso intensivo de tempo dependendo do tamanho das tabelas que contêm as colunas criptografadas. Portanto, é prudente planejar as rotações de CEK de acordo.
- No entanto, a rotação CMK não interfere no desempenho do banco de dados e pode ser feita com funções separadas.

O diagrama a seguir mostra as opções de repositório de chaves para as chaves mestras de coluna no Always Encrypted

![Provedores de armazenamento do CMK Always Encrypted](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Como posso otimizar e proteger o tráfego entre minha organização e o banco de dados SQL

O tráfego de rede entre sua organização e o banco de dados SQL geralmente seria roteado pela rede pública. No entanto, se você optar por otimizar esse caminho e torná-lo mais seguro, poderá examinar a rota expressa. A rota expressa permite, essencialmente, estender sua rede corporativa para a plataforma do Azure por meio de uma conexão privada. Ao fazer isso, você não passa pela Internet pública. Você também obtém maior segurança, confiabilidade e otimização de roteamento que se traduz em menores latências de rede e velocidades muito mais rápidas do que você normalmente passará pela Internet pública. Se você estiver planejando transferir uma parte significativa dos dados entre sua organização e o Azure, o uso do Express Route pode gerar benefícios de custo. Você pode escolher entre três modelos de conectividade diferentes para a conexão de sua organização com o Azure:

- [Colocalização de Cloud Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Qualquer para qualquer](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

O Express Route também permite que você propulso até o dobro do limite de largura de banda que você adquire sem custo adicional. Também é possível configurar a conectividade entre regiões usando a rota expressa. Para ver uma lista de provedores de conectividade ER, consulte: [Parceiros de rota expressa e locais de emparelhamento](../expressroute/expressroute-locations.md). Os artigos a seguir descrevem a rota expressa em mais detalhes:

- [Introdução ao Express Route](../expressroute/expressroute-introduction.md)
- [Pré-requisitos](../expressroute/expressroute-prerequisites.md)
- [Fluxos de trabalho](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>O banco de dados SQL está em conformidade com quaisquer requisitos regulatórios e como isso ajuda na conformidade da minha organização

O banco de dados SQL é compatível com uma variedade de conformidades regulatórias. Para exibir o conjunto mais recente de conformidades que foram atendidas pelo banco de dados SQL, visite a [central de confiabilidade da Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e faça uma busca detalhada das conformidades que são importantes para sua organização para ver se o banco de dados SQL está incluído nos serviços compatíveis do Azure. É importante observar que, embora o banco de dados SQL possa ser certificado como um serviço em conformidade, ele ajuda na conformidade do serviço de sua organização, mas não o garante automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitoramento e manutenção de banco de dados inteligente após a migração

Depois de migrar seu banco de dados para o banco de dados SQL, você vai querer monitorar seu banco de dados (por exemplo, verificar como a utilização de recursos é como ou as verificações de DBCC) e executar a manutenção regular (por exemplo, recriar ou reorganizar índices, estatísticas, etc.). Felizmente, o banco de dados SQL é inteligente no sentido de que ele usa as tendências históricas e as métricas e estatísticas registradas para ajudá-lo proativamente a monitorar e manter seu banco de dados, para que seu aplicativo seja executado sempre de forma ideal. Em alguns casos, o BD SQL do Azure pode executar tarefas de manutenção automaticamente, dependendo de sua configuração. Há três facetas para monitorar seu banco de dados no banco de dados SQL:

- Monitoramento e otimização do desempenho.
- Otimização de segurança.
- Otimização de custo.

### <a name="performance-monitoring-and-optimization"></a>Monitoramento e otimização do desempenho

Com as informações de desempenho de consulta, você pode obter recomendações personalizadas para a carga de trabalho do banco de dados para que seus aplicativos possam continuar sendo executados em um nível ideal – sempre. Você também pode configurá-lo para que essas recomendações sejam aplicadas automaticamente e você não precise se preocupar em executar tarefas de manutenção. Com Index Advisor, você pode implementar automaticamente recomendações de índice com base em sua carga de trabalho – isso é chamado de ajuste automático. As recomendações evoluem conforme a carga de trabalho do aplicativo é alterada para fornecer as sugestões mais relevantes. Você também pode obter a opção de revisar manualmente essas recomendações e aplicá-las a seu critério.  

### <a name="security-optimization"></a>Otimização de segurança

O banco de dados SQL fornece recomendações de segurança acionáveis para ajudá-lo a proteger sua detecção de vírus e ameaças para identificar e investigar atividades suspeitas de banco de dados que podem representar um thread potencial para o banco de dados. A [avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é um serviço de verificação e relatório de banco de dados que permite que você monitore o estado de segurança de seus bancos em escala e identifique riscos de segurança e descompasso de uma linha de base de segurança definida por você. Após cada verificação, uma lista personalizada de etapas acionáveis e scripts de correção é fornecida, bem como um relatório de avaliação que pode ser usado para ajudar a atender aos requisitos de conformidade.

Com a central de segurança do Azure, você identifica as recomendações de segurança no quadro e as aplica com um único clique.

### <a name="cost-optimization"></a>Otimização de custos

A plataforma SQL do Azure analisa o histórico de utilização em todos os bancos de dados em um servidor para avaliar e recomendar opções de otimização de custos para você. Essa análise geralmente leva um fortnight para analisar e criar recomendações acionáveis. O pool elástico é uma dessas opções. A recomendação é exibida no portal como uma faixa:

![recomendações de pool elástico](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Você também pode exibir essa análise na seção "Supervisor":

![recomendações do pool elástico-supervisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Como fazer monitorar o desempenho e a utilização de recursos no banco de dados SQL

No banco de dados SQL, você pode aproveitar as informações inteligentes da plataforma para monitorar o desempenho e ajustar de acordo. Você pode monitorar o desempenho e a utilização de recursos no banco de dados SQL usando os seguintes métodos:

#### <a name="azure-portal"></a>Portal do Azure

O portal do Azure mostra a utilização de um banco de dados selecionando o banco de dados e clicando no gráfico no painel Visão geral. Você pode modificar o gráfico para mostrar várias métricas, incluindo percentual de CPU, porcentagem de DTU, porcentagem de e/s de dados, porcentagem de sessões e porcentagem de tamanho do banco de dado.

![Gráfico de monitoramento](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Monitorando chart2](./media/sql-database-manage-after-migration/chart.png)

Nesse gráfico, você também pode configurar alertas por recurso. Esses alertas permitem que você responda às condições de recurso com um email, grave em um ponto de extremidade HTTPS/HTTP ou execute uma ação. Para obter mais informações, consulte [criar alertas](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Exibições de gerenciamento dinâmico

Você pode consultar a exibição de gerenciamento dinâmico [Sys. dm _db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) para retornar o histórico de estatísticas de consumo de recursos da última hora e a exibição do catálogo do sistema [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para retornar o histórico dos últimos 14 dias.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Análise de desempenho de consultas](sql-database-query-performance.md) permite que você veja um histórico das principais consultas de consumo de recursos e consultas de longa execução para um banco de dados específico. Você pode identificar rapidamente as principais consultas por utilização de recursos, duração e frequência de execução. Você pode controlar as consultas e detectar a regressão. Este recurso requer que [repositório de consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) esteja habilitado e ativo para o banco de dados.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Análise de SQL do Azure (versão prévia) nos logs do Azure Monitor

[Os logs de Azure monitor](../azure-monitor/insights/azure-sql.md) permitem coletar e visualizar as principais métricas de desempenho de SQL Azure do Azure, dando suporte a até 150.000 bancos de dados sql e 5.000 pools elásticos do SQL por espaço de trabalho. Você pode usá-lo para monitorar e receber notificações. Você pode monitorar as métricas do banco de dados SQL e do pool elástico em várias assinaturas e pools elásticos do Azure e pode ser usado para identificar problemas em cada camada de uma pilha de aplicativos.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou percebendo problemas de desempenho: Como a minha metodologia de solução de problemas do banco de dados SQL difere da SQL Server

Uma parte principal das técnicas de solução de problemas que você usaria para diagnosticar problemas de desempenho de consulta e de banco de dados permanece a mesma. Depois que o mesmo mecanismo de SQL Server alimenta a nuvem. No entanto, a plataforma do banco de BD SQL do Azure foi criada em ' inteligência '. Ele pode ajudar você a solucionar problemas e diagnosticar problemas de desempenho ainda mais facilmente. Ele também pode executar algumas dessas ações corretivas em seu nome e, em alguns casos, corrigi-las proativamente-automaticamente.

Sua abordagem para solucionar problemas de desempenho pode se beneficiar significativamente com o uso de recursos inteligentes como o [análise de desempenho de consultas (QPI)](sql-database-query-performance.md) e [Assistente do banco de dados](sql-database-advisor.md) em conjunto e, portanto, a diferença na metodologia é diferente Isso respeita – você não precisa mais fazer o trabalho manual de desconsiderar os detalhes essenciais que podem ajudá-lo a solucionar o problema em questão. A plataforma faz o trabalho árduo para você. Um exemplo disso é QPI. Com o QPI, você pode fazer drill-through até o nível de consulta e examinar as tendências históricas e descobrir quando exatamente a consulta foi regressiva. O Assistente do Banco de Dados fornece recomendações sobre coisas que podem ajudá-lo a melhorar seu desempenho geral em geral, como os índices ausentes, remover índices, parametrizar suas consultas etc.

Com a solução de problemas de desempenho, é importante identificar se ele é apenas o aplicativo ou o banco de dados que o está fazendo backup, o que está afetando o desempenho do aplicativo. Geralmente, o problema de desempenho está na camada de aplicativo. Pode ser a arquitetura ou o padrão de acesso a dados. Por exemplo, considere que você tem um aplicativo informativo que é sensível à latência de rede. Nesse caso, seu aplicativo sofre devido à ocorrência de muitas solicitações curtas ("informativo") entre o aplicativo e o servidor e em uma rede congestionada, essas viagens são somadas rapidamente. Para melhorar o desempenho nesse caso, você pode usar [consultas em lote](sql-database-performance-guidance.md#batch-queries). O uso de lotes o ajuda imensamente porque agora suas solicitações são processadas em um lote; Portanto, ajudando você a reduzir a latência de ida e volta e melhorar o desempenho do aplicativo.

Além disso, se você observar uma degradação no desempenho geral do seu banco de dados, poderá monitorar as exibições de gerenciamento dinâmico [Sys. dm _db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [Sys. resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para entender o consumo de CPU, e/s e memória. Seu desempenho pode ser afetado porque o banco de dados está com falta de recursos. Pode ser necessário alterar o tamanho da computação e/ou a camada de serviço com base nas demandas de carga de trabalho crescentes e reduzidos.

Para obter um conjunto abrangente de recomendações para ajuste de problemas de desempenho, consulte: [Ajuste seu banco de dados](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Como fazer garantir que estou usando a camada de serviço e o tamanho de computação apropriados

O banco de dados SQL oferece várias camadas de serviço básicas, Standard e Premium. Cada camada de serviço Obtém um desempenho previsível garantido associado a essa camada de serviço. Dependendo de sua carga de trabalho, você pode ter intermitências de atividade em que a utilização de recursos pode atingir o teto do tamanho de computação atual em que você está. Nesses casos, é útil primeiro começar avaliando se qualquer ajuste pode ajudar (por exemplo, adicionar ou alterar um índice, etc.). Se você ainda encontrar problemas de limite, considere mudar para uma camada de serviço ou tamanho de computação superior.

|**Camada de serviços**|**Cenários de caso de uso comuns**|
|---|---|
|**Básica**|Aplicativos com muitos usuários e um banco de dados que não têm altos requisitos de simultaneidade, escala e desempenho. |
|**Standard**|Aplicativos com requisitos consideráveis de simultaneidade, escala e desempenho, juntamente com demandas de e/s de baixo e médio porte. |
|**Premium**|Aplicativos com muitos usuários simultâneos, alta CPU/memória e altas demandas de e/s. Aplicativos sensíveis à alta simultaneidade, alta taxa de transferência e latência podem aproveitar o nível Premium. |
|||

Para verificar se você está com o tamanho de computação certo, você pode monitorar a consulta e o consumo de recursos do banco de dados por meio de uma das maneiras mencionadas acima em "Como fazer monitorar o desempenho e a utilização de recursos no banco de dados SQL". Caso você ache que seus bancos de dados/consultas estão em execução consistentemente em CPU/memória, etc., você pode considerar a possibilidade de escalar verticalmente para um tamanho de computação mais alto. Da mesma forma, se você observar que mesmo durante o horário de pico, você não parece usar os recursos como muito; Considere reduzir verticalmente do tamanho da computação atual.

Se você tiver um padrão de aplicativo SaaS ou um cenário de consolidação de banco de dados, considere usar um pool elástico para otimização de custos. O pool elástico é uma ótima maneira de obter consolidação de banco de dados e otimização de custos. Para ler mais sobre como gerenciar vários bancos de dados usando o pool elástico, confira: [Gerenciar pools e bancos de dados](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Com que frequência preciso executar verificações de integridade do banco de dados para meu banco de dados

O banco de dados SQL usa algumas técnicas inteligentes que permitem que ele manipule determinadas classes de corrupção de dado automaticamente e sem qualquer perda de dados. Essas técnicas são integradas ao serviço e são aproveitadas pelo serviço quando surgem a necessidade. Regularmente, os backups de banco de dados em todo o serviço são testados, restaurando-os e executando DBCC CHECKDB nele. Se houver problemas, o banco de dados SQL os endereça proativamente. O [reparo automático de página](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) é utilizado para corrigir páginas que estão corrompidas ou têm problemas de integridade de dados. As páginas de banco de dados sempre são verificadas com a configuração de soma de verificação padrão que verifica a integridade da página. O banco de dados SQL monitora e revisa de forma proativa a integridade do dado do seu banco e, se surgirem problemas, os resolve com a prioridade mais alta. Além disso, você pode optar por, opcionalmente, executar suas próprias verificações de integridade em seu.  Para obter mais informações, consulte [integridade de dados no banco de dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Movimentação de dados após a migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Como fazer exportar e importar dados como arquivos BACPAC do banco de dados SQL

- **Exportar**: Você pode exportar seu banco de dados SQL do Azure como um arquivo BACPAC do portal do Azure

   ![exportação de banco de dados](./media/sql-database-export/database-export1.png)

- **Importar**: Você também pode importar dados como um arquivo BACPAC para o banco de dado usando o portal do Azure.

   ![importação de banco de dados](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Como fazer sincronizar os dados entre o SQL Database e o SQL Server

Você tem várias maneiras de conseguir isso:

- **[Sincronização de dados](sql-database-sync-data.md)** – esse recurso ajuda a sincronizar dados bidirecionalmente entre vários bancos de dados de SQL Server locais e o banco de dados SQL. Para sincronizar com bancos de dados do SQL Server locais, você precisa instalar e configurar o agente de sincronização em um computador local e abrir a porta TCP de saída 1433.
- **[Replicação de transação](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – com replicação de transação, você pode sincronizar seus dados do local para o Azure SQL DB com o local sendo o Publicador e o banco de dados SQL do Azure sendo o Assinante. Por enquanto, apenas essa configuração tem suporte. Para obter mais informações sobre como migrar seus dados do local para o SQL do Azure com tempo de inatividade mínimo, consulte: [Usar replicação de transação](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [banco de dados SQL](sql-database-technical-overview.md).
