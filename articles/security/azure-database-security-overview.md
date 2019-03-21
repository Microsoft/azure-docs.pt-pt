---
title: Descrição geral da segurança da base de dados do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da base de dados do Azure, funcionalidades de segurança.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: TomSh
ms.openlocfilehash: 7e0e93c82279ec1a4fbecbbf27c7a1866286b2f8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860521"
---
# <a name="azure-database-security-overview"></a>Descrição geral da segurança da base de dados do Azure

A segurança é uma grande preocupação para a gestão de bases de dados e, sempre foi uma prioridade para a base de dados do Azure SQL. Base de dados SQL do Azure suporta segurança de ligação com as regras de firewall e de encriptação da ligação. Suporta a autenticação com o nome de utilizador e palavra-passe e autenticação do Azure Active Directory (Azure AD), que utiliza identidades geridas pelo Azure Active Directory. Autorização utiliza o controlo de acesso baseado em funções.

Base de dados SQL do Azure suporta a encriptação através de encriptação em tempo real e desencriptação de bases de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação.

A Microsoft fornece outras formas de encriptar dados da empresa:

-   Encriptação ao nível da célula está disponível para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferente.
-   Se precisar de um módulo de hardware de segurança ou gerenciamento central da sua hierarquia de chaves de encriptação, considere a utilização do Azure Key Vault com o SQL Server numa máquina virtual do Azure (VM).
-   Sempre encriptado (atualmente em pré-visualização) facilita a encriptação transparente aos aplicativos. Também permite que os clientes encriptem dados confidenciais dentro de aplicativos de cliente sem partilhar as chaves de encriptação com a base de dados SQL.

Auditoria de base de dados SQL do Azure permite que as empresas a registo de eventos para uma auditoria registo no armazenamento do Azure. A Auditoria da Base de Dados SQL também pode ser integrada no Microsoft Power BI para facilitar a leitura detalhada de relatórios e análises.

Bases de dados SQL do Azure podem ser totalmente protegidos para satisfazer mais normas ou requisitos de segurança, incluindo a HIPAA, ISO 27001/27002 e PCI DSS nível 1. A lista atual de certificações de conformidade de segurança está disponível na [site do Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/services/).

Este artigo explica as noções básicas de proteger bases de dados SQL do Microsoft Azure para dados relacionais, estruturados e tabulares. Em particular, este artigo irá ajudá-lo a começar a utilizar recursos para proteger dados, controlar o acesso e a monitorização proativa.

## <a name="protection-of-data"></a>Proteção de dados

Base de dados SQL ajuda a proteger os seus dados ao fornecer encriptação:

