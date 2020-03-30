---
title: Visão geral de segurança
description: Saiba mais sobre a Base de Dados Azure SQL e a segurança do Servidor SQL, incluindo as diferenças entre a nuvem e o Servidor SQL no local.
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
ms.openlocfilehash: 2a4c6dd3dd6f1bb2f15e31226086c73fb8e63521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124837"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Uma visão geral das capacidades de segurança da Base de Dados Azure SQL

Este artigo descreve os fundamentos de assegurar o nível de dados de uma aplicação utilizando a Base de Dados Azure SQL. A estratégia de segurança descrita segue a abordagem em camadas de defesa em profundidade, como mostra a imagem abaixo, e move-se de fora em:

![sql-security-layer.png](media/sql-database-security-overview/sql-security-layer.png)

## <a name="network-security"></a>Segurança da rede

A Microsoft Azure SQL Database fornece um serviço de base de dados relacional para aplicações em nuvem e empresa. Para ajudar a proteger os dados dos clientes, as firewalls impedem o acesso da rede ao servidor de base de dados até que o acesso seja explicitamente concedido com base no endereço IP ou na origem de tráfego da rede Azure Virtual.

### <a name="ip-firewall-rules"></a>Regras de firewall IP

As regras de firewall IP concedem acesso a bases de dados com base no endereço IP originário de cada pedido. Para mais informações, consulte a visão geral da Base de [Dados Azure SQL e](sql-database-firewall-configure.md)das regras de firewall do Armazém de Dados SQL .

### <a name="virtual-network-firewall-rules"></a>Regras de firewall de rede virtual

[Os pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) do serviço de rede virtual estendem a sua conectividade de rede virtual sobre a espinha dorsal do Azure e permitem que a Base de Dados Azure SQL identifique a subnet de rede virtual de que o tráfego é originário. Para permitir que o tráfego chegue à Base de Dados Azure SQL, utilize as [etiquetas](../virtual-network/security-overview.md) de serviço SQL para permitir o tráfego de saída através de Grupos de Segurança da Rede.

[As regras](sql-database-vnet-service-endpoint-rule-overview.md) de rede virtual permitem que a Base de Dados Azure SQL apenas aceite comunicações enviadas a partir de subredes selecionadas dentro de uma rede virtual.

> [!NOTE]
> Controlar o acesso com regras de firewall *não* se aplica a **uma instância gerida**. Para obter mais informações sobre a configuração de rede necessária, consulte [a ligação a um caso gerido](sql-database-managed-instance-connect-app.md)

## <a name="access-management"></a>Gestão de acesso

> [!IMPORTANT]
> A gestão de bases de dados e servidores de base de dados dentro do Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para mais informações sobre este artigo, consulte o [controlo de acesso baseado em Role no portal Azure](../role-based-access-control/overview.md).

### <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o utilizador é quem afirmam ser. A Base de Dados Azure SQL suporta dois tipos de autenticação:

- **Autenticação SQL:**

    A autenticação da base de dados SQL refere-se à autenticação de um utilizador ao ligar-se à Base de [Dados Azure SQL](sql-database-technical-overview.md) utilizando o nome de utilizador e a palavra-passe. Durante a criação do servidor de base de dados para a base de dados, deve ser especificado um login "Server admin" com um nome de utilizador e uma palavra-passe. Utilizando estas credenciais, um "administrador de servidor" pode autenticar qualquer base de dados desse servidor de base de dados como o proprietário da base de dados. Depois disso, podem ser criados registos e utilizadores sQL adicionais pela administração do servidor, que permitem aos utilizadores conectarem-se usando o nome de utilizador e a palavra-passe.

