---
title: Visão geral da segurança do banco de dados do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos recursos de segurança do banco de dados do Azure.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: dc4c36735ade8a094acbf897ee040a239a1bed48
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727417"
---
# <a name="azure-database-security-overview"></a>Visão geral da segurança do banco de dados Azure

A segurança é uma das principais preocupações para o gerenciamento de bancos de dados e sempre foi uma prioridade para o Azure SQL Database. O banco de dados SQL do Azure suporta segurança de conexão com regras de firewall e criptografia de conexão. Ele dá suporte à autenticação com o nome de usuário e a senha e a autenticação do Azure Active Directory (Azure AD), que usa identidades gerenciadas pelo Azure Active Directory. A autorização usa o controle de acesso baseado em função.

O banco de dados SQL do Azure dá suporte à criptografia executando criptografia e descriptografia em tempo real de bancos de dados, backups associados e arquivos de log de transações em repouso sem a necessidade de alterações no aplicativo.

A Microsoft fornece maneiras adicionais de criptografar dados corporativos:

-   A criptografia em nível de célula está disponível para criptografar colunas específicas ou até mesmo células de dados com chaves de criptografia diferentes.
-   Se você precisar de um módulo de segurança de hardware ou gerenciamento central de sua hierarquia de chaves de criptografia, considere o uso de Azure Key Vault com SQL Server em uma VM (máquina virtual) do Azure.
-   Always Encrypted (atualmente em visualização) torna a criptografia transparente para os aplicativos. Ele também permite que os clientes criptografem dados confidenciais dentro de aplicativos cliente sem compartilhar as chaves de criptografia com o SQL Database.

A auditoria do banco de dados SQL do Azure permite que as empresas registrem eventos em um log de auditoria no armazenamento do Azure. A Auditoria da Base de Dados SQL também pode ser integrada no Microsoft Power BI para facilitar a leitura detalhada de relatórios e análises.

