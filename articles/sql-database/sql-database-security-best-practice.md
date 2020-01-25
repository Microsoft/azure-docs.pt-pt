---
title: Manual de práticas recomendadas de segurança para o banco de dados SQL do Azure | Microsoft Docs
description: Este artigo fornece diretrizes gerais para as práticas recomendadas de segurança no banco de dados SQL do Azure.
ms.service: sql-database
ms.subservice: security
author: VanMSFT
ms.author: vanto
ms.topic: article
ms.date: 12/23/2019
ms.reviewer: ''
ms.openlocfilehash: 82297850bf6d03215963a1f81dda166550f2b0d5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715190"
---
# <a name="azure-sql-database-security-best-practices-playbook"></a>Manual de práticas recomendadas de segurança do banco de dados SQL do Azure

## <a name="overview"></a>Visão geral

Este documento fornece orientação sobre como resolver requisitos comuns de segurança para aplicativos novos ou existentes usando o banco de dados SQL do Azure. Ele é organizado por áreas de segurança de alto nível. Para lidar com ameaças específicas, consulte a seção [ameaças comuns de segurança e possíveis mitigações](#common-security-threats-and-potential-mitigations) . Embora algumas das recomendações apresentadas sejam aplicáveis ao migrar aplicativos do local para o Azure, os cenários de migração não são o foco deste documento.

### <a name="azure-sql-database-deployment-offers-covered-in-this-guide"></a>Ofertas de implantação do banco de dados SQL do Azure abordadas neste guia

- [Bancos](https://docs.microsoft.com/azure/sql-database/sql-database-single-index)de dados SQL: [bases](sql-database-single-database.md) de dados individuais e [pools elásticos](sql-database-elastic-pool.md) em [servidores de banco de dados SQL do Azure](sql-database-servers.md)
- [Instâncias gerenciadas](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index)

### <a name="sql-deployment-offers-not-covered-in-this-guide"></a>Ofertas de implantação do SQL não abordadas neste guia

- Armazém de Dados SQL do Azure
- VMs do SQL do Azure (IaaS)
- SQL Server no local

### <a name="audience"></a>Audiência

As audiências pretendidas para este guia são os clientes que fazem perguntas sobre como proteger o banco de dados SQL do Azure. As funções interessadas neste artigo de prática recomendada incluem, mas não se limitando a:

- Arquitetos de segurança
- Gerenciadores de segurança
- Responsáveis pela conformidade
- Responsáveis pela privacidade
- Engenheiros de segurança

### <a id="using"></a>Usando este guia

Este documento tem como complemento a nossa documentação de [segurança do banco de dados SQL do Azure](sql-database-security-overview.md) .

A menos que indicado de outra forma, recomendamos que você siga todas as práticas recomendadas listadas em cada seção para atingir o respectivo objetivo ou requisito. Para atender a padrões de conformidade de segurança específicos ou práticas recomendadas, controles de conformidade regulatória importantes são listados na seção requisitos ou metas, onde for aplicável. Estes são os padrões de segurança e as normas que são referenciadas neste documento:

- [FedRAMP](https://www.fedramp.gov/documents/): AC-04, AC-06
- [SOC](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/sorhome.html): cm-3, SDL-3
- [ISO/IEC 27001](https://www.iso27001security.com/html/27001.html): controle de acesso, criptografia
- [Práticas de Osa (garantia de segurança operacional) da Microsoft](https://www.microsoft.com/en-us/securityengineering/osa/practices): prática #1-6 e #9
- [Controles de segurança da publicação especial do NIST 800-53](https://nvd.nist.gov/800-53): AC-5, AC-6
- [PCI DSS](https://www.pcisecuritystandards.org/document_library): 6.3.2, 6.4.2

### <a name="feedback"></a>Comentários

Planejamos continuar a atualizar as recomendações e as práticas recomendadas listadas aqui. Forneça entradas ou correções para este documento usando o link de **comentários** na parte inferior deste artigo.

## <a name="authentication"></a>Autenticação

A autenticação é o processo de provar que o usuário é quem alega ser. O banco de dados SQL do Azure dá suporte a dois tipos de autenticação:

- Autenticação do SQL
- Autenticação do Azure Active Directory

### <a name="central-management-for-identities"></a>Gerenciamento central de identidades

O gerenciamento de identidade central oferece os seguintes benefícios:

- Gerencie contas de grupo e controle permissões de usuário sem duplicar logons em servidores e bancos de dados do banco de dados SQL do Azure.
- Gerenciamento de permissões simplificado e flexível.
- Gerenciamento de aplicativos em escala.

**Como implementar**:

- Use a autenticação do Azure Active Directory (AD do Azure) para o gerenciamento centralizado de identidades.

**Práticas recomendadas**:

- Crie um locatário do Azure AD e [crie usuários](../active-directory/fundamentals/add-users-azure-active-directory.md) para representar usuários humanos e criar [entidades de serviço](../active-directory/develop/app-objects-and-service-principals.md) para representar aplicativos, serviços e ferramentas de automação. As entidades de serviço são equivalentes às contas de serviço no Windows e no Linux. 

- Atribuir direitos de acesso aos recursos para entidades de segurança do Azure AD por meio da atribuição de Grupo: criar grupos do Azure AD, conceder acesso a grupos e adicionar membros individuais aos grupos. Em seu banco de dados, crie usuários de banco de dados independente que mapeiem seus grupos do Azure AD. 
  - Consulte os artigos, [configurar e gerenciar a autenticação de Azure Active Directory com o SQL](sql-database-aad-authentication-configure.md) e [usar o Azure ad para autenticação com o SQL](sql-database-aad-authentication.md).
  > [!NOTE]
  > Em uma instância gerenciada, você também pode criar logons que são mapeados para entidades de segurança do Azure AD no banco de dados mestre. Consulte [Create login (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

- O uso de grupos do Azure AD simplifica o gerenciamento de permissões e o proprietário do grupo, e o proprietário do recurso pode adicionar/remover membros de/para o grupo. 

- Crie um grupo separado para o administrador do Azure AD para servidores de banco de BD SQL.

  - Consulte o artigo [provisionar um administrador de Azure Active Directory para seu servidor de banco de dados SQL do Azure](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

- Monitorar alterações de associação de grupo do Azure AD usando relatórios de atividade de auditoria do Azure AD. 

- Para uma instância gerenciada, é necessária uma etapa separada para criar o administrador do Azure AD. 
  - Consulte o artigo [provisionar um administrador de Azure Active Directory para sua instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). 

> [!NOTE]
> - A autenticação do Azure AD é registrada nos logs de auditoria do Azure SQL, mas não nos logs de entrada do Azure AD.
> - As permissões de RBAC concedidas no Azure não se aplicam às permissões de banco de dado SQL do Azure. Essas permissões devem ser criadas/mapeadas manualmente no banco de BD SQL usando permissões SQL existentes.
> - Na autenticação do Azure AD do lado do cliente, é necessário acessar a Internet ou por meio de UDR (rota definida pelo usuário) para uma VNet.
> - O token de acesso do AD do Azure é armazenado em cache no lado do cliente e seu tempo de vida depende da configuração do token. Confira o artigo [tempos de vida de token configuráveis no Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

> [!NOTE]
> Mencionado em: #2 de prática de OSA, controle de acesso ISO (AC)

A autenticação multifator do Azure (MFA) ajuda a fornecer segurança adicional, exigindo mais de uma forma de autenticação.

**Como implementar**:

- [Habilite o MFA](../active-directory/authentication/concept-mfa-howitworks.md) no Azure ad usando o acesso condicional e use a autenticação interativa. 

- A alternativa é habilitar a MFA para todo o domínio do Azure AD ou do AD.

**Práticas recomendadas**:

- Ativar o acesso condicional no Azure AD (requer assinatura Premium). 
  - Consulte o artigo [acesso condicional no Azure ad](../active-directory/conditional-access/overview.md).  

- Crie grupos do Azure AD e habilite a política de MFA para grupos selecionados usando o acesso condicional do Azure AD. 
  - Consulte o artigo [planejar a implantação de acesso condicional](../active-directory/conditional-access/plan-conditional-access.md). 

- A MFA pode ser habilitada para todo o Azure AD ou para todo o Active Directory federado com o Azure AD. 

- Use o modo de autenticação interativa do Azure AD para o BD SQL em que uma senha é solicitada interativamente, seguida pela autenticação MFA:      
  - Use a autenticação universal no SSMS. Consulte o artigo [usando a autenticação do AAD multifator com o banco de dados SQL do Azure e o azure SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md).
  - Use a autenticação interativa com suporte no SQL Server Data Tools (SSDT). Consulte o artigo [Azure Active Directory suporte no SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory?view=azuresqldb-current).
  - Use outras ferramentas do SQL com suporte para MFA. 
    - Suporte ao assistente do SSMS para exportar/extrair/implantar banco de dados  
    - [SqlPackage. exe](https://docs.microsoft.com/sql/tools/sqlpackage): opção '/UA ' 
    - [utilitário sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility): Option-G (interativo)
    - [utilitário bcp](https://docs.microsoft.com/sql/tools/bcp-utility): Option-G (interativo) 

- Implemente seus aplicativos para se conectar ao banco de dados SQL do Azure usando a autenticação interativa com suporte a MFA. 
  - Consulte o artigo [conectar-se ao banco de dados SQL do Azure com a autenticação multifator do Azure](active-directory-interactive-connect-azure-sql-db.md). 
  > [!NOTE]
  > Esse modo de autenticação requer identidades baseadas no usuário. Nos casos em que um modelo de identidade confiável é usado, ignorando a autenticação de usuário individual do Azure AD (por exemplo, usando a identidade gerenciada para recursos do Azure), a MFA não se aplica.

### <a name="minimize-the-use-of-password-based-authentication-for-users"></a>Minimizar o uso de autenticação baseada em senha para usuários

> [!NOTE]
> Mencionado em: #4 de prática de OSA, controle de acesso ISO (AC)

Os métodos de autenticação baseados em senha são uma forma mais fraca de autenticação. As credenciais podem ser comprometidas ou desfeitas indevidos.

**Como implementar**:

- Use uma autenticação integrada do Azure AD que elimina o uso de senhas.

**Práticas recomendadas**:

- Use a autenticação de logon único usando as credenciais do Windows. Federar o domínio do AD local com o Azure AD e usar a autenticação integrada do Windows (para computadores ingressados no domínio com o Azure AD).
  - Consulte o artigo [suporte do SSMS para autenticação integrada do Azure ad](sql-database-aad-authentication-configure.md#active-directory-integrated-authentication).

### <a name="minimize-the-use-of-password-based-authentication-for-applications"></a>Minimizar o uso de autenticação baseada em senha para aplicativos 

> [!NOTE]
> Mencionado em: #4 de prática de OSA, controle de acesso ISO (AC)

**Como implementar**:

- Habilite a identidade gerenciada do Azure. Você também pode usar a autenticação integrada ou baseada em certificado. 

**Práticas recomendadas**:

- Use [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).
  - [Identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md) 
  - [Identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
  - [Usar o banco de dados SQL do Azure do serviço de aplicativo com identidade gerenciada (sem alterações de código)](https://github.com/Azure-Samples/app-service-msi-entityframework-dotnet)

- Use a autenticação baseada em certificado para um aplicativo. 
  - Consulte este [exemplo de código](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/token). 

- Use a autenticação do Azure AD para o domínio federado integrado e o computador ingressado no domínio (consulte a seção acima).
  - Consulte o [aplicativo de exemplo para autenticação integrada](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth/integrated).

### <a name="protect-passwords-and-secrets"></a>Proteger senhas e segredos

Para casos em que as senhas não são podem ser evitados, verifique se elas estão protegidas.

**Como implementar**:

- Use Azure Key Vault para armazenar senhas e segredos. Sempre que aplicável, use o MFA para o banco de dados SQL do Azure com usuários do Azure AD.

**Práticas recomendadas**:

- Se não for possível evitar senhas ou segredos, armazene senhas de usuário e segredos de aplicativo no Azure Key Vault e gerencie o acesso por meio de políticas de acesso Key Vault. 

- Várias estruturas de desenvolvimento de aplicativos também podem oferecer mecanismos específicos à estrutura para proteger segredos no aplicativo. Por exemplo: [aplicativo ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=windows).

### <a name="use-sql-authentication-for-legacy-applications"></a>Usar a autenticação do SQL para aplicativos herdados 

A autenticação do SQL refere-se à autenticação de um usuário ao conectar-se ao banco de dados SQL do Azure usando o nome de usuário e senha. Será necessário criar um logon em cada servidor do banco de dados SQL ou em uma instância gerenciada, e um usuário criado em cada banco de dados.

**Como implementar**:

- Use a autenticação do SQL.

**Práticas recomendadas**:

- Como administrador do servidor, crie logons e usuários. A menos que o uso de usuários de banco de dados independente tenha senhas, todas as senhas são armazenadas no banco de dados mestre.
  - Consulte o artigo [controlando e concedendo acesso ao banco de dados SQL e SQL data warehouse](sql-database-manage-logins.md).

- Siga as práticas recomendadas de gerenciamento de senhas:
  - Forneça uma senha complexa, composta por letras, dígitos (0-9) e caracteres não alfanuméricos (como $, !, #, #ou %).
  - Use senhas mais longas em vez de caracteres selecionados aleatoriamente mais curtos.
  - Impor a alteração de senha manual pelo menos a cada 90 dias.

## <a name="access-management"></a>Gestão de acesso

O gerenciamento de acesso é o processo de controlar e gerenciar o acesso e os privilégios de usuários autorizados ao banco de dados SQL do Azure.

### <a name="implement-principle-of-least-privilege"></a>Implementar o princípio de privilégios mínimos

> [!NOTE]
> Mencionado em: controles FedRamp AC-06, NIST: AC-6, prática de OSA #3

O princípio de privilégios mínimos indica que os usuários não devem ter mais privilégios do que o necessário para concluir suas tarefas. Para obter mais informações, consulte o artigo [apenas administração suficiente](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview).

**Como implementar**:

Atribua apenas as [permissões](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) necessárias para concluir as tarefas necessárias:

- No plano de dados SQL: 
    - Use permissões granulares e funções de banco de dados definidas pelo usuário (ou funções de servidor em MI): 
        1. Criar as funções necessárias
            - [CRIAR FUNÇÃO](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql)
            - [CRIAR FUNÇÃO DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-role-transact-sql)
        1. Criar usuários necessários
            - [CRIAR USUÁRIO](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)
        1. Adicionar usuários como membros a funções 
            - [ALTERAR FUNÇÃO](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql)
            - [ALTERAR FUNÇÃO DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql)
        1. Em seguida, atribua permissões a funções. 
            - [CONCEDER](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) 
    - Certifique-se de não atribuir usuários a funções desnecessárias.

- Em Azure Resource Manager:
  - Use funções BUILTIN, se disponíveis ou funções RBAC personalizadas, e atribua as permissões necessárias.
    - [Funções internas do Azure](../role-based-access-control/built-in-roles.md) 
    - [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)

**Práticas recomendadas**:

As práticas recomendadas a seguir são opcionais, mas resultarão em melhor capacidade de gerenciamento e suporte de sua estratégia de segurança: 

- Se possível, comece com o menor conjunto possível de permissões e comece a adicionar permissões uma a uma se houver uma necessidade real (e justificativa) – em oposição à abordagem oposta: levando as permissões de longe passo a passo. 

- Evite atribuir permissões a usuários individuais. Use funções (funções de banco de dados ou de servidor) de forma consistente. As funções ajudam muito com as permissões de relatório e solução de problemas. (O RBAC do Azure dá suporte apenas à atribuição de permissão por meio de funções.) 

- Use funções internas quando as permissões das funções corresponderem exatamente às permissões necessárias para o usuário. Você pode atribuir usuários a várias funções. 

- Crie e use funções personalizadas quando as funções internas concederem permissões demais ou insuficientes. Funções típicas que são usadas na prática: 
  - Implantação de segurança 
  - Administrador 
  - Programador 
  - Equipe de suporte 
  - Auditor 
  - Processos automatizados 
  - Utilizador final 

- Lembre-se de que as permissões no Mecanismo de Banco de Dados de SQL Server podem ser aplicadas nos escopos a seguir. Quanto menor o escopo, menor o impacto das permissões concedidas: 
  - Servidor de banco de dados SQL do Azure (funções especiais no banco de dados mestre) 
  - Base de Dados 
  - Esquema (consulte também: [Schema-design para SQL Server: recomendações para design de esquema com a segurança em mente](http://andreas-wolter.com/en/schema-design-for-sql-server-recommendations-for-schema-design-with-security-in-mind/))
  - Objeto (tabela, exibição, procedimento, etc.) 
  > [!NOTE]
  > Não é recomendável aplicar permissões no nível de objeto porque esse nível adiciona complexidade desnecessária à implementação geral. Se você decidir usar permissões de nível de objeto, elas deverão ser claramente documentadas. O mesmo se aplica às permissões de nível de coluna, que são ainda menos recomendadas pelos mesmos motivos. As regras padrão para [Deny](https://docs.microsoft.com/sql/t-sql/statements/deny-object-permissions-transact-sql) não se aplicam a colunas.

- Execute verificações regulares usando a [VA (avaliação de vulnerabilidade)](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) para testar se há muitas permissões.

### <a name="implement-separation-of-duties"></a>Implementar separação de tarefas

> [!NOTE]
> Mencionado em: FedRamp: AC-04, NIST: AC-5, ISO: A. 6.1.2, PCI 6.4.2, SOC: CM-3, SDL-3

A separação de tarefas, também chamada de diferenciação de direitos, descreve a necessidade de dividir tarefa confidencial em várias tarefas que são atribuídas a diferentes usuários, geralmente para evitar violações de dados.

**Como implementar**:

- Identifique o nível necessário de separação de tarefas. Exemplos: 
  - Entre ambientes de desenvolvimento/teste e de produção 
  - Tarefas confidenciais de segurança do vs. tarefas de nível de gerenciamento do DBA (administrador de banco de dados) vs tarefas de desenvolvedor. 
    - Exemplos: auditor, criação de política de segurança para RLS (segurança em nível de função), implementando objetos de banco de dados SQL com permissões DDL.

- Identifique uma hierarquia abrangente de usuários (e processos automatizados) que acessam o sistema.

- Crie funções de acordo com os grupos de usuários necessários e atribua permissões a funções. 
  - Para tarefas em nível de gerenciamento no portal do Azure ou por meio da automação do PowerShell, use funções RBAC. Localize uma função interna que corresponda ao requisito ou crie uma função personalizada do RBAC usando as permissões disponíveis 
  - Crie funções de servidor para tarefas em todo o servidor (criando novos logons, bancos de dados) em uma instância gerenciada. 
  - Criar funções de banco de dados para tarefas em nível de banco de dados.

- Para determinadas tarefas confidenciais, considere a criação de procedimentos armazenados especiais assinados por um certificado para executar as tarefas em nome dos usuários. 
  - Exemplo: [tutorial: assinando procedimentos armazenados com um certificado](https://docs.microsoft.com/sql/relational-databases/tutorial-signing-stored-procedures-with-a-certificate) 

- Implemente Transparent Data Encryption (TDE) com chaves gerenciadas pelo cliente no Azure Key Vault para habilitar a separação de tarefas entre o proprietário dos dados e o proprietário da segurança. 
  - Consulte o artigo [Configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure na portal do Azure](../storage/common/storage-encryption-keys-portal.md). 

- Para garantir que um DBA não possa ver os dados que são considerados altamente confidenciais e ainda pode realizar tarefas do DBA, você pode usar Always Encrypted com separação de funções. 
  - Consulte os artigos, [visão geral do gerenciamento de chaves para Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted), [provisionamento de chave com separação de função](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-always-encrypted-keys-using-powershell#KeyProvisionWithRoles)e [rotação de chave mestra de coluna com separação de funções](https://docs.microsoft.com/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell#column-master-key-rotation-with-role-separation). 

- Em casos em que não é viável pelo menos não sem custos e esforços importantes que podem tornar o sistema quase inutilizável, as comprometeções podem ser feitas e atenuadas com o uso de controles de compensação, como: 
  - Intervenção humana em processos. 
  - Trilhas de auditoria – para obter mais informações sobre auditoria, consulte, [auditar eventos de segurança críticos](#audit-critical-security-events).

**Práticas recomendadas**:

- Certifique-se de que contas diferentes sejam usadas para ambientes de desenvolvimento/teste e produção. Contas diferentes ajudam a cumprir a separação de sistemas de produção & de teste.

- Evite atribuir permissões a usuários individuais. Use funções (funções de banco de dados ou de servidor) de forma consistente. Ter funções ajuda muito com as permissões de relatório e solução de problemas.

- Use papéis incorporados quando as permissões correspondem exatamente às permissões necessárias – se a união de todas as permissões de várias funções incorporadas levar a uma correspondência de 100%, também pode atribuir múltiplas funções simultaneamente. 

- Crie e use funções personalizadas quando funções internas concederem permissões demais ou permissões insuficientes. 

- As atribuições de função também podem ser feitas temporariamente, também conhecidas como DSD (separação dinâmica de funções), dentro das etapas de trabalho do SQL Agent no T-SQL ou usando o Azure PIM para funções RBAC. 

- Verifique se os DBAs não têm acesso às chaves de criptografia ou aos repositórios de chaves e os administradores de segurança com acesso às chaves não têm acesso ao banco de dados por vez. 

- Sempre certifique-se de ter uma trilha de auditoria para ações relacionadas à segurança. 

- Você pode recuperar a definição das funções RBAC internas para ver as permissões usadas e criar uma função personalizada com base em trechos e acumulações delas por meio do PowerShell 

- Uma vez que qualquer membro da db_owner papel de base de dados pode alterar as definições de segurança como a Encriptação transparente de dados (TDE), ou alterar o SLO, esta adesão deve ser concedida com cuidado. Por outro lado, muitas tarefas, como alterar qualquer definição de base de dados, como alterar opções de DB, requerem privilégios db_owner. A auditoria desempenha um papel fundamental em qualquer solução.

- Não é possível impedir que um db_owner de visualizar os dados dos utilizadores apenas com permissões. Se houver dados altamente sensíveis numa base de dados, o Always Encrypted pode ser usado para evitar que db_owners ou qualquer outro DBA os veja.

> [!NOTE]
> A obtenção de SoD (separação de funções) é desafiadora para tarefas relacionadas à segurança ou à solução de problemas. Outras áreas, como desenvolvimento e funções de usuário final, são mais fáceis de separar. A maioria dos controles relacionados à conformidade permite o uso de funções de controle alternativo, como a auditoria quando outras soluções não são práticas.

Para os leitores que desejam aprofundar-se no SoD, recomendamos os seguintes recursos: 

- Para o banco de dados SQL do Azure:  
  - [Controlando e concedendo acesso ao banco de dados SQL e SQL Data Warehouse](sql-database-manage-logins.md)
  - [Separação de mecanismos de tarefas para o desenvolvedor de aplicativos](https://docs.microsoft.com/previous-versions/sql/sql-server-2008/cc974525(v=sql.100)) 
  - [Separação de tarefas no SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=39269)
  - [Assinando procedimentos armazenados no SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server)

- Para o gerenciamento de recursos do Azure:
  - [Funções internas do Azure](../role-based-access-control/built-in-roles.md) 
  - [Custom roles for Azure resources](../role-based-access-control/custom-roles.md) (Funções personalizadas para recursos do Azure)
  - [Usando Azure AD Privileged Identity Management para acesso elevado](https://www.microsoft.com/en-us/itshowcase/using-azure-ad-privileged-identity-management-for-elevated-access)

### <a name="perform-regular-code-reviews"></a>Executar revisões de código regulares

> [!NOTE]
> Mencionado em: PCI: 6.3.2, SOC: SDL-3 

A separação de tarefas não está limitada aos dados no banco de dado, mas inclui o código do aplicativo. O código mal-intencionado pode potencialmente burlar os controles de segurança. Antes de implantar o código personalizado na produção, é essencial examinar o que está sendo implantado.

**Como implementar**:

- Use uma ferramenta de banco de dados como Azure Data Studio que ofereça suporte ao controle do código-fonte. 

- Implemente um processo de implantação de código segregado.

- Antes de confirmar a ramificação principal, uma pessoa (diferente do autor do próprio código) tem de inspecionar o código quanto à potencial elevação de riscos de privilégios, bem como modificações de dados mal-intencionados para proteger contra fraudes e acesso não autorizado. Isso pode ser feito usando mecanismos de controle do código-fonte.

**Práticas recomendadas**:

- Padronização: ajuda a implementar um procedimento padrão que deve ser seguido para qualquer atualização de código. 

- A avaliação de vulnerabilidade contém regras que verificam permissões excessivas, o uso de algoritmos de criptografia antigos e outros problemas de segurança em um esquema de banco de dados. 

- Verificações adicionais podem ser feitas em um ambiente de QA ou de teste usando a proteção avançada contra ameaças que verifica o código vulnerável à injeção de SQL.

- Exemplos de o que deve ser examinado: 
  - Criação de um usuário ou alteração de configurações de segurança de dentro de uma implantação automatizada de SQL-Code-Update. 
  - Um procedimento armazenado, que, dependendo dos parâmetros fornecidos, atualiza um valor monetário em uma célula de forma não compatível. 

- Certifique-se de que a pessoa que realiza a revisão seja um indivíduo diferente do autor do código de origem e seja especialista em revisões de código e codificação segura.

- Certifique-se de conhecer todas as fontes de alterações de código: o código pode estar em scripts T-SQL; pode ser que comandos ad hoc sejam executados ou implantados em forma de exibições, funções, gatilhos e procedimentos armazenados. Ele também pode fazer parte das definições de trabalho do SQL Agent (etapas), ser executado de pacotes do SSIS, Azure Data Factory e outros serviços.

## <a name="data-protection"></a>Proteção de dados

A proteção de dados é um conjunto de recursos para proteger informações importantes contra o comprometimento por criptografia ou ofuscação.

> [!NOTE]
> O Microsoft atesta o banco de dados SQL do Azure como sendo compatível com FIPS 140-2 nível 1. Isso é feito depois de verificar o uso estrito dos algoritmos aceitáveis de nível 1 do FIPS 140-2 e das instâncias validadas do FIPS 140-2 nível 1 desses algoritmos, incluindo consistência com comprimentos de chave necessários, gerenciamento de chaves, geração de chave e armazenamento de chave. Esse atestado destina-se a permitir que nossos clientes respondam à necessidade ou ao requisito para o uso de instâncias validadas do FIPS 140-2 nível 1 no processamento de dados ou na entrega de sistemas ou aplicativos. Definimos os termos "conformidade com o nível 1 do FIPS 140-2" e "regulamentação do FIPS 140-2 nível 1" usados na instrução acima para demonstrar sua aplicabilidade pretendida ao uso do governo canadense e norte-americano do termo diferente "FIPS 140-2 nível 1 validado". 


### <a name="encrypt-data-in-transit"></a>Criptografar dados em trânsito

> [!NOTE]
> Mencionado em: #6 de prática de OSA, família de controle ISO: criptografia

Protege seus dados enquanto os dados são movidos entre o cliente e o servidor. Consulte [segurança de rede](#network-security).

### <a name="encrypt-data-at-rest"></a>Criptografe dados em repouso

> [!NOTE]
> Mencionado em: #6 de prática de OSA, família de controle ISO: criptografia

A criptografia em repouso é a proteção criptográfica dos dados quando ela é mantida em arquivos de banco de dados, log e backup.

**Como implementar**:

- A [TDE (criptografia de banco de dados transparente)](transparent-data-encryption-azure-sql.md) com chaves gerenciadas pelo serviço é habilitada por padrão para qualquer banco de dados criado após 2017 no banco de dados SQL do Azure.
- Em uma instância gerenciada, se o banco de dados for criado como resultado de uma operação de restauração de um servidor local, a configuração TDE do banco de dados original será respeitada. Se o banco de dados original não tiver o TDE habilitado, recomendamos que TDE seja ativado manualmente para a instância gerenciada.

**Práticas recomendadas**:

- Não armazene dados que requerem criptografia em repouso no banco de dados mestre. O banco de dados mestre não pode ser criptografado com TDE.

- Use chaves gerenciadas pelo cliente no Azure Key Vault se precisar de maior transparência e controle granular sobre a proteção TDE. Azure Key Vault permite a capacidade de revogar permissões a qualquer momento para tornar o banco de dados inacessível. Você pode gerenciar centralmente protetores de TDE junto com outras chaves ou girar o protetor de TDE em seu próprio agendamento usando Azure Key Vault.

- Se você estiver usando chaves gerenciadas pelo cliente no Azure Key Vault, siga os artigos, [diretrizes para configurar o TDE com Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#recommendations-when-configuring-akv) e [como configurar a Dr geográfica com o Azure Key Vault](transparent-data-encryption-byok-azure-sql.md#geo-dr-and-customer-managed-tde).

### <a name="protect-sensitive-data-in-use-from-high-privileged-unauthorized-users"></a>Proteger dados confidenciais em uso de usuários com alto privilégio e não autorizados

Os dados em uso são os dados armazenados na memória do sistema de banco de dados durante a execução de consultas SQL. Se o seu banco de dados do armazena um dado confidencial, sua organização pode ser necessária para garantir que usuários com alto privilégio, como operadores da Microsoft ou DBAs em sua organização, sejam impedidos de extrair os dados da memória do processo de SQL Server e não sejam capazes para exibir os dados de texto sem formatação ao consultar o banco de dado.

**Como implementar**:

- Use [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) para garantir que os dados confidenciais não sejam expostos em texto não criptografado no banco de dados SQL do Azure, mesmo na memória/em uso. Isso protege os dados de DBAs (administradores de BD) e administradores de nuvem (ou atores ruins que podem representar usuários com alto privilégio, mas não autorizados) e oferece mais controle sobre quem pode acessar seus dados.

**Práticas recomendadas**:

- Always Encrypted não é um substituto para criptografar dados em repouso (TDE) ou em trânsito (SSL/TLS) e não deve ser usado para dados não confidenciais a fim de minimizar o impacto de desempenho e de funcionalidade. Usar Always Encrypted em conjunto com TDE e TLS é recomendado para proteção abrangente de dados em repouso, em trânsito e em uso. 

- Gerencie chaves de Always Encrypted com separação de função se você estiver usando Always Encrypted para proteger dados de DBAs mal-intencionados. Com a separação de funções, um administrador de segurança cria as chaves físicas e um DBA cria objetos de metadados de chave mestra de coluna e chave de criptografia de coluna, descrevendo as chaves físicas no banco de dados. Durante esse processo, o administrador de segurança não precisa acessar o banco de dados e o DBA não precisa acessar as chaves físicas em texto não criptografado. 
  - Consulte o artigo [Gerenciando chaves com a separação de funções](https://docs.microsoft.com/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted#managing-keys-with-role-separation) para obter detalhes. 

- Armazene suas chaves mestras de coluna em Azure Key Vault para facilitar o gerenciamento. Evite usar o repositório de certificados do Windows (e, em geral, soluções distribuídas do repositório de chaves, como soluções de gerenciamento de chaves centrais) que tornam o gerenciamento de chaves difícil. 

- Pense atentamente através das compensações de usar várias chaves (chave mestra de coluna ou chaves de criptografia de coluna). Mantenha o número de chaves pequeno para reduzir o custo de gerenciamento de chaves. Uma chave mestra de coluna e uma chave de criptografia de coluna por banco de dados geralmente são suficientes em ambientes de estado estacionário (não no meio de uma rotação de chave), a menos que você tenha grupos de usuários diferentes, cada um usando chaves diferentes e acessando dados diferentes.  

- Gire as chaves mestras de coluna de acordo com seus requisitos de conformidade. Se você também precisar girar as chaves de criptografia de coluna, considere usar a criptografia online para minimizar o tempo de inatividade do aplicativo. 
  - Consulte o artigo [Considerações sobre desempenho e disponibilidade](https://docs.microsoft.com/sql/relational-databases/security/encryption/configure-column-encryption-using-powershell#performance-and-availability-considerations). 

- Use a criptografia determinística se os cálculos (igualdade) nos dados precisarem ter suporte. Caso contrário, use a criptografia aleatória. Evite usar criptografia determinística para conjuntos de dados de entropia baixa ou conjuntos de dados com distribuição publicamente conhecida. 

- Se você estiver preocupado com o acesso de seus dados de terceiros legalmente sem seu consentimento, verifique se todos os aplicativos e ferramentas que têm acesso às chaves e aos dados em texto não criptografado são executados fora do Microsoft Azure Cloud. Sem acesso às chaves, a terceira parte não terá como descriptografar os dados, a menos que eles ignorem a criptografia.

- Always Encrypted não dá suporte facilmente à concessão de acesso temporário às chaves (e aos dados protegidos). Por exemplo, se você precisar compartilhar as chaves com um DBA, para permitir que o DBA execute algumas operações de limpeza em dados confidenciais e criptografados, a única maneira de a confiabilidade revogar o acesso aos dados do DBA será girar as chaves de criptografia de coluna e Colu chaves mestras de MN que protegem os dados, que é uma operação cara. 

- Para acessar os valores de texto sem formatação em colunas criptografadas, um usuário precisa ter acesso ao CMK, protegendo as colunas, que é configurada no repositório de chaves que contém o CMK. Além disso, o usuário precisa ter as permissões de banco de dados exibir qualquer definição de chave mestra de coluna e exibir qualquer coluna de definição de chave de criptografia.

### <a name="control-access-of-application-users-to-sensitive-data-through-encryption"></a>Controlar o acesso de usuários de aplicativos a dados confidenciais por meio de criptografia

A criptografia pode ser usada como uma maneira de garantir que apenas usuários de aplicativos específicos que tenham acesso às chaves de criptografia e possam exibir ou atualizar os dados.

**Como implementar**:

- Usar criptografia em nível de célula (CLE). Consulte o artigo [criptografar uma coluna de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) para obter detalhes. 
- Como alternativa, considere o uso de Always Encrypted, mas lembre-se de sua limitação, listada abaixo.

**Melhores práticas**

Ao usar CLE:

- Controlar o acesso a chaves por meio de permissões e funções do SQL. 

- Use AES (AES 256 recomendado) para criptografia de dados. Os algoritmos, como RC4, DES e TripleDES, são preteridos e não devem ser usados devido a vulnerabilidades conhecidas. 

- Proteja chaves simétricas com chaves/certificados assimétricos (não senhas) para evitar o uso do 3DES. 

- Tenha cuidado ao migrar um banco de dados usando a criptografia em nível de célula por meio de exportação/importação (arquivos bacpac). 
  - Consulte o artigo, [Recomendações para usar encriptação de nível celular na Base de Dados Azure SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/) sobre como evitar a perda de chaves ao migrar dados, e para outras orientações de boas práticas.

Ao usar Always Encrypted, tenha em mente que Always Encrypted foi projetado principalmente para proteger dados confidenciais em uso de usuários de alto privilégio do banco de dados SQL do Azure (operadores de nuvem, DBAs) – consulte [proteger dados confidenciais em uso de usuários com alto privilégio e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users). Esteja atento aos seguintes desafios ao usar Always Encrypted para proteger dados de usuários de aplicativos:

- Depois de conceder a um usuário acesso a dados confidenciais concedendo ao usuário permissões para acessar a chave de criptografia de coluna e a chave mestra de coluna, para revogar de forma confiável o acesso necessário para girar a chave de criptografia de coluna, que é uma operação cara que requer Criptografando novamente todas as colunas, a chave de criptografia de coluna protege. 

- Por padrão, todos os drivers de cliente da Microsoft que oferecem suporte a Always Encrypted mantêm um cache global (um por aplicativo) de chaves de criptografia de coluna. Depois que um driver de cliente adquire uma chave de criptografia de coluna de texto não criptografado contatando um repositório de chaves que mantém uma chave mestra de coluna, a chave de criptografia de coluna de texto sem formatação é armazenada em cache, o que torna o isolamento de dados de usuários de um desafio de aplicativo de vários usuários. Se seu aplicativo representar os usuários finais ao interagir com um repositório de chaves (como Azure Key Vault), depois que a consulta de um usuário popula o cache com uma chave de criptografia de coluna, uma consulta subsequente que requer a mesma chave, mas é disparada por outro usuário, usará o chave armazenada em cache. O driver não chamará o repositório de chaves e não verificará se o segundo usuário tem permissão para acessar a chave de criptografia da coluna. Como resultado, o usuário poderá ver os dados criptografados mesmo que o usuário não tenha acesso às chaves. Para obter o isolamento de usuários em um aplicativo de vários usuários, talvez seja necessário desabilitar o cache de chave de criptografia de coluna, o que causará sobrecargas de desempenho adicionais, pois o driver precisará entrar em contato com o repositório de chaves para cada criptografia ou descriptografia de dados operacional.

### <a name="protect-data-against-unauthorized-viewing-by-application-users-while-preserving-data-format"></a>Proteger dados contra exibição não autorizada por usuários de aplicativos enquanto preserva o formato de dados
Outra técnica para impedir que usuários não autorizados exibam dados é ofuscar ou mascarar os dados, preservando os tipos de dados e formatos para garantir que os aplicativos de usuário possam continuar manipulando e exibindo os dados.

**Como implementar**:

- Use [máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) para ofuscar colunas de tabela.

> [!NOTE]
> Always Encrypted não funciona com Máscara de Dados Dinâmicos. Não é possível criptografar e mascarar a mesma coluna, o que significa que você precisa priorizar a proteção de dados em uso versus mascarar os dados para os usuários do aplicativo por meio de Máscara de Dados Dinâmicos.

**Práticas recomendadas**:

> [!NOTE]
> Máscara de Dados Dinâmicos não pode ser usado para proteger dados de usuários de alto privilégio. As políticas de mascaramento não se aplicam aos utilizadores com acesso administrativo, como db_owner.

- Não permita que os usuários de aplicativos executem consultas ad hoc (pois eles podem contornar Máscara de Dados Dinâmicos).  
  - Consulte o artigo [ignorando o mascaramento usando a inferência ou técnicas de força bruta](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking#security-note-bypassing-masking-using-inference-or-brute-force-techniques) para obter detalhes.  

- Use uma política de controle de acesso apropriada (por meio de permissões do SQL, funções, RLS) para limitar as permissões de usuário para fazer atualizações nas colunas mascaradas. A criação de uma máscara em uma coluna não impede atualizações nessa coluna. Portanto, embora os usuários recebam dados mascarados ao consultar a coluna mascarada, os mesmos usuários poderão atualizar os dados se tiverem permissões de gravação.    

-  Máscara de Dados Dinâmicos não preserva as propriedades estatísticas dos valores mascarados, o que pode afetar os resultados da consulta (por exemplo, consultas que contêm predicados de filtragem ou junções nos dados mascarados).

## <a name="network-security"></a>Segurança da rede
Segurança de rede refere-se a controles de acesso e práticas recomendadas para proteger seus dados em trânsito para o banco de dados SQL do Azure.

### <a name="configure-my-client-to-connect-securely-to-azure-sql-database"></a>Configurar meu cliente para se conectar com segurança ao banco de dados SQL do Azure 
Impedir que computadores cliente e aplicativos se conectem ao banco de dados SQL do Azure contra vulnerabilidades conhecidas devido à dependência de protocolos antigos e conjuntos de codificação.

**Como implementar**:

- Verifique se os computadores cliente que se conectam ao banco de dados SQL do Azure estão usando o protocolo [TLS](sql-database-security-overview.md#transport-layer-security-tls-encryption-in-transit).

**Práticas recomendadas**:

- Configurar todos os seus aplicativos e ferramentas para se conectar ao banco de BD SQL com criptografia habilitada 
  - Encrypt = on, TrustServerCertificate = off (ou equivalente com drivers que não são da Microsoft). 

- Se seu aplicativo usa um driver que não dá suporte a TLS ou oferece suporte a uma versão mais antiga do TLS, substitua o driver, se possível. Se não for possível, avalie cuidadosamente os riscos de segurança. 

- Reduza os vetores de ataque por meio de vulnerabilidades no SSL 2,0, SSL 3,0, TLS 1,0 e TLS 1,1 desabilitando-os em computadores cliente que se conectam ao banco de dados SQL do Azure por [configurações de registro TLS (segurança de camada de transporte)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-10). 

- Verifique os conjuntos de codificação disponíveis no cliente por [conjuntos de codificação em TLS/SSL (Schannel SSP)](https://docs.microsoft.com/windows/desktop/SecAuthN/cipher-suites-in-schannel) e, especificamente, desabilite o 3Des por [configuração da ordem do conjunto de codificação TLS](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order). 

- Se você estiver usando uma instância gerenciada, use o tipo de conexão **proxy** (padrão), pois isso impõe a criptografia do lado do servidor. O tipo de conexão de **redirecionamento** atualmente não dá suporte à imposição de criptografia e só está disponível em conexões IP privadas. 
  - Para obter mais informações, consulte [arquitetura de conectividade do SQL do Azure – política de conexão](sql-database-connectivity-architecture.md#connection-policy).
  - A criptografia é imposta para os tipos de conexão proxy e redirecionar. 

### <a name="minimize-attack-surface"></a>Minimizar a superfície de ataque
Minimize o número de recursos que podem ser atacados por um usuário mal-intencionado implementando controles de acesso à rede para o banco de dados SQL do Azure.

> [!NOTE]
> Mencionado em: #5 de prática de OSA

**Como implementar**:

Em um servidor de banco de dados SQL do Azure (que contém banco de dados singleton ou pools elásticos):
- Defina permitir acesso aos serviços do Azure como desativado.

- Use pontos de extremidade de serviço de rede virtual e regras de firewall de rede virtual.

- Use o link privado (visualização).

Em uma instância gerenciada:
- Siga as diretrizes em [requisitos de rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

**Práticas recomendadas**:

- Restringindo o acesso ao banco de dados SQL do Azure conectando-se em um ponto de extremidade privado (por exemplo, usando um caminho de dado privado): 
  - Uma instância gerenciada pode ser isolada dentro de uma rede virtual para impedir o acesso externo. Aplicativos e ferramentas que residem na mesma ou em uma rede virtual emparelhada na mesma região podem acessá-lo diretamente. Aplicativos e ferramentas que estão em região diferente podem utilizar a conexão VNet a VNet ou o emparelhamento de circuito do ExpressRoute para estabelecer a conexão. O cliente deve usar NSG (grupos de segurança de rede) para restringir o acesso pela porta 1433 somente a recursos que exigem acesso a uma instância gerenciada 
  - Para um servidor de banco de dados SQL (que contenha um ou mais pools elásticos), use o recurso de [link privado](sql-database-private-endpoint-overview.md) que fornece um IP privado dedicado para o servidor de banco de dados SQL dentro de sua rede virtual. Você também pode usar [pontos de extremidade de serviço de rede virtual com regras de firewall de rede virtual](sql-database-vnet-service-endpoint-rule-overview.md) para restringir o acesso aos servidores do banco de dados SQL.
  - Os usuários móveis devem utilizar conexões VPN ponto a site para se conectarem por meio do caminho de dados.
  - Os usuários conectados à sua rede local devem utilizar a conexão VPN site a site ou o ExpressRoute para se conectarem por meio do caminho de dados.

- Você pode acessar o banco de dados SQL do Azure conectando-se a um ponto de extremidade público (por exemplo, usando um caminho de dado público). As seguintes práticas recomendadas devem ser consideradas: 
  - Para um servidor de banco de dados SQL, use [regras de firewall de IP](sql-database-firewall-configure.md) para restringir o acesso somente a endereços IP autorizados.
  - Para uma instância gerenciada, use NSG (grupos de segurança de rede) para restringir o acesso pela porta 3342 somente aos recursos necessários. Para obter mais informações, consulte [usar uma instância gerenciada do banco de dados SQL do Azure com segurança com pontos de extremidade públicos](sql-database-managed-instance-public-endpoint-securely.md). 

> [!NOTE]
> Um ponto de extremidade público de instância gerenciada não está habilitado por padrão e deve ser habilitado explicitamente. Se a política da empresa não permitir o uso de pontos de extremidade públicos, use [Azure Policy](../governance/policy/overview.md) para impedir a habilitação de pontos de extremidade públicos em primeiro lugar.

- Configurar os componentes de rede do Azure: 
  - Siga [as práticas recomendadas do Azure para segurança de rede](../security/fundamentals/network-best-practices.md).
  - Planejar a configuração de rede virtual (VNet) por práticas recomendadas descritas em perguntas frequentes [(FAQ) e plano da rede virtual do Azure](../virtual-network/virtual-networks-faq.md) . 
  - Segmente uma VNet em várias sub-redes e atribua recursos para função semelhante à mesma sub-rede (por exemplo, recursos de front-end vs back-end).
  - Use [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md) para controlar o tráfego entre sub-redes dentro do limite de VNet do Azure.
  - Habilite o [observador de rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) para sua assinatura para monitorar o tráfego de rede de entrada e saída.

### <a name="configure-power-bi-for-secure-connections-to-azure-sql-database"></a>Configurar Power BI para conexões seguras com o banco de dados SQL do Azure

**Práticas recomendadas**:

- Para Power BI Desktop, use o caminho de dados particular sempre que possível. 

- Verifique se Power BI Desktop está se conectando usando o TLS 1.2 definindo a chave do registro no computador cliente de acordo com as configurações de [protocolo TLS](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) . 

- Restringir o acesso a dados para usuários específicos por meio da [RLS (segurança em nível de linha) com Power bi](https://docs.microsoft.com/power-bi/service-admin-rls). 

- Para Power BI serviço, use o [Gateway de dados local](https://docs.microsoft.com/power-bi/service-gateway-onprem), tendo em mente [limitações e considerações](https://docs.microsoft.com/power-bi/service-gateway-deployment-guidance#installation-considerations-for-the-on-premises-data-gateway).

### <a name="configure-app-service-for-secure-connections-to-azure-sql-database"></a>Configurar o serviço de aplicativo para conexões seguras com o banco de dados SQL do Azure

**Práticas recomendadas**:

- Para um aplicativo Web simples, a conexão por meio do ponto de extremidade público requer a configuração **permitir serviços do Azure** para ativado. 

- [Integre seu aplicativo a uma rede virtual do Azure](../app-service/web-sites-integrate-with-vnet.md) para conectividade de caminho de dados particular para uma instância gerenciada. Opcionalmente, você também pode implantar um aplicativo Web com os [ambientes do serviço de aplicativo (ase)](../app-service/environment/intro.md). 

- Para aplicativo Web com ASE ou aplicativo Web integrado de VNet conectando-se a um banco de dados no servidor de banco de dados SQL, você pode usar [pontos de extremidade de serviço vnet e regras de firewall de vnet](sql-database-vnet-service-endpoint-rule-overview.md) para limitar o acesso de uma VNet e sub-rede específicas e, em seguida, definir **permitir que os serviços do Azure** sejam desativados Você também pode conectar o ASE a uma instância gerenciada em um caminho de dados privado.  

- Verifique se seu aplicativo Web está configurado de acordo com o artigo, [as práticas recomendadas para proteger aplicativos Web e móveis de PaaS usando Azure app serviço](../security/security-paas-applications-using-app-services.md). 

- Instale o [WAF (firewall do aplicativo Web)](../application-gateway/waf-overview.md) para proteger seu aplicativo Web contra explorações e vulnerabilidades comuns.

### <a name="configure-azure-vm-hosting-for-secure-connections-to-azure-sql-database"></a>Configurar a hospedagem de VM do Azure para conexões seguras com o banco de dados SQL do Azure

**Práticas recomendadas**:

- Use uma combinação de regras de permissão e negação no NSGs de VMs do Azure para controlar quais regiões podem ser acessadas da VM.

- Verifique se sua VM está configurada de acordo com o artigo, [as práticas recomendadas de segurança para cargas de trabalho de IaaS no Azure](../security/azure-security-iaas.md).

- Verifique se todas as VMs estão associadas a uma VNet e a uma sub-rede específicas. 

- Avalie se você precisa da rota padrão 0.0.0.0/Internet conforme a orientação em [sobre o túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md#about-forced-tunneling). 
  - Se sim – por exemplo, sub-rede de front-end-mantenha a rota padrão.
  - Se não, por exemplo, a camada intermediária ou a sub-rede de back-end, então habilite o túnel forçado para que nenhum tráfego passe pela Internet para alcançar no local (a. k. a entre locais). 

- Implemente [rotas padrão opcionais](../virtual-network/virtual-networks-udr-overview.md#optional-default-routes) se você estiver usando o emparelhamento ou conectando-se ao local. 

- Implemente [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md#user-defined) se você precisar enviar todo o tráfego na VNet para uma solução de virtualização de rede para a inspeção de pacotes. 

- Use [pontos de extremidade de serviço de VNet](sql-database-vnet-service-endpoint-rule-overview.md) para proteger o acesso aos serviços PaaS como o armazenamento do Azure por meio da rede de backbone do Azure. 

### <a name="protect-against-distributed-denial-of-service-ddos-attacks"></a>Proteger contra ataques de DDoS (negação de serviço distribuído)
Ataques de DDoS (negação de serviço distribuído) são tentativas de um usuário mal-intencionado enviar uma inundação do tráfego de rede para o banco de dados SQL do Azure com o objetivo de sobrecarregar a infraestrutura do Azure e fazer com que ela rejeite logons e cargas de trabalho válidos.

> [!NOTE]
> Mencionado em: #9 de prática de OSA

**Como implementar**:

A proteção contra DDoS é habilitada automaticamente como parte da plataforma do Azure. Ele inclui o monitoramento de tráfego Always on e a mitigação em tempo real de ataques de nível de rede em pontos de extremidade públicos. 

- Use a [proteção contra DDoS do Azure](../virtual-network/ddos-protection-overview.md) para monitorar endereços IP públicos associados aos recursos implantados em redes virtuais.

- Use a [proteção avançada contra ameaças para o banco de dados SQL do Azure](sql-database-threat-detection-overview.md) para detectar ataques de dos (negação de serviço) contra bancos.

**Práticas recomendadas**:

- Siga as práticas descritas em [minimizar a superfície de ataque](#minimize-attack-surface) para minimizar as ameaças de ataque de DDoS. 

- O alerta de **credenciais SQL de força bruta** da proteção avançada contra ameaças ajuda a detectar ataques de força bruta. Em alguns casos, o alerta pode até mesmo distinguir cargas de trabalho de teste de penetração. 

- Para aplicativos de Hospedagem de VM do Azure que se conectam ao banco de dados SQL: 
  - Siga a recomendação para restringir o acesso por meio de pontos de extremidade voltados para a Internet na central de segurança do Azure. 
  - Use conjuntos de dimensionamento de máquinas virtuais para executar várias instâncias do seu aplicativo em VMs do Azure. 
  - Desabilite o RDP e o SSH da Internet para evitar ataques de força bruta. 

## <a name="monitoring-logging-and-auditing"></a>Monitoramento, registro em log e auditoria  
Esta seção refere-se a recursos para ajudá-lo a detectar atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Ele também descreve as práticas recomendadas para configurar a auditoria de banco de dados para rastrear e capturar eventos de banco de dados.

### <a name="protect-databases-against-attacks"></a>Proteger bancos de dados contra ataques 
A proteção avançada contra ameaças permite que você detecte e responda a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança em atividades anormais.

**Como implementar**:

- Use a [proteção avançada contra ameaças para SQL](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts) para detectar tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados, incluindo:
  - Ataque de injeção de SQL.
  - Roubo/vazamento de credenciais.
  - Abuso de privilégio.
  - Vazamento de dados.

**Práticas recomendadas**:

- Configure a de [segurança de dados avançada](sql-database-advanced-data-security.md#getting-started-with-ads) para o banco de dado SQL do Azure para um servidor de banco de dados SQL específico ou uma instância gerenciada ou para todos os servidores de banco do dados SQL e instâncias gerenciadas em uma assinatura alternando para a [camada Standard da central de segurança](../security-center/security-center-pricing.md) 

- Para uma experiência de investigação completa, é recomendável habilitar a [auditoria do banco de dados SQL](sql-database-auditing.md) para rastrear eventos de banco de dados e gravá-los em um log de auditoria em uma conta de armazenamento do Azure ou no espaço de trabalho do Azure log Analytics. 

### <a name="audit-critical-security-events"></a>Auditar eventos de segurança críticos
O rastreamento de eventos de banco de dados ajuda você a compreender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança. Ele também habilita e facilita a adesão aos padrões de conformidade. 

**Como implementar**:

- Habilite a [auditoria do banco de dados SQL](sql-database-auditing.md) para acompanhar eventos de banco de dados e gravá-los em um log de auditoria em sua conta de armazenamento do Azure, log Analytics espaço de trabalho (versão prévia) ou hubs de eventos (versão prévia). 

- Os logs de auditoria podem ser gravados em uma conta de armazenamento do Azure, em um espaço de trabalho Log Analytics para consumo por Azure Monitor logs ou no Hub de eventos para consumo usando o Hub de eventos. Você pode configurar qualquer combinação dessas opções e os logs de auditoria serão gravados em cada um. 

**Práticas recomendadas**:

- Ao configurar a [auditoria de banco](sql-database-auditing.md) de dados SQL no servidor de banco de dados para auditoria de eventos, todos os bancos de dados existentes e recém-criados nesse servidor serão auditados.
- Por padrão, a política de auditoria inclui todas as ações (consultas, procedimentos armazenados e logons bem-sucedidos e com falha) em relação aos bancos de dados, o que pode resultar em alto volume de logs de auditoria. É recomendável que os clientes [configurem a auditoria para diferentes tipos de ações e grupos de ações usando o PowerShell](sql-database-auditing.md#subheading-7), para controlar o número de ações auditadas e para minimizar o risco de perda de eventos. Isso permitirá que os clientes capturem apenas os dados de auditoria verdadeiramente necessários.
- Os logs de auditoria podem ser consumidos diretamente no [portal do Azure](https://portal.azure.com/)ou do local de armazenamento que foi configurado. 


> [!NOTE]
> Habilitar a auditoria para Log Analytics incorrerá em custo com base nas taxas de ingestão. Esteja ciente do custo associado com o uso dessa [opção](https://azure.microsoft.com/pricing/details/monitor/)ou considere armazenar os logs de auditoria em uma conta de armazenamento do Azure. 

**Recursos adicionais**:

- [Auditoria do banco de dados SQL](sql-database-auditing.md)
- [SQL Server auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) 

### <a name="secure-audit-logs"></a>Proteger logs de auditoria
Para dar suporte à separação de tarefas e separar o DBA de auditores, é fundamental tomar medidas para restringir o acesso à conta de armazenamento. 

**Como implementar**:

- Ao salvar os logs de auditoria no armazenamento do Azure, certifique-se de que o acesso à conta de armazenamento seja restrito aos princípios de segurança mínimos usando o controle de acesso à conta de armazenamento.

- Para obter mais informações, consulte [autorizando o acesso ao armazenamento do Azure](../storage/common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

**Práticas recomendadas**:

- Controlar o acesso ao destino de auditoria é um conceito fundamental para separar DBA de auditores. 

- Ao auditar o acesso a dados confidenciais, considere proteger os dados com a criptografia de dados para evitar o vazamento de informações para o auditor. Para obter mais informações, consulte a seção [proteger dados confidenciais em uso de usuários com alto privilégio e não autorizados](#protect-sensitive-data-in-use-from-high-privileged-unauthorized-users).

## <a name="security-management"></a>Gestão de Segurança

Esta seção descreve os diferentes aspectos e as práticas recomendadas para o gerenciamento de sua postura de segurança de bancos de dados. Ele inclui as práticas recomendadas para garantir que seus bancos de dados estejam configurados para atender aos padrões de segurança, para descobrir e classificar e controlar o acesso a dados potencialmente confidenciais em seus bancos. 

### <a name="ensure-that-the-databases-are-configured-to-meet-security-best-practices"></a>Verifique se os bancos de dados estão configurados para atender às práticas recomendadas de segurança 

Aprimore de forma proativa a segurança do banco de dados descobrindo e corrigindo possíveis vulnerabilidades do banco de dados.

**Como implementar**:

- Habilite a [avaliação de vulnerabilidades do SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) (VA) para verificar se há problemas de segurança no banco de dados e executar periodicamente automaticamente em seus bancos de dados.

**Práticas recomendadas**:

- Inicialmente, execute o VA em seus bancos de dados e itere corrigindo verificações com falha que são as práticas recomendadas de segurança opostas e configurando linhas de base para configurações aceitáveis até que a verificação saia de ' limpar ' (todas as verificações são aprovadas).  

- Configure verificações recorrentes periódicas para serem executadas uma vez por semana e configure a pessoa relevante para receber emails de resumo. 

- Examine o resumo do VA após cada verificação semanal. Para quaisquer vulnerabilidades encontradas, avalie a descompasso do resultado da verificação anterior e determine se a verificação deve ser resolvida ou se há um motivo legítimo para a alteração na configuração.   

- Resolver verificações e atualizar linhas de base quando relevantes ou criar itens de tíquete para resolver ações e rastreá-los até que eles sejam resolvidos. 

**Recursos adicionais**:

- [Avaliação de vulnerabilidade do SQL](https://docs.microsoft.com/sql/relational-databases/security/sql-vulnerability-assessment) 
- [O serviço de avaliação de vulnerabilidade do SQL ajuda a identificar vulnerabilidades de banco de dados](sql-vulnerability-assessment.md)

### <a name="identify-and-tag-sensitive-data"></a>Identificar e marcar dados confidenciais 

Descubra colunas que potencialmente contêm dados confidenciais. Classifique as colunas para utilizar cenários de proteção e auditoria com base em sensibilidade avançada. 

**Como implementar**:

- Use a [descoberta de dados SQL e a classificação](sql-database-data-discovery-and-classification.md) para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos. 
  - Exiba as recomendações de classificação criadas pela descoberta automatizada no painel de classificação e descoberta de dados SQL e aceite as classificações relevantes, de modo que seus dados confidenciais sejam marcados persistentemente com classificação las. 
  - Adicione manualmente classificações para quaisquer campos de dados confidenciais adicionais que não foram descobertos pelo mecanismo automatizado. 
- Para obter mais informações, consulte [classificação de & do SQL Data Discovery](https://docs.microsoft.com/sql/relational-databases/security/sql-data-discovery-and-classification).

**Práticas recomendadas**:

- Monitore o painel de classificação regularmente para obter uma avaliação precisa do estado de classificação do banco de dados. Um relatório sobre o estado de classificação do banco de dados pode ser exportado ou impresso para compartilhamento para fins de conformidade e auditoria.

- Monitore continuamente o status dos dados confidenciais recomendados na avaliação de vulnerabilidades do SQL, rastreando a regra de descoberta de dados confidenciais e identificando qualquer descompasso nas colunas recomendadas para classificação.  

- Use a classificação de forma adequada às necessidades específicas de sua organização. Personalize sua política de proteção de informações (rótulos de sensibilidade, tipos de informações, lógica de descoberta) na política de [proteção de informações do SQL](../security-center/security-center-info-protection-policy.md) na central de segurança do Azure. 

### <a name="track-access-to-sensitive-data"></a>Controlar o acesso a dados confidenciais 
Monitore quem acessa dados confidenciais e captura consultas em dados confidenciais em logs de auditoria.

**Como implementar**:

- Use a auditoria do SQL e a classificação de dados em combinação. 
  - No log de [auditoria do banco](sql-database-auditing.md) de dados SQL, você pode controlar o acesso especificamente a seus dados confidenciais e exibir informações como os dados que foram acessados, bem como seu rótulo de confidencialidade (consulte auditoria de [acesso a dados confidenciais](sql-database-data-discovery-and-classification.md#subheading-3)). 

**Práticas recomendadas**:

- Consulte as práticas recomendadas para as seções auditoria e classificação de dados: 
  - [Auditar eventos de segurança críticos](#audit-critical-security-events) 
  - [Identificar e marcar dados confidenciais](#identify-and-tag-sensitive-data) 

### <a name="visualize-security-and-compliance-status"></a>Visualizar o status de conformidade e segurança 

Use um sistema de gerenciamento de segurança de infraestrutura unificado que fortaleça a postura de segurança de seus data centers (incluindo bancos de dados SQL). Exiba uma lista de recomendações relacionadas à segurança de seus bancos de dados e status de conformidade.

**Como implementar**:

- Monitore as recomendações de segurança relacionadas ao SQL e as ameaças ativas na [central de segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/).

## <a name="common-security-threats-and-potential-mitigations"></a>Ameaças comuns de segurança e possíveis mitigações

Esta seção ajuda a localizar medidas de segurança para proteção contra determinados vetores de ataque. Espera-se que a maioria das atenuações possa ser obtida seguindo uma ou mais das diretrizes de segurança acima.

### <a name="security-threat-data-exfiltration"></a>Ameaça de segurança: data vazamento

Vazamento de dados é a cópia, transferência ou recuperação não autorizada de dados de um computador ou servidor. Consulte uma definição para [Data vazamento](https://en.wikipedia.org/wiki/Data_exfiltration) na Wikipédia.

Conectar-se ao servidor de banco de dados SQL do Azure por meio de um ponto de extremidade público apresenta um risco de vazamentoção do dado, pois exige que os clientes Abram seus firewalls para IPs  

**Cenário 1**: um aplicativo em uma VM do Azure conecta-se a um banco de dados em um servidor de banco de dados SQL do Azure. Um ator não autorizado obtém acesso à VM e A compromete. Nesse cenário, vazamento de dados significa que uma entidade externa que usa a VM não autorizada se conecta ao banco de dados, copia os dados pessoais e os armazena em um armazenamento de BLOBs ou em um banco de dados SQL diferente em uma assinatura diferente.

**Cenário 2**: um DBA Rouge. Esse cenário geralmente é gerado por clientes sensíveis à segurança de setores regulamentados. Nesse cenário, um usuário de alto privilégio pode copiar dados do banco de dado SQL do Azure para outra assinatura não controlada pelo proprietário dos dados.

**Possíveis mitigações**:

Hoje, o banco de dados SQL do Azure oferece as seguintes técnicas para atenuar as ameaças de vazamentoção de data: 

- Use uma combinação de regras de permissão e negação no NSGs de VMs do Azure para controlar quais regiões podem ser acessadas da VM. 
- Se você estiver usando um servidor de banco de dados SQL do Azure (que contém os bancos elásticos ou dados singleton), defina o seguinte:
  - Permitir que os serviços do Azure sejam desativados.
  - Permita somente o tráfego da sub-rede que contém sua VM do Azure Configurando uma regra de firewall de VNet.
  - Usar [link privado](sql-database-private-endpoint-overview.md)
- Para uma instância gerenciada, usar acesso IP privado, por padrão, resolve a primeira preocupação de vazamento de dados de uma VM não autorizada. Ative o recurso de delegação de sub-rede em uma sub-rede que atenderá a definir automaticamente a política mais restritiva em uma sub-rede de instância gerenciada.
- A preocupação com o DBA não autorizado é mais exposta com uma instância gerenciada, pois tem uma área de superfície maior e os requisitos de rede são visíveis para os clientes. A melhor mitigação para isso é aplicar todas as práticas neste guia de segurança para evitar o cenário de DBA não autorizado em primeiro lugar (não apenas para vazamento de dados). Always Encrypted é um método para proteger dados confidenciais criptografando-os e mantendo a chave inacessível para o DBA.

## <a name="security-aspects-of-business-continuity-and-availability"></a>Aspectos de segurança de continuidade dos negócios e disponibilidade

A maioria dos padrões de segurança aborda a disponibilidade de dados em termos de continuidade operacional, obtida pela implementação de redundância e recursos de failover para evitar pontos únicos de falha. Para cenários de desastre, é uma prática comum manter backups de arquivos de dados e de log. A seção a seguir fornece uma visão geral de alto nível dos recursos que são internos ao Azure, bem como opções adicionais que podem ser configuradas para atender a necessidades específicas: 

- O Azure oferece alta disponibilidade interna: [alta disponibilidade e banco de dados SQL do Azure](sql-database-high-availability.md) 

- A camada de Comercialmente Crítico inclui grupos de failover, zonas de várias disponibilidade, backups de log completos e diferenciais e backups de restauração pontual habilitados por padrão:  
  - [Alta disponibilidade e banco de dados SQL do Azure-configuração com redundância de zona](sql-database-high-availability.md#zone-redundant-configuration)
  - [Cópias de segurança automatizadas](sql-database-automated-backups.md)
  - [Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados – restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

- Recursos adicionais de continuidade de negócios, como grupos de failover automático em diferentes áreas geográficas do Azure, podem ser configurados conforme descrito aqui: [visão geral da continuidade dos negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md)

## <a name="next-steps"></a>Passos seguintes

- Veja [uma visão geral dos recursos de segurança do banco de dados SQL do Azure](sql-database-security-overview.md)