- **Autenticação do Diretório Ativo Azure:**

    A autenticação do Diretório Ativo Azure é um mecanismo de ligação à Base de [Dados Azure SQL](sql-database-technical-overview.md) e ao [SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades no Azure Ative Directory (Azure AD). A autenticação da AD Azure permite aos administradores gerir centralmente as identidades e permissões dos utilizadores de bases de dados, juntamente com outros serviços da Microsoft numa localização central. Isto inclui a minimização do armazenamento de palavras-passe e permite políticas centralizadas de rotação de passwords.

     Um administrador de servidor chamado administrador de **Diretório Ativo** deve ser criado para utilizar a autenticação Azure AD com base de dados SQL. Para mais informações, consulte a Ligação à Base de [Dados SQL utilizando a autenticação de diretório ativo Azure](sql-database-aad-authentication.md). A autenticação azure AD suporta contas geridas e federadas. As contas federadas suportam utilizadores e grupos do Windows para um domínio de cliente federado com AD Azure.

    As opções adicionais de autenticação do Azure AD disponíveis são a autenticação universal do Diretório Ativo para ligações do Estúdio de Gestão de [Servidores SQL,](sql-database-ssms-mfa-authentication.md) incluindo [a autenticação multi-factor](../active-directory/authentication/concept-mfa-howitworks.md) e [o acesso condicional.](sql-database-conditional-access.md)

> [!IMPORTANT]
> A gestão de bases de dados e servidores dentro do Azure é controlada pelas atribuições de funções da sua conta de utilizador do portal. Para mais informações sobre este artigo, consulte o [controlo de acesso baseado em Role no portal Azure](../role-based-access-control/overview.md). Controlar o acesso com regras de firewall *não* se aplica a **uma instância gerida**. Consulte o seguinte artigo sobre [a ligação a uma instância gerida](sql-database-managed-instance-connect-app.md) para obter mais informações sobre a configuração de rede necessária.

## <a name="authorization"></a>Autorização

A autorização refere-se às permissões atribuídas a um utilizador dentro de uma Base de Dados Azure SQL, e determina o que o utilizador pode fazer. As permissões são controladas adicionando contas de utilizador a [funções](/sql/relational-databases/security/authentication-access/database-level-roles) de base de dados e atribuindo permissões ao nível da base de dados para essas funções ou concedendo ao utilizador [determinadas permissões ao nível do objeto](/sql/relational-databases/security/permissions-database-engine). Para mais informações, consulte [Logins e utilizadores](sql-database-manage-logins.md)

Como uma boa prática, crie papéis personalizados quando necessário. Adicione os utilizadores ao papel com os menores privilégios necessários para fazer a sua função de trabalho. Não atribuir permissões diretamente aos utilizadores. A conta de administração do servidor é um membro do papel de db_owner incorporado, que tem permissões extensas e deve ser concedido apenas a poucos utilizadores com deveres administrativos. Para aplicações de base de dados Azure SQL, utilize o [EXECUTE AS](/sql/t-sql/statements/execute-as-clause-transact-sql) para especificar o contexto de execução do módulo chamado ou utilize funções de [aplicação](/sql/relational-databases/security/authentication-access/application-roles) com permissões limitadas. Esta prática garante que a aplicação que se liga à base de dados tem os menores privilégios necessários pela aplicação. Seguir estas boas práticas também promove a separação de deveres.

### <a name="row-level-security"></a>Segurança ao Nível da Linha

A Segurança de Nível de Linha permite que os clientes controlem o acesso às linhas numa tabela de bases de dados com base nas características do utilizador que executa uma consulta (por exemplo, membro do grupo ou contexto de execução). A Segurança de Nível de Linha também pode ser usada para implementar conceitos de segurança personalizados baseados no rótulo. Para obter mais informações, veja [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security).

![azure-database-rls.png](media/sql-database-security-overview/azure-database-rls.png)

## <a name="threat-protection"></a>Proteção contra ameaças

A Base de Dados SQL assegura os dados dos clientes fornecendo capacidades de auditoria e deteção de ameaças.

### <a name="sql-auditing-in-azure-monitor-logs-and-event-hubs"></a>Auditoria SQL em registos e centros de eventos do Monitor Azure

A auditoria da Base de Dados SQL rastreia as atividades da base de dados e ajuda a manter o cumprimento das normas de segurança, registando eventos de base de dados a um registo de auditoria numa conta de armazenamento Azure propriedade do cliente. A auditoria permite que os utilizadores monitorizem as atividades em curso na base de dados, bem como analisar e investigar atividades históricas para identificar potenciais ameaças ou suspeitas de abuso e violações de segurança. Para mais informações, consulte Começar com a Auditoria de Base de [Dados SQL](sql-database-auditing.md).  

### <a name="advanced-threat-protection"></a>Proteção Avançada Contra Ameaças

A Advanced Threat Protection está a analisar os registos do Seu Servidor SQL para detetar comportamentos incomuns e tentativas potencialmente nocivas de aceder ou explorar bases de dados. São criados alertas para atividades suspeitas como a injeção de SQL, potenciais ataques de infiltração de dados e ataques de força bruta ou para anomalias nos padrões de acesso para capturar escaladas de privilégios e violações do uso de credenciais. Os alertas são vistos a partir do Centro de [Segurança Azure,](https://azure.microsoft.com/services/security-center/)onde são fornecidos os detalhes das atividades suspeitas e recomendações para uma investigação posterior dada juntamente com ações para mitigar a ameaça. A Proteção avançada de Ameaças pode ser ativada por servidor por uma taxa adicional. Para mais informações, consulte Começar com a Base de [Dados SQL Advanced Threat Protection](sql-database-threat-detection.md).

![azure-database-td.jpg](media/sql-database-security-overview/azure-database-td.jpg)

## <a name="information-protection-and-encryption"></a>Proteção de informação e encriptação

### <a name="transport-layer-security-tls-encryption-in-transit"></a>TLS de segurança da camada de transporte (Encriptação em trânsito)

A Base de Dados SQL protege os dados dos clientes encriptando os dados em movimento com a Segurança da Camada de [Transporte](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).

O Sql Server aplica sempre a encriptação (SSL/TLS) para todas as ligações. Isto garante que todos os dados estão encriptados "em trânsito" entre o cliente e o servidor, independentemente da definição de **Encrypt** ou **TrustServerCertificate** na cadeia de ligação.

Como uma boa prática, recomende que na cadeia de ligação da sua aplicação especifique uma ligação encriptada e _**não**_ confie no certificado de servidor. Isto força a sua aplicação a verificar o certificado de servidor e assim impede que a sua aplicação seja vulnerável ao homem nos ataques do tipo médio.

Por exemplo, ao utilizar o ADO.NET controlador, este é realizado através de **Encrypt=True** e **TrustServerCertificate=False**. Se obtiver a sua cadeia de ligação a partir do portal Azure, terá as definições corretas.

> [!IMPORTANT]
> Note que alguns controladores não Microsoft podem não utilizar TLS por padrão ou confiar numa versão mais antiga do TLS (<1.2) para funcionar. Neste caso, o SQL Server ainda lhe permite ligar à sua base de dados. No entanto, recomendamos que avalie os riscos de segurança de permitir que tais condutores e aplicação se conectem à Base de Dados SQL, especialmente se armazenar dados sensíveis.
>
> Para mais informações sobre TLS e conectividade, consulte [considerações de TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)

### <a name="transparent-data-encryption-encryption-at-rest"></a>Encriptação transparente de dados (Encriptação em repouso)

[A Encriptação transparente de dados (TDE) para a Base de Dados SQL azure](transparent-data-encryption-azure-sql.md) adiciona uma camada de segurança para ajudar a proteger os dados em repouso do acesso não autorizado ou offline a ficheiros ou backups brutos. Os cenários comuns incluem roubo de datacenter ou eliminação não segura de hardware ou meios, tais como discos e fitas de backup.O TDE encripta toda a base de dados usando um algoritmo de encriptação AES, que não requer que os desenvolvedores de aplicações façam alterações nas aplicações existentes.

No Azure, todas as bases de dados SQL recém-criadas são encriptadas por padrão e a chave de encriptação da base de dados está protegida por um certificado de servidor incorporado.  A manutenção e rotação do certificado são geridas pelo serviço e não requerem nenhuma entrada do utilizador. Os clientes que preferem assumir o controlo das chaves de encriptação podem gerir as chaves no [Cofre de Chaves Azure](../key-vault/key-vault-secure-your-key-vault.md).

### <a name="key-management-with-azure-key-vault"></a>Gestão chave com cofre de chave Azure

[O](transparent-data-encryption-byok-azure-sql.md) suporte bring Your Own Key (BYOK) para [encriptação de dados transparente](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) permite que os clientes se adequem à gestão e rotação de chaves utilizando o Azure Key Vault , o sistema de gestão de chaves externas baseado na nuvem do [Azure.](../key-vault/key-vault-secure-your-key-vault.md) Se o acesso da base de dados ao cofre da chave for revogado, uma base de dados não pode ser desencriptada e lida na memória. O Azure Key Vault fornece uma plataforma central de gestão de chaves, aproveita módulos de segurança de hardware monitorizados de forma rigorosa (HSMs), e permite a separação de deveres entre a gestão de chaves e dados para ajudar a satisfazer os requisitos de conformidade de segurança.

### <a name="always-encrypted-encryption-in-use"></a>Sempre Encriptado (Encriptação em uso)

![azure-database-ae.png](media/sql-database-security-overview/azure-database-ae.png)

[Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine) é uma funcionalidade concebida para proteger dados sensíveis armazenados em colunas de base de dados específicas de acesso (por exemplo, números de cartões de crédito, números de identificação nacionais ou dados com base na _necessidade de saber)._ Isto inclui administradores de bases de dados ou outros utilizadores privilegiados autorizados a aceder à base de dados para executar tarefas de gestão, mas não têm necessidade de aceder aos dados específicos nas colunas encriptadas. Os dados são sempre encriptados, o que significa que os dados encriptados são desencriptados apenas para processamento por aplicações de clientes com acesso à chave de encriptação.  A chave de encriptação nunca é exposta à SQL e pode ser armazenada na Loja de [Certificados do Windows](sql-database-always-encrypted.md) ou no [Cofre de Chaves Azure](sql-database-always-encrypted-azure-key-vault.md).

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

![azure-database-ddm.png](media/sql-database-security-overview/azure-database-ddm.png)

A base de dados dinâmica SQL limita a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados. A máscara de dados dinâmicos descobre automaticamente dados potencialmente sensíveis na Base de Dados Azure SQL e fornece recomendações acionáveis para mascarar estes campos, com o mínimo impacto na camada de aplicação. Funciona ao ofuscar os dados confidenciais no conjunto de resultados de uma consulta em campos de base de dados designados, enquanto os dados na base de dados não são alterados. Para mais informações, consulte Começar com a base de [dados dinâmica SQL](sql-database-dynamic-data-masking-get-started.md).

## <a name="security-management"></a>Gestão de segurança

### <a name="vulnerability-assessment"></a>Avaliação de vulnerabilidades

[A avaliação](sql-vulnerability-assessment.md) de vulnerabilidade é um serviço fácil de configurar que pode descobrir, rastrear e ajudar a remediar potenciais vulnerabilidades da base de dados com o objetivo de melhorar proativamente a segurança geral da base de dados. A avaliação de vulnerabilidade (VA) faz parte da oferta avançada de segurança de dados (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. A avaliação de vulnerabilidade pode ser acedida e gerida através do portal Central SQL ADS.

### <a name="data-discovery--classification"></a>Deteção e classificação de dados

A descoberta de dados & classificação (atualmente em pré-visualização) fornece capacidades avançadas incorporadas na Base de Dados Azure SQL para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. Descobrir e classificar os seus dados mais sensíveis (negócios/financeiros, cuidados de saúde, dados pessoais, etc.) pode desempenhar um papel fundamental na sua estatura organizacional de proteção de informação. Pode funcionar como infraestrutura para:

- Vários cenários de segurança, como a monitorização (auditoria) e o alerta sobre o acesso anómalo a dados sensíveis.
- Controlar o acesso e reforçar a segurança das bases de dados contendo dados altamente sensíveis.
- Ajudar a cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentares.

Para mais informações, consulte [Começar com a descoberta de dados & classificação](sql-database-data-discovery-and-classification.md).

### <a name="compliance"></a>Conformidade

Além das funcionalidades e funcionalidades acima referidas que podem ajudar a sua aplicação a cumprir vários requisitos de segurança, a Azure SQL Database também participa em auditorias regulares, tendo sido certificada contra uma série de normas de conformidade. Para mais informações, consulte o [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

## <a name="next-steps"></a>Passos seguintes

- Para uma discussão sobre a utilização de logins, contas de utilizador, funções de base de dados e permissões na Base de Dados SQL, consulte [Gerir logins e contas de utilizador](sql-database-manage-logins.md).
- Para uma discussão sobre a auditoria da base de dados, consulte a auditoria da Base de [Dados SQL](sql-database-auditing.md).
- Para uma discussão sobre deteção de ameaças, consulte a deteção de ameaças da Base de [Dados SQL](sql-database-threat-detection.md).
