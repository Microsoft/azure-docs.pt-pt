---
title: Visão geral da segurança
description: Saiba mais sobre o banco de dados SQL do Azure e a segurança de SQL Server, incluindo as diferenças entre a nuvem e o SQL Server local.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, carlrab, emlisa
ms.date: 05/14/2019
ms.openlocfilehash: 4aa45cc1e8b79186d3ddd5d2b2964addb3929b1a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978578"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma visão geral dos recursos de segurança do banco de dados SQL do Azure

Este artigo descreve as noções básicas de como proteger a camada de dados de um aplicativo usando o Azure SQL Database. A estratégia de segurança descrita segue a abordagem de defesa aprofundada em camadas, conforme mostrado na imagem abaixo, e se move de fora para dentro:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança da rede

O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para aplicativos de nuvem e empresariais. Para ajudar a proteger os dados do cliente, os firewalls impedem o acesso à rede para o servidor de banco até que o acesso seja explicitamente concedido com base no endereço IP ou origem do tráfego de rede virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall de IP

As regras de firewall IP concedem acesso a bancos de dados com base no endereço IP de origem de cada solicitação. Para obter mais informações, consulte [visão geral do banco de dados SQL do Azure e SQL data warehouse regras de firewall](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

Os [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) estendem sua conectividade de rede virtual no backbone do Azure e habilitam o banco de dados SQL do Azure para identificar a sub-rede da rede virtual da qual o tráfego é originado. Para permitir que o tráfego alcance o banco de dados SQL do Azure, use as [marcas de serviço](../virtual-network/security-overview.md) SQL para permitir o tráfego de saída por meio de grupos de segurança de rede

[As regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) permitem que o banco de dados SQL do Azure aceite somente comunicações que são enviadas de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> Controlling access with firewall rules does *not* apply to **a managed instance**. Para obter mais informações sobre a configuração de rede necessária, consulte [conectando-se a uma instância gerenciada](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gestão de acesso

> [!IMPORTANT]
> O gerenciamento de bancos de dados e servidores de banco de dados no Azure é controlado pelas atribuições de função da sua conta de usuário do Portal. For more information on this article, see [Role-based access control in Azure portal](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem alega ser. O banco de dados SQL do Azure dá suporte a dois tipos de autenticação:

- **Autenticação do SQL**:

    A autenticação do banco de dados SQL refere-se à autenticação de um usuário ao conectar-se ao [banco de dados SQL do Azure](sql-database-technical-overview.md) usando o nome e a senha Durante a criação do servidor de banco de dados, um logon de "administrador do servidor" com um nome de usuário e senha devem ser especificados. Usando essas credenciais, um "administrador do servidor" pode se autenticar em qualquer banco de dados nesse servidor de banco de dados como o proprietário do banco de dados. Depois disso, logons e usuários adicionais do SQL podem ser criados pelo administrador do servidor, o que permite que os usuários se conectem usando o nome de usuário e a senha.

- **Autenticação Azure Active Directory**:

    Azure Active Directory autenticação é um mecanismo de conexão com o [banco de dados SQL do Azure](sql-database-technical-overview.md) e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (AD do Azure). Azure AD authentication allows administrators to centrally manage the identities and permissions of database users along with other Microsoft services in one central location. This includes the minimization of password storage and enables centralized password rotation policies.

     A server admin called the **Active Directory administrator** must be created to use Azure AD authentication with SQL Database. For more information, see [Connecting to SQL Database By Using Azure Active Directory Authentication](sql-database-aad-authentication.md). Azure AD authentication supports both managed and federated accounts. The federated accounts support Windows users and groups for a customer domain federated with Azure AD.

    Additional Azure AD authentication options available are [Active Directory Universal Authentication for SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) connections including [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) and [Conditional Access](sql-database-conditional-access.md).

> [!IMPORTANT]
> Managing databases and servers within Azure is controlled by your portal user account's role assignments. For more information on this article, see [Role-based access control in Azure portal](../role-based-access-control/overview.md). Controlling access with firewall rules does *not* apply to **a managed instance**. Please see the following article on [connecting to a managed instance](sql-database-managed-instance-connect-app.md) for more information about the networking configuration needed.

## <a name="authorization"></a>Autorização

Authorization refers to the permissions assigned to a user within an Azure SQL Database, and determines what the user is allowed to do. Permissions are controlled by adding user accounts to [database roles](/sql/relational-databases/security/authentication-access/database-level-roles) and assigning database-level permissions to those roles or by granting the user certain [object-level permissions](/sql/relational-databases/security/permissions-database-engine). For more information, see [Logins and users](sql-database-manage-logins.md)

As a best practice, create custom roles when needed. Add users to the role with the least privileges required to do their job function. Do not assign permissions directly to users. The server admin account is a member of the built-in db_owner role, which has extensive permissions and should only be granted to few users with administrative duties. For Azure SQL Database applications, use the [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) to specify the execution context of the called module or use [Application Roles](/sql/relational-databases/security/authentication-access/application-roles) with limited permissions. This practice ensures that the application that connects to the database has the least privileges needed by the application. Following these best practices also fosters separation of duties.

### <a name="row-level-security"></a>Segurança ao nível da linha

Row-Level Security enables customers to control access to rows in a database table based on the characteristics of the user executing a query (for example, group membership or execution context). Row-Level Security can also be used to implement custom Label-based security concepts. Para obter mais informações, veja [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

SQL Database secures customer data by providing auditing and threat detection capabilities.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>SQL auditing in Azure Monitor logs and Event Hubs

SQL Database auditing tracks database activities and helps to maintain compliance with security standards by recording database events to an audit log in a customer-owned Azure storage account. Auditing allows users to monitor ongoing database activities, as well as analyze and investigate historical activity to identify potential threats or suspected abuse and security violations. For more information, see Get started with [SQL Database Auditing](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada Contra Ameaças

Advanced Threat Protection is analyzing your SQL Server logs to detect unusual behavior and potentially harmful attempts to access or exploit databases. Alerts are created for suspicious activities such as SQL injection, potential data infiltration, and brute force attacks or for anomalies in access patterns to catch privilege escalations and breached credentials use. Alerts are viewed from the  [Azure Security Center](https://azure.microsoft.com/services/security-center/), where the details of the suspicious activities are provided and recommendations for further investigation given along with actions to mitigate the threat. Advanced Threat Protection can be enabled per server for an additional fee. For more information, see [Get started with SQL Database Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Information protection and encryption

### <a name="transport-layer-security-tls-encryption-in-transit"></a>Transport Layer Security TLS (Encryption-in-transit)

SQL Database secures customer data by encrypting data in motion with [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Sql Server enforces encryption (SSL/TLS) at all times for all connections. This ensures all data is encrypted "in transit" between the client and server irrespective of the setting of **Encrypt** or **TrustServerCertificate** in the connection string.

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

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso projetado para proteger dados confidenciais armazenados em colunas de bancos de dados específicas a partir do acesso (por exemplo, números de cartão de crédito, números de identificação nacional, ou data de acordo _com a necessidade de conhecimento_ ). Isso inclui administradores de banco de dados ou outros usuários privilegiados que estão autorizados a acessar o banco de dados para executar tarefas de gerenciamento, mas não têm necessidade comercial de acessar os dados específicos nas colunas criptografadas. Os dados são sempre criptografados, o que significa que os dados criptografados são descriptografados somente para processamento por aplicativos cliente com acesso à chave de criptografia.  A chave de criptografia nunca é exposta ao SQL e pode ser armazenada no [repositório de certificados do Windows](sql-database-always-encrypted.md) ou no [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Mascaramento de dados dinâmicos

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

O mascaramento de dados dinâmicos do Database do SQL limita a exposição de dados confidenciais mascarando-os para usuários sem privilégios. O mascaramento de dados dinâmicos descobre automaticamente os dados potencialmente confidenciais no Azure SQL Database e fornece recomendações acionáveis para mascarar esses campos, com impacto mínimo sobre a camada de aplicativo. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para obter mais informações, consulte Introdução [à máscara de dados dinâmicos do banco de dados SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Gestão da segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidades

A [avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, controlar e ajudar a corrigir possíveis vulnerabilidades de banco de dados com o objetivo de melhorar proativamente a segurança geral do banco de dados. A avaliação de vulnerabilidade (VA) faz parte da oferta do ADS (segurança de dados avançada), que é um pacote unificado para recursos avançados de segurança do SQL. A avaliação de vulnerabilidade pode ser acessada e gerenciada por meio do portal de anúncios do SQL central.

### <a name="data-discovery--classification"></a>Deteção e classificação de dados

A classificação de & de dados de descoberta (atualmente em visualização) fornece recursos avançados incorporados ao banco de dados SQL do Azure para descobrir, classificar, rotular e proteger as informações confidenciais em seus bancos. Descobrir e classificar seus dados confidenciais mais importantes (negócios/financeiros, saúde, dados pessoais etc.) pode desempenhar uma função dinâmica em sua estatura de proteção de informações organizacionais. Pode funcionar como infraestrutura para:

- Vários cenários de segurança, como monitoramento (auditoria) e alertas de acesso anormal a dados confidenciais.
- Controlar o acesso ao e à proteção da segurança do, bancos de dados que contêm os mais altamente confidenciais.
- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentares.

Para obter mais informações, consulte Introdução [à classificação de & de descoberta de dados](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformidade

Além dos recursos e funcionalidades acima que podem ajudar seu aplicativo a atender a vários requisitos de segurança, o banco de dados SQL do Azure também participa de auditorias regulares e foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte a [central de confiabilidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , em que você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para uma discussão sobre a auditoria do banco de dados, consulte [auditoria do banco de dados SQL](sql-database-auditing.md).
- Para obter uma discussão sobre a detecção de ameaças, consulte [detecção de ameaças do banco de dados SQL](sql-database-threat-detection.md).