Os bancos de dados SQL do Azure podem ser rigidamente protegidos para atender à maioria dos requisitos regulatórios ou de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS nível 1. Uma lista atual de certificações de conformidade de segurança está disponível no [site da central de confiabilidade Microsoft Azure](https://azure.microsoft.com/support/trust-center/services/).

Este artigo apresenta as noções básicas de proteção dos bancos de dados do Microsoft Azure SQL para a estrutura, a tabela e a data relacional. Em particular, este artigo irá ajudá-lo a começar a utilizar recursos para proteger dados, controlar o acesso e a monitorização proativa.

## <a name="protection-of-data"></a>Proteção de dados

O banco de dados SQL ajuda a proteger os seus dados fornecendo criptografia:

- Para dados em movimento por meio da [segurança de camada de transporte (TLS)](https://support.microsoft.com/kb/3135244).
- Para dados em repouso por meio da Transparent [Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242).
- Para dados em uso por meio de [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Para outras formas de encriptar os seus dados, considere:

-   A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
-   [Azure Key Vault com SQL Server em uma VM do Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), se você precisar de um módulo de segurança de hardware ou gerenciamento central de sua hierarquia de chave de criptografia.

### <a name="encryption-in-motion"></a>Criptografia em movimento

Um problema comum para todos os aplicativos cliente/servidor é a necessidade de privacidade à medida que os dados se movimentam em redes públicas e privadas. Se a movimentação de dados pela rede não estiver criptografada, haverá a chance de que ela possa ser capturada e roubada por usuários não autorizados. Quando estiver lidando com os serviços de banco de dados, verifique se eles são criptografados entre o cliente e o servidor. Verifique também se os dados são criptografados entre servidores de banco de dados que se comunicam entre si e com aplicativos de camada intermediária.

Um problema ao administrar uma rede é proteger os dados que estão sendo enviados entre aplicativos em uma rede não confiável. Você pode usar o [TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, usá-lo para criptografar mensagens entre as partes autenticadas.

No processo de autenticação, um cliente TLS/SSL envia uma mensagem para um servidor TLS/SSL. O servidor responde com as informações que o servidor precisa para se autenticar. O cliente e o servidor executam uma troca adicional de chaves de sessão e a caixa de diálogo de autenticação termina. Quando a autenticação é concluída, a comunicação protegida por SSL pode começar entre o servidor e o cliente por meio das chaves de criptografia simétrica que são estabelecidas durante o processo de autenticação.

Todas as conexões com o banco de dados SQL do Azure exigem Criptografia (TLS/SSL) em todos os momentos, enquanto os dados estão "em trânsito" para e do banco de dados. O banco de dados SQL usa TLS/SSL para autenticar servidores e clientes e, em seguida, usá-lo para criptografar mensagens entre as partes autenticadas. 

Na cadeia de conexão do seu aplicativo, você deve especificar parâmetros para criptografar a conexão e não para confiar no certificado do servidor. (Isso é feito para você se você copiar a cadeia de conexão do portal do Azure.) Caso contrário, a conexão não verificará a identidade do servidor e será suscetível a ataques "Man-in-the-middle". Para o driver ADO.net, por exemplo, esses parâmetros de cadeia de `Encrypt=True` conexão `TrustServerCertificate=False`são e.

### <a name="encryption-at-rest"></a>Encriptação inativa

Você pode tomar várias precauções para ajudar a proteger o banco de dados. Por exemplo, crie um sistema seguro, criptografe ativos confidenciais e crie um firewall em todos os servidores de banco de dados. Mas, em um cenário em que a mídia física (como unidades ou fitas de backup) é roubada, uma parte mal-intencionada pode apenas restaurar ou anexar o banco de dados e procurar os mesmos.

Uma solução é criptografar os dados confidenciais no banco e proteger as chaves que são usadas para criptografar os dados com um certificado. Essa solução impede que qualquer pessoa sem as chaves use os dados, mas esse tipo de proteção deve ser planejado.

Para resolver esse problema, SQL Server e o banco de dados SQL dão suporte à Transparent [Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). A Transparent Data Encryption criptografa os arquivos de dados SQL Server e SQL Database, conhecidos como dados de criptografia em repouso.

A Transparent Data Encryption ajuda a proteger contra a ameaça de atividades mal-intencionadas. Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo.  

A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados. No banco de dados SQL, a chave de criptografia de banco de dados é protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de banco de dados SQL.

Se um banco de dados estiver em uma relação de DR geográfica, ele será protegido por uma chave diferente em cada servidor. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles compartilharão o mesmo certificado interno. A Microsoft gira automaticamente esses certificados pelo menos a cada 90 dias. 

Para obter mais informações, consulte Transparent [Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Criptografia em uso (cliente)

A maioria das violações de dados envolve o roubo de dados críticos, como números de cartão de crédito ou informações de identificação pessoal. Os bancos de dados podem ser escondido de tesouro de informações confidenciais. Eles podem conter dados pessoais dos clientes (como números de identificação nacional), informações confidenciais competitivas e propriedade intelectual. Dados perdidos ou roubados, especialmente dados do cliente, podem resultar em danos à marca, desvantagem competitiva e multas graves – até mesmo processos judiciais.

![O recurso Always Encrypted ilustrado como um bloqueio e uma chave](./media/database-security-overview/azure-database-fig1.png)

[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx) é um recurso projetado para proteger dados confidenciais armazenados no banco de dados SQL do Azure ou em bancos de SQL Server. O Always Encrypted permite que os clientes criptografem dados confidenciais dentro de aplicativos cliente e nunca revelem as chaves de criptografia para o mecanismo de banco de dados (SQL Server ou banco de dados SQL).

Always Encrypted fornece uma separação entre as pessoas que possuem os dados (e pode exibi-la) e as pessoas que gerenciam os dados (mas não devem ter acesso). Ele ajuda a garantir que os administradores de banco de dados locais, operadores de banco de dados de nuvem ou outros usuários com alto privilégio, mas não autorizados, não possam acessar os dados criptografados.

Além disso, Always Encrypted torna a criptografia transparente para os aplicativos. Um driver habilitado para Always Encrypted é instalado no computador cliente para que ele possa criptografar e descriptografar automaticamente os dados confidenciais no aplicativo cliente. O driver criptografa os dados em colunas confidenciais antes de passar os dados para o mecanismo do banco de dados. O driver reescreve automaticamente as consultas para que a semântica para o aplicativo seja preservada. Da mesma forma, o driver descriptografa de modo transparente os dados armazenados em colunas de banco de dado criptografado, contidos nos resultados da consulta.

## <a name="access-control"></a>Controlo de acesso

Para fornecer segurança, o banco de dados SQL controla o acesso usando:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os usuários a ações e dados específicos.

### <a name="database-access"></a>Acesso à Base de Dados

A proteção de dados começa com o controle do acesso aos seus dados. O datacenter que hospeda seus dados gerencia o acesso físico. Você pode configurar um firewall para gerenciar a segurança na camada de rede. Você também controla o acesso Configurando logons para autenticação e definindo permissões para funções de servidor e banco de dados.

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

O [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](/azure/sql-database/sql-database-firewall-configure).

O serviço do banco de dados SQL do Azure está disponível somente pela porta TCP 1433. Para acessar um banco de dados SQL do seu computador, verifique se o Firewall do computador cliente permite a comunicação TCP de saída na porta TCP 1433. Se as conexões de entrada não forem necessárias para outros aplicativos, bloqueie-as na porta TCP 1433.

#### <a name="authentication"></a>Authentication

A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

-   **Autenticação SQL Server**: Quando é criada uma instância SQL lógica, é criada conta de início de sessão individual, denominada Conta de Subscritor da Base de Dados SQL. Essa conta se conecta usando [SQL Server autenticação](/azure/sql-database/sql-database-security-overview) (nome de usuário e senha). Esta conta é administradora na instância do servidor lógico e em todas as bases de dados de utilizador ligadas a essa instância. As permissões da conta do assinante não podem ser restritas. Só pode existir uma destas contas.
-   **Autenticação Azure Active Directory**: A [autenticação do Azure ad](/azure/sql-database/sql-database-aad-authentication) é um mecanismo de conexão ao banco de dados SQL do azure e SQL data warehouse do Azure usando identidades no Azure AD. Você pode usá-lo para gerenciar de forma centralizada as identidades dos usuários do banco de dados.

![Autenticação do Azure AD com o banco de dados SQL](./media/database-security-overview/azure-database-fig2.png)

 As vantagens da autenticação do Azure AD incluem:
  - Ele fornece uma alternativa para SQL Server autenticação.
  - Ele ajuda a interromper a proliferação de identidades de usuário entre servidores de banco de dados e permite a rotação de senha em um único lugar.
  - Você pode gerenciar permissões de banco de dados usando grupos externos (AD do Azure).
  - Ele pode eliminar o armazenamento de senhas habilitando a autenticação integrada do Windows e outras formas de autenticação às quais o Azure AD dá suporte.

#### <a name="authorization"></a>Autorização

A [autorização](/azure/sql-database/sql-database-manage-logins) refere-se ao que um usuário pode fazer em um banco de dados SQL do Azure. Ele é controlado pelas [associações de função](https://msdn.microsoft.com/library/ms189121) de banco de dados da sua conta de usuário e [permissões em nível de objeto](https://msdn.microsoft.com/library/ms191291.aspx). A autorização é o processo de determinação de quais recursos protegíveis uma entidade de segurança pode acessar e quais operações são permitidas para esses recursos.

### <a name="application-access"></a>Acesso à aplicação

#### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

Um representante de serviço em um Call Center pode identificar os chamadores por vários dígitos de seu número de seguro social ou número de cartão de crédito. Mas esses itens de dados não devem ser totalmente expostos ao representante de serviço.

Você pode definir uma regra de mascaramento que mascara todos os últimos quatro dígitos de um número do seguro social ou número de cartão de crédito no conjunto de resultados de qualquer consulta.

![Mascaramento em um número enviado entre um banco de dados SQL e aplicativos de negócios](./media/database-security-overview/azure-database-fig3.png)

Como outro exemplo, uma máscara de dados apropriada pode ser definida para proteger informações de identificação pessoal. Um desenvolvedor pode então consultar ambientes de produção para fins de solução de problemas sem violar os regulamentos de conformidade.

A [máscara de dados dinâmica da Base de Dados SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. Há suporte para o mascaramento de dados dinâmicos para a versão V12 do Azure SQL Database.

O mascaramento de [dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a revelar com impacto mínimo sobre a camada de aplicativo. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

> [!Note]
> O mascaramento de dados dinâmicos pode ser configurado pelas funções administrador de banco de dados do Azure, administrador do servidor ou responsável pela segurança.

#### <a name="row-level-security"></a>Segurança ao Nível da Linha

Outro requisito de segurança comum para bancos de dados multilocatários é [a segurança em nível de linha](https://msdn.microsoft.com/library/dn765131.aspx). Você pode usar esse recurso para controlar o acesso a linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta. (Exemplos de características são Associação de grupo e contexto de execução.)

![Segurança em nível de linha que permite que um usuário acesse linhas em uma tabela por meio de um aplicativo cliente](./media/database-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizada na camada de banco de dados, em vez de ficar distante da data em outra camada de aplicativo. O sistema de banco de dados aplica as restrições de acesso toda vez que o acesso a dados é tentado em qualquer camada. Isso torna seu sistema de segurança mais confiável e robusto, reduzindo a área de superfície do seu sistema de segurança.

A segurança em nível de linha apresenta o controle de acesso baseado em predicado. Ele apresenta uma avaliação flexível e centralizada que pode levar em consideração metadados ou quaisquer outros critérios que o administrador determinar, conforme apropriado. O predicado é usado como um critério para determinar se o usuário tem o acesso apropriado aos dados com base em atributos de usuário. Você pode implementar o controle de acesso baseado em rótulo usando o controle de acesso baseado em predicado.

## <a name="proactive-monitoring"></a>Monitorização proativa

O banco de dados SQL ajuda a proteger os seus dados fornecendo recursos de *auditoria* e *detecção de ameaças* .

### <a name="auditing"></a>Auditoria

A [auditoria do banco de dados SQL do Azure](/azure/sql-database/sql-database-auditing-get-started) aumenta sua capacidade de obter informações sobre eventos e alterações que ocorrem no banco de dados. Os exemplos são atualizações e consultas nos dados.

A auditoria do banco de dados SQL rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulatória, compreender a atividade do banco de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança. A auditoria permite e facilita a adesão a padrões de conformidade, mas não garante a conformidade.

Você pode usar a auditoria do banco de dados SQL para:

- **Manter** uma trilha de auditoria de eventos selecionados. Você pode definir categorias de ações de banco de dados a serem auditadas.
- **Relatório** de atividade do banco de dados. Você pode usar relatórios pré-configurados e um painel para começar rapidamente com a atividade e o relatório de eventos.
- **Analise** os relatórios. Você pode encontrar eventos suspeitos, atividades incomuns e tendências.

Há dois métodos de auditoria:

-   **Auditoria de blob**: Os registos são escritos no armazenamento de blobs do Azure e Esse é um método de auditoria mais recente. Ele fornece maior desempenho, dá suporte à auditoria de nível de objeto de maior granularidade e é mais econômico.
-   **Auditoria de tabela**: Os logs são gravados no armazenamento de tabelas do Azure.

### <a name="threat-detection"></a>Deteção de ameaças

A [proteção avançada contra ameaças para o banco de dados SQL do Azure](/azure/sql-database/sql-advanced-threat-protection) detecta atividades suspeitas que indicam possíveis ameaças à segurança. Você pode usar a detecção de ameaças para responder a eventos suspeitos no banco de dados, como injeção de SQL, conforme eles ocorrem. Ele fornece alertas e permite o uso da auditoria do banco de dados SQL do Azure para explorar os eventos suspeitos.

![Detecção de ameaças para o banco de dados SQL e um aplicativo Web, com um invasor externo e um insider mal-intencionado](./media/database-security-overview/azure-database-fig5.jpg)

A ATP (proteção avançada contra ameaças) do SQL fornece um conjunto de recursos avançados de segurança do SQL, incluindo a descoberta de dados & classificação, avaliação de vulnerabilidade e detecção de ameaças. 

- [Classificação de & de descoberta de dados](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Avaliação de Vulnerabilidades](/azure/sql-database/sql-vulnerability-assessment)  
- [Detecção de ameaças](/azure/sql-database/sql-database-threat-detection)

O [banco de dados do Azure para PostgreSQL proteção avançada contra ameaças](/azure/postgresql/concepts-data-access-and-security-threat-protection) fornece uma nova camada de segurança, que permite detectar e responder a ameaças potenciais à medida que elas ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários recebem um alerta sobre atividades suspeitas de banco de dados e possíveis vulnerabilidades, bem como padrões de consultas e acessos de banco de dados anormais. A proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL integra alertas com a central de segurança do Azure. Os tipos de alertas incluem:

- Acesso de local incomum
- Acesso de data center incomuns do Azure 
- Acesso de entidade de segurança não familiar 
- Acesso de um aplicativo potencialmente prejudicial 
- Banco de dados do Azure de força bruta para credenciais do PostgreSQL 

[Banco de dados do Azure para MySQL proteção avançada contra ameaças](/azure/mysql/concepts-data-access-and-security-threat-protection) fornece proteção semelhante à proteção avançada do PostgreSQL.  

## <a name="centralized-security-management"></a>Gerenciamento de segurança centralizado

O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Ele ajuda a detectar ameaças que poderiam passar despercebidas e trabalha com um amplo ecossistema de soluções de segurança.

A [central de segurança](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ajuda você a proteger os dados no SQL Database, fornecendo visibilidade sobre a segurança de todos os seus servidores e bancos de dados. Com a central de segurança, você pode:

- Defina políticas para a criptografia e a auditoria do banco de dados SQL.
- Monitore a segurança dos recursos do banco de dados SQL em todas as suas assinaturas.
- Identifique e corrija rapidamente problemas de segurança.
- Integre alertas da [detecção de ameaças do banco de dados SQL do Azure](/azure/sql-database/sql-database-threat-detection).

A central de segurança dá suporte ao acesso baseado em função.

## <a name="sql-information-protection"></a>Proteção das Informações SQL

A [proteção de informações do SQL](/azure/sql-database/sql-database-data-discovery-and-classification) descobre e classifica automaticamente dados potencialmente confidenciais, fornece um mecanismo de rotulagem para marcação persistente de dados confidenciais com atributos de classificação e fornece um painel detalhado que mostra o Estado de classificação do banco de dados.  

Além disso, ele calcula a sensibilidade do conjunto de resultados de consultas SQL, para que as consultas que extraem dados confidenciais possam ser auditadas explicitamente e os dados possam ser protegidos. Para obter mais detalhes sobre a proteção de informações do SQL, confira descoberta e classificação de dados do Azure SQL Database.

Você pode configurar [políticas de proteção de informações do SQL](/azure/security-center/security-center-info-protection-policy) na central de segurança do Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um mercado online de aplicações e serviços que permite às empresas recém-criadas e aos fabricantes independentes de software (ISV) oferecerem as suas soluções aos clientes Azure de todo o mundo.
O Azure Marketplace combina Microsoft Azure ecossistemas de parceiros em uma plataforma unificada para atender melhor aos clientes e parceiros. Você pode [executar uma pesquisa](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para exibir os produtos de segurança de banco de dados disponíveis no Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

- [Proteja seu banco de dados SQL do Azure](/azure/sql-database/sql-database-security-tutorial)
- [Central de segurança do Azure e serviço do banco de dados SQL do Azure](/azure/security-center/security-center-sql-database)
- [Detecção de ameaças do banco de dados SQL](/azure/sql-database/sql-database-threat-detection)
- [Melhorar o desempenho do banco de dados SQL](/azure/sql-database/sql-database-performance-tutorial)