- Para dados em movimento através de [Transport Layer Security (TLS)](https://support.microsoft.com/kb/3135244).
- Para dados em descanso através da [encriptação de dados transparente](https://go.microsoft.com/fwlink/?LinkId=526242).
- Para os dados em utilização por meio [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

Para outras formas de encriptar os seus dados, considere:

-   A [encriptação ao nível da célula](https://msdn.microsoft.com/library/ms179331.aspx), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
-   [O Azure Key Vault com o SQL Server numa VM do Azure](https://blogs.technet.com/b/kv/archive/2015/01/12/using-the-key-vault-for-sql-server-encryption.aspx), se precisar de um módulo de hardware de segurança ou gerenciamento central da sua hierarquia de chaves de encriptação.

### <a name="encryption-in-motion"></a>Encriptação em movimento

Um problema comum de todos os aplicativos cliente/servidor é a necessidade de privacidade, como os dados são movidos através de redes públicas e privadas. Se mover através de uma rede de dados não são encriptados, é provável que pode ser capturado e roubo por utilizadores não autorizados. Quando está lidando com os serviços de base de dados, certifique-se de que os dados são encriptados entre o cliente de base de dados e o servidor. Além disso, certifique-se de que os dados são encriptados entre servidores de base de dados que se comunicam entre si e com aplicações de camada intermediária.

Um problema ao administrar uma rede é proteger os dados que estão a ser enviados entre as aplicações numa rede não fidedigna. Pode usar [TLS/SSL](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) para autenticar servidores e clientes e, em seguida, utilizá-la para encriptar mensagens entre as partes autenticadas.

No processo de autenticação, um cliente TLS/SSL envia uma mensagem para um servidor TLS/SSL. O servidor responde com as informações que o servidor precisa para se autenticar. O cliente e o servidor efetuam uma troca adicional de chaves de sessão e a caixa de diálogo de autenticação termina. Quando autenticação for concluída, a comunicação protegida por SSL pode começar entre o servidor e o cliente através de chaves de encriptação simétrica estabelecidas durante o processo de autenticação.

Todas as ligações à base de dados do Azure SQL exigem a encriptação (TLS/SSL) em todos os momentos enquanto os dados se "em trânsito" para e da base de dados. Base de dados SQL utiliza o TLS/SSL para autenticar servidores e clientes e, em seguida, utilizá-la para encriptar mensagens entre as partes autenticadas. 

Na cadeia de ligação da sua aplicação, tem de especificar parâmetros para encriptar a ligação e não para confiar no certificado de servidor. (Isto é feito por si se copiar a cadeia de ligação fora do portal do Azure.) Caso contrário, a ligação não irá verificar a identidade do servidor e estará suscetível a ataques "man-in-the-middle". Para o controlador ADO.NET, por exemplo, esses parâmetros de cadeia de ligação são `Encrypt=True` e `TrustServerCertificate=False`.

### <a name="encryption-at-rest"></a>Encriptação inativa

Pode tomar várias precauções para ajudar a proteger a base de dados. Por exemplo, um sistema seguro de design, encriptar ativos confidenciais e criar uma firewall ao redor dos servidores da base de dados. Mas, num cenário em que o suporte de dados físico (por exemplo, discos ou bandas de cópia de segurança) é roubados, mal-intencionados podem simplesmente restaurar ou expor a base de dados e procurar os dados.

Uma solução é encriptar os dados confidenciais na base de dados e Proteja as chaves que são utilizadas para encriptar os dados com um certificado. Esta solução impede que qualquer pessoa sem as chaves a utilizar os dados, mas esse tipo de proteção têm de ser previsto.

Para resolver este problema, o SQL Server e o suporte de base de dados SQL [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current&viewFallbackFrom=sql-server-2017). Encriptação de dados transparente criptografa arquivos de dados do SQL Server e base de dados SQL, conhecidos como dados de encriptação em repouso.

Encriptação de dados transparente ajuda a proteger contra ameaças de atividades maliciosas. Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação.  

Encriptação de dados transparente criptografa o armazenamento de uma base de dados completa com uma chave simétrica denominada a chave de encriptação da base de dados. Na base de dados SQL, a chave de encriptação da base de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de base de dados SQL.

Se uma base de dados está numa relação Geo-DR, ela é protegida por uma chave diferente em cada servidor. Se duas bases de dados estiverem ligados ao mesmo servidor, eles compartilham o mesmo certificado incorporado. Microsoft gira automaticamente estes certificados, pelo menos a cada 90 dias. 

Para obter mais informações, consulte [encriptação de dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde).

### <a name="encryption-in-use-client"></a>Encriptação em uso (cliente)

A maioria das violações de dados envolvem o roubo de dados críticos, como números de cartão de crédito ou informações de identificação pessoal. Bases de dados podem ser troves Tesouro de informações confidenciais. Eles podem conter dados pessoais (como números de identificação nacional) dos clientes, informações relacionadas à concorrência confidenciais e propriedade intelectual. Dados perdidos ou roubados, especialmente dados do cliente, podem resultar em danos de marca, desvantagem competitiva e multas sérias – até mesmo processos judiciais.

![A funcionalidade Always Encrypted ilustrada como um bloqueio e uma chave](./media/azure-databse-security-overview/azure-database-fig1.png)

[Sempre encriptado](https://msdn.microsoft.com/library/mt163865.aspx) é um recurso criado para proteger dados confidenciais armazenados em bases de dados de base de dados do Azure SQL ou SQL Server. Always Encrypted permite aos clientes encriptar dados confidenciais dentro de aplicativos de cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server).

Sempre encriptado fornece uma separação entre as pessoas que proprietários dos dados (e os podem ver) e as pessoas que gerir os dados (mas não devem ter acesso). Ele ajuda a garantir que no local da base de dados por administradores, operadores de base de dados na cloud ou outros privilégios elevados, mas os utilizadores não autorizados não é possível acessar os dados criptografados.

Além disso, Always Encrypted facilita a encriptação transparente aos aplicativos. Um driver habilitados para sempre encriptados está instalado no computador cliente para que possa automaticamente criptografar e descriptografar dados confidenciais no aplicativo cliente. O driver criptografa os dados nas colunas confidenciais antes de passar os dados para o motor de base de dados. O driver reescreve automaticamente consultas para que a semântica para a aplicação é preservada. Da mesma forma, o driver de forma transparente desencripta-os, armazenados em colunas de banco de dados criptografado, contidas nos resultados da consulta.

## <a name="access-control"></a>Controlo de acesso

Para fornecer segurança, base de dados SQL controla o acesso ao utilizar:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os utilizadores a ações específicas e dados.

### <a name="database-access"></a>Acesso a bases de dados

Proteção de dados começa com a controlar o acesso aos seus dados. O datacenter que aloja os seus dados gere o acesso físico. Pode configurar uma firewall para gerir a segurança na camada da rede. Também controlar o acesso ao configurar os inícios de sessão para autenticação e definir permissões para funções de servidor e base de dados.

#### <a name="firewall-and-firewall-rules"></a>Firewall e regras de firewall

[Base de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) fornece um serviço de base de dados relacional para o Azure e outros aplicativos baseados na internet. Para ajudar a proteger os seus dados, as firewalls impedem todos os acessos ao seu servidor de base de dados enquanto não especificar que computadores têm acesso. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido. Para obter mais informações, veja [Descrição geral das regras de firewall da Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

O serviço de base de dados do Azure SQL está disponível apenas através da porta TCP 1433. Para acessar um banco de dados SQL no seu computador, certifique-se de que a firewall de computador do cliente permite comunicação TCP de saída na porta TCP 1433. Se as conexões de entrada não são necessários para outras aplicações, bloqueá-las na porta TCP 1433.

#### <a name="authentication"></a>Authentication

A autenticação diz respeito à forma como prova a sua identidade quando se liga à base de dados. A Base de Dados SQL suporta dois tipos de autenticação:

-   **Autenticação do SQL Server**: Quando é criada uma instância SQL lógica, é criada conta de início de sessão individual, denominada Conta de Subscritor da Base de Dados SQL. Esta conta liga-se utilizando [autenticação do SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview) (nome de utilizador e palavra-passe). Esta conta é administradora na instância do servidor lógico e em todas as bases de dados de utilizador ligadas a essa instância. As permissões da conta de subscritor não podem ser limitadas. Só pode existir uma destas contas.
-   **Autenticação do Azure Active Directory**: [Autenticação do Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) é um mecanismo de ligação a base de dados do Azure SQL e o Azure SQL Data Warehouse, utilizando as identidades no Azure AD. Pode usá-lo para gerir centralmente identidades de utilizadores de base de dados.

![Autenticação do Azure AD com a base de dados SQL](./media/azure-databse-security-overview/azure-database-fig2.png)

 As vantagens da autenticação do Azure AD incluem:
  - Ele fornece uma alternativa à autenticação do SQL Server.
  - Ele ajuda a impedir a proliferação de identidades de utilizador em servidores de base de dados e permite a rotação da palavra-passe num único local.
  - Pode gerir permissões de base de dados utilizando grupos externos (Azure AD).
  - Isso pode eliminar armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportadas pelo Azure AD.

#### <a name="authorization"></a>Autorização

[Autorização](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins) refere-se ao que um utilizador pode fazer dentro de uma base de dados SQL do Azure. Ele é controlado pela base de dados da sua conta de utilizador [associações das funções](https://msdn.microsoft.com/library/ms189121) e [ao nível do objeto permissões](https://msdn.microsoft.com/library/ms191291.aspx). Autorização é o processo de determinar quais os recursos com capacidade de segurança pode aceder a uma entidade de segurança e as operações que têm permissão para esses recursos.

### <a name="application-access"></a>Acesso à aplicação

#### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

Um representante do serviço num call center pode identificar os autores de chamadas por vários dígitos do seu número da Previdência ou o número de cartão de crédito. Mas esses itens de dados não devem ser totalmente expostos para o representante do serviço.

Pode definir uma regra de máscara que dissimula todas, exceto os últimos quatro dígitos de um número da Previdência social ou o número de cartão de crédito no conjunto de resultados de qualquer consulta.

![A máscara de um número enviados entre aplicações de base de dados e de negócios de SQL](./media/azure-databse-security-overview/azure-database-fig3.png)

Como outro exemplo, pode definir uma máscara de dados apropriados para proteger as informações de identificação pessoal. Um desenvolvedor, em seguida, pode consultar os ambientes de produção para fins de resolução de problemas sem violar os regulamentos de conformidade.

A [máscara de dados dinâmica da Base de Dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita a exposição de dados confidenciais ao mascará-los para utilizadores sem privilégios. Máscara de dados dinâmica é suportada para a versão V12 da base de dados do Azure SQL.

[Máscara de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ajuda a impedir o acesso não autorizado a dados confidenciais ao permitir-lhe designar a quantidade de dados confidenciais a revelar com um impacto mínimo na camada da aplicação. É uma funcionalidade de segurança baseada em políticas que omite os dados confidenciais no conjunto de resultados de uma consulta em campos da base de dados designados, sendo que os dados na base de dados não são alterados.

> [!Note]
> Máscara de dados dinâmica pode ser configurada por funções de diretor de segurança, administrador de servidor ou o administrador de banco de dados do Azure.

#### <a name="row-level-security"></a>Segurança ao Nível da Linha

É outro requisito comum de segurança para bases de dados multi-inquilino [segurança ao nível da linha](https://msdn.microsoft.com/library/dn765131.aspx). Pode utilizar esta funcionalidade para controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que está a executar uma consulta. (As características de exemplo são o contexto de execução e associação de grupo.)

![Segurança ao nível da linha, permitindo ao usuário acesso linhas numa tabela através de uma aplicação de cliente](./media/azure-databse-security-overview/azure-database-fig4.png)

A lógica de restrição de acesso está localizado na camada de base de dados em vez de ausente dos dados na outra camada de aplicativos. O sistema de base de dados aplica as restrições de acesso sempre que esse acesso a dados é tentado a partir de qualquer camada. Isso faz com que seu sistema de segurança mais robusta e confiável, reduzindo a área de superfície do seu sistema de segurança.

Segurança ao nível da linha apresenta o controle de acesso baseado no predicado. Ele apresenta uma avaliação centralizada e flexível que pode levar em consideração metadados ou qualquer outro critério, que o administrador determina conforme adequada. O predicado é utilizado como um critério para determinar se o usuário possui ou não o acesso apropriado aos dados com base em atributos de utilizador. Pode implementar o controlo de acesso com base na etiqueta utilizando o controlo de acesso baseado no predicado.

## <a name="proactive-monitoring"></a>Monitorização proativa

Base de dados SQL ajuda a proteger os seus dados, fornecendo *auditoria* e *deteção de ameaças* capacidades.

### <a name="auditing"></a>Auditoria

[A auditoria de base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) aumenta sua capacidade de obter informações sobre eventos e as alterações que ocorrem na base de dados. Os exemplos são atualizações e consultas aos dados.

A auditoria de base de dados SQL controla os eventos de base de dados e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure. A auditoria pode ajudá-lo a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança. A auditoria permite e facilita o cumprimento das normas de conformidade, mas não garante a conformidade.

Pode utilizar a base de dados SQL a auditoria para:

- **Reter** uma trilha de auditoria de eventos selecionados. Pode definir categorias de ações de base de dados para serem auditados.
- **Relatório** na atividade de base de dados. Pode utilizar um dashboard e relatórios pré-configurados para começar rapidamente com a atividade e relatórios de eventos.
- **Analisar** relatórios. Pode encontrar eventos suspeitos, atividade invulgar e tendências.

Existem dois métodos de auditorias:

-   **Auditoria de BLOBs**: Os registos são escritos no armazenamento de blobs do Azure e Esse é um método de auditoria mais recente. Fornece um desempenho mais elevado, suporta a auditoria e ao nível do objeto do maior granularidade e é mais económico.
-   **Auditoria de tabelas**: Os registos são escritos para o armazenamento de tabelas do Azure.

### <a name="threat-detection"></a>Deteção de ameaças

[Proteção avançada contra ameaças para a base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-advanced-threat-protection) Deteta atividades suspeitas que indicam possíveis ameaças de segurança. Pode utilizar a deteção de ameaças para responder a eventos suspeitos na base de dados, como injeções de SQL, à medida que ocorrem. Ele fornece alertas e permite a utilização de auditoria de SQL Database do Azure para explorar os eventos suspeitos.

![Deteção de ameaças para a base de dados SQL e uma aplicação web, com um atacante externo e um empregado malicioso](./media/azure-databse-security-overview/azure-database-fig5.jpg)

Da proteção contra ameaças avançadas SQL (ATP) fornece um conjunto de funções de segurança SQL avançadas, incluindo dados de deteção e classificação, avaliação de vulnerabilidade e a deteção de ameaças. 

- [Dados de deteção e classificação](../sql-database/sql-database-data-discovery-and-classification.md)
- [Avaliação de vulnerabilidade](../sql-database/sql-vulnerability-assessment.md)  
- [Deteção de ameaças](../sql-database/sql-database-threat-detection.md)

[Base de dados do Azure para PostgreSQL Advanced Threat Protection](../postgresql/concepts-data-access-and-security-threat-protection.md) fornece uma nova camada de segurança, o que permite-lhe detetar e responder a potenciais ameaças à medida que ocorrem ao fornecer alertas de segurança relativamente a atividades anómalas. Os utilizadores recebem um alerta após a atividades suspeitas da base de dados e potenciais vulnerabilidades, bem como os padrões de acesso e consultas de base de dados anómalas. Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL integra alertas no Centro de segurança do Azure. Tipo de alertas incluem:

- Acesso a partir de uma localização invulgar
- Acesso a partir do Centro de dados do Azure invulgar 
- Acesso a partir de principal invulgar 
- Acesso a partir de uma aplicação potencialmente prejudicial 
- Base de dados do Azure para PostgreSQL credenciais de força bruta 

[Base de dados do Azure para MySQL Advanced Threat Protection](../mysql/concepts-data-access-and-security-threat-protection.md) fornece proteção semelhante à proteção avançada de PostgreSQL.  

## <a name="centralized-security-management"></a>Gestão de segurança centralizada

O [Centro de Segurança do Azure](https://azure.microsoft.com/documentation/services/security-center/) ajuda-o a evitar, detetar e responder a ameaças. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. Ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

[Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-sql-database) ajuda a salvaguardar os dados na base de dados SQL, oferecendo visibilidade sobre a segurança de todos os seus servidores e bases de dados. Com o Centro de segurança, pode:

- Defina políticas para auditoria e encriptação de base de dados SQL.
- Monitorize a segurança dos recursos de base de dados SQL em todas as suas subscrições.
- Identificar e resolver problemas de segurança rapidamente.
- Integrar alertas a partir [deteção de ameaças da base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection).

Centro de segurança suporta o acesso baseado em funções.

## <a name="sql-information-protection"></a>Proteção das Informações SQL

[Proteção de informações de SQL](../sql-database/sql-database-data-discovery-and-classification.md) automaticamente Deteta e classifica os dados potencialmente confidenciais, fornece um mecanismo de etiquetagem de forma permanente marcar dados confidenciais com atributos de classificação e fornece uma apresentação do dashboard detalhado o estado de classificação da base de dados.  

Além disso, ele calcula o resultado definido sensibilidade das consultas SQL, para que as consultas que extraem dados confidenciais podem ser auditadas explicitamente, e os dados podem ser protegidos. Para obter mais detalhes sobre a proteção de informações do SQL, consulte a deteção de dados do Azure SQL da base de dados e classificação.

Pode configurar [políticas de proteção de informações de SQL](../security-center/security-center-info-protection-policy.md) no Centro de segurança do Azure.

## <a name="azure-marketplace"></a>Azure Marketplace

O Azure Marketplace é um mercado online de aplicações e serviços que permite às empresas recém-criadas e aos fabricantes independentes de software (ISV) oferecerem as suas soluções aos clientes Azure de todo o mundo.
O Azure Marketplace combina os ecossistemas de parceiros do Microsoft Azure numa plataforma unificada para melhor servir clientes e parceiros. Pode [executar uma pesquisa](https://azuremarketplace.microsoft.com/marketplace/apps?search=Database%20Security&page=1) para visualizar os produtos de segurança de base de dados disponíveis no Azure Marketplace.

## <a name="next-steps"></a>Passos Seguintes

- [Proteger a sua base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial)
- [Serviço de centro de segurança do Azure e base de dados do Azure SQL](https://docs.microsoft.com/azure/security-center/security-center-sql-database)
- [Deteção de ameaças da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Melhorar o desempenho da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-performance-tutorial)
