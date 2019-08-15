---
title: Práticas recomendadas de segurança do banco de dados-Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas para a segurança de banco de dados do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 72b15d77baedae318d4503f2d481b08202730459
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927994"
---
# <a name="azure-database-security-best-practices"></a>Melhores práticas de segurança de bases de dados do Azure
Este artigo descreve as práticas recomendadas para segurança de banco de dados.

As práticas recomendadas baseiam-se em um consenso de opinião e funcionam com os recursos atuais da plataforma Azure e os conjuntos de recursos. As opiniões e tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="secure-databases"></a>Proteger bancos de dados
A segurança é uma das principais preocupações para o gerenciamento de bancos de dados e sempre foi uma prioridade para o [Azure SQL Database](../../sql-database/index.yml). Seus bancos de dados podem ser rigidamente protegidos para ajudar a atender à maioria dos requisitos regulatórios ou de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS nível 1. Uma lista atual de certificações de conformidade de segurança está disponível no [site da central de confiabilidade da Microsoft](https://azure.microsoft.com/support/trust-center/services/). Você também pode optar por posicionar seus bancos de dados em data centers do Azure específicos com base nos requisitos regulatórios.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Usar regras de firewall para restringir o acesso ao banco de dados
O Banco de Dados SQL do Microsoft Azure fornece um serviço de banco de dados relacional para o Azure e outros aplicativos baseados na Internet. Para fornecer segurança de acesso, o banco de dados SQL controla o acesso com:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os usuários a ações e dados específicos.

Os firewalls impedem todo o acesso ao seu servidor de banco de dados até que você especifique quais computadores têm permissão. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

A figura a seguir mostra onde você define um firewall de servidor no banco de dados SQL:

![Regras de firewall](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço do banco de dados SQL do Azure está disponível somente pela porta TCP 1433. Para acessar um banco de dados SQL do seu computador, verifique se o Firewall do computador cliente permite a comunicação TCP de saída na porta TCP 1433. Bloqueie as conexões de entrada na porta TCP 1433 usando regras de firewall, se você não precisar dessas conexões para outros aplicativos.

Como parte do processo de conexão, as conexões de máquinas virtuais do Azure são redirecionadas para um endereço IP e porta que são exclusivos para cada função de trabalho. O número de porta está no intervalo de 11000 a 11999. Para obter mais informações sobre portas TCP, consulte [portas além de 1433 para ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Além das regras de IP, o firewall gerencia as regras de rede virtual. As regras de rede virtual são baseadas em pontos de extremidade de serviço de rede virtual. Regras de rede virtual podem ser preferíveis a regras de IP em alguns casos. Para saber mais, consulte [pontos de extremidade de serviço de rede virtual e regras para o banco de dados SQL do Azure](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Habilitar autenticação de banco de dados
O banco de dados SQL dá suporte a dois tipos de autenticação, SQL Server autenticação e autenticação do Azure AD.

### <a name="sql-server-authentication"></a>*Autenticação SQL Server*

Os benefícios incluem o seguinte:

- Ele permite que o banco de dados SQL dê suporte a ambientes com sistemas operacionais mistos, onde todos os usuários não são autenticados por um domínio do Windows.
- Permite que o banco de dados SQL dê suporte a aplicativos mais antigos e aplicativos fornecidos por parceiros que exigem autenticação SQL Server.
- Permite que os usuários se conectem de domínios desconhecidos ou não confiáveis. Um exemplo é um aplicativo em que os clientes estabelecidos se conectam com os logons SQL Server atribuídos para receber o status de seus pedidos.
- Permite que o banco de dados SQL dê suporte a aplicativos baseados na Web em que os usuários criam suas próprias identidades.
- Permite que os desenvolvedores de software distribuam seus aplicativos usando uma hierarquia de permissões complexa com base em logons SQL Server predefinidos conhecidos.

> [!NOTE]
> A autenticação SQL Server não pode usar o protocolo de segurança Kerberos.

Se você usar a autenticação SQL Server, deverá:

- Gerencie as credenciais fortes por conta própria.
- Proteja as credenciais na cadeia de conexão.
- (Potencialmente) proteger as credenciais passadas pela rede do servidor Web para o banco de dados. Para obter mais informações, consulte [como: Conecte-se a SQL Server usando a autenticação do](/previous-versions/msp-n-p/ff648340(v=pandp.10))SQL no ASP.NET 2,0.

### <a name="azure-active-directory-ad-authentication"></a>*Autenticação do Azure Active Directory (AD)*
A autenticação do Azure AD é um mecanismo de conexão ao banco de dados SQL do Azure e [SQL data warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure AD. Com a autenticação do Azure AD, você pode gerenciar as identidades de usuários de banco de dados e outros serviços da Microsoft em um local central. O gerenciamento de identificação central fornece um único local para gerenciar usuários de banco de dados e simplifica o gerenciamento de permissões.

> [!NOTE]
> Recomendamos o uso da autenticação do Azure AD sobre o uso de SQL Server autenticação.

Os benefícios incluem o seguinte:

- Ele fornece uma alternativa para SQL Server autenticação.
- Ele ajuda a interromper a proliferação de identidades de usuário entre servidores de banco de dados.
- Ele permite a rotação de senha em um único lugar.
- Os clientes podem gerenciar permissões de banco de dados usando grupos externos (Azure AD).
- Ele pode eliminar o armazenamento de senhas habilitando a autenticação integrada do Windows e outras formas de autenticação com suporte pelo Azure Active Directory.
- Ele usa usuários de banco de dados independente para autenticar identidades no nível do banco de dados.
- Ele dá suporte à autenticação baseada em token para aplicativos que se conectam ao banco de dados SQL.
- Ele dá suporte a AD FS (Federação de domínio) ou autenticação nativa de usuário/senha para uma instância de Azure Active Directory local sem sincronização de domínio.
- O Azure AD dá suporte a conexões de SQL Server Management Studio que usam Active Directory autenticação universal, que inclui a autenticação multifator. A autenticação multifator fornece uma autenticação forte com uma variedade de opções de verificação – chamada telefônica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicativo móvel. Para obter mais informações, consulte [suporte do SSMS para autenticação multifator do Azure AD com o banco de dados SQL e SQL data warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md).

As etapas de configuração incluem os seguintes procedimentos para configurar e usar a autenticação do Azure AD:

- Crie e popule o Azure AD.
- Opcional: Associe ou altere a instância de Active Directory que está associada atualmente à sua assinatura do Azure.
- Crie um administrador de Azure Active Directory para o banco de dados SQL do Azure ou o [SQL data warehouse do Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure seus computadores cliente.
- Crie usuários de banco de dados independente em seu banco de dados mapeado para identidades do Azure AD.
- Conecte-se ao banco de dados usando identidades do Azure AD.

Você pode encontrar informações detalhadas em [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL, instância gerenciada ou SQL data warehouse](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Proteja seus dados usando criptografia e segurança em nível de linha
A Transparent [Data Encryption do banco de dados SQL do Azure](../../sql-database/transparent-data-encryption-azure-sql.md) ajuda a proteger dados em disco e protege contra acesso não autorizado ao hardware. Ele executa criptografia e descriptografia em tempo real do banco de dados, backups associados e arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. A Transparent Data Encryption criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada a chave de criptografia do banco de dados.

Mesmo quando todo o armazenamento é criptografado, é importante também criptografar o próprio banco de dados. Esta é uma implementação da abordagem de defesa aprofundada para proteção de dados. Se você estiver usando o banco de dados SQL do Azure e quiser proteger os dados confidenciais (como números de segurança social ou de cartão de crédito), poderá criptografar bancos de dado com a criptografia AES de 256 bits validada pelo FIPS 140-2. Essa criptografia atende aos requisitos de muitos padrões do setor (por exemplo, HIPAA e PCI).

Os arquivos relacionados à [extensão do pool de buffers (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não são criptografados quando você criptografa um banco de dados usando a Transparent Data Encryption. Você deve usar ferramentas de criptografia no nível do sistema de arquivos como o [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) ou o [Encrypting File System (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) para arquivos relacionados ao BPE.

Como um usuário autorizado, como um administrador de segurança ou um administrador de banco de dados, pode acessá-los mesmo que o banco de dados esteja criptografado com Transparent Data Encryption, você também deve seguir estas recomendações:

- Habilite a autenticação de SQL Server no nível de banco de dados.
- Use a autenticação do Azure AD usando [funções de RBAC](/azure/role-based-access-control/overview).
- Verifique se os usuários e aplicativos usam contas separadas para autenticação. Dessa forma, você pode limitar as permissões concedidas a usuários e aplicativos e reduzir o risco de atividades mal-intencionadas.
- Implemente a segurança em nível de banco de dados usando funções de banco de dados fixas (como db_datareader ou db_datawriter). Ou você pode criar funções personalizadas para seu aplicativo para conceder permissões explícitas a objetos de banco de dados selecionados.

Para outras maneiras de proteger seus dados, considere:

- A [encriptação ao nível da célula](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), que permite que os clientes criptografem dados confidenciais dentro de aplicativos cliente e nunca revelem as chaves de criptografia para a mecanismo de banco de dados (banco de dados SQL ou SQL Server). Como resultado, o Always Encrypted fornece uma separação entre aqueles que possuem os dados (e pode exibi-los) e aqueles que gerenciam os dados (mas não devem ter acesso).
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security), que permite que os clientes controlem o acesso a linhas em uma tabela de banco de dados com base nas características do usuário que está executando uma consulta. (Exemplos de características são Associação de grupo e contexto de execução.)

As organizações que não estão usando criptografia no nível de banco de dados podem ser mais suscetíveis a ataques que comprometam dados localizados em bancos de dado SQL.

Você pode saber mais sobre a Transparent Data Encryption do banco de dados SQL lendo o artigo [Transparent Data Encryption com o Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Habilitar auditoria de banco de dados
A auditoria de uma instância do SQL Server Mecanismo de Banco de Dados ou de um banco de dados individual envolve rastreamento e registro em log de eventos. Por SQL Server, você pode criar auditorias que contêm especificações para eventos de nível de servidor e especificações para eventos de nível de banco de dados. Os eventos auditados podem ser gravados nos logs de eventos ou em arquivos de auditoria.

Há vários níveis de auditoria para SQL Server, dependendo dos requisitos governamentais ou de padrões para sua instalação. SQL Server auditoria fornece ferramentas e processos para habilitar, armazenar e exibir auditorias em vários objetos de servidor e de banco de dados.

A [auditoria do banco de dados SQL do Azure](/azure/sql-database/sql-database-auditing) rastreia eventos do banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure.

A auditoria pode ajudá-lo a manter a conformidade regulatória, compreender a atividade do banco de dados e encontrar discrepâncias e anomalias que podem apontar para preocupações comerciais ou violações de segurança. A auditoria facilita a adesão aos padrões de conformidade, mas não garante a conformidade.

Para saber mais sobre a auditoria de banco de dados e como habilitá-la, confira [introdução à auditoria do banco de dados SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Habilitar detecção de ameaças de banco de dados
A proteção contra ameaças vai além da detecção. A proteção contra ameaças ao banco de dados inclui:

- Descobrindo e classificando seus dados mais confidenciais para que você possa proteger seus dados.
- Implementar configurações seguras em seu banco de dados para que você possa proteger seu banco de dados.
- Detectar e responder a ameaças potenciais à medida que elas ocorrem para que você possa responder e corrigir rapidamente.

**Prática recomendada**: Descubra, classifique e Rotule os dados confidenciais em seus bancos de dado.   
**Detalhe**: Classifique os dados no banco de dado SQL habilitando a [descoberta e a classificação de dados](/azure/sql-database/sql-database-data-discovery-and-classification) no banco de dados SQL do Azure. Você pode monitorar o acesso aos seus dados confidenciais no painel do Azure ou baixar relatórios.

**Prática recomendada**: Acompanhe as vulnerabilidades do banco de dados para que você possa melhorar proativamente a segurança do banco de dados.   
**Detalhe**: Use o serviço de [avaliação](/azure/sql-database/sql-vulnerability-assessment) de vulnerabilidades do banco de dados SQL do Azure, que verifica possíveis vulnerabilidades no banco de dados. O serviço emprega uma base de dados de conhecimento de regras que sinaliza vulnerabilidades de segurança e mostra desvios das práticas recomendadas, como configurações incorretas, excesso de permissões e dados confidenciais não protegidos.

As regras se baseiam nas práticas recomendadas da Microsoft e se concentram nos problemas de segurança que apresentam os maiores riscos ao seu banco de dados e seus valiosos. Eles abordam problemas de nível de banco de dados e de segurança no nível de servidor, como configurações de firewall do servidor e permissões de nível de servidor. Essas regras também representam muitos dos requisitos de órgãos regulatórios para atender aos seus padrões de conformidade.

**Prática recomendada**: Habilitar a detecção de ameaças.  
**Detalhe**:  Habilite a [detecção de ameaças](/azure/sql-database/sql-database-threat-detection) do banco de dados SQL do Azure para obter alertas de segurança e recomendações sobre como investigar e atenuar ameaças. Você obtém alertas sobre atividades suspeitas de banco de dados, vulnerabilidades potenciais e ataques de injeção de SQL, bem como padrões de consulta e acesso de banco de dados anormais.

A [proteção avançada contra ameaças](/azure/sql-database/sql-advanced-threat-protection) é um pacote unificado para recursos avançados de segurança do SQL. Ele inclui os serviços mencionados anteriormente: Descoberta de dados e classificação, avaliação de vulnerabilidade e detecção de ameaças. Ele fornece um único local para habilitar e gerenciar esses recursos.

A habilitação desses recursos ajuda você a:

- Atenda aos padrões de privacidade de dados e aos requisitos de conformidade regulatória.
- Controle o acesso aos seus bancos de dados e proteja sua segurança.
- Monitore um ambiente de banco de dados dinâmico onde as alterações são difíceis de rastrear.
- Detecte e responda a possíveis ameaças.

Além disso, a detecção de ameaças integra alertas com a central de segurança do Azure para uma exibição central do estado de segurança de todos os seus recursos do Azure.

## <a name="enable-feature-restrictions"></a>Habilitar restrições de recurso

Os dados contidos em seus bancos podem ser expostos a invasores usando vetores de ataque que aproveitam erros de banco de dados e tempos de execução de consulta. O banco de dados SQL do Azure fornece vários mecanismos de restrição de recursos para proteger seu banco de dados. Para saber mais, consulte [restrições de recursos do banco de dados SQL](/azure/sql-database/sql-database-feature-restrictions).

## <a name="next-steps"></a>Passos Seguintes
Veja [padrões e práticas recomendadas de segurança do Azure](best-practices-and-patterns.md) para obter mais práticas recomendadas de segurança para usar ao projetar, implantar e gerenciar suas soluções de nuvem usando o Azure.

Os recursos a seguir estão disponíveis para fornecer informações mais gerais sobre segurança do Azure e serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre o mais recente na segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou enviadas por email parasecure@microsoft.com
