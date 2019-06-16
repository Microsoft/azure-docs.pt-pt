---
title: Base de dados de melhores práticas de segurança - Microsoft Azure
description: Este artigo fornece um conjunto de práticas recomendadas para segurança de base de dados do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9cd02172af5246c60b93a6e4696988268abed506
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258687"
---
# <a name="azure-database-security-best-practices"></a>Práticas recomendadas de segurança da base de dados do Azure
Este artigo descreve as melhores práticas de segurança da base de dados.

As melhores práticas baseiam-se um consenso de opinião, e trabalhar com recursos atuais da plataforma do Azure e conjuntos de recursos. Opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="secure-databases"></a>Proteger bases de dados
A segurança é uma grande preocupação para a gestão de bases de dados e, sempre foi uma prioridade para o [base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/). As bases de dados podem ser fortemente protegidos para ajudar a satisfazer mais normas ou requisitos de segurança, incluindo a HIPAA, ISO 27001/27002 e PCI DSS nível 1. A lista atual de certificações de conformidade de segurança está disponível na [site do Microsoft Trust Center](https://azure.microsoft.com/support/trust-center/services/). Também pode optar por colocar as bases de dados nos datacenters do Azure específicos com base nos requisitos de regulamentação.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Utilize regras de firewall para restringir o acesso de base de dados
Base de dados de SQL do Microsoft Azure fornece um serviço de base de dados relacional para o Azure e outros aplicativos baseados na internet. Para fornecer segurança de acesso, base de dados SQL controla o acesso com:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os usuários comprovem sua identidade.
- Mecanismos de autorização que limitam os utilizadores a ações específicas e dados.

As firewalls impedem todo o acesso ao seu servidor de base de dados até especificar que computadores têm permissão. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

A seguir mostra a figura local onde configurou uma firewall de servidor na base de dados SQL:

![Regras da firewall](./media/azure-database-security-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço de base de dados do Azure SQL está disponível apenas através da porta TCP 1433. Para acessar um banco de dados SQL no seu computador, certifique-se de que a firewall de computador do cliente permite comunicação TCP de saída na porta TCP 1433. Bloquear ligações de entrada na porta TCP 1433 utilizando regras de firewall, se não tiver estas ligações para outros aplicativos.

Como parte do processo de ligação, as ligações a partir de máquinas virtuais do Azure são redirecionadas para um endereço IP e porta são exclusivos para cada função de trabalho. O número de porta está no intervalo de 11000 a 11999. Para obter mais informações sobre as portas TCP, consulte [portas para além do 1433 para ADO.NET 4.5](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Para além das regras IP, o firewall gere regras de rede virtual. Regras de rede virtual se baseiam nos pontos finais de serviço de rede virtual. Regras de rede virtual podem ser preferíveis para regras IP em alguns casos. Para obter mais informações, consulte [pontos finais de serviço de rede Virtual e regras para a base de dados do Azure SQL](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Ativar a autenticação de base de dados
Base de dados SQL suporta dois tipos de autenticação, a autenticação do SQL Server e a autenticação do Azure AD.

### <a name="sql-server-authentication"></a>*Autenticação do SQL Server*

Os benefícios incluem o seguinte:

- Ele permite que base de dados SQL oferecer suporte a ambientes com sistemas de operativos mistos, onde todos os utilizadores não são autenticados por um domínio Windows.
- Permite que a base de dados SQL oferecer suporte a aplicativos mais antigos e de aplicativos fornecido pelo parceiro que necessitam de autenticação do SQL Server.
- Permite aos utilizadores ligar a partir de domínios desconhecidos ou não fidedignos. Um exemplo é um aplicativo em que os clientes estabelecidos ligar com atribuído inícios de sessão do SQL Server para receber o estado dos seus pedidos.
- Permite a base de dados SQL oferecer suporte a aplicativos baseados na web em que os utilizadores criar suas próprias identidades.
- Permite que os desenvolvedores de software distribuir seus aplicativos usando uma hierarquia de permissão complexas com base em inícios de sessão de SQL Server do conhecido e predefinidos.

> [!NOTE]
> Autenticação do SQL Server não é possível utilizar o protocolo de segurança do Kerberos.

Se utilizar a autenticação do SQL Server, tem de:

- As credenciais fortes gerida por si.
- Protege as credenciais na cadeia de ligação.
- Protege (potencialmente) as credenciais transmitidas através da rede do servidor web para a base de dados. Para obter mais informações, consulte [como: Ligar ao SQL Server utilizando a autenticação SQL no ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Autenticação do Azure Active Directory (AD)*
Autenticação do Azure AD é um mecanismo de ligação a base de dados do Azure SQL e [o SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) usando identidades no Azure AD. Com a autenticação do Azure AD, pode gerir as identidades dos utilizadores de base de dados e outros serviços da Microsoft num local central. Gerenciamento de ID central fornece um único local para gerir utilizadores de base de dados e simplifica a gestão de permissões.

> [!NOTE]
> Recomendamos a utilização da autenticação do Azure AD sobre a utilização da autenticação do SQL Server.

Os benefícios incluem o seguinte:

- Ele fornece uma alternativa à autenticação do SQL Server.
- Ele ajuda a parar a proliferação de identidades de utilizador em servidores de base de dados.
- Ele permite que a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- Isso pode eliminar armazenar palavras-passe através da autenticação integrada do Windows e outras formas de autenticação suportado pelo Azure Active Directory.
- Ele usa os utilizadores de base de dados contida para autenticar identidades ao nível da base de dados.
- Suporta a autenticação baseada em tokens para aplicativos que se ligam a base de dados SQL.
- Ele dá suporte a AD FS (Federação do domínio) ou autenticação de utilizador/palavra-passe nativo para uma instância local do Azure Active Directory sem sincronização de domínio.
- O Azure AD suporta ligações do SQL Server Management Studio, que utilizam a autenticação do Active Directory Universal, que inclui o multi-factor Authentication. Multi-factor Authentication fornece uma autenticação segura com uma variedade de opções de verificação — telefonema, mensagem de texto, smart cards com PIN ou notificação de aplicação móvel. Para obter mais informações, consulte [SSMS suporte para AD multi-factor Authentication do Azure com a base de dados SQL e SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação do Azure AD:

- Criar e preencher do Azure AD.
- Opcional: Associar ou alterar a instância do Active Directory que está atualmente associada à sua subscrição do Azure.
- Criar um administrador do Azure Active Directory para a base de dados do Azure SQL ou [do Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure os computadores cliente.
- Crie utilizadores de base de dados contidos na base de dados mapeado para identidades do Azure AD.
- Ligar à base de dados com identidades do Azure AD.

Pode encontrar informações detalhadas nos [autenticação de utilização do Azure Active Directory para a autenticação com base de dados SQL, instância gerida ou SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Proteger os seus dados através da utilização de criptografia e segurança ao nível da linha
[A encriptação de dados transparente de base de dados SQL do Azure](../sql-database/transparent-data-encryption-azure-sql.md) ajuda a proteger dados no disco e protege contra acesso não autorizado ao hardware. Ele executa criptografia em tempo real e a descriptografia da base de dados, cópias de segurança associadas e ficheiros de registo de transação em repouso sem a necessidade de alterações à aplicação. Encriptação de dados transparente criptografa o armazenamento de uma base de dados completa com uma chave simétrica denominada a chave de encriptação da base de dados.

Mesmo quando o armazenamento completo é encriptado, é importante também para encriptar a base de dados em si. Esta é uma implementação da abordagem de defesa em profundidade para proteção de dados. Se estiver a utilizar o SQL Database do Azure e pretende proteger dados confidenciais (por exemplo, cartão de crédito ou números de cpf), pode encriptar bases de dados com FIPS 140-2 validados encriptação AES de 256 bits. Esta encriptação cumpre os requisitos de muitos padrões da indústria (por exemplo, HIPAA e PCI).

Ficheiros relacionados com [extensão do conjunto (BPE) da memória intermédia](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não estão encriptados quando criptografa uma base de dados através de encriptação de dados transparente. Tem de utilizar ferramentas de encriptação de nível de sistema de ficheiros, como [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) ou o [Encrypting File System (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) para ficheiros relacionados com o BPE.

Uma vez que um utilizador autorizado como um administrador de segurança ou um administrador de banco de dados pode aceder aos dados, mesmo que a base de dados é encriptado com encriptação de dados transparente, também deve seguir estas recomendações:

- Ative a autenticação do SQL Server ao nível da base de dados.
- Autenticação de utilização do Azure AD utilizando [funções RBAC](../role-based-access-control/overview.md).
- Certifique-se de que os utilizadores e aplicações usam contas separadas para autenticar. Dessa forma, pode limitar as permissões concedidas a utilizadores e aplicações e reduzir o risco de atividades maliciosas.
- Implementar a segurança ao nível da base de dados através de funções de base de dados fixa (como db_datareader ou db_datawriter). Ou pode criar funções personalizadas para a sua aplicação conceder permissões explícitas para objetos de base de dados selecionada.

Para outras formas de proteger os seus dados, considere:

- A [encriptação ao nível da célula](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine), que permite aos clientes encriptar dados confidenciais em aplicativos cliente e nunca revelar as chaves de encriptação para o motor de base de dados (base de dados SQL ou SQL Server). Como resultado, o Always Encrypted fornece uma separação entre os proprietários dos dados (e os podem ver) e aqueles que gerir os dados (mas não devem ter acesso).
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security), que permite aos clientes controlar o acesso às linhas numa tabela de base de dados com base nas características do utilizador que está a executar uma consulta. (As características de exemplo são o contexto de execução e associação de grupo.)

As organizações que não estiver a utilizar criptografia em nível de base de dados podem ser mais suscetíveis a ataques que comprometer dados localizados nas bases de dados SQL.

Pode saber mais sobre a encriptação de dados transparente de base de dados SQL ao ler o artigo [encriptação de dados transparente com a base de dados do Azure SQL](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Ativar a auditoria de base de dados
Uma instância do motor de base de dados do SQL Server ou uma base de dados individual a auditoria envolve a controlar e ao registo de eventos. Para o SQL Server, pode criar as auditorias que contêm as especificações para eventos ao nível do servidor e as especificações para eventos de nível de base de dados. Eventos auditados podem ser escritos para os registos de eventos ou para ficheiros de auditoria.

Existem vários níveis de auditoria para o SQL Server, dependendo do Governo ou requisitos de padrões para a sua instalação. A auditoria de SQL Server fornece ferramentas e processos para ativar, armazenar e visualização auditorias em vários server e objetos de base de dados.

[A auditoria de base de dados SQL do Azure](../sql-database/sql-database-auditing.md) faixas de base de dados eventos e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure.

A auditoria pode ajudá-lo a manter a conformidade regulamentar, compreender a atividade de base de dados e encontrar discrepâncias e anomalias que podem indicar preocupações empresariais ou violações de segurança. Auditoria facilita o cumprimento das normas de conformidade, mas não garante a conformidade.

Para saber mais sobre a auditoria de base de dados e como para ativá-la, veja [introdução à auditoria da base de dados SQL](../sql-database/sql-database-auditing.md).

## <a name="enable-database-threat-detection"></a>Ativar deteção de ameaças da base de dados
Proteção contra ameaças vai além da deteção. Inclui a proteção de ameaças da base de dados:

- A detetar e classificar os dados mais confidenciais, para que possa proteger seus dados.
- Implementando as configurações seguras na base de dados para que possa proteger sua base de dados.
- Detetar e responder a potenciais ameaças à medida que ocorrem, para que possa responder e remediar rapidamente.

**Melhor prática**: Detetar, classificar e etiquetar os dados confidenciais em seus bancos de dados.   
**Detalhe**: Classificar os dados na base de dados SQL, permitindo [dados de deteção e classificação](../sql-database/sql-database-data-discovery-and-classification.md) na base de dados do Azure SQL. Pode monitorizar o acesso aos seus dados confidenciais no dashboard do Azure ou baixar relatórios.

**Melhor prática**: Controlar as vulnerabilidades de base de dados, de modo proativo pode melhorar a segurança da base de dados.   
**Detalhe**: Utilizar a base de dados do SQL do Azure [avaliação de vulnerabilidade](../sql-database/sql-vulnerability-assessment.md) service, que verifica a existência de possíveis vulnerabilidades de base de dados. O serviço emprega uma base de dados de conhecimento de regras que sinalize vulnerabilidades de segurança e Mostrar desvios das melhores práticas, tais como configurações incorretas, permissões excessivas e dados confidenciais não protegidos.

As regras são baseadas em melhores práticas da Microsoft e concentrar-se os problemas de segurança que apresentam os maiores riscos para a base de dados e os respetivos dados valiosos. Eles abrangem os problemas de nível de base de dados e questões de segurança ao nível do servidor, como definições de firewall do servidor e permissões ao nível do servidor. Estas regras também representam muitos dos requisitos de órgãos normativos para atender aos seus padrões de conformidade.

**Melhor prática**: Ative deteção de ameaças.  
**Detalhe**:  Ativar o Azure SQL Database [deteção de ameaças](../sql-database/sql-database-threat-detection.md) obter alertas de segurança e recomendações sobre como investigar e mitigar ameaças. Obtenha alertas sobre atividades suspeitas da base de dados, potenciais vulnerabilidades e ataques de injeção de SQL, bem como base de dados anómala padrões de acesso e a consulta.

[Proteção avançada contra ameaças](../sql-database/sql-advanced-threat-protection.md) é um pacote unificado para funções de segurança avançadas do SQL. Ele inclui os serviços mencionados anteriormente: Dados de deteção e classificação, avaliação de vulnerabilidade e a deteção de ameaças. Ele fornece um único local para ativar e gerir esses recursos.

Ativar estas capacidades ajuda-o:

- Cumpre as normas de privacidade de dados e os requisitos de conformidade a normas.
- Controlar o acesso aos seus bancos de dados e proteger a segurança dos mesmos.
- Monitorize um ambiente de base de dados dinâmicos em que são difíceis de rastrear alterações.
- Detetar e responder a potenciais ameaças.

Além disso, a deteção de ameaças integra alertas no Centro de segurança do Azure para uma visão central do Estado de segurança de todos os seus recursos do Azure.

## <a name="enable-feature-restrictions"></a>Ativar restrições de recurso

Os dados contidos nas suas bases de dados podem ser expostos a ataques com vetores de ataque que tiram partido dos erros de base de dados e tempos de execução da consulta. Base de dados SQL do Azure fornece vários mecanismos de restrição de funcionalidade para proteger a sua base de dados. Para obter mais informações, consulte [restrições de recurso de base de dados SQL](../sql-database/sql-database-feature-restrictions.md).

## <a name="next-steps"></a>Passos Seguintes
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais acerca da segurança do Azure e serviços Microsoft relacionados:
* [Blogue de equipa de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as mais recentes da segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – onde podem ser comunicadas as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, ou por e-mail para secure@microsoft.com
