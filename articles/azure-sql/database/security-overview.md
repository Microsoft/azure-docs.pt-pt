---
title: Visão geral da segurança
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre segurança na Base de Dados Azure SQL e na Azure SQL Managed Instance, incluindo como difere do SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto, emlisa
ms.date: 10/26/2020
ms.openlocfilehash: bb9e17a4befcdcf1a322734c6cc5d75d653f23e6
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676141"
---
# <a name="an-overview-of-azure-sql-database-and-sql-managed-instance-security-capabilities"></a>Uma visão geral da Base de Dados Azure SQL e capacidades de segurança de instância gerida sql
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo descreve os fundamentos da garantia do nível de dados de uma aplicação utilizando a Base de [Dados Azure SQL](sql-database-paas-overview.md) e [a Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md). A estratégia de segurança descrita segue a abordagem em camadas de defesa em profundidade, como mostrado na imagem abaixo, e move-se de fora para:

![Diagrama de defesa em camadas em profundidade. Os dados dos clientes estão envoltos em camadas de segurança da rede, gestão de acessos e proteções contra ameaças e informações.](./media/security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança da rede

A Base de Dados SQL do Microsoft Azure e a SQL Managed Instance fornecem um serviço de base de dados relacional para aplicações em nuvem e empresa. Para ajudar a proteger os dados dos clientes, as firewalls impedem o acesso à rede ao servidor até que o acesso seja explicitamente concedido com base no endereço IP ou na origem do tráfego da rede Virtual Azure.

### <a name="ip-firewall-rules"></a>Regras de firewall IP

As regras de firewall IP concedem acesso a bases de dados com base no endereço IP originário de cada pedido. Para mais informações, consulte [as regras gerais da Base de Dados Azure SQL e das regras de firewall Azure Synapse Analytics](firewall-configure.md).

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

[Os pontos finais do serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md) alargam a conectividade da rede virtual sobre a espinha dorsal do Azure e permitem ao Azure SQL Database identificar a sub-rede de rede virtual de que o tráfego é originário. Para permitir que o tráfego chegue à Base de Dados Azure SQL, utilize as tags de serviço SQL para permitir o tráfego de saída através de [Grupos](../../virtual-network/security-overview.md) de Segurança de Rede.

[As regras de rede virtual](vnet-service-endpoint-rule-overview.md) permitem ao Azure SQL Database apenas aceitar comunicações que são enviadas a partir de sub-redes selecionadas dentro de uma rede virtual.

> [!NOTE]
> Controlar o acesso com regras de firewall *não* se aplica a **SQL Managed Instance** . Para obter mais informações sobre a configuração de rede necessária, consulte [Ligar a uma instância gerida](../managed-instance/connect-application-instance.md)

## <a name="access-management"></a>Gestão de acesso

> [!IMPORTANT]
> A gestão de bases de dados e servidores dentro do Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para obter mais informações sobre este artigo, consulte [o controlo de acesso baseado em funções no portal Azure](../../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o utilizador é quem diz ser. Azure SQL Database e SQL Managed Instance suportam dois tipos de autenticação:

- **Autenticação SQL:**

    A autenticação SQL refere-se à autenticação de um utilizador ao ligar-se à Base de Dados Azure SQL ou à Instância Gerida Azure SQL utilizando o nome de utilizador e a palavra-passe. Um **login de administração do servidor** com um nome de utilizador e senha deve ser especificado quando o servidor está a ser criado. Utilizando estas credenciais, um **administrador de servidor** pode autenticar em qualquer base de dados desse servidor ou instância como o proprietário da base de dados. Depois disso, podem ser criados logins e utilizadores SQL adicionais pela administração do servidor, que permitem aos utilizadores conectar-se usando o nome de utilizador e a palavra-passe.

- **Autenticação do Diretório Ativo Azure:**

    A autenticação do Azure Ative Directory é um mecanismo de ligação à Base de [Dados Azure SQL](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades no Azure Ative Directory (Azure AD). A autenticação AZure AD permite aos administradores gerir centralmente as identidades e permissões dos utilizadores da base de dados, juntamente com outros serviços Azure numa localização central. Isto inclui a minimização do armazenamento de palavras-passe e permite políticas centralizadas de rotação de palavras-passe.

     Um administrador de servidor chamado **administrador ative directory** deve ser criado para usar a autenticação AZure AD com base de dados SQL. Para obter mais informações, consulte [a Ligação à Base de Dados SQL utilizando a autenticação do Diretório Ativo Azure](authentication-aad-overview.md). A autenticação AZURE AD suporta contas geridas e federadas. As contas federadas suportam utilizadores e grupos windows para um domínio de clientes federados com Azure AD.

    As opções adicionais de autenticação Azure AD disponíveis são [autenticação universal do Diretório Ativo para](authentication-mfa-ssms-overview.md) ligações sql Server Management Studio, incluindo [autenticação multi-factor](../../active-directory/authentication/concept-mfa-howitworks.md) e [acesso condicional](conditional-access-configure.md).

> [!IMPORTANT]
> A gestão de bases de dados e servidores dentro do Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para obter mais informações sobre este artigo, consulte [o controlo de acesso baseado em funções no portal Azure](../../role-based-access-control/overview.md). Controlar o acesso com regras de firewall *não* se aplica a **SQL Managed Instance** . Consulte o seguinte artigo sobre [a ligação a uma instância gerida](../managed-instance/connect-application-instance.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um utilizador dentro de uma base de dados na Base de Dados Azure SQL ou na Azure SQL Managed Instance, e determina o que o utilizador está autorizado a fazer. As permissões são controladas adicionando contas de utilizador às [funções de base](/sql/relational-databases/security/authentication-access/database-level-roles) de dados e atribuindo permissões de nível de base a essas funções ou concedendo ao utilizador [determinadas permissões ao nível dos objetos](/sql/relational-databases/security/permissions-database-engine). Para mais informações, consulte [Logins e utilizadores](logins-create-manage.md)

Como uma boa prática, crie papéis personalizados quando necessário. Adicione os utilizadores ao papel com os privilégios menos necessários para fazer a sua função de trabalho. Não adtribua permissões diretamente aos utilizadores. A conta de administração do servidor é um membro da função de db_owner incorporada, que tem permissões extensivas e só deve ser concedida a poucos utilizadores com funções administrativas. Para aplicações, utilize o [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo ou utilizar [funções de aplicação](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Esta prática garante que a aplicação que se liga à base de dados tem os privilégios menos necessários pela aplicação. Seguir estas boas práticas também fomenta a separação de deveres.

### <a name="row-level-security"></a>Segurança ao Nível da Linha

Row-Level A Segurança permite que os clientes controlem o acesso a linhas numa tabela de bases de dados com base nas características do utilizador executar uma consulta (por exemplo, membro do grupo ou contexto de execução). Row-Level Security também pode ser usado para implementar conceitos de segurança personalizados baseados em etiquetas. Para obter mais informações, consulte [a segurança de nível de linha.](/sql/relational-databases/security/row-level-security)

![Diagrama que mostra que Row-Level Security protege linhas individuais de uma base de dados SQL a partir do acesso dos utilizadores através de uma aplicação do cliente.](./media/security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

A SQL Database e a SQL Managed Instance protegem os dados dos clientes fornecendo capacidades de auditoria e deteção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria SQL em registos do Monitor Azure e Centros de Eventos

A SQL Database e a SQL Managed Instance auditing rastreia as atividades da base de dados e ajuda a manter o cumprimento das normas de segurança, registando eventos de base de dados a um registo de auditoria numa conta de armazenamento Azure detida pelo cliente. A auditoria permite que os utilizadores monitorizem as atividades em curso na base de dados, bem como analisem e investiguem atividades históricas para identificar potenciais ameaças ou suspeitas de abuso e violações de segurança. Para obter mais informações, consulte Começar com [a SQL Database Auditing](../../azure-sql/database/auditing-overview.md).  

### <a name="advanced-threat-protection"></a>Advanced Threat Protection

A Advanced Threat Protection está a analisar os seus registos para detetar comportamentos incomuns e tentativas potencialmente nocivas de aceder ou explorar bases de dados. São criados alertas para atividades suspeitas como injeção de SQL, potencial infiltração de dados e ataques de força bruta ou para anomalias nos padrões de acesso para capturar escaladas de privilégios e utilização de credenciais violadas. Os alertas são vistos a partir do Centro de  [Segurança do Azure,](https://azure.microsoft.com/services/security-center/)onde são fornecidos os detalhes das atividades suspeitas e recomendações para uma investigação mais aprofundada dada juntamente com ações para mitigar a ameaça. A Proteção Avançada de Ameaças pode ser ativada por servidor por uma taxa adicional. Para obter mais informações, consulte [Começar com a SqL Database Advanced Threat Protection](threat-detection-configure.md).

![Diagrama mostrando o acesso à base de dados SQL para uma aplicação web de um intruso externo e infiltrado malicioso.](./media/security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="transport-layer-security-encryption-in-transit"></a>Segurança da camada de transporte (encriptação em trânsito)

Base de Dados SQL, SQL Managed Instance e Azure Synapse Analytics protegem os dados dos clientes encriptando dados em movimento com [a Segurança da Camada de Transporte (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

Base de Dados SQL, SQL Managed Instance e Azure Synapse aplicam encriptação (SSL/TLS) em todos os momentos para todas as ligações. Isto garante que todos os dados são encriptados "em trânsito" entre o cliente e o servidor, independentemente da definição de **Encrypt** ou **TrustServerCertificate** na cadeia de ligação.

Como melhor prática, recomende que na cadeia de ligação utilizada pela aplicação, especifique uma ligação encriptada e _**não**_ confie no certificado do servidor. Isto força a sua aplicação a verificar o certificado do servidor e assim impede que a sua aplicação seja vulnerável ao homem nos ataques do tipo médio.

Por exemplo, ao utilizar o ADO.NET controlador, isto é realizado através do  **Encrypt=True** and **TrustServerCertificate=False** . Se obtiver o fio de ligação a partir do portal Azure, terá as definições corretas.

> [!IMPORTANT]
> Note que alguns controladores não Microsoft podem não utilizar OTS por padrão ou confiar numa versão mais antiga do TLS (<1.2) para funcionar. Neste caso, o servidor ainda lhe permite ligar-se à sua base de dados. No entanto, recomendamos que avalie os riscos de segurança de permitir que tais condutores e aplicações se conectem à Base de Dados SQL, especialmente se armazenar dados sensíveis.
>
> Para mais informações sobre TLS e conectividade, consulte [considerações de TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Encriptação de dados transparentes (encriptação em repouso)

[A encriptação de dados transparente (TDE) para a Base de Dados SQL, SQL Managed Instance e Azure Synapse Analytics](transparent-data-encryption-tde-overview.md) adiciona uma camada de segurança para ajudar a proteger os dados em repouso do acesso não autorizado ou offline a ficheiros ou cópias de segurança brutos. Os cenários comuns incluem roubo de centros de dados ou eliminação não garantida de hardware ou meios, tais como discos e fitas de backup.O TDE encripta toda a base de dados usando um algoritmo de encriptação AES, que não requer que os desenvolvedores de aplicações façam quaisquer alterações às aplicações existentes.

No Azure, todas as bases de dados recentemente criadas são encriptadas por padrão e a chave de encriptação da base de dados está protegida por um certificado de servidor incorporado.  A manutenção e rotação do certificado são geridas pelo serviço e não requerem qualquer entrada do utilizador. Os clientes que preferirem assumir o controlo das chaves de encriptação podem gerir as chaves no [Cofre da Chave Azure](../../key-vault/general/secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestão chave com cofre de chaves Azure

[O](transparent-data-encryption-byok-overview.md) suporte da Sua Própria Chave (BYOK) para [encriptação de dados transparentes](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) permite que os clientes tomem posse de gestão e rotação de chaves usando [o Azure Key Vault](../../key-vault/general/secure-your-key-vault.md), o sistema de gestão de chaves externo baseado na nuvem da Azure. Se o acesso da base de dados ao cofre de chaves for revogado, uma base de dados não pode ser desencriptado e lido na memória. O Azure Key Vault fornece uma plataforma central de gestão de chaves, aproveita módulos de segurança de hardware bem monitorizados (HSMs) e permite a separação de deveres entre a gestão de chaves e dados para ajudar a satisfazer os requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Sempre encriptado (encriptação em uso)

![Diagrama mostrando o básico da funcionalidade Always Encrypted. Uma base de dados SQL com fechadura só é acedida por uma aplicação que contenha uma chave.](./media/security-overview/azure-database-ae.png)

[Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é uma funcionalidade concebida para proteger dados sensíveis armazenados em colunas de bases de dados específicas do acesso (por exemplo, números de cartões de crédito, números de identificação nacionais ou dados numa base _de necessidade de saber)._ Isto inclui administradores de bases de dados ou outros utilizadores privilegiados que estão autorizados a aceder à base de dados para executar tarefas de gestão, mas não têm necessidade de aceder aos dados específicos nas colunas encriptadas. Os dados são sempre encriptados, o que significa que os dados encriptados são desencriptados apenas para processamento por aplicações do cliente com acesso à chave de encriptação. A chave de encriptação nunca é exposta à Base de Dados SQL ou à SQL Managed Instance e pode ser armazenada na Loja de [Certificados](always-encrypted-certificate-store-configure.md) do Windows ou no [Cofre da Chave Azure](always-encrypted-azure-key-vault-configure.md).

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

![Diagrama mostrando máscara de dados dinâmica. Uma aplicação de negócios envia dados para uma base de dados SQL que mascara os dados antes de enviá-los de volta para a aplicação empresarial.](./media/security-overview/azure-database-ddm.png)

A mascaração dinâmica de dados limita a exposição sensível aos dados, mascarando-os a utilizadores não privilegiados. A máscara dinâmica de dados descobre automaticamente dados potencialmente sensíveis na Base de Dados Azure SQL e na SQL Managed Instance e fornece recomendações acccáveis para mascarar estes campos, com o mínimo impacto na camada de aplicação. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para obter mais informações, consulte [Começar com a Base de Dados SQL e a máscara dinâmica de dados da SQL Managed Instance](dynamic-data-masking-overview.md).

## <a name="security-management"></a>Gestão de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidades

[A avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço fácil de configurar que pode descobrir, rastrear e ajudar a corrigir potenciais vulnerabilidades de base de dados com o objetivo de melhorar proativamente a segurança global da base de dados. A avaliação de vulnerabilidade (VA) faz parte da oferta do Azure Defender para a oferta SQL, que é um pacote unificado para capacidades avançadas de segurança SQL. A avaliação de vulnerabilidade pode ser acedida e gerida através do Azure Defender central para o portal SQL.

### <a name="data-discovery-and-classification"></a>Deteção e classificação de dados

A descoberta e classificação de dados (atualmente em pré-visualização) fornece capacidades avançadas incorporadas na Base de Dados Azure SQL e na SQL Managed Instance para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. Descobrir e classificar os seus dados mais sensíveis (negócio/financeiro, cuidados de saúde, dados pessoais, etc.) pode desempenhar um papel fundamental na sua estatura organizacional de proteção de informação. Pode funcionar como infraestrutura para:

- Vários cenários de segurança, como a monitorização (auditoria) e alertando para o acesso anómalo a dados sensíveis.
- Controlar o acesso e endurecer a segurança de bases de dados que contenham dados altamente sensíveis.
- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentares.

Para mais informações, consulte [Começar com a descoberta e classificação de dados.](data-discovery-and-classification-overview.md)

### <a name="compliance"></a>Conformidade

Além das funcionalidades e funcionalidades acima referidas que podem ajudar a sua aplicação a cumprir vários requisitos de segurança, a Azure SQL Database também participa em auditorias regulares, tendo sido certificada contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center](https://www.microsoft.com/trust-center/compliance/compliance-overview) onde pode encontrar a lista mais atual de certificações de conformidade da Base de Dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para uma discussão sobre a utilização de logins, contas de utilizador, funções de base de dados e permissões na Base de Dados SQL e na SQL Managed Instance, consulte [Gerir logins e contas de utilizador.](logins-create-manage.md)
- Para uma discussão sobre a auditoria da base de dados, consulte [a auditoria.](../../azure-sql/database/auditing-overview.md)
- Para uma discussão sobre a deteção de ameaças, consulte [a deteção de ameaças.](threat-detection-configure.md)
