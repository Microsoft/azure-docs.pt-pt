---
title: Livro de jogadas para abordar requisitos comuns de segurança
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Este artigo fornece requisitos de segurança comuns e boas práticas na Base de Dados Azure SQL e na Azure SQL Managed Instance.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=2
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 09/21/2020
ms.reviewer: ''
ms.openlocfilehash: 5d83f6585500316515139f937a56889dfc1f8fac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642702"
---
# <a name="playbook-for-addressing-common-security-requirements-with-azure-sql-database-and-azure-sql-managed-instance"></a>Livro de jogadas para abordar requisitos comuns de segurança com base de dados Azure SQL e Azure SQL Gestão De Instância
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo fornece as melhores práticas sobre como resolver os requisitos comuns de segurança. Nem todos os requisitos são aplicáveis a todos os ambientes, e deverá consultar a sua base de dados e a sua equipa de segurança sobre quais as funcionalidades a implementar.

## <a name="solving-common-security-requirements"></a>Resolução de requisitos comuns de segurança

Este documento fornece orientações sobre como resolver requisitos de segurança comuns para aplicações novas ou existentes usando a Base de Dados Azure SQL e Azure SQL Managed Instance. É organizado por áreas de segurança de alto nível. Para abordar ameaças específicas, consulte a secção de ameaças à [segurança comum e a potenciais mitigações.](#common-security-threats-and-potential-mitigations) Embora algumas das recomendações apresentadas sejam aplicáveis quando as aplicações migratórias de no local para Azure, os cenários de migração não são o foco deste documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Ofertas de implementação de bases de dados Azure SQL abrangidas por este guia

- [Base de Dados Azure SQL](./index.yml): [bases de dados individuais](single-database-overview.md) e [piscinas elásticas](elastic-pool-overview.md) em [servidores](logical-servers.md)
- [Instância Gerida do SQL no Azure](../managed-instance/sql-managed-instance-paas-overview.md)

### <a name="deployment-offers-not-covered-in-this-guide"></a>Ofertas de implantação não abrangidas por este guia

- Azure Synapse Analytics
- VMs Azure SQL (IaaS)
- SQL Server

### <a name="audience"></a>Audiência

Os públicos pretendidos para este guia são os clientes que enfrentam questões sobre como garantir a Base de Dados Azure SQL. As funções interessadas neste artigo de boas práticas incluem, mas não se limitando a:

- Arquitetos de Segurança
- Gestores de Segurança
- Agentes de Conformidade
- Oficiais de Privacidade
- Engenheiros de Segurança

### <a name="using-this-guide"></a><a id="using"></a> Usando este guia

Este documento destina-se a ser um companheiro da nossa documentação de segurança existente [na Base de Dados Azure SQL.](security-overview.md)

Salvo indicação em contrário, recomendamos que siga todas as melhores práticas enumeradas em cada secção para alcançar o objetivo ou requisito respetivo. Para cumprir normas específicas de conformidade com a segurança ou as melhores práticas, são listados controlos de conformidade regulamentares importantes na secção Requisitos ou Objetivos, sempre que aplicável. Estas são as normas e regulamentos de segurança que são referenciados neste artigo:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): CM-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): Controlo de Acesso, Criptografia
- [Práticas de Segurança Operacional da Microsoft (OSA):](https://www.microsoft.com/securityengineering/osa/practices)Prática #1-6 e #9
- [NIST Publicação Especial 800-53 Controlos de Segurança](https://nvd.nist.gov/800-53): AC-5, AC-6
- [DCI:](https://www.pcisecuritystandards.org/document_library)6.3.2, 6.4.2

Pretendemos continuar a atualizar as recomendações e as melhores práticas aqui enumeradas. Forneça a entrada ou quaisquer correções para este documento utilizando o link **Feedback** na parte inferior deste artigo.

## <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o utilizador é quem diz ser. Azure SQL Database e SQL Managed Instance suportam dois tipos de autenticação:

- Autenticação do SQL
- Autenticação do Azure Active Directory

> [!NOTE]
> A autenticação do Azure Ative Directory não pode ser suportada para todas as ferramentas e aplicações de terceiros.

### <a name="central-management-for-identities"></a>Gestão central das identidades

A gestão central da identidade oferece os seguintes benefícios:

- Gerencie as contas do grupo e controle as permissões dos utilizadores sem duplicar os logins em servidores, bases de dados e instâncias geridas.
- Gestão de permissões simplificadas e flexíveis.
- Gestão de aplicações em escala.

**Como implementar:**

- Utilize a autenticação do Azure Ative Directory (Azure AD) para gestão centralizada de identidade.

**Boas práticas:**

- Crie um inquilino AZure AD e [crie utilizadores](../../active-directory/fundamentals/add-users-azure-active-directory.md) para representar utilizadores humanos e criar [diretores de serviços](../../active-directory/develop/app-objects-and-service-principals.md) para representar apps, serviços e ferramentas de automação. Os principais serviços são equivalentes às contas de serviço no Windows e linux.

- Atribuir direitos de acesso aos recursos aos principais da Azure AD através da atribuição de grupos: Criar grupos Azure AD, conceder acesso a grupos e adicionar membros individuais aos grupos. Na sua base de dados, crie utilizadores de bases de dados contidos que mapeiem os seus grupos AD Azure. Para atribuir permissões dentro da base de dados, coloque os utilizadores associados aos seus grupos AZURE AD em funções de base de dados com as permissões apropriadas.
  - Consulte os artigos, [Configuure e gerencie a autenticação do Azure Ative Directory com SQL](authentication-aad-configure.md) e [Use Azure AD para autenticação com SQL](authentication-aad-overview.md).
  > [!NOTE]
  > Em SQL Managed Instance, também pode criar logins que mapeiam para os principais AD do Azure na base de dados principal. Consulte [O LOGIN CREATE (Transact-SQL)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

- A utilização de grupos AD Azure simplifica a gestão da permissão e tanto o proprietário do grupo, como o proprietário do recurso pode adicionar/remover membros de/para o grupo.

- Crie um grupo separado para administradores AD Azure para cada servidor ou instância gerida.

  - Consulte o artigo, [Provision a Azure Ative Directory para o seu servidor](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

- Monitor Azure AD altera alterações de membros do grupo usando relatórios de atividade de auditoria Azure AD.

- Para um caso gerido, é necessário um passo separado para criar um administrador AD Azure.
  - Consulte o artigo, [Provision a Azure Ative Directory para a sua instância gerida](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

> [!NOTE]
>
> - A autenticação Azure AD é registada em registos de auditoria Azure SQL, mas não em registos de login Azure AD.
> - As permissões Azure RBAC concedidas em Azure não se aplicam a permissões de Azure SQL Database ou SQL Managed Instance. Tais permissões devem ser criadas/mapeadas manualmente utilizando permissões SQL existentes.
> - Do lado do cliente, a autenticação AZure AD necessita de acesso à internet ou via Rota Definida pelo Utilizador (UDR) a uma rede virtual.
> - O token de acesso Azure AD está em cache no lado do cliente e a sua vida útil depende da configuração simbólica. Veja o artigo, [Configurar vidas simbólicas no Azure Ative Directory](../../active-directory/develop/active-directory-configurable-token-lifetimes.md)
> - Para obter orientações sobre problemas de resolução de problemas Azure AD Authentication, consulte o seguinte blog: [Troubleshooting Azure AD](https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991).

### <a name="azure-ad-multi-factor-authentication"></a>Multi-Factor Authentication do Azure AD

> Mencionado em: Osa Practice #2, ISO Access Control (AC)

A autenticação multi-factor Azure AD ajuda a fornecer segurança adicional, exigindo mais do que uma forma de autenticação.

**Como implementar:**

- [Ativar a autenticação multi-factor](../../active-directory/authentication/concept-mfa-howitworks.md) em Ad Azure utilizando o Acesso Condicional e utilizar a autenticação interativa.

- A alternativa é ativar a autenticação multi-factor para todo o domínio AD ou AD AZure.

**Boas práticas:**

- Ativar o Acesso Condicional em AD Azure (requer subscrição Premium).
  - Consulte o artigo, [Acesso Condicional em Azure AD](../../active-directory/conditional-access/overview.md).  

- Crie grupos AD Azure e ative a política de autenticação multi-factor para grupos selecionados que utilizem o Acesso Condicional AD Azure.
  - Consulte o artigo, [Plan Conditional Access Deployment](../../active-directory/conditional-access/plan-conditional-access.md).

- A Autenticação Multi-Factor pode ser ativada para todo o AD Azure ou para todo o Ative Directory federado com Azure AD.

- Utilize o modo de autenticação interativa Azure AD para a Base de Dados Azure SQL e a exemplo gerida do Azure SQL onde uma palavra-passe é solicitada interativamente, seguida de Autenticação Multi-Factor:
  - Utilize autenticação universal em SSMS. Consulte o artigo, utilizando a [autenticação Azure AD multi-factor com base de dados Azure SQL, SQL Managed Instance, Azure Synapse (suporte SSMS para autenticação multi-factor)](authentication-mfa-ssms-overview.md).
  - Utilize autenticação interativa suportada em Ferramentas de Dados do Servidor SQL (SSDT). Consulte o artigo, [suporte ao Diretório Ativo Azure em Ferramentas de Dados do Servidor SQL (SSDT)](/sql/ssdt/azure-active-directory?view=azuresqldb-current&preserve-view=true).
  - Utilize outras ferramentas SQL que suportem a autenticação multi-factor.
    - Suporte ao assistente SSMS para base de dados de exportação/extração/implantação  
    - [sqlpackage.exe: ](/sql/tools/sqlpackage)opção '/ua'
    - [sqlcmd Utility](/sql/tools/sqlcmd-utility): opção -G (interativo)
    - [bcp Utilitário](/sql/tools/bcp-utility): opção -G (interativo)

- Implemente as suas aplicações para ligar à Base de Dados Azure SQL ou à Azure SQL Managed Instance utilizando a autenticação interativa com suporte à autenticação multi-factor.
  - Consulte o artigo, [Ligue-se à Base de Dados Azure SQL com autenticação multi-factor Azure AD](active-directory-interactive-connect-azure-sql-db.md).
  > [!NOTE]
  > Este modo de autenticação requer identidades baseadas no utilizador. Nos casos em que seja utilizado um modelo de identidade fidedigno que esteja a contornar a autenticação individual do utilizador Azure AD (por exemplo, utilização de identidade gerida para recursos Azure), a Autenticação Multi-Factor não se aplica.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimizar a utilização da autenticação baseada em palavras-passe para os utilizadores

> Mencionado em: Osa Practice #4, ISO Access Control (AC)

Os métodos de autenticação baseados em palavras-passe são uma forma mais fraca de autenticação. As credenciais podem ser comprometidas ou erroneamente dadas.

**Como implementar:**

- Utilize uma autenticação integrada AZURE AD que elimine o uso de palavras-passe.

**Boas práticas:**

- Utilize uma única autenticação de sinalização utilizando credenciais do Windows. Federar o domínio AD no local com Azure AD e utilizar a autenticação integrada do Windows (para máquinas de domínio com Azure AD).
  - Consulte o artigo, [suporte SSMS para autenticação integrada Azure AD](authentication-aad-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimizar a utilização da autenticação baseada em palavras-passe para aplicações

> Mencionado em: Osa Practice #4, ISO Access Control (AC)

**Como implementar:**

- Ativar a identidade gerida do Azure. Também pode utilizar a autenticação integrada ou baseada em certificados.

**Boas práticas:**

- Utilize [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).
  - [Identidade gerida atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)
  - [Identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Utilize a Base de Dados Azure SQL do Azure App Service com identidade gerida (sem alterações de código)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Utilize a autenticação baseada em cert para uma aplicação.
  - Consulte esta [amostra de código.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token)

- Utilize a autenticação Azure AD para o domínio federado integrado e para a máquina de união de domínios (ver secção acima).
  - Consulte o [pedido de amostra para autenticação integrada.](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated)

### <a name="protect-passwords-and-secrets"></a>Proteja senhas e segredos

Para casos em que as palavras-passe não são evitáveis, certifique-se de que estão seguras.

**Como implementar:**

- Use o Cofre de Chaves Azure para armazenar senhas e segredos. Sempre que aplicável, utilize a Autenticação Multi-Factor para Azure SQL Database com utilizadores AZure AD.

**Boas práticas:**

- Se evitar palavras-passe ou segredos não for possível, guarde senhas de utilizador e segredos de aplicação no Cofre de Chaves Azure e gere o acesso através das políticas de acesso do Key Vault.

- Vários quadros de desenvolvimento de aplicações também podem oferecer mecanismos específicos para proteger segredos na aplicação. Por exemplo: [ASP.NET aplicação core](/aspnet/core/security/app-secrets?tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Utilize a autenticação SQL para aplicações antigas

A autenticação SQL refere-se à autenticação de um utilizador ao ligar-se à Base de Dados Azure SQL ou à SQL Managed Instance utilizando o nome de utilizador e a palavra-passe. Um login terá de ser criado em cada servidor ou instância gerida, e um utilizador criado em cada base de dados.

**Como implementar:**

- Utilize a autenticação SQL.

**Boas práticas:**

- Como administrador de servidor ou exemplo, crie logins e utilizadores. A menos que utilizem utilizadores de bases de dados contidos com palavras-passe, todas as palavras-passe são armazenadas na base de dados principal.
  - Consulte o artigo, [Controlando e concedendo acesso à base de dados SQL, SQL Managed Instance e Azure Synapse Analytics](logins-create-manage.md).

## <a name="access-management"></a>Gestão de acesso

A gestão de acessos (também denominada Autorização) é o processo de controlo e gestão do acesso e privilégios dos utilizadores autorizados à Base de Dados Azure SQL ou à SQL Managed Instance.

### <a name="implement-principle-of-least-privilege"></a>Implementar princípio de menor privilégio

> Mencionado em: FedRamp controla AC-06, NIST: AC-6, OSA Practice #3

O princípio do menor privilégio diz que os utilizadores não devem ter mais privilégios do que o necessário para completar as suas tarefas. Para mais informações, consulte o artigo [Administração suficiente.](/powershell/scripting/learn/remoting/jea/overview)

**Como implementar:**

Atribua apenas as [permissões necessárias](/sql/relational-databases/security/permissions-database-engine) para completar as tarefas necessárias:

- Nas bases de dados SQL:
  - Utilize permissões granulares e funções de base de dados definidas pelo utilizador (ou funções de servidor em Instância Gerida):
    1. Criar as funções necessárias
       - [CRIAR PAPEL](/sql/t-sql/statements/create-role-transact-sql)
       - [CRIAR O PAPEL DO SERVIDOR](/sql/t-sql/statements/create-server-role-transact-sql)
    1. Criar utilizadores necessários
       - [CRIAR UTILIZADOR](/sql/t-sql/statements/create-user-transact-sql)
    1. Adicionar os utilizadores como membros a funções
       - [ALTERAR PAPEL](/sql/t-sql/statements/alter-role-transact-sql)
       - [ALTERAR O PAPEL DO SERVIDOR](/sql/t-sql/statements/alter-server-role-transact-sql)
    1. Em seguida, atribuir permissões para papéis.
       - [GRANT](/sql/t-sql/statements/grant-transact-sql)
  - Certifique-se de que não atribui os utilizadores a funções desnecessárias.

- Em Azure Resource Manager:
  - Utilize funções incorporadas se estiver disponível ou a azure funções personalizadas e atribuir as permissões necessárias.
    - [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
    - [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)

**Boas práticas:**

As seguintes boas práticas são opcionais, mas resultarão numa melhor gestão e suporte da sua estratégia de segurança:

- Se possível, comece com o conjunto de permissões menos possível e comece a adicionar permissões uma a uma se houver uma necessidade real (e justificação) – ao contrário da abordagem oposta: tirar permissões passo a passo.

- Abstenha-se de atribuir permissões a utilizadores individuais. Utilize as funções (bases de dados ou funções de servidor) de forma consistente. As funções ajudam muito com as permissões de relato e resolução de problemas. (O Azure RBAC só suporta a atribuição de permissão através de funções.)

- Criar e utilizar funções personalizadas com as permissões exatas necessárias. Funções típicas que são usadas na prática:
  - Implantação de segurança
  - Administrador
  - Programador
  - Pessoal de apoio
  - Auditor
  - Processos automatizados
  - Utilizador final
  
- Utilize funções incorporadas apenas quando as permissões das funções corresponderem exatamente às permissões necessárias para o utilizador. Pode atribuir utilizadores a múltiplas funções.

- Lembre-se que as permissões no motor da base de dados podem ser aplicadas nos seguintes âmbitos (quanto menor for o âmbito, menor o impacto das permissões concedidas):
  - Servidor (funções especiais na base de dados principal) em Azure
  - Base de Dados
  - Esquema
    - É uma boa prática usar esquemas para conceder permissões dentro de uma base de dados. (ver também: [Schema-design: Recomendações para o desenho de Schema com segurança em mente)](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/)
  - Objeto (tabela, vista, procedimento, etc.)

  > [!NOTE]
  > Não é aconselhável aplicar permissões ao nível do objeto porque este nível adiciona complexidade desnecessária à implementação global. Se decidir utilizar permissões de nível de objetos, estas devem ser claramente documentadas. O mesmo se aplica às permissões de nível de coluna, que são ainda menos recomendáveis pelas mesmas razões. Esteja também ciente de que, por defeito, um [DENY](/sql/t-sql/statements/deny-object-permissions-transact-sql) de nível de tabela não substitui uma subvenção de nível de coluna. Isto exigiria que a [configuração do servidor de conformidade de conformidade comum](/sql/database-engine/configure-windows/common-criteria-compliance-enabled-server-configuration-option) fosse ativada.

- Efetue verificações regulares usando [a Avaliação de Vulnerabilidade (VA)](/sql/relational-databases/security/sql-vulnerability-assessment) para testar demasiadas permissões.

### <a name="implement-separation-of-duties"></a>Implementar Separação de Deveres

> Mencionado em: FedRamp: AC-04, NIST: AC-5, ISO: A.6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A Separação de Deveres, também denominada Segregação de Deveres, descreve a exigência de dividir tarefas sensíveis em múltiplas tarefas que são atribuídas a diferentes utilizadores. A separação de deveres ajuda a prevenir violações de dados.

**Como implementar:**

- Identificar o nível exigido de Separação de Deveres. Exemplos:
  - Entre ambientes de desenvolvimento/teste e produção
  - Tarefas sensíveis em termos de segurança vs Tarefas de nível de gestão do Administrador de Base de Dados (DBA) vs tarefas de desenvolvedor.
    - Exemplos: Auditor, criação de política de segurança para segurança de nível de função (RLS), implementação de objetos de base de dados SQL com permissões DDL.

- Identifique uma hierarquia abrangente de utilizadores (e processos automatizados) que acedam ao sistema.

- Criar funções de acordo com os grupos de utilizador necessários e atribuir permissões a funções.
  - Para tarefas de nível de gestão no portal Azure ou através da automação PowerShell utilize funções Azure. Ou encontra uma função incorporada que corresponda ao requisito, ou crie uma função personalizada Azure usando as permissões disponíveis
  - Criar funções de Servidor para tarefas em todo o servidor (criando novos logins, bases de dados) num caso gerido.
  - Criar funções de base de dados para tarefas ao nível da base de dados.

- Para determinadas tarefas sensíveis, considere a criação de procedimentos especiais armazenados assinados por um certificado para executar as tarefas em nome dos utilizadores. Uma vantagem importante dos procedimentos armazenados assinados digitalmente é que, se o procedimento for alterado, as permissões concedidas à versão anterior do procedimento são imediatamente eliminadas.
  - Exemplo: [Tutorial: Assinatura de Procedimentos Armazenados com Certificado](/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate)

- Implementar encriptação de dados transparentes (TDE) com chaves geridas pelo cliente no Cofre da Chave Azure para permitir a separação de direitos entre o proprietário de dados e o proprietário de segurança.
  - Consulte o artigo, [configurar as chaves geridas pelo cliente para encriptação de armazenamento Azure a partir do portal Azure](../../storage/common/customer-managed-keys-configure-key-vault.md).

- Para garantir que um DBA não pode ver dados considerados altamente sensíveis e que ainda podem fazer tarefas DBA, pode utilizar Sempre Encriptado com a separação de papéis.
  - Consulte os artigos, [Visão geral da Gestão de Chaves para Sempre Encriptado,](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) [Provisionamento de Chaves com Separação de Papéis](/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)e [Rotação de Chave Master de Coluna com Separação de Funções](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation).

- Nos casos em que a utilização de Always Encrypted não seja viável, ou pelo menos não sem custos e esforços importantes que possam mesmo tornar o sistema quase inutilizável, os compromissos podem ser feitos e atenuados através da utilização de controlos compensatórios como:
  - Intervenção humana em processos.
  - Pistas de auditoria – para obter mais informações sobre Auditoria, ver, [Audit eventos críticos de segurança](#audit-critical-security-events).

**Boas práticas:**

- Certifique-se de que são utilizadas contas diferentes para ambientes de Desenvolvimento/Teste e Produção. Contas diferentes ajudam a cumprir a separação dos sistemas de teste e produção.

- Abstenha-se de atribuir permissões a utilizadores individuais. Utilize as funções (bases de dados ou funções de servidor) de forma consistente. Ter papéis ajuda muito com permissões de relato e resolução de problemas.

- Use funções incorporadas quando as permissões correspondem exatamente às permissões necessárias – se a união de todas as permissões de várias funções incorporadas levar a uma correspondência de 100%, também pode atribuir várias funções simultaneamente.

- Crie e utilize funções definidas pelo utilizador quando as funções incorporadas concedem demasiadas permissões ou permissões insuficientes.

- As atribuições de funções também podem ser feitas temporariamente, também conhecidas como Separação Dinâmica de Deveres (DSD), quer dentro dos passos de trabalho do agente SQL em T-SQL ou usando Azure PIM para funções de Azure.

- Certifique-se de que os DBAs não têm acesso às chaves de encriptação ou às lojas-chave, e que os Administradores de Segurança com acesso às chaves não têm acesso à base de dados por sua vez. A utilização de [Gestão de Chaves Extensíveis (EKM)](/sql/relational-databases/security/encryption/extensible-key-management-ekm) pode facilitar a sua separação. [O Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pode ser usado para implementar eKM.

- Certifique-se sempre de ter um rasto de auditoria para ações relacionadas com a segurança.

- Pode recuperar a definição das funções incorporadas do Azure para ver as permissões utilizadas e criar um papel personalizado baseado em excertos e acumulações destes através do PowerShell.

- Como qualquer membro da função de base de dados db_owner pode alterar definições de segurança como a Encriptação de Dados Transparente (TDE), ou alterar a SLO, esta adesão deve ser concedida com cuidado. No entanto, há muitas tarefas que requerem privilégios db_owner. Tarefa como alterar qualquer definição de base de dados, como alterar opções de DB. A auditoria desempenha um papel fundamental em qualquer solução.

- Não é possível restringir as permissões de um db_owner, impedindo assim que uma conta administrativa veja os dados dos utilizadores. Se houver dados altamente sensíveis numa base de dados, o Always Encrypted pode ser usado para impedir db_owners ou qualquer outro DBA de o visualizar.

> [!NOTE]
> Alcançar a Separação de Deveres (SoD) é um desafio para tarefas relacionadas com a segurança ou resolução de problemas. Outras áreas como o desenvolvimento e as funções de utilizador final são mais fáceis de segregar. A maioria dos controlos relacionados com a conformidade permite a utilização de funções de controlo alternativos, como a Auditoria quando outras soluções não são práticas.

Para os leitores que querem mergulhar mais profundamente no SoD, recomendamos os seguintes recursos:

- Para a base de dados Azure SQL e para a sql Gestded Instance:  
  - [Controlar e conceder acesso à base de dados](logins-create-manage.md)
  - [Separação de funções do motor para o desenvolvedor de aplicações](/previous-versions/sql/sql-server-2008/cc974525(v=sql.100))
  - [Separação de Deveres](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Assinatura de Procedimentos Armazenados](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Para a gestão de recursos Azure:
  - [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md)
  - [Funções personalizadas do Azure](../../role-based-access-control/custom-roles.md)
  - [Utilização de Gestão de Identidade Privilegiada Azure AD para acesso elevado](https://www.microsoft.com/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Realizar revisões de código regulares

> Mencionado em: PCI: 6.3.2, SOC: SDL-3

A separação de direitos não se limita aos dados numa base de dados, mas inclui o código de aplicação. O código malicioso pode potencialmente contornar os controlos de segurança. Antes de implementar código personalizado para a produção, é essencial rever o que está a ser implementado.

**Como implementar:**

- Utilize uma ferramenta de base de dados como o Azure Data Studio que suporta o controlo de origem.

- Implementar um processo de implantação de código segregado.

- Antes de se comprometer com o ramo principal, uma pessoa (que não seja o autor do próprio código) tem de inspecionar o código para uma possível elevação de riscos de privilégios, bem como modificações de dados maliciosas para proteger contra fraudes e acesso fraudulento. Isto pode ser feito utilizando mecanismos de controlo de fontes.

**Boas práticas:**

- Normalização: Ajuda a implementar um procedimento padrão que deve ser seguido para quaisquer atualizações de código.

- A Avaliação de Vulnerabilidades contém regras que verificam permissões excessivas, o uso de algoritmos de encriptação antigos e outros problemas de segurança dentro de um esquema de base de dados.

- Podem ser efetuados mais controlos num ambiente de QA ou de teste utilizando a Advanced Threat Protection que verifica códigos vulneráveis à injeção de SQL.

- Exemplos do que procurar:
  - Criação de um utilizador ou alteração de definições de segurança dentro de uma implementação automatizada de atualização de código SQL.
  - Um procedimento armazenado, que, dependendo dos parâmetros fornecidos, atualiza um valor monetário numa célula de forma não conforme.

- Certifique-se de que a pessoa que conduz a revisão é um indivíduo diferente do autor do código de origem e conhecedor em revisões de código e codificação segura.

- Certifique-se de saber todas as fontes de alterações de código. O código pode estar nos Scripts T-SQL. Podem ser comandos ad-hoc para serem executados ou implantados em formas de Visualizações, Funções, Gatilhos e Procedimentos Armazenados. Pode fazer parte das definições de trabalho do agente SQL (Etapas). Também pode ser executado a partir de pacotes SSIS, Azure Data Factory, e outros serviços.

## <a name="data-protection"></a>Proteção de dados

A proteção de dados é um conjunto de capacidades para salvaguardar informações importantes do compromisso por encriptação ou obfuscação.

> [!NOTE]
> A Microsoft atesta a base de dados Azure SQL e a SQL Managed Instance como sendo compatíveis com o NÍVEL 1 1. Isto é feito após verificar a utilização estrita de algoritmos aceitáveis FIPS 140-2 Nível 1 e instâncias validadas fips 140-2 Nível 1 desses algoritmos, incluindo consistência com os comprimentos-chave necessários, gestão de chaves, geração de chaves e armazenamento de chaves. Este atestado destina-se a permitir que os nossos clientes respondam à necessidade ou exigência da utilização de instâncias validadas fips 140-2 Nível 1 no processamento de dados ou entrega de sistemas ou aplicações. Definimos os termos "CONFORMIDADE FIPS 140-2 Nível 1" e "CONFORMIDADE FIPS 140-2 Nível 1" usados na declaração acima para demonstrar a sua aplicabilidade pretendida ao uso do governo dos EUA e do Canadá do termo diferente "FIPS 140-2 Nível 1 validado".

### <a name="encrypt-data-in-transit"></a>Encriptar dados em trânsito

> Mencionado em: OSA Practice #6, ISO Control Family: Criptografia

Protege os seus dados enquanto os dados se movem entre o seu cliente e o seu servidor. Consulte a [Segurança da Rede](#network-security).

### <a name="encrypt-data-at-rest"></a>Encriptar dados inativos

> Mencionado em: OSA Practice #6, ISO Control Family: Criptografia

A encriptação em repouso é a proteção criptográfica dos dados quando são persistir em ficheiros de base de dados, registos e cópias de segurança.

**Como implementar:**

- [A Encriptação transparente da Base de Dados (TDE)](transparent-data-encryption-tde-overview.md) com chaves geridas pelo serviço são ativadas por padrão para quaisquer bases de dados criadas após 2017 na Base de Dados Azure SQL e na SQL Managed Instance.
- Num caso gerido, se a base de dados for criada a partir de uma operação de restauro utilizando um servidor no local, a definição de TDE da base de dados original será honrada. Se a base de dados original não tiver O TDE ativado, recomendamos que o TDE seja ligado manualmente para a instância gerida.

**Boas práticas:**

- Não armazene dados que exijam encriptação em repouso na base de dados principal. A base de dados principal não pode ser encriptada com TDE.

- Utilize chaves geridas pelo cliente no Cofre da Chave Azure se precisar de maior transparência e controlo granular sobre a proteção TDE. O Azure Key Vault permite a capacidade de revogar permissões a qualquer momento para tornar a base de dados inacessível. Pode gerir centralmente os protetores TDE juntamente com outras teclas ou rodar o protetor TDE na sua própria agenda utilizando o Cofre da Chave Azure.

- Se estiver a utilizar chaves geridas pelo cliente no Cofre da Chave Azure, siga os artigos, [Diretrizes para configurar o TDE com cofre de chave Azure](transparent-data-encryption-byok-overview.md#recommendations-when-configuring-akv) e [como configurar o Geo-DR com o Cofre da Chave Azure](transparent-data-encryption-byok-overview.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteger dados sensíveis na utilização de utilizadores altamente privilegiados e não autorizados

Os dados em uso são os dados armazenados na memória do sistema de base de dados durante a execução de consultas SQL. Se a sua base de dados armazena dados sensíveis, a sua organização poderá ser obrigada a garantir que os utilizadores altamente privilegiados estão impedidos de visualizar dados sensíveis na sua base de dados. Os utilizadores de alto privilégio, como os operadores da Microsoft ou DBAs na sua organização, devem ser capazes de gerir a base de dados, mas impedidos de visualizar e potencialmente exfiltrar dados sensíveis da memória do processo SQL ou consultando a base de dados.

As políticas que determinam quais os dados sensíveis e se os dados sensíveis devem ser encriptados na memória e não acessíveis aos administradores em texto simples, são específicos da sua organização e regulamentos de conformidade a que necessita aderir. Consulte o requisito relacionado: [Identifique e marque dados sensíveis.](#identify-and-tag-sensitive-data)

**Como implementar:**

- Utilize [sempre encriptados](/sql/relational-databases/security/encryption/always-encrypted-database-engine) para garantir que os dados sensíveis não são expostos em texto simples na Base de Dados Azure SQL ou na SQL Managed Instance, mesmo na memória/utilização. Sempre encriptado protege os dados de Administradores de Bases de Dados (DBAs) e administradores em nuvem (ou maus atores que podem personificar utilizadores altamente privilegiados mas não autorizados) e dá-lhe mais controlo sobre quem pode aceder aos seus dados.

**Boas práticas:**

- Sempre Encriptado não é um substituto para encriptar dados em repouso (TDE) ou em trânsito (SSL/TLS). Sempre encriptado não deve ser usado para dados não sensíveis para minimizar o desempenho e o impacto da funcionalidade. A utilização sempre encriptada em conjunto com a TDE e a Segurança da Camada de Transporte (TLS) é recomendada para uma proteção abrangente dos dados em repouso, em trânsito e em uso.

- Avalie o impacto da encriptação das colunas de dados sensíveis identificadas antes de implementar Sempre Encriptado numa base de dados de produção. Em geral, a Always Encrypted reduz a funcionalidade de consultas em colunas encriptadas e tem outras limitações, listadas em [Sempre Encriptados - Detalhes de Recursos](/sql/relational-databases/security/encryption/always-encrypted-database-engine#feature-details). Por isso, poderá ser necessário rearquitectar a sua aplicação para re-implementar a funcionalidade, uma consulta não suporta, do lado do cliente ou/e refacciona o seu esquema de base de dados, incluindo as definições de procedimentos, funções, vistas e gatilhos armazenados. As aplicações existentes podem não funcionar com colunas encriptadas se não aderirem às restrições e limitações de Always Encrypted. Enquanto o ecossistema das ferramentas, produtos e serviços da Microsoft que suportam sempre encriptados está a crescer, alguns deles não funcionam com colunas encriptadas. A encriptação de uma coluna também pode ter impacto no desempenho da consulta, dependendo das características da sua carga de trabalho.

- Gerir sempre as teclas encriptadas com a separação de funções se estiver a usar Sempre Encriptado para proteger dados de DBAs maliciosos. Com a separação de papéis, um administrador de segurança cria as chaves físicas. O DBA cria a chave principal da coluna e os objetos de metadados chave de encriptação da coluna que descrevem as chaves físicas na base de dados. Durante este processo, o administrador de segurança não precisa de acesso à base de dados, e o DBA não precisa de acesso às chaves físicas em texto simples.
  - Consulte o artigo, [Managing Keys with Role Separation](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) para mais detalhes.

- Guarde as suas chaves principais de coluna no Cofre da Chave Azure para facilitar a gestão. Evite utilizar o Windows Certificate Store (e, em geral, soluções de lojas de chaves distribuídas, em oposição às soluções centrais de gestão de chaves) que dificultam a gestão das chaves.

- Pense cuidadosamente através das trocas de utilização de várias teclas (tecla principal de coluna ou teclas de encriptação de colunas). Mantenha o número de chaves pequenas para reduzir o custo de gestão das chaves. Uma chave-chave principal de coluna e uma chave de encriptação de coluna por base de dados são normalmente suficientes em ambientes de estado estável (não no meio de uma rotação chave). Pode precisar de chaves adicionais se tiver diferentes grupos de utilizadores, cada um usando diferentes teclas e acedendo a diferentes dados.  

- Rode as teclas principais da coluna de acordo com os seus requisitos de conformidade. Se também precisar de rodar as teclas de encriptação da coluna, considere usar a encriptação online para minimizar o tempo de inatividade da aplicação.
  - Consulte o artigo, [Considerações de Desempenho e Disponibilidade.](/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations)

- Utilize encriptação determinística se os cálculos (igualdade) nos dados precisarem de ser suportados. Caso contrário, utilize encriptação aleatória. Evite utilizar encriptação determinística para conjuntos de dados de baixa entropia ou conjuntos de dados com distribuição publicamente conhecida.

- Se estiver preocupado com o acesso de terceiros aos seus dados legalmente sem o seu consentimento, certifique-se de que todas as aplicações e ferramentas que tenham acesso às chaves e dados em texto simples correm para fora da Microsoft Azure Cloud. Sem acesso às chaves, o terceiro não terá forma de desencriptar os dados a menos que contornem a encriptação.

- Sempre encriptado não suporta facilmente o acesso temporário às chaves (e aos dados protegidos). Por exemplo, se precisar de partilhar as chaves com um DBA para permitir que o DBA faça algumas operações de limpeza em dados sensíveis e encriptados. A única forma de revogar a fiabilidade do acesso aos dados do DBA será rodar tanto as teclas de encriptação da coluna como as teclas principais da coluna que protegem os dados, que é uma operação dispendiosa.

- Para aceder aos valores de texto simples em colunas encriptadas, um utilizador precisa de ter acesso à Chave Master da Coluna (CMK) que protege as colunas, que está configurada na loja de chaves que detém o CMK. O utilizador também precisa de ter a **DEFINIÇÃO DE CHAVE MASTER DE COLUNA** E VER QUAISQUER permissões DE **DEFINIÇÃO DE CHAVE DE ENCRIPTAÇÃO** DE COLUNAS.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controlar o acesso dos utilizadores de aplicações a dados sensíveis através da encriptação

A encriptação pode ser usada como uma forma de garantir que apenas utilizadores de aplicações específicas que tenham acesso a chaves criptográficas possam ver ou atualizar os dados.

**Como implementar:**

- Utilize encriptação ao nível celular (CLE). Consulte o artigo, [Criptografe uma Coluna de Dados](/sql/relational-databases/security/encryption/encrypt-a-column-of-data) para obter mais detalhes.
- Use Sempre Encriptado, mas esteja ciente da sua limitação. As limitações estão listadas abaixo.

**Melhores práticas**

Ao utilizar o CLE:

- Controlar o acesso às chaves através de permissões e funções SQL.

- Utilize AES (recomendado AES 256) para encriptação de dados. Os algoritmos, tais como RC4, DES e TripleDES, são depreciados e não devem ser usados devido a vulnerabilidades conhecidas.

- Proteja as teclas simétricas com chaves/certificados assimétricos (não palavras-passe) para evitar a utilização de 3DES.

- Tenha cuidado ao migrar uma base de dados utilizando Cell-Level Encriptação através da exportação/importação (ficheiros bacpac).
  - Consulte o artigo, [Recomendações para a utilização da Encriptação de Nível celular na Base de Dados Azure SQL](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database) sobre como evitar perder chaves ao migrar dados, e para outras orientações de boas práticas.

Tenha em mente que a Always Encrypted foi concebida principalmente para proteger dados sensíveis na utilização de utilizadores de alto privilégio da Base de Dados Azure SQL (operadores de nuvem, DBAs) - consulte [Proteger dados sensíveis na utilização de utilizadores altamente privilegiados e não autorizados.](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users) Esteja atento aos seguintes desafios ao utilizar o Always Encrypted para proteger os dados dos utilizadores da aplicação:

- Por padrão, todos os controladores de clientes da Microsoft que suportam Sempre Encriptado mantêm uma cache global (uma por aplicação) de chaves de encriptação de colunas. Uma vez que um condutor de cliente adquire uma chave de encriptação de coluna de texto simples contactando uma loja de chaves segurando uma chave principal de coluna, a chave de encriptação da coluna de texto simples está em cache. Isto torna os dados isolados dos utilizadores de uma aplicação multiutilizador desafiante. Se a sua aplicação se destinar a utilizadores finais ao interagir com uma loja de chaves (como o Azure Key Vault), depois de a consulta de um utilizador povoar a cache com uma chave de encriptação de coluna, uma consulta subsequente que requer a mesma chave mas que é desencadeada por outro utilizador utilizará a chave em cache. O controlador não liga para a loja de chaves e não verificará se o segundo utilizador tem permissão para aceder à chave de encriptação da coluna. Como resultado, o utilizador pode ver os dados encriptados mesmo que o utilizador não tenha acesso às chaves. Para conseguir o isolamento dos utilizadores dentro de uma aplicação multiutilizador, pode desativar a chave de encriptação de colunas. A desativação da caching irá causar despesas adicionais de desempenho, uma vez que o controlador terá de contactar a loja de chaves para cada operação de encriptação ou desencriptação de dados.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteja os dados contra a visualização não autorizada pelos utilizadores da aplicação, preservando o formato de dados

Outra técnica para impedir que utilizadores não autorizados possam visualizar dados é obfundar ou mascarar os dados, preservando tipos e formatos de dados para garantir que as aplicações do utilizador possam continuar a manusear e exibir os dados.

**Como implementar:**

- Utilize [máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) para obstacular colunas de mesa.

> [!NOTE]
> Sempre encriptado não funciona com máscara de dados dinâmicos. Não é possível encriptar e mascarar a mesma coluna, o que implica que é necessário priorizar a proteção de dados em uso vs. mascarar os dados para os utilizadores da sua aplicação através de Dynamic Data Masking.

**Boas práticas:**

> [!NOTE]
> A Máscara dinâmica de dados não pode ser utilizada para proteger dados de utilizadores de alto privilégio. As políticas de mascaramento não se aplicam aos utilizadores com acesso administrativo como db_owner.

- Não permita que os utilizadores de aplicações corram consultas ad-hoc (pois podem ser capazes de trabalhar em torno de Dynamic Data Masking).  
  - Consulte o artigo, [ignorando máscaras utilizando técnicas de inferência ou força bruta](/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) para detalhes.  

- Utilize uma política de controlo de acesso adequada (através de permissões, funções, RLS SQL) para limitar as permissões do utilizador para escoar atualizações nas colunas mascaradas. Criar uma máscara numa coluna não impede atualizações a essa coluna. Os utilizadores que recebem dados mascarados ao consultarem a coluna mascarada, podem atualizar os dados se tiverem permissões de escrita.

- O Mascaramento dinâmico de dados não preserva as propriedades estatísticas dos valores mascarados. Isto pode ter impacto nos resultados da consulta (por exemplo, consultas que contenham predicados filtrantes ou juntas nos dados mascarados).

## <a name="network-security"></a>Segurança da rede

Segurança da rede refere-se a controlos de acesso e boas práticas para proteger os seus dados em trânsito para a Base de Dados Azure SQL.

### <a name="configure-my-client-to-connect-securely-to-sql-databasesql-managed-instance"></a>Configure o meu cliente para ligar de forma segura à Base de Dados SQL/SQL Gestded Instance

As melhores práticas sobre como impedir que máquinas e aplicações de clientes com vulnerabilidades bem conhecidas (por exemplo, utilizando protocolos TLS mais antigos e suites de cifra) se liguem à Base de Dados SQL do Azure e à SQL Managed Instance.

**Como implementar:**

- Certifique-se de que as máquinas clientes que se ligam à Base de Dados Azure SQL e à SQL Managed Instance estão a utilizar  [a Segurança da Camada de Transporte (TLS)](security-overview.md#transport-layer-security-encryption-in-transit).

**Boas práticas:**

- Configure todas as suas apps e ferramentas para ligar à Base de Dados SQL com encriptação ativada
  - Encriptar = Ligado, TrustServerCertificate = Desligado (ou equivalente com controladores não Microsoft).

- Se a sua aplicação utilizar um controlador que não suporte OLS ou suporte uma versão mais antiga do TLS, substitua o controlador, se possível. Se não for possível, avalie cuidadosamente os riscos de segurança.

- Reduzir os vetores de ataque através de vulnerabilidades em SSL 2.0, SSL 3.0, TLS 1.0 e TLS 1.1, desativando-os em máquinas clientes que ligam à Base de Dados Azure SQL por [definições de registo de segurança da camada de transporte (TLS).](/windows-server/security/tls/tls-registry-settings#tls-10)

- Consulte as suítes cifras disponíveis no cliente: [Cipher Suites in TLS/SSL (Schannel SSP)](/windows/desktop/SecAuthN/cipher-suites-in-schannel). Especificamente, desative 3DES por [encomenda de suíte cifra TLS configurante.](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order)

- Para a Base de Dados Azure SQL e para a SQL Managed Instance, a encriptação é aplicada tanto para os tipos de conexão Proxy como para redirecionar. Para Azure SQL Managed Instance, utilize o tipo **de ligação Proxy** (padrão) pois este impõe encriptação a partir do lado do servidor. O tipo de ligação **Redirect** atualmente não suporta a aplicação da encriptação e só está disponível em ligações IP privadas.

- Para mais informações, consulte [Azure SQL Database Connectivity Architecture - Política de conexão](connectivity-architecture.md#connection-policy).

### <a name="minimize-attack-surface"></a>Minimizar a superfície de ataque

Minimize o número de funcionalidades que podem ser atacadas por um utilizador malicioso. Implementar controlos de acesso à rede para a Base de Dados Azure SQL.

> Mencionado em: Osa Practice #5

**Como implementar:**

Na Base de Dados SQL:

- Definir Permitir o acesso aos serviços de Azure a OFF ao nível do servidor
- Utilize pontos finais do Serviço VNet e regras de firewall VNet.
- Utilizar Link Privado (pré-visualização).

Em SQL Caso gerido:

- Siga as diretrizes dos [requisitos da Rede](../managed-instance/connectivity-architecture-overview.md#network-requirements).

**Boas práticas:**

- Restringir o acesso à Base de Dados Azure SQL e à SQL Managed Instance através da ligação num ponto final privado (por exemplo, utilizando uma via de dados privado):
  - Um caso gerido pode ser isolado dentro de uma rede virtual para impedir o acesso externo. As aplicações e ferramentas que se encontram na mesma rede virtual ou esprevada na mesma região poderiam aceder diretamente à mesma. Aplicações e ferramentas que se encontram em diferentes regiões poderiam utilizar a ligação virtual rede-rede para rede virtual ou o circuito ExpressRoute para estabelecer a ligação. O cliente deve utilizar os Grupos de Segurança da Rede (NSG) para restringir o acesso ao porto 1433 apenas a recursos que exijam acesso a uma instância gerida.
  - Para uma Base de Dados SQL, utilize a funcionalidade [Private Link](../../private-link/private-endpoint-overview.md) que fornece um IP privado dedicado para o servidor dentro da sua rede virtual. Também pode utilizar [pontos finais de serviço de rede virtual com regras de firewall de rede virtual](vnet-service-endpoint-rule-overview.md) para restringir o acesso aos seus servidores.
  - Os utilizadores móveis devem utilizar ligações VPN ponto a local para se conectarem sobre a via dos dados.
  - Os utilizadores ligados à sua rede no local devem utilizar a ligação VPN local ou o ExpressRoute para se conectarem ao longo da via de dados.

- Pode aceder à Base de Dados Azure SQL e à SQL Managed Instance ligando-se a um ponto final público (por exemplo, utilizando uma via de dados públicos). Devem ser consideradas as seguintes boas práticas:
  - Para um servidor na Base de Dados SQL, utilize [as regras de firewall IP](firewall-configure.md) para restringir o acesso a apenas endereços IP autorizados.
  - Para a SQL Managed Instance, utilize grupos de segurança de rede (NSG) para restringir o acesso sobre a porta 3342 apenas aos recursos necessários. Para obter mais informações, consulte [uma instância gerida de forma segura com pontos finais públicos](../managed-instance/public-endpoint-overview.md).

> [!NOTE]
> O ponto final público sql Managed Instance não está ativado por defeito e deve ser explicitamente ativado. Se a política da empresa não permitir a utilização de pontos finais públicos, utilize a [Política Azure](../../governance/policy/overview.md) para evitar, em primeiro lugar, a colocação de pontos finais públicos.

- Configurar componentes de rede Azure:
  - Siga [as melhores práticas da Azure para a segurança da rede.](../../security/fundamentals/network-best-practices.md)
  - Planeie a configuração da Rede Virtual de acordo com as melhores práticas descritas na [Rede Virtual Azure frequentemente feitas perguntas (FAQ)](../../virtual-network/virtual-networks-faq.md) e plano.
  - Segmentar uma rede virtual em várias sub-redes e atribuir recursos para funções semelhantes à mesma sub-rede (por exemplo, recursos frontais vs back-end).
  - Utilize [grupos de segurança de rede (NSGs)](../../virtual-network/network-security-groups-overview.md) para controlar o tráfego entre sub-redes dentro do limite da rede virtual Azure.
  - Ativar [o Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) para a sua subscrição para monitorizar o tráfego de rede de entrada e saída.

### <a name="configure-power-bi-for-secure-connections-to-sql-databasesql-managed-instance"></a>Configure o Power BI para ligações seguras à Base de Dados SQL/SqL Gestded Instance

**Boas práticas:**

- Para Power BI Desktop, utilize o caminho de dados privado sempre que possível.

- Certifique-se de que o Power BI Desktop está a ligar-se utilizando o TLS1.2 definindo a chave de registo na máquina do cliente de acordo com as definições de registo de [Segurança da Camada de Transporte (TLS).](/windows-server/security/tls/tls-registry-settings)

- Restringir o acesso de dados a utilizadores específicos através da [segurança ao nível da linha (RLS) com o Power BI](/power-bi/service-admin-rls).

- Para o Serviço de Bis de Energia, utilize o [gateway de dados no local,](/power-bi/service-gateway-onprem)tendo em conta [limitações e considerações](/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-sql-databasesql-managed-instance"></a>Configure o Serviço de Aplicações para ligações seguras à Base de Dados de Base de Dados SQL/SQL Gestão de Instâncias

**Boas práticas:**

- Para uma simples Aplicação Web, a ligação sobre o ponto final público requer a definição **de Allow Azure Services** to ON.

- [Integre a sua aplicação com uma Rede Virtual Azure](../../app-service/web-sites-integrate-with-vnet.md) para a conectividade do caminho de dados privados a uma instância gerida. Opcionalmente, também pode implementar uma Web App com [Ambientes de Serviço de Aplicações (ASE)](../../app-service/environment/intro.md).

- Para a Web App com ase ou rede virtual Integrada Web App conectando-se a uma base de dados na Base de Dados SQL, pode utilizar [pontos finais de serviço de rede virtual e regras de firewall de rede virtual](vnet-service-endpoint-rule-overview.md) para limitar o acesso a partir de uma rede virtual específica e sub-rede. Em **seguida, desemote Os Serviços Azure** off. Também pode ligar o ASE a uma instância gerida em SQL Managed Instance sobre uma via de dados privada.  

- Certifique-se de que a sua Web App está configurada de acordo com o artigo, [as melhores práticas para garantir a plataforma como um serviço (PaaS) web e aplicações móveis usando o Azure App Service](../../security/fundamentals/paas-applications-using-app-services.md).

- Instale [firewall de aplicação web (WAF)](../../web-application-firewall/ag/ag-overview.md) para proteger a sua aplicação web de explorações e vulnerabilidades comuns.

### <a name="configure-azure-virtual-machine-hosting-for-secure-connections-to-sql-databasesql-managed-instance"></a>Configurar a máquina virtual Azure hospedagem para ligações seguras à Base de Dados SQL/SQL Gestão de Instâncias

**Boas práticas:**

- Utilize uma combinação de regras de Permitir e Negar sobre os NSGs das máquinas virtuais Azure para controlar quais as regiões que podem ser acedidas a partir do VM.

- Certifique-se de que o seu VM está configurado de acordo com o artigo, [As melhores práticas de segurança para cargas de trabalho iaaS em Azure](../../security/fundamentals/iaas.md).

- Certifique-se de que todos os VMs estão associados a uma rede virtual específica e a uma sub-rede.

- Avalie se precisa da rota padrão 0.0.0/Internet por orientação [sobre túneis forçados](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling).
  - Se sim – por exemplo, sub-rede frontal – então mantenha a rota predefinido.
  - Se não – por exemplo, sub-rede de nível médio ou de back-end – então permitir a escavação de força para que nenhum tráfego vá pela Internet para chegar ao local (também conhecido como cross-premis).

- Implemente [as rotas predefinicionais opcionais](../../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se estiver a usar o seu conhecimento ou ligação às instalações.

- Implemente [as Rotas Definidas](../../virtual-network/virtual-networks-udr-overview.md#user-defined) para o Utilizador se precisar enviar todo o tráfego da rede virtual para um Aparelho Virtual de Rede para inspeção de pacotes.

- Utilize [pontos finais de serviço de rede virtual](vnet-service-endpoint-rule-overview.md) para acesso seguro a serviços PaaS como o Azure Storage através da rede de espinha dorsal Azure.

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Proteger contra ataques de Negação de Serviço Distribuídos (DDoS)

Os ataques de Negação de Serviço Distribuído (DDoS) são tentativas de um utilizador malicioso enviar uma inundação de tráfego de rede para a Base de Dados Azure SQL com o objetivo de esmagar a infraestrutura Azure e fazê-la rejeitar logins válidos e carga de trabalho.

> Mencionado em: Osa Practice #9

**Como implementar:**

A proteção DDoS é ativada automaticamente como parte da Plataforma Azure. Inclui a monitorização do tráfego sempre on-on e a mitigação em tempo real dos ataques a nível da rede em pontos finais públicos.

- Utilize [a Proteção DDoS Azure](../../ddos-protection/ddos-protection-overview.md) para monitorizar endereços IP públicos associados a recursos implantados em redes virtuais.

- Utilize [proteção de ameaças avançadas para a base de dados Azure SQL](threat-detection-overview.md) para detetar ataques de Negação de Serviço (DoS) contra bases de dados.

**Boas práticas:**

- Siga as práticas descritas no [Minimiz Attack Surface](#minimize-attack-surface) ajuda a minimizar as ameaças de ataque do DDoS.

- O alerta de credenciais de proteção de ameaças avançadas **sql** ajuda a detetar ataques de força bruta. Em alguns casos, o alerta pode até distinguir cargas de trabalho de teste de penetração.

- Para aplicações de hospedagem Azure VM conectando-se à Base de Dados SQL:
  - Siga a recomendação para restringir o acesso através de pontos finais virados para a Internet no Azure Security Center.
  - Utilize conjuntos de escala de máquina virtual para executar várias instâncias da sua aplicação em VMs Azure.
  - Desative o RDP e o SSH da Internet para evitar ataques de força bruta.

## <a name="monitoring-logging-and-auditing"></a>Monitorização, Registo e Auditoria

Esta secção refere-se a capacidades para o ajudar a detetar atividades anómalas que indicam tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados. Também descreve as melhores práticas para configurar a auditoria da base de dados para rastrear e capturar eventos de base de dados.

### <a name="protect-databases-against-attacks"></a>Proteger bases de dados contra ataques

A proteção avançada de ameaças permite-lhe detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas.

**Como implementar:**

- Utilize [proteção de ameaças avançadas para o SQL](threat-detection-overview.md#alerts) detetar tentativas incomuns e potencialmente nocivas de aceder ou explorar bases de dados, incluindo:
  - Ataque de injeção SQL.
  - Credenciais roubo/fuga.
  - Abuso de privilégios.
  - Exfiltração de dados.

**Boas práticas:**

- Configurar [o Azure Defender para o SQL](azure-defender-for-sql.md)   para um servidor específico ou uma instância gerida. Também pode configurar o Azure Defender para SQL para todos os servidores e casos geridos numa subscrição, mudando para [o nível Standard do Azure Security Center](../../security-center/security-center-pricing.md).

- Para uma experiência de investigação completa, é recomendado para permitir a [Auditoria de Bases de Dados SQL.](../../azure-sql/database/auditing-overview.md) Com a auditoria, pode rastrear eventos de base de dados e escrevê-los num registo de auditoria numa conta de Armazenamento Azure ou no espaço de trabalho do Azure Log Analytics.

### <a name="audit-critical-security-events"></a>Auditoria a eventos críticos de segurança

O rastreio de eventos de base de dados ajuda-o a compreender a atividade da base de dados. Pode obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança. Também permite e facilita a adesão às normas de conformidade.

**Como implementar:**

- Permitir [auditoria de base de dados SQL](../../azure-sql/database/auditing-overview.md) ou Auditoria de Instância [Gerida](../managed-instance/auditing-configure.md) para rastrear eventos de base de dados e escrevê-los para um registo de auditoria na sua conta de Armazenamento de Azure, Log Analytics (pré-visualização) ou Centros de Eventos (pré-visualização).

- Os registos de auditoria podem ser escritos numa conta de Armazenamento Azure, num espaço de trabalho log Analytics para consumo por registos do Azure Monitor ou para o centro de eventos para consumo utilizando o centro de eventos. Pode configurar qualquer combinação destas opções, e os registos de auditoria serão escritos a cada um.

**Boas práticas:**

- Ao configurar a [Auditoria de Bases de Dados SQL](../../azure-sql/database/auditing-overview.md) no seu servidor ou auditoria de [instâncias geridas](../managed-instance/auditing-configure.md) para eventos de auditoria, todas as bases de dados existentes e recém-criadas nesse servidor serão auditadas.
- Por política de auditoria por defeito inclui todas as ações (consultas, procedimentos armazenados e logins bem sucedidos e falhados) contra as bases de dados, o que pode resultar em elevado volume de registos de auditoria. Recomenda-se que os clientes [configurem auditorias para diferentes tipos de ações e grupos de ação utilizando o PowerShell.](./auditing-overview.md#manage-auditing) Configurar isto ajudará a controlar o número de ações auditadas e minimizar o risco de perda de eventos. As configurações de auditoria personalizadas permitem que os clientes capturem apenas os dados de auditoria necessários.
- Os registos de auditoria podem ser consumidos diretamente no [portal Azure,](https://portal.azure.com/)ou a partir do local de armazenamento que foi configurado.

> [!NOTE]
> Permitir a auditoria ao Log Analytics incorrerá em custos baseados nas taxas de ingestão. Esteja ciente do custo associado com a utilização desta [opção,](https://azure.microsoft.com/pricing/details/monitor/)ou considere guardar os registos de auditoria numa conta de armazenamento Azure.

**Recursos adicionais:**

- [Auditoria de bases de dados SQL](../../azure-sql/database/auditing-overview.md)
- [Auditoria ao Servidor SQL](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="secure-audit-logs"></a>Registos de auditoria seguros

Restringir o acesso à conta de armazenamento para apoiar a Separação de Direitos e separar a DBA dos Auditores.

**Como implementar:**

- Ao guardar os registos de auditoria para o Azure Storage, certifique-se de que o acesso à Conta de Armazenamento está limitado aos princípios mínimos de segurança. Controle quem tem acesso à conta de armazenamento.
- Para mais informações, consulte [autorizando o acesso ao Azure Storage](../../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Boas práticas:**

- Controlar o Acesso ao Alvo de Auditoria é um conceito-chave na separação da DBA dos Auditores.

- Ao auditar o acesso a dados sensíveis, considere proteger os dados com encriptação de dados para evitar fugas de informação ao Auditor. Para obter mais informações, consulte a secção [Proteger dados sensíveis na utilização de utilizadores altamente privilegiados e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Gestão de Segurança

Esta secção descreve os diferentes aspetos e boas práticas para gerir a postura de segurança das suas bases de dados. Inclui as melhores práticas para garantir que as suas bases de dados estão configuradas para cumprir os padrões de segurança, para descobrir e classificar e rastrear o acesso a dados potencialmente sensíveis nas suas bases de dados.

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Certifique-se de que as bases de dados estão configuradas para atender às melhores práticas de segurança

Melhorar proativamente a segurança da sua base de dados descobrindo e remediando potenciais vulnerabilidades de base de dados.

**Como implementar:**

- Permitir que a [SQL Vulnerability Assessment](/sql/relational-databases/security/sql-vulnerability-assessment) (VA) digitalize a sua base de dados por questões de segurança e que seja executada periodicamente nas suas bases de dados.

**Boas práticas:**

- Inicialmente, execute VA nas suas bases de dados e itere, remediando verificações falhadas que se opõem às melhores práticas de segurança. Configurar linhas de base para configurações aceitáveis até que a varredura saia _limpa,_ ou todas as verificações foram passadas.  

- Configure as análises periódicas recorrentes para executar uma vez por semana e configurar a pessoa relevante para receber e-mails sumários.

- Reveja o resumo do VA após cada digitalização semanal. Para quaisquer vulnerabilidades encontradas, avalie o desvio do resultado da verificação anterior e determine se a verificação deve ser resolvida. Reveja se há uma razão legítima para a alteração na configuração.

- Resolver verificações e atualizar linhas de base quando relevantes. Crie itens de bilhetes para resolver ações e rastreá-los até que sejam resolvidos.

**Recursos adicionais:**

- [Avaliação de Vulnerabilidades do SQL](/sql/relational-databases/security/sql-vulnerability-assessment)
- [O serviço de avaliação de vulnerabilidades SQL ajuda-o a identificar vulnerabilidades de base de dados](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificar e marcar dados sensíveis

Descubra colunas que possam conter dados sensíveis. O que é considerado dados sensíveis depende fortemente do cliente, da regulação da conformidade, etc., e precisa de ser avaliado pelos utilizadores responsáveis por esses dados. Classifique as colunas para utilizar cenários avançados de auditoria e proteção baseados na sensibilidade.

**Como implementar:**

- Utilize [a SQL Data Discovery and Classification](data-discovery-and-classification-overview.md) para descobrir, classificar, rotular e proteger os dados sensíveis nas suas bases de dados.
  - Veja as recomendações de classificação que são criadas pela descoberta automatizada no painel de deteção e classificação de dados SQL. Aceite as classificações relevantes, de modo a que os seus dados sensíveis são persistentemente marcados com etiquetas de classificação.
  - Adicione manualmente classificações para quaisquer campos de dados sensíveis adicionais que não foram descobertos pelo mecanismo automatizado.
- Para mais informações, consulte [a SQL Data Discovery and Classification](/sql/relational-databases/security/sql-data-discovery-and-classification).

**Boas práticas:**

- Monitorize regularmente o painel de classificação para uma avaliação precisa do estado de classificação da base de dados. Um relatório sobre o estado de classificação da base de dados pode ser exportado ou impresso para partilhar para efeitos de conformidade e auditoria.

- Monitorize continuamente o estado dos dados sensíveis recomendados na Avaliação de Vulnerabilidade SQL. Rastree a regra sensível de descoberta de dados e identifique qualquer deriva nas colunas recomendadas para a classificação.  

- Utilize a classificação de uma forma adaptada às necessidades específicas da sua organização. Personalize a sua política de Proteção de Informação (etiquetas de sensibilidade, tipos de informação, lógica de descoberta) na política [de Proteção de Informação SQL](../../security-center/security-center-info-protection-policy.md) no Centro de Segurança Azure.

### <a name="track-access-to-sensitive-data"></a>Rastrear o acesso a dados sensíveis

Monitorize quem acede a dados sensíveis e capture consultas sobre dados sensíveis em registos de auditoria.

**Como implementar:**

- Utilize a Auditoria de SQL e a Classificação de Dados em combinação.
  - No seu registo [de Auditoria de Base de Dados SQL,](../../azure-sql/database/auditing-overview.md) pode rastrear o acesso especificamente a dados sensíveis. Também pode ver informações como os dados acedidos, bem como o seu rótulo de sensibilidade. Para mais informações, consulte [data Discovery e Classification](data-discovery-and-classification-overview.md) and [Auditing access a dados sensíveis.](data-discovery-and-classification-overview.md#audit-sensitive-data)

**Boas práticas:**

- Consulte as melhores práticas para as secções de Auditoria e Classificação de Dados:
  - [Auditoria a eventos críticos de segurança](#audit-critical-security-events)
  - [Identificar e marcar dados sensíveis](#identify-and-tag-sensitive-data)

### <a name="visualize-security-and-compliance-status"></a>Visualizar o estado de segurança e conformidade

Utilize um sistema unificado de gestão de segurança de infraestrutura que fortaleça a postura de segurança dos seus centros de dados (incluindo bases de dados na Base de Dados SQL). Consulte uma lista de recomendações relativas à segurança das suas bases de dados e ao estado de conformidade.

**Como implementar:**

- Monitorize recomendações de segurança relacionadas com o SQL e ameaças ativas no [Centro de Segurança Azure](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Ameaças de segurança comuns e potenciais mitigações

Esta secção ajuda-o a encontrar medidas de segurança para proteger contra certos vetores de ataque. Espera-se que a maioria das mitigações possa ser alcançada seguindo uma ou mais das diretrizes de segurança acima.

### <a name="security-threat-data-exfiltration"></a>Ameaça de segurança: Exfiltração de dados

A exfiltração de dados é a cópia, transferência ou recuperação não autorizada de dados de um computador ou servidor. Consulte uma definição para [exfiltração de dados](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipédia.

A ligação ao servidor por um ponto final público apresenta um risco de exfiltração de dados, uma vez que requer que os clientes abram as suas firewalls a IPs públicos.  

**Cenário 1**: Uma aplicação num Azure VM liga-se a uma base de dados na Base de Dados Azure SQL. Um ator desonesto tem acesso ao VM e compromete-o. Neste cenário, a exfiltração de dados significa que uma entidade externa que utiliza o VM fraudulento se conecta à base de dados, copia dados pessoais e armazena-os num armazenamento de bolhas ou numa base de dados SQL diferente numa subscrição diferente.

**Cenário 2**: Um Rouge DBA. Este cenário é muitas vezes levantado por clientes sensíveis à segurança das indústrias regulamentadas. Neste cenário, um utilizador de alto privilégio poderá copiar dados da Base de Dados Azure SQL para outra subscrição não controlada pelo titular dos dados.

**Potenciais atenuações:**

Hoje, a Azure SQL Database e a SQL Managed Instance oferecem as seguintes técnicas para mitigar ameaças de exfiltração de dados:

- Utilize uma combinação de regras de Permitir e Negar sobre os NSGs de VMs Azure para controlar quais as regiões que podem ser acedidas a partir do VM.
- Se utilizar um servidor na Base de Dados SQL, desa estale as seguintes opções:
  - Permitir que os Serviços Azure off.
  - Apenas permita o tráfego a partir da sub-rede que contém o seu Azure VM, configurando uma regra VNet Firewall.
  - Utilizar [link privado](../../private-link/private-endpoint-overview.md)
- Para a SQL Managed Instance, a utilização do acesso IP privado por predefinição aborda a primeira preocupação de exfiltração de dados de um VM fraudulento. Ligue a função de delegação da sub-rede numa sub-rede para definir automaticamente a política mais restritiva numa sub-rede SQL Managed Instance.
- A preocupação da Rogue DBA está mais exposta com a SQL Managed Instance, uma vez que tem uma área de superfície maior e os requisitos de networking são visíveis para os clientes. A melhor mitigação para isso é aplicar todas as práticas neste guia de segurança para evitar o cenário rogue DBA em primeiro lugar (não só para exfiltração de dados). Sempre Encriptado é um método para proteger dados sensíveis encriptando-os e mantendo a chave inacessível para o DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspetos de segurança da continuidade e disponibilidade do negócio

A maioria das normas de segurança aborda a disponibilidade de dados em termos de continuidade operacional, conseguida através da implementação de redundância e capacidades de fail-over para evitar pontos únicos de falha. Para cenários de desastres, é uma prática comum manter cópias de segurança dos ficheiros Data e Log.A secção seguinte fornece uma visão geral de alto nível das capacidades que são incorporadas em Azure. Também fornece opções adicionais que podem ser configuradas para atender a necessidades específicas:

- A Azure oferece alta disponibilidade incorporada: [Alta disponibilidade com Base de Dados SQL e SqL Gestd Instance](high-availability-sla.md)

- O nível Business Critical inclui grupos de failover, cópias de segurança completas e diferenciais e cópias de segurança de restauração pontuais ativadas por predefinição:  
  - [Cópias de segurança automatizadas](automated-backups-overview.md)
  - [Recuperar uma base de dados utilizando cópias de dados automatizadas - Restauro pontual](recovery-using-backups.md#point-in-time-restore)

- Podem ser configuradas funcionalidades adicionais de continuidade de negócios, tais como a configuração redundante da zona e grupos de falha automática em diferentes geos Azure: 
    - [Alta disponibilidade - Configuração redundante de zona para os níveis de serviço Premium & Business Critical](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)
    - [Alta disponibilidade - Configuração redundante de zona para o nível de serviço de finalidade geral](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
    - [Visão geral da continuidade do negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md)

## <a name="next-steps"></a>Passos seguintes

- Consulte [uma visão geral das capacidades de segurança da Base de Dados Azure SQL](security-overview.md)