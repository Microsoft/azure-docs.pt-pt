---
title: Visão geral da segurança da base de dados Azure Microsoft Docs
description: Este artigo fornece uma visão geral das funcionalidades de segurança da base de dados Azure.
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
ms.openlocfilehash: e5ed60ea59dc8cf19b8f9ca7e96777dbc6980171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906048"
---
# <a name="azure-database-security-overview"></a>Visão geral de segurança da base de dados Azure

A segurança é uma das principais preocupações para a gestão de bases de dados, e sempre foi uma prioridade para a Base de Dados Azure SQL. A Base de Dados Azure SQL suporta a segurança da ligação com regras de firewall e encriptação de ligação. Suporta a autenticação com nome de utilizador e senha e autenticação Azure Ative Directory (Azure AD), que utiliza identidades geridas pelo Azure Ative Directory. A autorização utiliza o controlo de acesso baseado em papéis.

A Base de Dados Azure SQL suporta a encriptação através da encriptação em tempo real e da desencriptação de bases de dados, cópias de segurança associadas e ficheiros de registo de transações em repouso sem exigir alterações na aplicação.

A Microsoft fornece formas adicionais de encriptar dados da empresa:

-   A encriptação a nível celular está disponível para encriptar colunas específicas ou mesmo células de dados com diferentes chaves de encriptação.
-   Se precisar de um módulo de segurança de hardware ou de uma gestão central da sua hierarquia de chaves de encriptação, considere utilizar o Cofre chave Azure com o SQL Server numa máquina virtual Azure (VM).
-   Sempre Encriptado (atualmente em pré-visualização) torna a encriptação transparente para as aplicações. Também permite que os clientes criptografem dados confidenciais dentro das aplicações dos clientes sem partilhar as chaves de encriptação com a Base de Dados SQL.

A Auditoria de Base de Dados Azure SQL permite às empresas registar eventos para um registo de auditoria no Armazenamento Azure. A Auditoria da Base de Dados SQL também pode ser integrada no Microsoft Power BI para facilitar a leitura detalhada de relatórios e análises.

