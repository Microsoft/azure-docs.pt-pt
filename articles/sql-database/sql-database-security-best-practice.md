---
title: Livro de jogadas para abordar requisitos comuns de segurança [ Microsoft Docs
titleSuffix: Azure SQL Database
description: Este artigo fornece requisitos de segurança comuns e boas práticas na Base de Dados Azure SQL.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 02/20/2020
ms.reviewer: ''
ms.openlocfilehash: 07e8d8690cb07cfde3e0def902bf6d8e7ba5b788
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232364"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database"></a>Livro de jogadas para abordar requisitos comuns de segurança com base de dados Azure SQL

> [!NOTE]
> Este documento fornece as melhores práticas sobre como resolver os requisitos comuns de segurança. Nem todos os requisitos são aplicáveis a todos os ambientes, e deve consultar a sua base de dados e a sua equipa de segurança sobre quais as funcionalidades a implementar.

## <a name="solving-common-security-requirements"></a>Resolução de requisitos comuns de segurança

Este documento fornece orientações sobre como resolver requisitos comuns de segurança para aplicações novas ou existentes utilizando a Base de Dados Azure SQL. É organizado por áreas de segurança de alto nível. Para abordar ameaças específicas, consulte as ameaças de segurança comuns e a secção [de potenciais mitigações.](#common-security-threats-and-potential-mitigations) Embora algumas das recomendações apresentadas sejam aplicáveis quando as candidaturas migratórias de instalações para OAzure, os cenários de migração não são o foco deste documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Ofertas de implementação da Base de Dados Azure SQL abrangidas por este guia

- Bases de [dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-index): bases de [dados únicas](sql-database-single-database.md) e [piscinas elásticas](sql-database-elastic-pool.md) em servidores de base de [dados Azure SQL](sql-database-servers.md)
- [Casos geridos](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Ofertas de implantação SQL não abrangidas por este guia

- Azure SQL Data Warehouse
- VMs Azure SQL (IaaS)
- SQL Server no local

### <a name="audience"></a>Audiência

Os públicos pretendidos para este guia são os clientes que enfrentam questões sobre como garantir a Base de Dados Azure SQL. Os papéis interessados neste artigo de boas práticas incluem, mas não se limitando a:

- Arquitetos de Segurança
- Gestores de Segurança
- Oficiais de Conformidade
- Agentes de Privacidade
- Engenheiros de Segurança

### <a name="using-this-guide"></a><a id="using"></a>Usando este guia

Este documento destina-se a ser um companheiro da nossa documentação de segurança da Base de [Dados Azure SQL](sql-database-security-overview.md) existente.

Salvo indicação em contrário, recomendamos que siga todas as boas práticas listadas em cada secção para alcançar o objetivo ou exigência respetivos. Para satisfazer normas específicas de conformidade de segurança ou boas práticas, os importantes controlos de conformidade regulamentar são listados na secção Requisitos ou Objetivos sempre que aplicável. Estas são as normas e regulamentos de segurança que são referenciados neste documento:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC:](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html)CM-3, SDL-3
- [ISO/IEC 27001:](https://www.iso27001security.com/html/27001.html)Controlo de Acesso, Criptografia
- [Práticas de Segurança Operacional da Microsoft (OSA) práticas](https://www.microsoft.com/en-us/securityengineering/osa/practices): Prática #1-6 e #9
- [NIST Publicação Especial 800-53 Controlos](https://nvd.nist.gov/800-53)de Segurança : AC-5, AC-6
- [PCI DSS:](https://www.pcisecuritystandards.org/document_library)6.3.2, 6.4.2

### <a name="feedback"></a>Comentários

Planeamos continuar a atualizar as recomendações e as melhores práticas aqui listadas. Forneça a entrada ou quaisquer correções para este documento utilizando o link **Feedback** na parte inferior deste artigo.

## <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o utilizador é quem afirmam ser. A Base de Dados Azure SQL suporta dois tipos de autenticação:

- Autenticação do SQL
- Autenticação do Azure Active Directory

> [!NOTE]
> A autenticação do Diretório Ativo Azure não pode ser suportada para todas as ferramentas e aplicações de terceiros.

### <a name="central-management-for-identities"></a>Gestão central de identidades

A gestão central da identidade oferece os seguintes benefícios:

- Gerencie as contas de grupo e controle as permissões dos utilizadores sem duplicar os logins através de servidores e bases de dados Azure SQL.
- Gestão simplificada e flexível da permissão.
- Gestão de aplicações à escala.

**Como implementar:**

- Utilize a autenticação azure Ative Directory (Azure AD) para gestão de identidade centralizada.

**Boas práticas:**

- Crie um inquilino da AD Azure e [crie utilizadores](../active-directory/fundamentals/add-users-azure-active-directory.md) para representar utilizadores humanos e criar diretores de [serviços](../active-directory/develop/app-objects-and-service-principals.md) para representar aplicações, serviços e ferramentas de automação. Os diretores de serviço são equivalentes a contas de serviço no Windows e Linux. 

- Atribuir direitos de acesso aos recursos aos principais da AD Azure através da atribuição do grupo: Criar grupos AD Azure, conceder acesso a grupos e adicionar membros individuais aos grupos. Na sua base de dados, crie utilizadores de bases de dados contidos que mapeiem os seus grupos De AD Azure. Para atribuir permissões dentro da base de dados, coloque os utilizadores associados aos seus grupos De AD Azure em funções de base de dados com as permissões adequadas.
  - Consulte os artigos, [configure e gere a autenticação do Diretório Ativo Azure com SQL](sql-database-aad-authentication-configure.md) e [use a AD Azure para autenticação com SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > Num caso gerido, também pode criar logins que mapeiam os principais da AD Azure na base de dados principal. Consulte [O LOGIN CREATE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- A utilização de grupos AD Azure simplifica a gestão da permissão e tanto o proprietário do grupo, como o proprietário do recurso pode adicionar/remover membros de/para o grupo. 

- Crie um grupo separado para administradores de AD Azure para cada servidor SQL DB.

  - Consulte o artigo, Provision um administrador de Diretório Ativo Azure para o seu servidor de base de [dados Azure SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Monitorizar as alterações de membros do grupo AD Do Monitor Utilizando relatórios de atividade de auditoria da AD Azure. 

- Para um caso gerido, é necessário um passo separado para criar administração Azure AD. 
  - Consulte o artigo, [Provision um administrador de Diretório Ativo Azure para a sua instância gerida](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - A autenticação Azure AD é registada em registos de auditoria Azure SQL, mas não em registos de entrada da Azure AD.
> - As permissões RBAC concedidas em Azure não se aplicam às permissões Do BD Do Azure. Tais permissões devem ser criadas/mapeadas manualmente em SQL DB utilizando permissões SQL existentes.
> - Do lado do cliente, a autenticação Azure AD necessita de acesso à internet ou via Via Definida pelo Utilizador (UDR) a uma VNet.
> - O token de acesso Azure AD está em cache do lado do cliente e a sua vida depende da configuração do símbolo. Ver o artigo, [Token Lifetimes Configurable em Azure Ative Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Para obter orientações sobre problemas de resolução de problemas em questões de autenticação adtiva do Azure AD, consulte o seguinte blog:<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> Mencionado em: OSA Practice #2, ISO Access Control (AC)

A Autenticação Multi-Factor Azure (MFA) ajuda a fornecer segurança adicional, exigindo mais de uma forma de autenticação.

**Como implementar:**

- Ative o [MFA](../active-directory/authentication/concept-mfa-howitworks.md) em Azure AD utilizando o Acesso Condicional e utilize a autenticação interativa. 

- A alternativa é ativar o MFA para todo o domínio Azure AD ou AD.

**Boas práticas:**

- Ativar o Acesso Condicional em AD Azure (requer subscrição Premium). 
  - Consulte o artigo, [Acesso Condicional em Azure AD](../active-directory/conditional-access/overview.md).  

- Crie o grupo Azure AD e ative a política de MFA para grupos selecionados utilizando o Acesso Condicional Azure AD. 
  - Consulte o artigo, [Plan Conditional Access Deployment](../active-directory/conditional-access/plan-conditional-access.md). 

- O MFA pode ser ativado para todo o Azure AD ou para todo o Diretório Ativo federado com Azure AD. 

- Utilize o modo de autenticação Azure AD Interactive para O SQL DB onde uma palavra-passe é solicitada interativamente, seguida de autenticação MFA:      
  - Utilize a autenticação universal em SSMS. Consulte o artigo, utilizando a [autenticação AAD multifactor com base de dados Azure SQL e Azure SQL Data Warehouse (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
  - Utilize a Autenticação Interativa suportada nas Ferramentas de Dados do Servidor SQL (SSDT). Consulte o artigo, [suporte de Diretório Ativo Azure em Ferramentas de Dados do Servidor SQL (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Utilize outras ferramentas SQL que suportem o MFA. 
    - Suporte do Assistente SSMS para base de dados de exportação/extração/implantação  
    - [sqlpackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage): opção '/ua' 
    - [Utilidade sqlcmd:](https://docs.microsoft.com/sql/tools/sqlcmd-utility)opção -G (interativa)
    - [utilidade bcp](https://docs.microsoft.com/sql/tools/bcp-utility): opção -G (interativa) 

- Implemente as suas aplicações para se ligar à Base de Dados Azure SQL utilizando a autenticação interativa com suporte mFA. 
  - Consulte o artigo, [Connect to Azure SQL Database com autenticação de multi-factor azure](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Este modo de autenticação requer identidades baseadas no utilizador. Nos casos em que é utilizado um modelo de identidade fidedigno que contorna a autenticação individual do utilizador Azure AD (por exemplo, utilizando identidade gerida para recursos Azure), o MFA não se aplica.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimizar a utilização da autenticação baseada em palavra-passe para os utilizadores

> Mencionado em: Osa Practice #4, ISO Access Control (AC)

Os métodos de autenticação baseados em palavra-passe são uma forma mais fraca de autenticação. As credenciais podem ser comprometidas ou erradamente dadas.

**Como implementar:**

- Utilize uma autenticação integrada azure AD que elimine o uso de senhas.

**Boas práticas:**

- Utilize uma autenticação única usando credenciais do Windows. Federar o domínio ad no local com AD Azure e utilizar a autenticação Integrada do Windows (para máquinas unidas ao domínio com AD Azure).
  - Consulte o artigo, [suporte SSMS para autenticação integrada Azure AD](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimizar a utilização da autenticação baseada em palavra-passe para aplicações 

> Mencionado em: Osa Practice #4, ISO Access Control (AC)

**Como implementar:**

- Ativar identidade gerida azure. Também pode utilizar autenticação integrada ou baseada em certificados. 

**Boas práticas:**

- Utilize [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)
  - [Identidade gerida atribuída pelo sistema](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Identidade gerida atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Utilize a Base de Dados Azure SQL do serviço de aplicações com identidade gerida (sem alterações de código)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Utilize a autenticação baseada em cert para uma aplicação. 
  - Consulte esta [amostra de código](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Utilize a autenticação Azure AD para domínio federado integrado e máquina unida em domínio (ver secção acima).
  - Consulte o [pedido de autenticação integrada.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)

### <a name="protect-passwords-and-secrets"></a>Proteja senhas e segredos

Para casos em que as palavras-passe não sejam evitáveis, certifique-se de que estão seguras.

**Como implementar:**

- Use o Cofre chave Azure para armazenar senhas e segredos. Sempre que aplicável, utilize o MFA para a Base de Dados Azure SQL com utilizadores da AD Azure.

**Boas práticas:**

- Se evitar palavras-passe ou segredos não for possível, guarde as palavras-passe dos utilizadores e os segredos de aplicação no Cofre de Chaves do Azure e gerencie o acesso através das políticas de acesso do Key Vault. 

- Vários quadros de desenvolvimento de aplicações também podem oferecer mecanismos específicos para proteger segredos na aplicação. Por exemplo: [ASP.NET aplicação central](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Utilize a autenticação SQL para aplicações antigas 

A autenticação SQL refere-se à autenticação de um utilizador ao ligar-se à Base de Dados Azure SQL utilizando o nome de utilizador e a palavra-passe. Será necessário criar um login em cada servidor de Base de Dados SQL ou numa instância gerida, e um utilizador criado em cada base de dados.

**Como implementar:**

- Utilize a autenticação SQL.

**Boas práticas:**

- Como administrador de servidor, crie logins e utilizadores. A menos que utilize utilizadores de bases de dados contidos com senhas, todas as palavras-passe são armazenadas na base de dados principal.
  - Consulte o artigo, [controlando e concedendo acesso à base de dados sQL e Armazém de Dados SQL](sql-database-manage-logins.md).

## <a name="access-management"></a>Gestão de acesso

A gestão de acesso (também denominada Autorização) é o processo de controlo e gestão do acesso e privilégios dos utilizadores autorizados à Base de Dados Azure SQL.

### <a name="implement-principle-of-least-privilege"></a>Implementar princípio de menor privilégio

> Mencionado em: FedRamp controla AC-06, NIST: AC-6, OSA Practice #3

O princípio do menor privilégio diz que os utilizadores não devem ter mais privilégios do que o necessário para completar as suas tarefas. Para mais informações, consulte o artigo [Administração suficiente.](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview)

**Como implementar:**

Atribuir apenas as [permissões necessárias](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) para completar as tarefas necessárias:

- Nas bases de dados SQL: 
    - Utilize permissões granulares e funções de base de dados definidas pelo utilizador (ou funções de servidor no MI): 
        1. Criar as funções necessárias
            - [CRIAR PAPEL](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CRIAR A FUNÇÃO DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Criar utilizadores necessários
            - [CRIAR O UTILIZADOR](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Adicione utilizadores como membros às funções 
            - [ALTERAR O PAPEL](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTERAR FUNÇÃO DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Em seguida, atribuir permissões aos papéis. 
            - [CONCESSÃO](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Certifique-se de não atribuir aos utilizadores funções desnecessárias.

- In Azure Resource Manager:
  - Utilize funções incorporadas se estiverem disponíveis ou personalizadas funções RBAC e atribuir as permissões necessárias.
    - [Papéis incorporados para Azure](../role-based-access-control/built-in-roles.md) 
    - [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)

**Boas práticas:**

As seguintes boas práticas são opcionais, mas resultarão numa melhor gestão e capacidade de apoio da sua estratégia de segurança: 

- Se possível, comece com o mínimo possível de permissões e comece a adicionar permissões uma a uma se houver uma necessidade real (e justificação) – ao contrário da abordagem oposta: tirar permissões passo a passo. 

- Abster-se de atribuir permissões a utilizadores individuais. Utilize as funções (bases de dados ou funções do servidor) de forma consistente. Os papéis ajudam muito na comunicação e na resolução de permissões. (O Azure RBAC apenas apoia a atribuição de permissão através de funções.) 

- Crie e use papéis personalizados com as permissões exatas necessárias. Funções típicas que são usadas na prática: 
  - Implantação de segurança 
  - Administrador 
  - Developer (Programador) 
  - Pessoal de apoio 
  - Auditor 
  - Processos automatizados 
  - Utilizador final 
  
- Utilize funções incorporadas apenas quando as permissões das funções corresponderem exatamente às permissões necessárias para o utilizador. Pode atribuir utilizadores a várias funções. 

- Lembre-se de que as permissões no Motor de Base de Dados do Servidor SQL podem ser aplicadas nos seguintes âmbitos. Quanto menor for o âmbito, menor é o impacto das permissões concedidas: 
  - Servidor de base de dados Azure SQL (funções especiais na base de dados principal) 
  - Base de Dados 
  - Esquema
      - É uma boa prática usar esquema para conceder permissões dentro de uma base de dados. (ver também: [Schema-design para SQL Server: recomendações para o design schema com segurança em mente)](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)
  - Objeto (tabela, vista, procedimento, etc.) 
  > [!NOTE]
  > Não é aconselhável aplicar permissões ao nível do objeto, uma vez que este nível adiciona complexidade desnecessária à implementação global. Se decidir utilizar permissões ao nível do objeto, estas devem ser claramente documentadas. O mesmo se aplica às permissões de nível de coluna, que são ainda menos recomendáveis pelas mesmas razões. Esteja também ciente de que, por defeito, um [DENY](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) de nível de tabela não sobrepor-se a um GRANT de nível de coluna. Isto exigiria que os [critérios comuns de conformidade da configuração](https://docs.microsoft.com/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) do servidor fossem ativados.

- Efetuar verificações regulares utilizando [a Avaliação de Vulnerabilidades (VA)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) para testar demasiadas permissões.

### <a name="implement-separation-of-duties"></a>Implementar a Separação de Direitos

> Mencionado em: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A Separação de Deveres, também denominada Segregação de Deveres descreve a exigência de dividir tarefas sensíveis em múltiplas tarefas que são atribuídas a diferentes utilizadores. A separação de Deveres ajuda a prevenir violações de dados.

**Como implementar:**

- Identificar o nível exigido de Separação de Deveres. Exemplos: 
  - Entre ambientes de desenvolvimento/teste e produção 
  - Tarefas sensíveis em termos de segurança vs Database Administrator (DBA) tarefas de nível de gestão vs tarefas de desenvolvimento. 
    - Exemplos: Auditor, criação de política de segurança para segurança de nível de funções (RLS), implementação de objetos de base de dados SQL com permissões DDL.

- Identifique uma hierarquia abrangente de utilizadores (e processos automatizados) que acedam ao sistema.

- Crie funções de acordo com os grupos de utilizadores necessários e atribua permissões às funções. 
  - Para tarefas de nível de gestão no portal Azure ou através da powerShell-automation use funções RBAC. Ou encontra uma função incorporada que corresponda ao requisito, ou cria uma função RBAC personalizada usando as permissões disponíveis 
  - Criar funções de Servidor para tarefas em todo o servidor (criação de novos logins, bases de dados) numa instância gerida. 
  - Criar funções de base de dados para tarefas ao nível da base de dados.

- Para determinadas tarefas sensíveis, considere a criação de procedimentos especiais armazenados assinados por um certificado para executar as tarefas em nome dos utilizadores. Uma vantagem importante dos procedimentos armazenados digitalmente assinados é que, se o procedimento for alterado, as permissões que foram concedidas à versão anterior do procedimento são imediatamente removidas.
  - Exemplo: Tutorial: Assinatura de [Procedimentos Armazenados com Certificado](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implementar encriptação transparente de dados (TDE) com chaves geridas pelo cliente no Cofre chave Azure para permitir a separação de deveres entre o proprietário de dados e o proprietário da segurança. 
  - Consulte o artigo, [Configure chaves geridas pelo cliente para encriptação de armazenamento Azure a partir do portal Azure](../storage/common/storage-encryption-keys-portal.md). 

- Para garantir que um DBA não pode ver dados que são considerados altamente sensíveis e ainda podem fazer tarefas de DBA, pode usar Sempre Encriptado com separação de papéis. 
  - Consulte os artigos, [visão geral da Gestão da Chave para Sempre Encriptado,](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [Disposição da Chave com Separação](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)de Papéis e Rotação de [Chave Master da Coluna com Separação](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation)de Papéis . 

- Nos casos em que a utilização de Sempre Encriptado não é viável, ou pelo menos não sem grandes custos e esforços que podem mesmo tornar o sistema quase inutilizável, os compromissos podem ser feitos e atenuados através da utilização de controlos compensatórios tais como: 
  - Intervenção humana em processos. 
  - Pistas de auditoria – para mais informações sobre Auditoria, ver, [Auditar eventos críticos](#audit-critical-security-events)de segurança.

**Boas práticas:**

- Certifique-se de que são utilizadas diferentes contas para ambientes de desenvolvimento/teste e produção. Contas diferentes ajudam a cumprir a separação dos sistemas de produção de teste &.

- Abster-se de atribuir permissões a utilizadores individuais. Utilize as funções (bases de dados ou funções do servidor) de forma consistente. Ter papéis ajuda muito na comunicação e na resolução de permissões.

- Use papéis incorporados quando as permissões correspondem exatamente às permissões necessárias – se a união de todas as permissões de várias funções incorporadas levar a uma correspondência de 100%, também pode atribuir múltiplas funções simultaneamente. 

- Criar e utilizar funções definidas pelo utilizador quando as funções incorporadas concedem demasiadas permissões ou permissões insuficientes. 

- As atribuições de funções também podem ser feitas temporariamente, também conhecidas como Separação Dinâmica de Deveres (DSD), seja dentro dos passos de Trabalho do Agente SQL em T-SQL ou usando o AZURE PIM para funções RBAC. 

- Certifique-se de que os DBAs não têm acesso às chaves de encriptação ou às lojas chave e que os Administradores de Segurança com acesso às teclas não têm acesso à base de dados por sua vez. A utilização da Gestão de [Chaves Extensíveis (EKM)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-ekm) pode facilitar esta separação. [O Cofre de Chaves Azure](https://azure.microsoft.com/services/key-vault/) pode ser usado para implementar o EKM. 

- Certifique-se sempre de ter um rasto de auditoria para ações relacionadas com a segurança. 

- Você pode recuperar a definição das funções RBAC incorporadas para ver as permissões usadas e criar um papel personalizado com base em excertos e acumulações destes através do PowerShell.

- Como qualquer membro da função de base de dados db_owner pode alterar definições de segurança como encriptação de dados transparentes (TDE), ou alterar o SLO, esta adesão deve ser concedida com cuidado. No entanto, há muitas tarefas que requerem privilégios db_owner. Tarefa como alterar qualquer definição de base de dados, como alterar opções de DB. A auditoria desempenha um papel fundamental em qualquer solução.

- Não é possível restringir as permissões de uma db_owner e, portanto, impedir uma conta administrativa de visualizar os dados dos utilizadores. Se houver dados altamente sensíveis numa base de dados, o Always Encrypted pode ser usado para evitar que db_owners ou qualquer outro DBA os veja.

> [!NOTE]
> Conseguir a Separação de Direitos (SoD) é um desafio para tarefas relacionadas com a segurança ou resolução de problemas. Outras áreas como o desenvolvimento e as funções de utilizador final são mais fáceis de segregar. A maioria dos controlos relacionados com a conformidade permitem a utilização de funções de controlo alternativas, como a Auditoria quando outras soluções não são práticas.

Para os leitores que queiram mergulhar mais profundamente na SoD, recomendamos os seguintes recursos: 

- Para base de dados Azure SQL:  
  - [Controlo e concessão de acesso à base de dados da Base de Dados SQL e armazém de dados SQL](sql-database-manage-logins.md)
  - [Separação de funções para o desenvolvedor de aplicações](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Separação de Deveres no Servidor SQL 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Assinatura de procedimentos armazenados no servidor SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Para a Gestão de Recursos Azure:
  - [Papéis incorporados para Azure](../role-based-access-control/built-in-roles.md) 
  - [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)
  - [Utilização de Gestão de Identidade Privilegiada da Azure AD para acesso elevado](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Realizar avaliações regulares de código

> Mencionado em: PCI: 6.3.2, SOC: SDL-3 

A separação de Direitos não se limita aos dados na base de dados, mas inclui o código de aplicação. O código malicioso pode potencialmente contornar os controlos de segurança. Antes de implementar o código personalizado para a produção, é essencial rever o que está a ser implementado.

**Como implementar:**

- Utilize uma ferramenta de base de dados como o Azure Data Studio que suporta o controlo de fontes. 

- Implementar um processo de implantação de código segregado.

- Antes de se comprometer com o ramo principal, uma pessoa (que não seja o autor do próprio código) tem de inspecionar o código para potenciais riscos de elevação de privilégios, bem como modificações de dados maliciosas para proteger contra fraudes e acessofraudulento. Isto pode ser feito utilizando mecanismos de controlo de origem.

**Boas práticas:**

- Normalização: Ajuda a implementar um procedimento padrão que deve ser seguido para quaisquer atualizações de código. 

- A Avaliação de Vulnerabilidade contém regras que verificam permissões excessivas, o uso de algoritmos de encriptação antigos e outros problemas de segurança dentro de um esquema de base de dados. 

- Verificações adicionais podem ser feitas num ambiente de QA ou de teste utilizando a Proteção Avançada de Ameaças que verifica código santificado à injeção de SQL.

- Exemplos do que procurar: 
  - Criação de um utilizador ou alteração das definições de segurança a partir de uma implementação automatizada de atualização de código SQL. 
  - Um procedimento armazenado, que, dependendo dos parâmetros fornecidos, atualiza um valor monetário numa célula de forma não conforme. 

- Certifique-se de que a pessoa que está a conduzir a revisão é um indivíduo diferente do autor do código originário e conhecedor em revisões de códigos e codificação segura.

- Certifique-se de conhecer todas as fontes de alterações de código. O código pode estar nos scripts T-SQL. Pode ser comandos ad-hoc para ser executado ou implantado sob formas de Vistas, Funções, Gatilhos e Procedimentos Armazenados. Pode fazer parte das definições de trabalho do Agente SQL (Passos). Também pode ser executado a partir de pacotes SSIS, Azure Data Factory, e outros serviços.

## <a name="data-protection"></a>Proteção de dados

A proteção de dados é um conjunto de capacidades para salvaguardar informações importantes do compromisso por encriptação ou obfuscção.

> [!NOTE]
> A Microsoft atesta a Base de Dados Azure SQL como sendo compatível com o FIPS 140-2 Level 1. Isto é feito após verificar a utilização rigorosa de algoritmos aceitáveis de nível 1 440-2 fips e fips 140-2 casos validados de nível 1 desses algoritmos, incluindo consistência com comprimentos-chave necessários, gestão chave, geração chave e armazenamento chave. Esta atesta destina-se a permitir que os nossos clientes respondam à necessidade ou exigência de utilização de instâncias validadas de Nível 140-1 do FIPS no tratamento de dados ou entrega de sistemas ou aplicações. Definimos os termos "FIPS 140-2 Level 1 compliant" e "FIPS 140-2 Level 1 compliance" usados na declaração acima para demonstrar a sua aplicabilidade pretendida ao uso do governo dos EUA e do Canadá do termo "FIPS 140-2 Nível 1 validado". 


### <a name="encrypt-data-in-transit"></a>Encriptar dados em trânsito

> Mencionado em: OSA Practice #6, ISO Control Family: Cryptography

Protege os seus dados enquanto os dados se movem entre o seu cliente e o servidor. Consulte a [Segurança da Rede](#network-security).

### <a name="encrypt-data-at-rest"></a>Encriptar dados inativos

> Mencionado em: OSA Practice #6, ISO Control Family: Cryptography

A encriptação em repouso é a proteção criptográfica dos dados quando é persistida na base de dados, registo e ficheiros de backup.

**Como implementar:**

- [A Encriptação transparente da Base de Dados (TDE)](transparent-data-encryption-azure-sql.md) com chaves geridas pelo serviço é ativada por padrão para quaisquer bases de dados criadas após 2017 na Base de Dados Azure SQL.
- Num caso gerido, se a base de dados for criada a partir de uma operação de restauro utilizando um servidor no local, a definição de TDE da base de dados original será honrada. Se a base de dados original não tiver TDE ativada, recomendamos que o TDE seja ligado manualmente para a instância gerida.

**Boas práticas:**

- Não guarde dados que exijam encriptação na base de dados principal. A base de dados principal não pode ser encriptada com TDE.

- Utilize as chaves geridas pelo cliente no Cofre chave Azure se necessitar de maior transparência e controlo granular sobre a proteção TDE. O Cofre chave Azure permite a capacidade de revogar permissões a qualquer momento para tornar a base de dados inacessível. Pode gerir centralmente os protetores TDE juntamente com outras teclas, ou rodar o protetor TDE na sua própria agenda utilizando o Cofre de Chaves Azure.

- Se estiver a utilizar chaves geridas pelo cliente no Cofre de Chaves Azure, siga os artigos, [diretrizes para configurar o TDE com](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) o Cofre de Chaves Azure e [como configurar o Geo-DR com](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde)o Cofre de Chaves Azure .

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteja dados sensíveis em uso de utilizadores altamente privilegiados e não autorizados

Os dados em uso são os dados armazenados em memória do sistema de base de dados durante a execução de consultas SQL. Se a sua base de dados armazenar dados confidenciais, a sua organização poderá ser obrigada a garantir que os utilizadores altamente privilegiados são impedidos de visualizar dados sensíveis na sua base de dados. Os utilizadores de alto privilégio, como os operadores da Microsoft ou dbAs na sua organização, devem ser capazes de gerir a base de dados, mas impedidos de visualizar e potencialmente exfiltrar dados sensíveis da memória do processo SQL Server ou consultando a base de dados.

As políticas que determinam quais os dados sensíveis e se os dados sensíveis devem ser encriptados na memória e não acessíveis aos administradores em texto simples, são específicos da sua organização e regulamentos de conformidade a que precisa aderir. Consulte o requisito relacionado: [Identificar e etiquetar dados sensíveis](#identify-and-tag-sensitive-data).

**Como implementar:**

- Utilize [sempre encriptado](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) para garantir que os dados sensíveis não sejam expostos em texto simples na Base de Dados Azure SQL, mesmo na memória/utilização. Sempre encriptado protege os dados de Administradores de Bases de Dados (DBAs) e administradores em nuvem (ou maus atores que podem personificar utilizadores privilegiados mas não autorizados) e dá-lhe mais controlo sobre quem pode aceder aos seus dados.

**Boas práticas:**

- Sempre encriptado não substitui encriptar dados em repouso (TDE) ou em trânsito (SSL/TLS). Sempre encriptado não deve ser usado para dados não sensíveis para minimizar o impacto de desempenho e funcionalidade. A utilização sempre encriptada em conjunto com a TDE e a Transport Layer Security (TLS) é recomendada para uma proteção abrangente dos dados em repouso, em trânsito e em uso. 

- Avalie o impacto da encriptação das colunas de dados sensíveis identificadas antes de ser implementado Sempre Encriptado numa base de dados de produção. Em geral, a Always Encrypted reduz a funcionalidade de consultas em colunas encriptadas e tem outras limitações, listadas em Detalhes de [Funcionalidades Sempre Encriptadas - Funcionalidades](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Por isso, poderá necessitar de rearchitectar a sua aplicação para reimplementar a funcionalidade, uma consulta não suporta, do lado do cliente ou/e refactor o esquema da sua base de dados, incluindo as definições de procedimentos, funções, visualizações e gatilhos armazenados. As aplicações existentes podem não funcionar com colunas encriptadas se não aderirem às restrições e limitações de Always Encrypted. Enquanto o ecossistema de ferramentas, produtos e serviços da Microsoft que suportam sempre encriptados está a crescer, alguns deles não funcionam com colunas encriptadas. Encriptar uma coluna também pode ter impacto no desempenho da consulta, dependendo das características da sua carga de trabalho. 

- Gerir as teclas sempre encriptadas com separação de funções se estiver a utilizar sempre encriptado para proteger dados de DBAs maliciosos. Com a separação de papéis, um administrador de segurança cria as chaves físicas. O DBA cria a chave principal da coluna e os objetos de encriptação da coluna, descrevendo as teclas físicas, na base de dados. Durante este processo, o administrador de segurança não precisa de acesso à base de dados, e o DBA não precisa de acesso às chaves físicas em texto simples. 
  - Consulte o artigo, [Managing Keys com Separação](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) de Papéis para mais detalhes. 

- Guarde as chaves principais da coluna no Cofre de Chaves Azure para facilitar a gestão. Evite utilizar a Windows Certificate Store (e, em geral, distribuisoluções de loja-chave, em oposição a soluções centrais de gestão de chaves) que dificultam a gestão das chaves. 

- Pense cuidadosamente através das trocas de utilização de várias teclas (chave principal da coluna ou chaves de encriptação da coluna). Mantenha o número de chaves pequenas para reduzir o custo de gestão da chave. Uma chave-mestre de colunas e uma chave de encriptação de uma coluna por base de dados é tipicamente suficiente em ambientes de estado estável (não no meio de uma rotação chave). Pode necessitar de chaves adicionais se tiver diferentes grupos de utilizadores, cada um usando chaves diferentes e acedendo a diferentes dados.  

- Rode as teclas principais da coluna de acordo com os seus requisitos de conformidade. Se também necessitar de rodar as teclas de encriptação da coluna, considere utilizar encriptação on-line para minimizar o tempo de inatividade da aplicação. 
  - Consulte o artigo, Considerações de [Desempenho e Disponibilidade.](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations) 

- Utilize encriptação determinística se as computações (igualdade) nos dados precisarem de ser suportadas. Caso contrário, utilize encriptação aleatória. Evite utilizar encriptação determinística para conjuntos de dados de baixa entropia ou conjuntos de dados com distribuição conhecida publicamente. 

- Se estiver preocupado com o acesso legal de terceiros aos seus dados sem o seu consentimento, certifique-se de que todas as aplicações e ferramentas que tenham acesso às chaves e dados em texto simples são executados fora do Microsoft Azure Cloud. Sem acesso às teclas, o terceiro não terá forma de desencriptar os dados a menos que contornem a encriptação.

- Sempre Encriptado não suporta facilmente a concessão de acesso temporário às teclas (e aos dados protegidos). Por exemplo, se precisar de partilhar as chaves com um DBA para permitir que o DBA faça algumas operações de limpeza em dados sensíveis e encriptados. A única forma de revogar o acesso aos dados do DBA será rodar as teclas de encriptação da coluna e as chaves-chave da coluna que protegem os dados, o que é uma operação dispendiosa. 

- Para aceder aos valores de texto simples em colunas encriptadas, um utilizador precisa de ter acesso à Chave Master da Coluna (CMK) que protege as colunas, que está configurada na loja-chave que detém a CMK. O utilizador também precisa de ter a DEFINIÇÃO DE **CHAVE MASTER DE COLUNA SI E** ver quaisquer permissões de base de dados DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE DEFINIÇÃO DE **TECLADEDeCÇÃO** DE ENCRIPTAÇÃO DE COLUNAS.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controlar o acesso dos utilizadores de aplicações a dados sensíveis através de encriptação

A encriptação pode ser usada como uma forma de garantir que apenas utilizadores específicos de aplicações que tenham acesso a chaves criptográficas possam visualizar ou atualizar os dados.

**Como implementar:**

- Utilize encriptação ao nível celular (CLE). Consulte o artigo, [Criptografe uma Coluna de Dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) para mais detalhes. 
- Use Sempre Encriptado, mas esteja ciente da sua limitação. As limitações estão listadas abaixo.

**Melhores práticas**

Ao utilizar o CLE:

- Controle o acesso às teclas através de permissões e funções SQL. 

- Utilize AES (Recomendado AES 256) para encriptação de dados. Os algoritmos, tais RC4, DES e TripleDES, são depreciados e não devem ser usados devido a vulnerabilidades conhecidas. 

- Proteja as teclas simétricas com chaves/certificados assimétricos (não palavras-passe) para evitar a utilização de 3DES. 

- Tenha cuidado ao migrar uma base de dados utilizando encriptação de nível celular através da exportação/importação (ficheiros bacpac). 
  - Consulte o artigo, [Recomendações para usar encriptação de nível celular na Base de Dados Azure SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) sobre como evitar a perda de chaves ao migrar dados, e para outras orientações de boas práticas.

Tenha em mente que a Always Encrypted foi concebida principalmente para proteger dados sensíveis em uso de utilizadores de alto privilégio da Base de Dados Azure SQL (operadores de nuvem, DBAs) - consulte [Proteger dados sensíveis em uso de utilizadores altamente privilegiados e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Esteja atento aos seguintes desafios ao utilizar Sempre Encriptado para proteger dados dos utilizadores da aplicação:

- Por padrão, todos os controladores de clientes da Microsoft que suportam Sempre Encriptado mantêm uma cache global (uma por aplicação) de chaves de encriptação de colunas. Uma vez que um condutor cliente adquire uma chave de encriptação de coluna de texto simples contactando uma loja de chaves que segura uma chave master de coluna, a chave de encriptação da coluna de texto simples está em cache. Isto torna os dados isolados dos utilizadores de uma aplicação multiutilizador desafiante. Se a sua aplicação personifica os utilizadores finais ao interagir com uma loja chave (como o Cofre de Chaves Azure), depois de a consulta de um utilizador povoar a cache com uma chave de encriptação de coluna, uma consulta subsequente que requer a mesma chave mas que é desencadeada por outro utilizador utilizará a chave em cache. O controlador não liga para a loja de chaves e não verifica se o segundo utilizador tem permissão para aceder à chave de encriptação da coluna. Como resultado, o utilizador pode ver os dados encriptados mesmo que o utilizador não tenha acesso às teclas. Para alcançar o isolamento dos utilizadores dentro de uma aplicação multiutilizador, pode desativar o cache da chave de encriptação da coluna. A desativação do cache causará despesas adicionais de desempenho, uma vez que o controlador terá de contactar a loja-chave para cada operação de encriptação ou desencriptação de dados.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteja os dados contra a visualização não autorizada pelos utilizadores da aplicação, preservando o formato de dados
Outra técnica para impedir que utilizadores não autorizados possam visualizar dados é obstar ou mascarar os dados, preservando os tipos e formatos de dados para garantir que as aplicações do utilizador podem continuar a manusear e exibir os dados.

**Como implementar:**

- Utilize máscara [de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) para obstinar colunas de tabelas.

> [!NOTE]
> Sempre encriptado não funciona com máscara de dados dinâmicos. Não é possível encriptar e mascarar a mesma coluna, o que implica que é necessário priorizar a proteção de dados no uso vs. mascarar os dados para os utilizadores da sua aplicação através de Máscara de Dados Dinâmicos.

**Boas práticas:**

> [!NOTE]
> A máscara de dados dinâmicos não pode ser usada para proteger dados de utilizadores de alto privilégio. As políticas de mascaramento não se aplicam aos utilizadores com acesso administrativo, como db_owner.

- Não permita que os utilizadores de aplicações executem consultas ad-hoc (pois podem ser capazes de trabalhar em torno da Máscara de Dados Dinâmicos).  
  - Consulte o artigo, contornar a [máscara utilizando inferência ou técnicas de força bruta](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) para obter detalhes.  

- Utilize uma política de controlo de acesso adequada (através de permissões, funções, RLS) para limitar as permissões dos utilizadores para efazer atualizações nas colunas mascaradas. Criar uma máscara numa coluna não impede atualizações dessa coluna. Os utilizadores que recebem dados mascarados ao consultar a coluna mascarada podem atualizar os dados se tiverem permissões de escrita.    

-  Mascaramento de Dados Dinâmicos não preserva as propriedades estatísticas dos valores mascarados. Isto pode ter impacto nos resultados da consulta (por exemplo, consultas que contenham predicados de filtragem ou se juntam aos dados mascarados).

## <a name="network-security"></a>Segurança da rede
A segurança da rede refere-se a controlos de acesso e às melhores práticas para proteger os seus dados em trânsito para a Base de Dados Azure SQL.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Configure o meu cliente para ligar de forma segura à Base de Dados Azure SQL 
As melhores práticas de como evitar que máquinas e aplicações de clientes com vulnerabilidades bem conhecidas (por exemplo, utilizando protocolos TLS mais antigos e suítes cifras) se conectem à Base de Dados Azure SQL.

**Como implementar:**

- Certifique-se de que as máquinas de clientes que ligam à Base de Dados SQL do Azure estão a utilizar a Segurança da Camada de [Transporte (TLS)](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Boas práticas:**

- Configure todas as suas aplicações e ferramentas para se conectar ao SQL DB com encriptação ativada 
  - Criptografia = On, TrustServerCertificate = Off (ou equivalente com controladores não Microsoft). 

- Se a sua aplicação utilizar um controlador que não suporta TLS ou suporte uma versão mais antiga do TLS, substitua o controlador, se possível. Se não for possível, avalie cuidadosamente os riscos de segurança. 

- Reduza os vetores de ataque através de vulnerabilidades em SSL 2.0, SSL 3.0, TLS 1.0 e TLS 1.1, desativando-os em máquinas de clientes que ligam à Base de Dados Azure SQL por definições de registo de Segurança da Camada de [Transporte (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10) 

- Consulte as suites cifra disponíveis no cliente: [Cipher Suites em TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel). Especificamente, desative 3DES por [Configuração De Cifra De Cifra TLS Encomenda](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)Suite . 

- Para a Base de Dados Azure SQL, a encriptação é executada para tipos de ligação Proxy e Redirect. Se estiver a utilizar uma instância gerida, utilize o tipo de ligação **Proxy** (predefinido) uma vez que isto impõe encriptação do lado do servidor. O tipo de ligação **Redirect** atualmente não suporta a aplicação da encriptação e está apenas disponível em ligações IP privadas. 

- Para mais informações, consulte [Azure SQL Connectivity Architecture - Política de Conexão](sql-database-connectivity-architecture.md#connection-policy).


### <a name="minimize-attack-surface"></a>Minimizar a superfície de ataque
Minimize o número de funcionalidades que podem ser atacadas por um utilizador malicioso. Implementar controlos de acesso à rede para a Base de Dados Azure SQL.

> Mencionado em: Osa Practice #5

**Como implementar:**

Num servidor de base de dados Azure SQL (contendo base de dados singleton ou piscinas elásticas):
- Definir permitir o acesso aos serviços do Azure para OFF.

- Utilize pontos finais do Serviço VNet e regras de firewall VNet.

- Utilize link privado (pré-visualização).

Num caso gerido:
- Siga as diretrizes nos [requisitos da Rede.](sql-database-managed-instance-connectivity-architecture.md#network-requirements) 

**Boas práticas:**

- Restringir o acesso à Base de Dados Azure SQL ligando-se num ponto final privado (por exemplo, utilizando uma via de dados privada): 
  - Uma instância gerida pode ser isolada dentro de um VNet para impedir o acesso externo. Aplicações e ferramentas que se encontram na mesma VNet ou na mesma região poderiam aceder-lhe diretamente. Aplicações e ferramentas que se encontram em diferentes regiões poderiam utilizar a ligação VNet-to-VNet ou o circuito ExpressRoute para estabelecer a ligação. O cliente deve usar os Grupos de Segurança da Rede (NSG) para restringir o acesso ao porto 1433 apenas a recursos que requerem acesso a uma instância gerida 
  - Para um servidor de base de dados SQL (contendo bases de dados únicas ou piscinas elásticas), utilize a funcionalidade [Private Link](sql-database-private-endpoint-overview.md) que fornece um IP privado dedicado para o servidor de base de dados SQL dentro do seu VNet. Também pode utilizar [pontos finais do VNet Service com regras](sql-database-vnet-service-endpoint-rule-overview.md) de firewall VNet para restringir o acesso aos seus servidores de Base de Dados SQL.
  - Os utilizadores móveis devem utilizar ligações VPN ponto-a-local para se conectarem através da trajetória de dados.
  - Os utilizadores ligados à sua rede no local devem utilizar a ligação VPN do local ou a ExpressRoute para se conectarem através da trajetória de dados.

- Pode aceder à Base de Dados Azure SQL ligando-se a um ponto final público (por exemplo, utilizando uma via de dados público). Devem ser consideradas as seguintes boas práticas: 
  - Para um servidor de base de dados SQL, utilize [regras de firewall IP](sql-database-firewall-configure.md) para restringir o acesso a apenas endereços IP autorizados.
  - Para uma instância gerida, utilize os Grupos de Segurança da Rede (NSG) para restringir o acesso ao porto 3342 apenas aos recursos necessários. Para mais informações, consulte Utilize uma base de [dados Azure SQL gerida de forma segura com pontos finais públicos](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Um ponto final de instância gerida não é ativado por defeito e deve ser ativado explicitamente. Se a política da empresa não for dada a utilização de pontos finais públicos, utilize a [Política Azure](../governance/policy/overview.md) para evitar, em primeiro lugar, a via final pública.

- Configurar componentes de rede Azure: 
  - Siga [as melhores práticas do Azure para a segurança da rede.](../security/fundamentals/network-best-practices.md)
  - Configuração da Rede Virtual do Plano (VNet) por boas práticas descritas na [Rede Virtual Azure frequentemente fez perguntas (FAQ)](../virtual-network/virtual-networks-faq.md) e planeia. 
  - Segmente um VNet em várias subredes e atribua recursos para um papel semelhante à mesma subnet (por exemplo, front-end vs recursos de back-end).
  - Utilize grupos de segurança de [rede (NSGs)](../virtual-network/security-overview.md) para controlar o tráfego entre as subredes dentro do limite Azure VNet.
  - Ative o [Vigilante da Rede Azure](../network-watcher/network-watcher-monitoring-overview.md) para a sua subscrição monitorizar o tráfego de rede de entrada e saída.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Configure Power BI para ligações seguras à Base de Dados Azure SQL

**Boas práticas:**

- Para o Power BI Desktop, utilize o caminho de dados privado sempre que possível. 

- Certifique-se de que o Power BI Desktop está a ligar-se utilizando o TLS1.2, definindo a chave de registo na máquina cliente de acordo com as definições de registo de Segurança da Camada de [Transporte (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 

- Restringir o acesso de dados a utilizadores específicos através de segurança de [nível de linha (RLS) com Power BI](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Para o Power BI Service, utilize a [porta de dados no local,](https://docs.microsoft.com/power-bi/service-gateway-onprem)tendo em conta [limitações e considerações](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Configure Serviço de Aplicações para ligações seguras à Base de Dados Azure SQL

**Boas práticas:**

- Para uma simples Aplicação Web, a ligação sobre o ponto final público requer a definição **de Serviços De Acesso Azure** à ON. 

- [Integre a sua aplicação com uma Rede Virtual Azure](../app-service/web-sites-integrate-with-vnet.md) para conectividade com o caminho de dados privados a uma instância gerida. Opcionalmente, também pode implementar uma Aplicação Web com Ambientes de Serviço de [Aplicações (ASE)](../app-service/environment/intro.md). 

- Para web app com Aplicação Web Integrada ASE ou VNet conectando-se a uma base de dados no servidor Base de Dados SQL, pode utilizar [pontos finais](sql-database-vnet-service-endpoint-rule-overview.md) do Serviço VNet e Regras de Firewall VNet para limitar o acesso a partir de uma VNet e subnet específicas. Em seguida, desempõe **os Serviços Azure.** Também pode ligar a ASE a uma instância gerida através de uma via de dados privada.  

- Certifique-se de que a sua Aplicação Web está configurada de acordo com o artigo, [as melhores práticas para garantir aplicações web e móveis PaaS utilizando](../security/security-paas-applications-using-app-services.md)o Serviço de Aplicações Azure . 

- Instale firewall de [aplicação web (WAF)](../application-gateway/waf-overview.md) para proteger a sua aplicação web de explorações e vulnerabilidades comuns.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Configure Azure VM hospedagem para ligações seguras à Base de Dados Azure SQL

**Boas práticas:**

- Utilize uma combinação de regras de Permitir e Negar sobre os NSGs de VMs Azure para controlar quais as regiões que podem ser acedidas a partir do VM.

- Certifique-se de que o seu VM está configurado de acordo com o artigo, as melhores práticas de segurança para cargas de [trabalho IaaS em Azure](../security/azure-security-iaas.md).

- Certifique-se de que todos os VMs estão associados a uma VNet e subnet específicas. 

- Avalie se precisa da rota padrão 0.0.0.0/Internet de acordo com a orientação [sobre a escavação forçada](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Se sim - por exemplo, sub-rede frontal - então mantenha a rota padrão.
  - Se não - por exemplo, sub-rede de nível médio ou back-end - permitir a escavação de força para que nenhum tráfego atravesse a Internet para chegar às instalações (também conhecido como cross-premises). 

- Implemente [as rotas de predefinição opcionais](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se estiver a utilizar o peering ou a ligar-se às instalações. 

- Implementar [rotas definidas](../virtual-network/virtual-networks-udr-overview.md#user-defined) pelo utilizador se precisar enviar todo o tráfego do VNet para um Aparelho Virtual de Rede para inspeção de pacotes. 

- Utilize [pontos finais do Serviço VNet](sql-database-vnet-service-endpoint-rule-overview.md) para acesso seguro a serviços PaaS como o Azure Storage através da rede de espinha dorsal Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Proteger contra ataques de negação de serviço distribuídos (DDoS)
Os ataques de Negação de Serviço Distribuídos (DDoS) são tentativas de um utilizador malicioso de enviar uma inundação de tráfego de rede para a Base de Dados Azure SQL com o objetivo de sobrecarregar a infraestrutura Azure e fazê-la rejeitar logins e carga de trabalho válidas.

> Mencionado em: Osa Practice #9

**Como implementar:**

A proteção DDoS é ativada automaticamente como parte da Plataforma Azure. Inclui a monitorização sempre on-traffic e a mitigação em tempo real dos ataques a nível da rede em pontos finais públicos. 

- Utilize a [Proteção Azure DDoS](../virtual-network/ddos-protection-overview.md) para monitorizar os endereços IP públicos associados aos recursos implantados em VNets.

- Utilize proteção avançada de ameaças para a base de [dados Azure SQL](sql-database-threat-detection-overview.md) para detetar ataques de Negação de Serviço (DoS) contra bases de dados.

**Boas práticas:**

- Seguir as práticas descritas na [Minimize Attack Surface](#minimize-attack-surface) ajuda a minimizar as ameaças de ataque do DDoS. 

- O alerta de proteção de ameaças avançada **sql** ajuda a detetar ataques de força bruta. Em alguns casos, o alerta pode até distinguir as cargas de trabalho de teste de penetração. 

- Para aplicações de hospedagem azure VM que liguem à Base de Dados SQL: 
  - Siga a recomendação para restringir o acesso através de pontos finais virados para a Internet no Centro de Segurança Azure. 
  - Utilize conjuntos de escala de máquinas virtuais para executar várias instâncias da sua aplicação em VMs Azure. 
  - Desative RDP e SSH da Internet para evitar ataques de força bruta. 

## <a name="monitoring-logging-and-auditing"></a>Monitorização, Exploração E Auditoria  
Esta secção refere-se a capacidades para ajudá-lo a detetar atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. Também descreve as melhores práticas para configurar a auditoria de bases de dados para rastrear e capturar eventos de base de dados.

### <a name="protect-databases-against-attacks"></a>Proteja as bases de dados contra ataques 
A proteção avançada de ameaças permite detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas.

**Como implementar:**

- Utilize proteção avançada contra ameaças para a [SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) para detetar tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados, incluindo:
  - Ataque de injeção SQL.
  - Credenciais roubo/fuga.
  - Abuso de privilégios.
  - Exfiltração de dados.

**Boas práticas:**

- Configure a [Segurança Avançada de Dados (ADS)](sql-database-advanced-data-security.md#getting-started-with-ads) para a Base de Dados Azure SQL para um servidor de base de dados SQL específico ou uma instância gerida. Também pode configurar ADS para todos os servidores de Base de Dados SQL e instâncias geridas numa subscrição, mudando para o nível Padrão do [Azure Security Center](../security-center/security-center-pricing.md). 

- Para uma experiência de investigação completa, é recomendado permitir a Auditoria da Base de [Dados SQL](sql-database-auditing.md). Com a auditoria, pode rastrear eventos de base de dados e escrevê-los num registo de auditoria numa conta de Armazenamento Azure ou no espaço de trabalho azure Log Analytics. 

### <a name="audit-critical-security-events"></a>Auditar eventos críticos de segurança
O rastreio de eventos de base de dados ajuda-o a compreender a atividade da base de dados. Pode obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança. Também permite e facilita a adesão às normas de conformidade. 

**Como implementar:**

- Ative a Auditoria de [Bases de Dados SQL](sql-database-auditing.md) para rastrear os eventos da base de dados e escrevê-los num registo de auditoria na sua conta de Armazenamento Azure, espaço de trabalho log Analytics (pré-visualização) ou Hubs de Eventos (pré-visualização). 

- Os registos de auditoria podem ser escritos numa conta de Armazenamento Azure, num espaço de trabalho de Log Analytics para consumo por registos do Azure Monitor ou para o centro de eventos para consumo através do hub de eventos. Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um. 

**Boas práticas:**

- Ao configurar a Auditoria de Bases de [Dados SQL](sql-database-auditing.md) no seu servidor de base de dados para auditar eventos, todas as bases de dados existentes e recém-criadas nesse servidor serão auditadas.
- Por política de auditoria por defeito inclui todas as ações (consultas, procedimentos armazenados e logins bem sucedidos e falhados) contra as bases de dados, o que pode resultar num elevado volume de registos de auditoria. É recomendado para os clientes [configurar auditoria para diferentes tipos de ações e grupos de ação usando powerShell](sql-database-auditing.md#manage-auditing). Configurar isto ajudará a controlar o número de ações auditadas e minimizará o risco de perda de eventos. A configuração de auditoria personalizada permite que os clientes capturem apenas os dados de auditoria necessários.
- Os registos de auditoria podem ser consumidos diretamente no [portal Azure,](https://portal.azure.com/)ou a partir do local de armazenamento que foi configurado. 


> [!NOTE]
> Permitir a auditoria ao Log Analytics incorrerá em custos baseados em taxas de ingestão. Tenha em conta o custo associado com a utilização desta [opção,](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os registos de auditoria numa conta de armazenamento Azure. 

**Outros recursos:**

- [Auditoria de Base de Dados SQL](sql-database-auditing.md)
- [Auditoria do Servidor SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Registos de auditoria seguros
Restringir o acesso à conta de armazenamento para apoiar a separação de direitos e separar a DBA dos Auditores. 

**Como implementar:**

- Ao guardar registos de auditoria para o Armazenamento Azure, certifique-se de que o acesso à Conta de Armazenamento está restrito aos princípios mínimos de segurança. Controle quem tem acesso à conta de armazenamento.
    - Para mais informações, consulte [Autorizar o acesso ao Armazenamento Azure.](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

**Boas práticas:**

- Controlar o acesso ao alvo de auditoria é um conceito fundamental na separação do DBA dos Auditores. 

- Ao auditar o acesso a dados sensíveis, considere proteger os dados com encriptação de dados para evitar fugas de informação ao Auditor. Para mais informações, consulte a secção [Proteja os dados sensíveis em uso de utilizadores altamente privilegiados e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Gestão de Segurança

Esta secção descreve os diferentes aspetos e as melhores práticas para gerir a postura de segurança das suas bases de dados. Inclui as melhores práticas para garantir que as suas bases de dados estão configuradas para cumprir os padrões de segurança, para descobrir e para classificar e rastrear o acesso a dados potencialmente sensíveis nas suas bases de dados. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Certifique-se de que as bases de dados estão configuradas para atender às melhores práticas de segurança 

Melhorar proativamente a sua segurança na base de dados, descobrindo e remediando potenciais vulnerabilidades de bases de dados.

**Como implementar:**

- Ative a [Avaliação de Vulnerabilidade sQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) para digitalizar a sua base de dados para problemas de segurança e para executar automaticamente nas suas bases de dados.

**Boas práticas:**

- Inicialmente, execute va nas suas bases de dados e iterar através da remediação de verificações falhadas que se opõem às melhores práticas de segurança. Configurar as linhas de base para configurações aceitáveis até que a varredura saia _limpa,_ ou todos os controlos já passaram.  

- Configure exames periódicos recorrentes para executar uma vez por semana e configure a pessoa relevante para receber e-mails sumários. 

- Reveja o resumo do VA após cada digitalização semanal. Para quaisquer vulnerabilidades encontradas, avalie a deriva do resultado da varredura anterior e determine se o cheque deve ser resolvido. Reveja se houver uma razão legítima para a mudança de configuração.   

- Resolver verificações e atualizar as linhas de base, se for caso disso. Crie itens de bilhete para resolver ações e rastreie-os até que sejam resolvidos. 

**Outros recursos:**

- [Avaliação de Vulnerabilidades do SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [O serviço de Avaliação de VulnerabilidadeS SQL ajuda-o a identificar vulnerabilidades na base de dados](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificar e etiquetar dados sensíveis 

Descubra colunas que possam conter dados sensíveis. O que é considerado dados sensíveis depende fortemente do cliente, regulação de conformidade, etc., e precisa de ser avaliado pelos utilizadores responsáveis por esses dados. Classifique as colunas para utilizar cenários avançados de auditoria e proteção baseados na sensibilidade. 

**Como implementar:**

- Utilize a [SQL Data Discovery and Classification](sql-database-data-discovery-and-classification.md) para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados. 
  - Veja as recomendações de classificação que são criadas pela descoberta automatizada no painel de deteção e classificação de dados SQL. Aceite as classificações relevantes, de modo a que os seus dados sensíveis sejam persistentemente marcados com etiquetas de classificação. 
  - Adicione manualmente classificações para quaisquer campos de dados sensíveis adicionais que não tenham sido descobertos pelo mecanismo automatizado. 
- Para mais informações, consulte [a SQL Data Discovery & Classification](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Boas práticas:**

- Monitorize regularmente o painel de classificação para uma avaliação exata do estado de classificação da base de dados. Um relatório sobre o estado de classificação da base de dados pode ser exportado ou impresso para partilhar para efeitos de conformidade e auditoria.

- Monitorize continuamente o estado dos dados sensíveis recomendados na Avaliação de Vulnerabilidade SQL. Acompanhe a regra de descoberta de dados sensíveis e identifique qualquer deriva nas colunas recomendadas para classificação.  

- Use a classificação de uma forma adaptada às necessidades específicas da sua organização. Personalize a sua política de Proteção de Informação (etiquetas de sensibilidade, tipos de informação, lógica de descoberta) na política [de proteção de informação SQL](../security-center/security-center-info-protection-policy.md) no Azure Security Center. 

### <a name="track-access-to-sensitive-data"></a>Acompanhar o acesso a dados sensíveis 
Monitorize quem acede a dados sensíveis e capture consultas sobre dados sensíveis em registos de auditoria.

**Como implementar:**

- Utilize a auditoria SQL e a classificação de dados em combinação. 
  - No seu registo de auditoria de base de [dados SQL,](sql-database-auditing.md) pode rastrear o acesso especificamente a dados sensíveis. Também pode ver informações como os dados a que foram acedidos, bem como o seu rótulo de sensibilidade. Para mais informações, consulte a [Data Discovery & Classificação](sql-database-data-discovery-and-classification.md) e [Auditoria de acesso a dados sensíveis](sql-database-data-discovery-and-classification.md#audit-sensitive-data). 

**Boas práticas:**

- Consulte as melhores práticas para as secções de Auditoria e Classificação de Dados: 
  - [Auditar eventos críticos de segurança](#audit-critical-security-events) 
  - [Identificar e etiquetar dados sensíveis](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualizar o estado de segurança e conformidade 

Utilize um sistema unificado de gestão de segurança de infraestruturas que fortaleça a postura de segurança dos seus centros de dados (incluindo bases de dados SQL). Consulte uma lista de recomendações relativas à segurança das suas bases de dados e ao estado de conformidade.

**Como implementar:**

- Monitorize recomendações de segurança relacionadas com a SQL e ameaças ativas no Centro de [Segurança Azure](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Ameaças comuns à segurança e potenciais atenuações

Esta secção ajuda-o a encontrar medidas de segurança para proteger contra certos vetores de ataque. Espera-se que a maioria das mitigações possam ser alcançadas seguindo uma ou mais das diretrizes de segurança acima.

### <a name="security-threat-data-exfiltration"></a>Ameaça à segurança: Exfiltração de dados

A exfiltração de dados é a cópia, transferência ou recuperação não autorizada de dados de um computador ou servidor. Consulte uma definição para [exfiltração](https://en.wikipedia.org/wiki/Data_exfiltration) de dados na Wikipédia.

A ligação ao servidor de base de dados Azure SQL sobre um ponto final público apresenta um risco de exfiltração de dados, uma vez que exige que os clientes abram as suas firewalls a IPs públicos.  

**Cenário 1**: Uma aplicação num VM Azure liga-se a uma base de dados num servidor de base de dados Azure SQL. Um ator desonesto tem acesso ao VM e compromete-o. Neste cenário, a exfiltração de dados significa que uma entidade externa que utiliza o VM fraudulento conecta-se à base de dados, copia dados pessoais e armazena-os num armazenamento blob ou numa base de dados SQL diferente numa subscrição diferente.

**Cenário 2**: Um DBA rouge. Este cenário é muitas vezes levantado por clientes sensíveis à segurança de indústrias regulamentadas. Neste cenário, um utilizador de alto privilégio poderá copiar dados da Base de Dados Azure SQL para outra subscrição não controlada pelo proprietário de dados.

**Potenciais atenuações:**

Hoje, a Azure SQL Database oferece as seguintes técnicas para atenuar ameaças de exfiltração de dados: 

- Utilize uma combinação de regras de Permitir e Negar sobre os NSGs de VMs Azure para controlar quais as regiões que podem ser acedidas a partir do VM. 
- Se utilizar um servidor de base de dados Azure SQL (contendo bases de dados singleton ou piscinas elásticas), detete as seguintes opções:
  - Permitir que os Serviços Azure se desliguem.
  - Apenas permita o tráfego da subnet contendo o seu VM Azure, configurando uma regra vNet Firewall.
  - Use [link privado](sql-database-private-endpoint-overview.md)
- Para uma instância gerida, a utilização de acesso IP privado por endereços predefinidos a primeira preocupação de exfiltração de dados de um VM fraudulento. Ligue a característica da delegação da sub-rede numa subnet a fixar automaticamente a política mais restritiva numa subrede de instância gerida.
- A preocupação da Rogue DBA está mais exposta com um caso gerido, uma vez que tem uma área de superfície maior e os requisitos de networking são visíveis para os clientes. A melhor mitigação para isso é aplicar todas as práticas neste guia de segurança para evitar o cenário de DBA desonesto em primeiro lugar (não só para exfiltração de dados). Sempre encriptado é um método para proteger dados sensíveis encriptando-os e mantendo a chave inacessível para o DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspetos de segurança da continuidade e disponibilidade do negócio

A maioria das normas de segurança aborda maçada de dados em termos de continuidade operacional, conseguida através da implementação de capacidades de despedimento e falha para evitar pontos únicos de falha. Para cenários de desastres, é uma prática comum manter cópias de segurança de Ficheiros de Dados e Registos.A secção seguinte fornece uma visão geral de alto nível das capacidades incorporadas em Azure. Também fornece opções adicionais que podem ser configuradas para atender às necessidades específicas: 

- Azure oferece alta disponibilidade incorporada: Alta disponibilidade e Base de [Dados Azure SQL](sql-database-high-availability.md) 

- O nível Business Critical inclui grupos de failover, zonas de multidisponibilidade, backups de registo completos e diferenciais e backups de restauração ponto-no-tempo ativados por padrão:  
  - [Base de dados Azure SQL de alta disponibilidade e SQL - Configuração redundante da zona](sql-database-high-availability.md#zone-redundant-configuration)
  - [Cópias de segurança automatizadas](sql-database-automated-backups.md)
  - [Recuperar uma Base de Dados SQL Azure utilizando cópias de dados automatizadas - restauro ponto-a-tempo](sql-database-recovery-using-backups.md#point-in-time-restore)

- Funcionalidades adicionais de continuidade do negócio, tais como grupos de falha automática em diferentes geos do Azure, podem ser configuradas como descrito aqui: [Visão geral da continuidade do negócio com base de dados Azure SQL](sql-database-business-continuity.md)

## <a name="next-steps"></a>Passos seguintes

- Consulte [uma visão geral das capacidades de segurança da Base de Dados Azure SQL](sql-database-security-overview.md)
