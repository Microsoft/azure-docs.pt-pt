---
title: Visão geral de segurança
description: Saiba mais sobre o banco de dados SQL do Azure e a segurança de SQL Server, incluindo as diferenças entre a nuvem e o SQL Server local.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: d952229ab327440771db6cc5ac64db2256491179
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823246"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma visão geral dos recursos de segurança do banco de dados SQL do Azure

Este artigo descreve as noções básicas de como proteger a camada de dados de um aplicativo usando o Azure SQL Database. A estratégia de segurança descrita segue a abordagem de defesa aprofundada em camadas, conforme mostrado na imagem abaixo, e se move de fora para dentro:

![SQL-Security-Layer. png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança da rede

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para aplicativos de nuvem e empresariais. Para ajudar a proteger os dados do cliente, os firewalls impedem o acesso à rede para o servidor de banco até que o acesso seja explicitamente concedido com base no endereço IP ou origem do tráfego de rede virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall IP concedem acesso a bancos de dados com base no endereço IP de origem de cada solicitação. Para obter mais informações, consulte [visão geral do banco de dados SQL do Azure e SQL data warehouse regras de firewall](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Os [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) estendem sua conectividade de rede virtual no backbone do Azure e habilitam o banco de dados SQL do Azure para identificar a sub-rede da rede virtual da qual o tráfego é originado. Para permitir que o tráfego alcance o banco de dados SQL do Azure, use as [marcas de serviço](../virtual-network/security-overview.md) SQL para permitir o tráfego de saída por meio de grupos de segurança de rede

[As regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) permitem que o banco de dados SQL do Azure aceite somente comunicações que são enviadas de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> O controle de acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Para obter mais informações sobre a configuração de rede necessária, consulte [conectando-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gestão de acesso

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores de banco de dados no Azure é controlado pelas atribuições de função da sua conta de usuário do Portal. Para obter mais informações sobre este artigo, consulte [controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem alega ser. O banco de dados SQL do Azure dá suporte a dois tipos de autenticação:

- **Autenticação do SQL**:

    A autenticação do banco de dados SQL refere-se à autenticação de um usuário ao conectar-se ao [banco de dados SQL do Azure](sql-database-technical-overview.md) usando o nome e a senha Durante a criação do servidor de banco de dados, um logon de "administrador do servidor" com um nome de usuário e senha devem ser especificados. Usando essas credenciais, um "administrador do servidor" pode se autenticar em qualquer banco de dados nesse servidor de banco de dados como o proprietário do banco de dados. Depois disso, logons e usuários adicionais do SQL podem ser criados pelo administrador do servidor, o que permite que os usuários se conectem usando o nome de usuário e a senha.

- **Autenticação Azure Active Directory**:

    Azure Active Directory autenticação é um mecanismo de conexão com o [banco de dados SQL do Azure](sql-database-technical-overview.md) e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (AD do Azure). A autenticação do Azure AD permite que os administradores gerenciem centralmente as identidades e permissões de usuários de banco de dados junto com outros serviços da Microsoft em um local central. Isso inclui a minimização do armazenamento de senha e permite políticas de rotação de senha centralizadas.

     Um administrador de servidor chamado de **Active Directory administrador** deve ser criado para usar a autenticação do Azure AD com o banco de dados SQL. Para obter mais informações, consulte [conectando-se ao banco de dados SQL usando Azure Active Directory autenticação](sql-database-aad-authentication.md). A autenticação do Azure AD dá suporte a contas gerenciadas e federadas. As contas federadas dão suporte a usuários e grupos do Windows para um domínio de cliente federado com o Azure AD.

    As opções de autenticação adicionais do Azure AD disponíveis são [Active Directory autenticação universal para SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) conexões, incluindo [a autenticação multifator](../active-directory/authentication/concept-mfa-howitworks.md) e o [acesso condicional](sql-database-conditional-access.md).

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores no Azure é controlado pelas atribuições de função da sua conta de usuário do Portal. Para obter mais informações sobre este artigo, consulte [controle de acesso baseado em função no portal do Azure](../role-based-access-control/overview.md). O controle de acesso com regras de firewall *não* se aplica a **uma instância gerenciada**. Consulte o seguinte artigo sobre como [se conectar a uma instância gerenciada](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um usuário em um banco de dados SQL do Azure e determina o que o usuário tem permissão para fazer. As permissões são controladas adicionando contas de usuário a [funções de banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles) e atribuindo permissões de nível de banco de dados a essas funções ou concedendo ao usuário determinadas [permissões de nível de objeto](/sql/relational-databases/security/permissions-database-engine). Para obter mais informações, consulte [logons e usuários](sql-database-manage-logins.md)

Como prática recomendada, crie funções personalizadas quando necessário. Adicione usuários à função com os privilégios mínimos necessários para realizar sua função de trabalho. Não atribua permissões diretamente aos usuários. A conta do administrador do servidor é um membro da função de db_owner interna, que tem permissões extensivas e só deve ser concedida a poucos usuários com tarefas administrativas. Para aplicativos de banco de dados SQL do Azure, use [Executar como](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo chamado ou usar [funções de aplicativo](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Essa prática garante que o aplicativo que se conecta ao banco de dados tenha os privilégios mínimos necessários para o aplicativo. Seguir essas práticas recomendadas também promove a separação de tarefas.

### <a name="row-level-security"></a>Segurança ao Nível da Linha

A segurança em nível de linha permite que os clientes controlem o acesso a linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta (por exemplo, associação de grupo ou contexto de execução). A segurança em nível de linha também pode ser usada para implementar conceitos de segurança personalizados baseados em rótulo. Para obter mais informações, veja [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security).

![Azure-Database-RLS. png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

O banco de dados SQL protege o cliente fornecendo recursos de auditoria e detecção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria do SQL em logs de Azure Monitor e hubs de eventos

A auditoria do banco de dados SQL rastreia as atividades do banco de dados e ajuda a manter a conformidade com os padrões de segurança ao gravar eventos de banco de dados em um log de auditoria em uma conta de armazenamento do Azure do cliente A auditoria permite que os usuários monitorem atividades de banco de dados em andamento, bem como analisem e investiguem atividades históricas para identificar possíveis ameaças ou violações suspeitas de segurança e abuso. Para obter mais informações, consulte Introdução à [auditoria do banco de dados SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada Contra Ameaças

A proteção avançada contra ameaças está analisando seus logs de SQL Server para detectar comportamento incomum e tentativas potencialmente prejudiciais de acessar ou explorar bancos de dados. Os alertas são criados para atividades suspeitas, como injeção de SQL, potencial pós-infiltração de dados e ataques de força bruta ou para anomalias em padrões de acesso para capturar as progressão de privilégio e o uso de credenciais violadas. Os alertas são exibidos na [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), onde os detalhes das atividades suspeitas são fornecidos e recomendações para uma investigação mais detalhada fornecida junto com as ações para atenuar a ameaça. A proteção avançada contra ameaças pode ser habilitada por servidor por uma taxa adicional. Para obter mais informações, consulte Introdução [à proteção avançada contra ameaças do banco de dados SQL](sql-database-threat-detection.md).

![Azure-Database-TD. jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção de informações e criptografia

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS de segurança da camada de transporte (criptografia em trânsito)

O banco de dados SQL protege o cliente criptografando os dados em movimento com a [segurança da camada de transporte](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

O SQL Server impõe a criptografia (SSL/TLS) em todos os momentos para todas as conexões. Isso garante que todos os dados sejam criptografados "em trânsito" entre o cliente e o servidor, independentemente da configuração de **Encrypt** ou **TrustServerCertificate** na cadeia de conexão.

Como prática recomendada, recomende que, na cadeia de conexão do aplicativo, você especifique uma conexão criptografada e _**não**_ confie no certificado do servidor. Isso força seu aplicativo a verificar o certificado do servidor e, portanto, impede que o aplicativo fique vulnerável a ataques de tipo intermediário.

Por exemplo, ao usar o driver ADO.NET, isso é realizado por meio de **encrypt = true** e **TrustServerCertificate = false**. Se você obter a cadeia de conexão do portal do Azure, ela terá as configurações corretas.

> [!IMPORTANT]
> Observe que alguns drivers que não são da Microsoft não podem usar o TLS por padrão ou contar com uma versão mais antiga do TLS (< 1.2) para funcionar. Nesse caso, SQL Server ainda permite que você se conecte ao seu banco de dados. No entanto, é recomendável que você avalie os riscos de segurança de permitir que esses drivers e aplicativos se conectem ao banco de dados SQL, especialmente se você armazená-los. 
>
> Para obter mais informações sobre TLS e conectividade, consulte [Considerações sobre TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Transparent Data Encryption (criptografia em repouso)

O [Transparent Data Encryption (TDE) para o banco de dados SQL do Azure](transparent-data-encryption-azure-sql.md) adiciona uma camada de segurança para ajudar a proteger dados em repouso contra acessos não autorizados ou offline a arquivos brutos ou backups. Os cenários comuns incluem roubo de Datacenter ou descarte inseguro de hardware ou mídia, como unidades de disco e fitas de backup. O TDE criptografa todo o banco de dados usando um algoritmo de criptografia AES, que não exige que os desenvolvedores de aplicativos façam alterações nos aplicativos existentes.

No Azure, todos os bancos de dados SQL recém-criados são criptografados por padrão e a chave de criptografia do banco de dados é protegida por um certificado de servidor interno.  A manutenção e a rotação do certificado são gerenciadas pelo serviço e não requer nenhuma entrada do usuário. Os clientes que preferem assumir o controle das chaves de criptografia podem gerenciar as chaves em [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gerenciamento de chaves com Azure Key Vault

O suporte a [Bring your own Key](transparent-data-encryption-byok-azure-sql.md) (BYOK) para [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) permite que os clientes apropriem-se do gerenciamento e da rotação de chaves usando [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), o sistema de gerenciamento de chaves externas baseado em nuvem do Azure. Se o acesso do banco de dados ao cofre de chaves for revogado, um banco de dados não poderá ser descriptografado e lido na memória. O Azure Key Vault fornece uma plataforma de gerenciamento de chaves central, aproveita os HSMs (módulos de segurança de hardware) monitorados e permite a separação de tarefas entre o gerenciamento de chaves e dados para ajudar a atender aos requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Always Encrypted (criptografia em uso)

![Azure-Database-ae. png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso projetado para proteger dados confidenciais armazenados em colunas de bancos de dados específicas a partir do acesso (por exemplo, números de cartão de crédito, números de identificação nacional, ou data de acordo _com a necessidade de conhecimento_ ). Isso inclui administradores de banco de dados ou outros usuários privilegiados que estão autorizados a acessar o banco de dados para executar tarefas de gerenciamento, mas não têm necessidade comercial de acessar os dados específicos nas colunas criptografadas. Os dados são sempre criptografados, o que significa que os dados criptografados são descriptografados somente para processamento por aplicativos cliente com acesso à chave de criptografia.  A chave de criptografia nunca é exposta ao SQL e pode ser armazenada no [repositório de certificados do Windows](sql-database-always-encrypted.md) ou no [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

![Azure-Database-DDM. png](media/sql-database-security-overview/azure-database-ddm.png)

O mascaramento de dados dinâmicos do Database do SQL limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. O mascaramento de dados dinâmicos descobre automaticamente os dados potencialmente confidenciais no Azure SQL Database e fornece recomendações acionáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para obter mais informações, consulte Introdução [à máscara de dados dinâmicos do banco de dados SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Gestão de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidades

A [avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, controlar e ajudar a corrigir possíveis vulnerabilidades de banco de dados com o objetivo de melhorar proativamente a segurança geral do banco de dados. A avaliação de vulnerabilidade (VA) faz parte da oferta do ADS (segurança de dados avançada), que é um pacote unificado para recursos avançados de segurança do SQL. A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

### <a name="data-discovery--classification"></a>Deteção e classificação de dados

A classificação de & de dados de descoberta (atualmente em visualização) fornece recursos avançados incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular e proteger as informações confidenciais em seus bancos. Descobrir e classificar seus dados confidenciais mais importantes (negócios/financeiros, saúde, dados pessoais etc.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Ele pode servir como uma infraestrutura para:

- Vários cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais.
- Controlar o acesso ao e à proteção da segurança do, bancos de dados que contêm os mais altamente confidenciais.
- Ajudando a atender aos padrões de privacidade de dados e aos requisitos de conformidade regulatória.

Para obter mais informações, consulte Introdução [à classificação de & de descoberta de dados](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformidade

Além dos recursos e funcionalidades acima que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o banco de dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

### <a name="feature-restrictions"></a>Restrições de funcionalidades

As restrições de recursos ajudam a impedir que algumas formas de injeção de SQL vazassem informações sobre o banco de dados, mesmo quando a injeção de SQL é bem-sucedida. Para obter mais informações, consulte [restrições de recursos do banco de dados SQL do Azure](sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Passos seguintes

- Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para uma discussão sobre a auditoria do banco de dados, consulte [auditoria do banco de dados SQL](sql-database-auditing.md).
- Para obter uma discussão sobre a detecção de ameaças, consulte [detecção de ameaças do banco de dados SQL](sql-database-threat-detection.md).