As bases de dados Azure SQL podem ser firmemente protegidas para satisfazer a maioria dos requisitos regulamentares ou de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS Level 1. Uma lista atual de certificações de conformidade de segurança está disponível no site do [Microsoft Azure Trust Center.](https://azure.microsoft.com/support/trust-center/services/)

Este artigo percorre os fundamentos de assegurar bases de dados SQL do Microsoft Azure para dados estruturados, tabulares e relacionais. Em particular, este artigo irá ajudá-lo a começar a utilizar recursos para proteger dados, controlar o acesso e a monitorização proativa.

## <a name="protection-of-data"></a>Proteção de dados

A Base de Dados SQL ajuda a proteger os seus dados fornecendo encriptação:

- Para dados em movimento através da Segurança da Camada de [Transporte (TLS)](https://support.microsoft.com/kb/3135244).
- Para dados em repouso através de [encriptação de dados transparentes](https://go.microsoft.com/fwlink/?LinkId=526242).
- Para dados utilizados através de [Sempre Encriptado](https://msdn.microsoft.com/library/mt163865.aspx).

Para outras formas de encriptar os seus dados, considere:

-   A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
-   [Cofre chave Azure com SQL Server num VM Azure,](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx)se precisar de um módulo de segurança de hardware ou gestão central da sua hierarquia de chaves de encriptação.

### <a name="encryption-in-motion"></a>Encriptação em movimento

Um problema comum para todas as aplicações de cliente/servidor é a necessidade de privacidade à medida que os dados se movem sobre redes públicas e privadas. Se os dados que se movem sobre uma rede não forem encriptados, há a possibilidade de que possam ser capturados e roubados por utilizadores não autorizados. Quando estiver a lidar com serviços de base de dados, certifique-se de que os dados estão encriptados entre o cliente da base de dados e o servidor. Certifique-se também de que os dados são encriptados entre servidores de base de dados que comunicam entre si e com aplicações de nível médio.

Um problema quando administra uma rede é garantir dados que estão a ser enviados entre aplicações através de uma rede não confiável. Pode utilizar [o TLS/SSL](/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, usá-lo para encriptar mensagens entre as partes autenticadas.

No processo de autenticação, um cliente TLS/SSL envia uma mensagem a um servidor TLS/SSL. O servidor responde com a informação de que o servidor precisa para se autenticar. O cliente e o servidor efetuam uma troca adicional de chaves de sessão e a caixa de diálogo de autenticação termina. Quando a autenticação estiver concluída, a comunicação segura por SSL pode começar entre o servidor e o cliente através das chaves de encriptação simétricas estabelecidas durante o processo de autenticação.

Todas as ligações à Base de Dados Azure SQL requerem encriptação (TLS/SSL) em todos os momentos, enquanto os dados estão "em trânsito" de e para a base de dados. A Base de Dados SQL utiliza tLS/SSL para autenticar servidores e clientes e depois usá-la para encriptar mensagens entre as partes autenticadas. 

Na cadeia de ligação da sua aplicação, deve especificar parâmetros para encriptar a ligação e não confiar no certificado de servidor. (Isto é feito para si se copiar a sua cadeia de ligação para fora do portal Azure.) Caso contrário, a ligação não verificará a identidade do servidor e será suscetível a ataques "man-in-the-middle". Para o ADO.NET condutor, por exemplo, `Encrypt=True` estes `TrustServerCertificate=False`parâmetros de corda de ligação são e .

### <a name="encryption-at-rest"></a>Encriptação inativa

Pode tomar várias precauções para ajudar a proteger a base de dados. Por exemplo, desenhe um sistema seguro, criptografe ativos confidenciais e construa uma firewall em torno dos servidores de base de dados. Mas num cenário em que os meios físicos (como unidades ou fitas de backup) são roubados, uma parte maliciosa pode simplesmente restaurar ou anexar a base de dados e navegar nos dados.

Uma solução é encriptar os dados sensíveis na base de dados e proteger as chaves que são usadas para encriptar os dados com um certificado. Esta solução impede que qualquer pessoa sem as chaves utilize os dados, mas este tipo de proteção deve ser planeada.

Para resolver este problema, o SQL Server e a SQL Database suportam [encriptação transparente de dados](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). A encriptação transparente de dados encripta ficheiros de dados do SQL Server e da SQL Database, conhecidos como dados de encriptação em repouso.

A encriptação transparente de dados ajuda a proteger contra a ameaça de atividades maliciosas. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação.  

A encriptação transparente de dados encripta o armazenamento de uma base de dados inteira utilizando uma chave simétrica chamada chave de encriptação da base de dados. Na Base de Dados SQL, a chave de encriptação da base de dados está protegida por um certificado de servidor incorporado. O certificado de servidor incorporado é único para cada servidor de base de dados SQL.

Se uma base de dados estiver numa relação Geo-DR, está protegida por uma chave diferente em cada servidor. Se duas bases de dados estiverem ligadas ao mesmo servidor, partilham o mesmo certificado incorporado. A Microsoft roda automaticamente estes certificados pelo menos a cada 90 dias. 

Para mais informações, consulte [encriptação](/sql/relational-databases/security/encryption/transparent-data-encryption-tde)de dados transparentes.

### <a name="encryption-in-use-client"></a>Encriptação em uso (cliente)

A maioria das violações de dados envolve o roubo de dados críticos, tais como números de cartões de crédito ou informações pessoalmente identificáveis. As bases de dados podem ser tesouros de informação sensível. Podem conter dados pessoais dos clientes (como números de identificação nacionais), informações confidenciais competitivas e propriedade intelectual. Dados perdidos ou roubados, especialmente dados dos clientes, podem resultar em danos na marca, desvantagem competitiva e multas graves — mesmo processos judiciais.

![A funcionalidade Sempre Encriptada ilustrada como fechadura e chave](./media/database-security-overview/azure-database-fig1.png)

[Sempre Encriptado](https://msdn.microsoft.com/library/mt163865.aspx) é uma funcionalidade concebida para proteger dados sensíveis armazenados nas bases de dados Azure SQL ou SQL Server. Sempre encriptado permite que os clientes criptografem dados confidenciais dentro das aplicações do cliente e nunca revelem as chaves de encriptação do motor de base de dados (Base de Dados SQL ou Servidor SQL).

Sempre Encriptado fornece uma separação entre pessoas que possuem os dados (e podem vê-lo) e pessoas que gerem os dados (mas não devem ter acesso). Ajuda a garantir que administradores de bases de dados no local, operadores de bases de dados na nuvem ou outros utilizadores de alto privilégio, mas não autorizados, não possam aceder aos dados encriptados.

Além disso, a Always Encrypted torna a encriptação transparente para as aplicações. Um controlador sempre encriptado é instalado no computador do cliente para que possa encriptar e desencriptar automaticamente dados sensíveis na aplicação do cliente. O condutor encripta os dados em colunas sensíveis antes de passar os dados para o motor da base de dados. O condutor reescreve automaticamente as consultas para que a semântica da aplicação seja preservada. Da mesma forma, o condutor desencripta de forma transparente os dados, armazenados em colunas de base de dados encriptadas, contidos em resultados de consulta.

## <a name="access-control"></a>Controlo de acesso

Para garantir a segurança, a Base de Dados SQL controla o acesso utilizando:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os utilizadores provem a sua identidade.
- Mecanismos de autorização que limitam os utilizadores a ações e dados específicos.

### <a name="database-access"></a>Acesso à base de dados

A proteção de dados começa com o controlo do acesso aos seus dados. O datacenter que acolhe os seus dados gere o acesso físico. Pode configurar uma firewall para gerir a segurança na camada de rede. Também controla o acesso configurando logins para autenticação e definindo permissões para funções de servidor e base de dados.

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

[A Azure SQL Database](https://azure.microsoft.com/services/sql-database/) fornece um serviço de base de dados relacional para o Azure e outras aplicações baseadas na Internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para mais informações, consulte a visão geral das regras de firewall da [Base de Dados Azure SQL](/azure/sql-database/sql-database-firewall-configure).

O serviço de base de dados Azure SQL está disponível apenas através da porta TCP 1433. Para aceder a uma base de dados SQL a partir do seu computador, certifique-se de que a firewall do computador do seu cliente permite a comunicação tCP de saída na porta TCP 1433. Se não forem necessárias ligações de entrada para outras aplicações, bloqueie-as na porta TCP 1433.

#### <a name="authentication"></a>Autenticação

A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

-   **Autenticação do Servidor SQL**: Uma única conta de login é criada quando uma instância lógica SQL é criada, chamada Conta de Assinante de Base de Dados SQL. Esta conta liga-se utilizando [a autenticação do Servidor SQL](/azure/sql-database/sql-database-security-overview) (nome de utilizador e palavra-passe). Esta conta é administradora na instância do servidor lógico e em todas as bases de dados de utilizador ligadas a essa instância. As permissões da conta de assinante não podem ser restringidas. Só pode existir uma destas contas.
-   **Autenticação do Diretório Ativo Azure**: A [autenticação Azure AD](/azure/sql-database/sql-database-aad-authentication) é um mecanismo de ligação à Base de Dados Azure SQL e ao Azure SQL Data Warehouse utilizando identidades em Azure AD. Pode utilizá-lo para gerir centralmente as identidades dos utilizadores de bases de dados.

![Autenticação Azure AD com Base de Dados SQL](./media/database-security-overview/azure-database-fig2.png)

 As vantagens da autenticação da AD Azure incluem:
  - Fornece uma alternativa à autenticação do Servidor SQL.
  - Ajuda a parar a proliferação de identidades de utilizadores através de servidores de base de dados e permite a rotação de palavras-passe num único local.
  - Pode gerir permissões de base de dados utilizando grupos externos (Azure AD).
  - Pode eliminar o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e outras formas de autenticação que a AD Azure suporta.

#### <a name="authorization"></a>Autorização

[A autorização](/azure/sql-database/sql-database-manage-logins) refere-se ao que um utilizador pode fazer dentro de uma base de dados Azure SQL. É controlado pelas subscrições de [papéis](https://msdn.microsoft.com/library/ms189121) da sua conta de utilizador e [permissões ao nível do objeto](https://msdn.microsoft.com/library/ms191291.aspx). A autorização é o processo de determinação dos recursos asseguraráveis a que um principal pode aceder e quais as operações que são permitidas para esses recursos.

### <a name="application-access"></a>Acesso à aplicação

#### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

Um representante de serviço num call center pode identificar os chamadores por vários dígitos do seu número de segurança social ou número de cartão de crédito. Mas esses dados não devem estar totalmente expostos ao representante do serviço.

Pode definir uma regra de mascaramento que mascara todos, menos os últimos quatro dígitos de um número de segurança social ou número de cartão de crédito no conjunto de resultados de qualquer consulta.

![Mascarar um número enviado entre uma base de dados SQL e aplicações empresariais](./media/database-security-overview/azure-database-fig3.png)

Como outro exemplo, uma máscara de dados adequada pode ser definida para proteger informações pessoalmente identificáveis. Um desenvolvedor pode então consultar ambientes de produção para fins de resolução de problemas sem violar os regulamentos de conformidade.

A base de [dados dinâmica SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados. A máscara de dados dinâmicos é suportada para a versão V12 da Base de Dados Azure SQL.

[A máscara de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado a dados sensíveis, permitindo-lhe designar quanto dos dados sensíveis revelar com o mínimo impacto na camada de aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

> [!Note]
> A máscara de dados dinâmicos pode ser configurada pelas funções de administrador a base de dados Azure, administração de servidores ou agentes de segurança.

#### <a name="row-level-security"></a>Segurança ao Nível da Linha

Outro requisito comum de segurança para bases de dados multiarrendatárias é a [Segurança de Nível Row](https://msdn.microsoft.com/library/dn765131.aspx). Pode utilizar esta funcionalidade para controlar o acesso a linhas numa tabela de bases de dados com base nas características do utilizador que está a executar uma consulta. (Exemplo de características são a adesão ao grupo e o contexto de execução.)

![Segurança de nível de linha permitindo que um utilizador aceda a linhas numa tabela através de uma aplicação de cliente](./media/database-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizada no nível de base de dados e não longe dos dados de outro nível de aplicação. O sistema de base de dados aplica as restrições de acesso sempre que o acesso aos dados é tentado a partir de qualquer nível. Isto torna o seu sistema de segurança mais fiável e robusto reduzindo a área de superfície do seu sistema de segurança.

A Segurança de Nível de Linha introduz o controlo de acesso baseado em predicados. Possui uma avaliação flexível e centralizada que pode ter em consideração metadados ou quaisquer outros critérios que o administrador determine como apropriado. O predicado é utilizado como critério para determinar se o utilizador tem ou não o acesso adequado aos dados com base nos atributos do utilizador. Pode implementar o controlo de acesso baseado na etiqueta utilizando um controlo de acesso baseado em predicados.

## <a name="proactive-monitoring"></a>Monitorização proativa

A Base de Dados SQL ajuda a proteger os seus dados fornecendo capacidades de *auditoria* e deteção de *ameaças.*

### <a name="auditing"></a>Auditoria

A auditoria da Base de [Dados Azure SQL](/azure/sql-database/sql-database-auditing-get-started) aumenta a sua capacidade de obter informações sobre eventos e alterações que ocorrem dentro da base de dados. Exemplos são atualizações e consultas contra os dados.

A auditoria da Base de Dados SQL rastreia os eventos da base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure. A auditoria pode ajudá-lo a manter o cumprimento regulamentar, compreender a atividade da base de dados e obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança. A auditoria permite e facilita a adesão às normas de conformidade, mas não garante o cumprimento.

Pode utilizar a auditoria da Base de Dados SQL para:

- **Mantenha** um rasto de auditoria de eventos selecionados. Pode definir categorias de ações de bases de dados para serem auditadas.
- **Relatório** sobre a atividade da base de dados. Pode utilizar relatórios pré-configurados e um dashboard para começar rapidamente a utilizar relatórios de eventos e de atividade.
- **Analise** relatórios. Pode encontrar eventos suspeitos, atividades invulgares e tendências.

Existem dois métodos de auditoria:

-   **Auditoria blob**: Os registos são escritos para armazenamento Azure Blob. Este é um novo método de auditoria. Proporciona um desempenho mais elevado, suporta uma auditoria ao nível de objetos de granularidade mais elevada e é mais rentável.
-   **Auditoria de tabelas**: Os registos são escritos para armazenamento de mesa azure.

### <a name="threat-detection"></a>Deteção de ameaças

[Advanced Threat Protection for Azure SQL Database](/azure/sql-database/sql-advanced-threat-protection) deteta atividades suspeitas que indicam potenciais ameaças à segurança. Pode utilizar a deteção de ameaças para responder a eventos suspeitos na base de dados, tais como injeções De SQL, à medida que ocorrem. Fornece alertas e permite a utilização de auditorias da Base de Dados Azure SQL para explorar os eventos suspeitos.

![Deteção de ameaças para base de dados SQL e uma aplicação web, com um intruso externo e um insider malicioso](./media/database-security-overview/azure-database-fig5.jpg)

A SQL Advanced Threat Protection (ATP) fornece um conjunto de capacidades avançadas de segurança SQL, incluindo a Deteção de & de Dados, Avaliação de Vulnerabilidades e Deteção de Ameaças. 

- [Classificação & de Descoberta de Dados](/azure/sql-database/sql-database-data-discovery-and-classification)
- [Avaliação de Vulnerabilidade](/azure/sql-database/sql-vulnerability-assessment)  
- [Deteção de Ameaças](/azure/sql-database/sql-database-threat-detection)

A Base de [Dados Azure para Proteção avançada de Ameaças PostgreSQL](/azure/postgresql/concepts-data-access-and-security-threat-protection) fornece uma nova camada de segurança, que lhe permite detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados e potenciais vulnerabilidades, bem como padrões anómalos de acesso à base de dados e consultas. Advanced Threat Protection for Azure Database for PostgreSQL integra alertas com o Azure Security Center. O tipo de alertas inclui:

- Acesso a partir de uma localização incomum
- Acesso a partir de centro de dados azure incomum 
- Acesso de um diretor desconhecido 
- Acesso a partir de uma aplicação potencialmente prejudicial 
- Brute força base de dados Azure para credenciais PostgreSQL 

[A Base de Dados Azure para a Proteção avançada de Ameaças MySQL](/azure/mysql/concepts-data-access-and-security-threat-protection) fornece proteção semelhante à Proteção Avançada PostgreSQL.  

## <a name="centralized-security-management"></a>Gestão centralizada de segurança

O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure. Ajuda a detetar ameaças que de outra forma podem passar despercebidas, e funciona com um vasto ecossistema de soluções de segurança.

[O Security Center](../../security-center/security-center-alerts-data-services.md) ajuda-o a salvaguardar dados na Base de Dados SQL, proporcionando visibilidade à segurança de todos os seus servidores e bases de dados. Com o Centro de Segurança, pode:

- Defina as políticas para encriptação e auditoria da Base de Dados SQL.
- Monitorize a segurança dos recursos da Base de Dados SQL em todas as suas subscrições.
- Identifique e remediarapidamente questões de segurança.
- Integre alertas da deteção de ameaças da Base de [Dados Azure SQL](/azure/sql-database/sql-database-threat-detection).

O Centro de Segurança suporta o acesso baseado em papéis.

## <a name="sql-information-protection"></a>Proteção de Informação SQL

[A SQL Information Protection](/azure/sql-database/sql-database-data-discovery-and-classification) descobre e classifica automaticamente dados potencialmente sensíveis, fornece um mecanismo de rotulagem para marcar dados sensíveis persistentemente com atributos de classificação, e fornece um dashboard detalhado que mostra o estado de classificação da base de dados.  

Além disso, calcula a sensibilidade do conjunto de resultados das consultas SQL, de modo a que as consultas que extraem dados sensíveis possam ser explicitamente auditadas, e os dados possam ser protegidos. Para mais detalhes sobre a Proteção de Informação SQL, consulte a Descoberta e Classificação de Dados da Base de Dados Azure SQL.

Pode configurar as políticas de Proteção de [Informação SQL](/azure/security-center/security-center-info-protection-policy) no Centro de Segurança Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um mercado de aplicações e serviços online que permite a start-ups e fornecedores de software independentes (ISVs) ofereceras as suas soluções aos clientes Azure em todo o mundo.
O Azure Marketplace combina os ecossistemas parceiros do Microsoft Azure numa plataforma unificada para melhor servir clientes e parceiros. Pode [pesquisar para](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) ver produtos de segurança de base de dados disponíveis no Mercado Azure.

## <a name="next-steps"></a>Passos seguintes

- [Proteja a sua base de dados Azure SQL](/azure/sql-database/sql-database-security-tutorial)
- [Azure Security Center e serviço de base de dados Azure SQL](/azure/security-center/security-center-sql-database)
- [Deteção de ameaças de base de dados SQL](/azure/sql-database/sql-database-threat-detection)
- [Melhorar o desempenho da base de dados SQL](/azure/sql-database/sql-database-performance-tutorial)
