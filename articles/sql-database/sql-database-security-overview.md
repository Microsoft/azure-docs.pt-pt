---
title: Descrição Geral da Segurança da Base de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre segurança de base de dados do Azure SQL e SQL Server, incluindo as diferenças entre o SQL Server no local e na cloud.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 121226ad9ca1ea0c29dd192ed69797b37245da46
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213930"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma descrição geral das capacidades de segurança da base de dados do Azure SQL

Este artigo descreve as noções básicas de proteger a camada de dados de um aplicativo usando SQL Database do Azure. A estratégia de segurança descrita segue a abordagem de defesa em profundidade em camadas, conforme mostrado na imagem abaixo e se move de fora em:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança da rede

Base de dados de SQL do Microsoft Azure fornece um serviço de base de dados relacional para aplicações na cloud e enterprise. Para ajudar a proteger os dados dos clientes, as firewalls impedem o acesso de rede para o servidor de base de dados até que o acesso é concedido com base no endereço IP ou a origem de tráfego de rede Virtual do Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall do IP

Regras de firewall do IP concedem acesso a bancos de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, consulte [descrição geral do Azure SQL Database e o SQL Data Warehouse, as regras de firewall](sql-database-firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

[Pontos finais de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) expandir a conectividade de rede virtual através do backbone do Azure e ativar a base de dados do Azure SQL identificar que tráfego provém da sub-rede da rede virtual. Para permitir o tráfego alcance a SQL Database do Azure, utilize o SQL [etiquetas de serviço](../virtual-network/security-overview.md) para permitir tráfego de saída por meio dos grupos de segurança de rede.

[Regras de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) ativar base de dados do Azure SQL aceitar apenas comunicações que são enviadas do selecionado sub-redes numa rede virtual.

> [!NOTE]
> Controlar o acesso com regras de firewall faz *não* aplicam-se ao **uma instância gerida**. Para obter mais informações sobre a configuração de rede necessária, consulte [ligar a uma instância gerida](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gestão de acesso

> [!IMPORTANT]
> Gestão de bases de dados e servidores de base de dados no Azure é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações sobre este artigo, consulte [controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Authentication

A autenticação é o processo de comprovar o usuário é quem eles alegam ser. Base de dados SQL do Azure suporta dois tipos de autenticação:

- **Autenticação do SQL**:

    Autenticação do SQL da base de dados refere-se para a autenticação de um utilizador quando se liga ao [base de dados do Azure SQL](sql-database-technical-overview.md) com o nome de utilizador e palavra-passe. Durante a criação de servidor de base de dados para a base de dados, tem de ser especificado um início de sessão "Administrador do servidor" com um nome de utilizador e palavra-passe. Com estas credenciais, um "administrador do servidor" pode autenticar qualquer base de dados nesse servidor de base de dados como o proprietário da base de dados. Depois disso, os utilizadores e inícios de sessão do SQL adicionais podem ser criados pelo administrador do servidor, que permitem aos utilizadores ligar com o nome de utilizador e palavra-passe.

- **Autenticação do Azure Active Directory**:

    Autenticação do Azure Active Directory é um mecanismo de ligação a [Azure SQL Database](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure Active Directory (Azure AD). Autenticação do Azure AD permite aos administradores gerir centralmente as identidades e permissões de utilizadores de base de dados, juntamente com outros serviços da Microsoft num local central. Isto inclui a minimização de armazenamento de palavra-passe e permite que as políticas de rotação da palavra-passe centralizado.

     Um administrador do servidor chamado os **administrador do Active Directory** deve ser criada para utilizar a autenticação do Azure AD com base de dados SQL. Para obter mais informações, consulte [ligar ao SQL da base de dados ao utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md). Autenticação do Azure AD suporta contas de Federado e não geridos. As contas federadas suportam Windows utilizadores e grupos para um domínio de cliente Federado com o Azure AD.

    Adicionais estão disponíveis as opções de autenticação do Azure AD [Active Directory Universal Authentication para o SQL Server Management Studio](sql-database-ssms-mfa-authentication.md) ligações incluindo [multi-factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) e [ Acesso condicional](sql-database-conditional-access.md).

> [!IMPORTANT]
> Gestão de bases de dados e servidores no Azure é controlada pelas atribuições de função da sua conta de utilizador do portal. Para obter mais informações sobre este artigo, consulte [controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/overview.md). Controlar o acesso com regras de firewall faz *não* aplicam-se ao **uma instância gerida**. Veja o seguinte artigo sobre [ligar a uma instância gerida](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

Autorização refere-se para as permissões atribuídas para um utilizador na base de dados SQL do Azure e determina o que o utilizador tem permissão para fazer. As permissões são controladas através da adição de contas de utilizador ao [funções de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles) que define permissões ao nível da base de dados ou conceder ao usuário determinado [ao nível do objeto permissões](/sql/relational-databases/security/permissions-database-engine). Para obter mais informações, consulte [inícios de sessão e utilizadores](sql-database-manage-logins.md)

Como melhor prática, adicione utilizadores à função com o mínimo de privilégios necessário para fazer a sua função de trabalho. A conta de administrador do servidor é um membro da função db_owner, que tem permissões de amplas e deve ser concedido aos utilizadores com cuidado. Quando utilizar aplicações com a base de dados do Azure SQL, utilize [funções de aplicação](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Isto garante que a aplicação ligar à base de dados tem o mínimo de privilégios necessário para o aplicativo.

### <a name="row-level-security"></a>Segurança ao Nível da Linha

Segurança ao nível da linha permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que executa uma consulta (por exemplo, grupo associação ou contexto de execução). Para obter mais informações, veja [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

  Este método de autenticação utiliza um nome de utilizador e palavra-passe. 

Para uma descrição geral de permissões na base de dados do Azure SQL, consulte [inícios de sessão e utilizadores](sql-database-manage-logins.md#permissions)

## <a name="threat-protection"></a>Proteção contra ameaças

Base de dados SQL protege os dados dos clientes, fornecendo capacidades de deteção de ameaças e auditoria.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria de SQL no Azure Monitor registos e os Hubs de eventos

A auditoria de base de dados SQL controla as atividades de base de dados e ajuda a manter a conformidade com as normas de segurança, registrando eventos de base de dados para uma auditoria iniciar sessão numa conta de armazenamento do Azure pertencentes ao cliente. A auditoria permite aos utilizadores monitorizar atividades de base de dados em curso, bem como analisar e investigar atividade do histórico para identificar potenciais ameaças ou suspeitas abuso e violações de segurança. Para obter mais informações, consulte Introdução à [a auditoria de base de dados SQL](sql-database-auditing.md).  

### <a name="threat-detection"></a>Deteção de ameaças

Tentarem ameaças melhora a deteção de auditoria através da análise de registos de auditoria para comportamentos incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados. Os alertas são criados para atividades suspeitas ou padrões de acesso anómalos, como ataques de injeção de SQL, potenciais infiltração de dados e ataques de palavra-passe de força bruta. Alertas de deteção de ameaças são visualizadas a partir da [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), onde são fornecidos os detalhes das atividades suspeitas e recomendações para ainda mais investigação fornecida junto com ações para mitigar a ameaça. Deteção de ameaças custa us $15/servidor/mês. É gratuito durante os primeiros 60 dias. Para obter mais informações, consulte [introdução à deteção de ameaças da base de dados de SQL](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção de informações e encriptação

### <a name="transport-layer-security-tls-encryption-in-transit"></a>O TLS (encriptação em trânsito) Transport Layer Security

Base de dados SQL protege os dados dos clientes ao encriptar os dados em movimento com [Transport Layer Security](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

> [!IMPORTANT]
> Base de dados SQL do Azure impõe a encriptação (SSL/TLS) em todos os momentos para todas as ligações, que garante que todos os dados são encriptados "em trânsito" entre a base de dados e o cliente. Este processo ocorre independentemente da definição de **Encrypt** ou **TrustServerCertificate** na cadeia de ligação.
>
> Na cadeia de ligação da sua aplicação, certifique-se de que especifica uma ligação encriptada e _não_ confiar no certificado de servidor (controlador de para o ADO.NET é **Encrypt = True** e  **TrustServerCertificate = False**). Isto ajuda a impedir que a aplicação a partir de um homem no meio ataque, forçando o aplicativo para verificar se o servidor e impor encriptação. Se obter a cadeia de ligação do portal do Azure, terá as definições corretas.
>
> Para obter informações sobre TLS e conectividade, consulte [considerações de TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Encriptação de dados transparente (encriptação em repouso)

[Encriptação de dados transparente (TDE) para a base de dados do Azure SQL](transparent-data-encryption-azure-sql.md) adiciona uma camada de segurança para ajudar a proteger dados em repouso contra acesso não autorizado ou offline para cópias de segurança ou de arquivos brutos. Cenários comuns incluem o roubo de datacenter ou disposição sem segurança de hardware ou suporte de dados, tais como unidades de disco e bandas de cópia de segurança. TDE encripta a base de dados usando um algoritmo de encriptação AES, que não requer os desenvolvedores de aplicativos efetuar quaisquer alterações aos aplicativos existentes.

No Azure, todas as recém-criadas bases de dados do SQL são criptografadas por padrão e a chave de encriptação da base de dados está protegida por um certificado de servidor interno.  Manutenção de certificado e a rotação são geridas pelo serviço e não exige uma entrada do usuário. Os clientes que preferem para assumir o controlo das chaves de encriptação podem gerir as chaves na [do Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestão de chaves com o Azure Key Vault

[Traga a sua própria chave](transparent-data-encryption-byok-azure-sql.md) suporte (BYOK) para [encriptação de dados transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) permite aos clientes obter a propriedade de gestão de chaves e o uso de rotação [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md), do Azure com base na cloud sistema de gestão de chaves externas. Se o acesso da base de dados para o Cofre de chaves é revogado, uma base de dados não é possível ser descriptografada e ler na memória. O Azure Key Vault fornece uma plataforma de gestão de chaves central, tira partido de módulos de segurança de rigidamente monitorizados de hardware (HSMs) e permite a separação de funções entre a gestão de chaves e dados para o ajudar a cumprir os requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>São sempre encriptados (encriptação em utilização)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é um recurso criado para proteger dados confidenciais armazenados em colunas de base de dados específicos de acesso (por exemplo, os números de cartão de crédito, números de identificação nacional ou dados num _precisa saber_ base). Isto inclui os administradores de banco de dados ou outros usuários com privilégios que estão autorizados a aceder a base de dados para efetuar tarefas de gestão, mas não empresarial precisam acessar os dados nas colunas encriptadas. Os dados são sempre encriptados, que significa que os dados encriptados são desencriptados apenas para processamento por aplicações cliente com acesso à chave de encriptação.  A chave de encriptação nunca é exposta para o SQL e podem ser armazenados no [Store de certificado do Windows](sql-database-always-encrypted.md) ou no [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md).

### <a name="masking"></a>A máscara

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

#### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

Máscara de dados dinâmicos da base de dados SQL limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. Automaticamente a máscara de dados dinâmicos Deteta dados potencialmente confidenciais na base de dados do Azure SQL e fornece recomendações passíveis de ação para mascarar estes campos, com um impacto mínimo na camada da aplicação. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para obter mais informações, consulte [introdução à máscara de dados dinâmicos da base de dados SQL](sql-database-dynamic-data-masking-get-started.md).

#### <a name="static-data-masking"></a>Máscara de dados estático

[Máscara de dados estáticos](/sql/relational-databases/security/static-data-masking) é uma ferramenta de lado do cliente disponíveis no [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 18.0 preview 5 e superior.  Máscara de dados estáticos permite aos utilizadores criar uma cópia de uma base de dados onde os dados nas colunas selecionadas tem sido mascarados permanentemente. As funções de máscara disponíveis incluem NULL máscara, máscara de valor único, shuffle e shuffle do grupo de máscara e máscara composta de cadeias de caracteres. Com a cópia mascarada da base de dados, as organizações conseguem separar a produção e ambientes de teste por meio do compartilhamento a cópia mascarada. Os dados confidenciais suficientemente estão protegidos e todas as outras características de base de dados tenham sido mantidas. A máscara de bases de dados é recomendada a onde for necessário acesso de terceiros para bases de dados.

## <a name="security-management"></a>Gestão de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidades

[Avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é fácil de configurar o serviço que pode detetar, controlar e ajudar a remediar potenciais vulnerabilidades das bases de dados com o objetivo para proativamente melhorar a segurança geral da base de dados. Avaliação de vulnerabilidades (VA) faz parte da oferta de segurança (ADS) avançada de dados, que é um pacote unificado para funções de segurança avançadas do SQL. Avaliação de vulnerabilidades pode ser acedida e gerenciada através do portal de anúncios de SQL central.

### <a name="data-discovery--classification"></a>Deteção e classificação de dados

Deteção de dados e classificação (atualmente em pré-visualização) fornece capacidades avançadas incorporadas na base de dados SQL do Azure para detetar, classificação, etiquetagem e proteger os dados confidenciais em seus bancos de dados. A detetar e classificar os dados confidenciais extrema (empresas financeiras, cuidados de saúde, os dados pessoais, etc.) podem desempenham um papel essencial na sua organização escritor de proteção de informações. Que possa servir como a infraestrutura para:

- Vários cenários de segurança, como monitorização (auditoria) e os alertas no anómalo acesso a dados confidenciais.
- Controlar o acesso ao e o sistema de proteção de segurança, bases de dados que contêm dados altamente confidenciais.
- Ajudar a atender a requisitos de conformidade a normas e de normas de privacidade de dados.

Para obter mais informações, consulte [começar com dados de deteção e classificação](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformidade

Além das funcionalidades acima e que podem ajudar a sua aplicação a cumprir diversos requisitos de segurança, a base de dados do Azure SQL também participa em auditorias regulares e foi certificada por uma série de normas de conformidade. Para obter mais informações, veja o [Centro de Fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/), onde pode encontrar a lista mais recente de [certificações de conformidade da Base de Dados SQL](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Passos Seguintes

- Para ver um debate da utilização das funcionalidades de controlo de acesso na Base de Dados SQL, veja [Controlar o acesso](sql-database-control-access.md).
- Para uma discussão sobre auditoria de base de dados, consulte [auditoria de base de dados SQL](sql-database-auditing.md).
- Para uma discussão sobre a deteção de ameaças, consulte [deteção de ameaças da base de dados SQL](sql-database-threat-detection.md).
