---
title: Boas práticas de segurança na base de dados - Microsoft Azure
description: Este artigo fornece um conjunto de boas práticas para a segurança da base de dados Azure.
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
ms.openlocfilehash: 0f2e0257c5bf855b0d9be61c43b68b4e30b3d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125112"
---
# <a name="azure-database-security-best-practices"></a>Melhores práticas de segurança de bases de dados do Azure
Este artigo descreve as melhores práticas para a segurança da base de dados.

As melhores práticas baseiam-se num consenso de opinião, e funcionam com as capacidades atuais da plataforma Azure e conjuntos de funcionalidades. As opiniões e as tecnologias mudam ao longo do tempo e este artigo é atualizado regularmente para refletir essas alterações.

## <a name="secure-databases"></a>Bases de dados seguras
A segurança é uma das principais preocupações para a gestão de bases de dados, e sempre foi uma prioridade para a Base de [Dados Azure SQL.](../../sql-database/index.yml) As suas bases de dados podem ser bem protegidas para ajudar a satisfazer a maioria dos requisitos regulamentares ou de segurança, incluindo HIPAA, ISO 27001/27002 e PCI DSS Level 1. Uma lista atual de certificações de conformidade de segurança está disponível no site do [Microsoft Trust Center](https://azure.microsoft.com/support/trust-center/services/). Também pode optar por colocar as suas bases de dados em datacenters específicos do Azure com base em requisitos regulamentares.

## <a name="use-firewall-rules-to-restrict-database-access"></a>Utilize regras de firewall para restringir o acesso à base de dados
A Microsoft Azure SQL Database fornece um serviço de base de dados relacional para o Azure e outras aplicações baseadas na Internet. Para garantir a segurança no acesso, a Base de Dados SQL controla o acesso com:

- Regras de firewall que limitam a conectividade por endereço IP.
- Mecanismos de autenticação que exigem que os utilizadores provem a sua identidade.
- Mecanismos de autorização que limitam os utilizadores a ações e dados específicos.

As firewalls impedem qualquer acesso ao seu servidor de base de dados até especificar quais os computadores que têm permissão. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

A figura que se segue mostra onde define uma firewall do servidor na Base de Dados SQL:

![Regras da firewall](./media/database-best-practices/azure-database-security-best-practices-Fig1.png)

O serviço de base de dados Azure SQL está disponível apenas através da porta TCP 1433. Para aceder a uma base de dados SQL a partir do seu computador, certifique-se de que a firewall do computador do seu cliente permite a comunicação tCP de saída na porta TCP 1433. Bloqueie as ligações de entrada na porta TCP 1433 utilizando regras de firewall, se não precisar destas ligações para outras aplicações.

Como parte do processo de ligação, as ligações das máquinas virtuais Azure são redirecionadas para um endereço IP e porta que são únicas para cada função do trabalhador. O número de porta está no intervalo de 11000 a 11999. Para mais informações sobre as portas da TCP, consulte [portos para além de 1433 para ADO.NET 4,5](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).

Para obter mais informações sobre as regras de firewall na Base de Dados SQL, veja [Regras de firewall da Base de Dados SQL](../../sql-database/sql-database-firewall-configure.md).

> [!Note]
> Além das regras ip, a firewall gere as regras de rede virtuais. As regras de rede virtual baseiam-se em pontos finais do serviço de rede virtual. As regras de rede virtuais podem ser preferíveis às regras de PI em alguns casos. Para saber mais, consulte pontos finais de serviço de rede virtual e regras para a Base de [Dados Azure SQL](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="enable-database-authentication"></a>Ativar a autenticação da base de dados
A Base de Dados SQL suporta dois tipos de autenticação, autenticação do Servidor SQL e autenticação AD Azure.

### <a name="sql-server-authentication"></a>*Autenticação do Servidor SQL*

Os benefícios incluem o seguinte:

- Permite à SQL Database suportar ambientes com sistemas operativos mistos, onde todos os utilizadores não são autenticados por um domínio Windows.
- Permite que a Base de Dados SQL suporte aplicações mais antigas e aplicações fornecidas por parceiros que requerem autenticação do Servidor SQL.
- Permite que os utilizadores se conectem a partir de domínios desconhecidos ou não confiáveis. Um exemplo é uma aplicação onde clientes estabelecidos se conectam com logins sql server atribuídos para receber o estado das suas encomendas.
- Permite que a Base de Dados SQL suporte aplicações baseadas na Web onde os utilizadores criam as suas próprias identidades.
- Permite que os desenvolvedores de software distribuam as suas aplicações utilizando uma complexa hierarquia de permissão baseada em logins conhecidos e predefinidos do SQL Server.

> [!NOTE]
> A autenticação do Servidor SQL não pode usar o protocolo de segurança Kerberos.

Se utilizar a autenticação do Servidor SQL, deve:

- Gerencie as credenciais fortes.
- Proteja as credenciais na corda de ligação.
- (Potencialmente) proteger as credenciais transmitidas sobre a rede do servidor web para a base de dados. Para mais informações, consulte [Como: Ligar ao servidor SQL utilizando a autenticação SQL em ASP.NET 2.0](/previous-versions/msp-n-p/ff648340(v=pandp.10)).

### <a name="azure-active-directory-ad-authentication"></a>*Autenticação do Diretório Ativo Azure (AD)*
A autenticação Azure AD é um mecanismo de ligação à Base de Dados Azure SQL e ao [SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) utilizando identidades em Azure AD. Com a autenticação da AD Azure, pode gerir as identidades dos utilizadores de bases de dados e de outros serviços da Microsoft numa localização central. A gestão de IDs centralizada disponibiliza um único local para gerir utilizadores da base de dados e simplifica a gestão de permissões.

> [!NOTE]
> Recomendamos a utilização da autenticação Azure AD sobre a utilização da autenticação do Servidor SQL.

Os benefícios incluem o seguinte:

- Fornece uma alternativa à autenticação do Servidor SQL.
- Ajuda a parar a proliferação de identidades de utilizadores através de servidores de base de dados.
- Permite a rotação da palavra-passe num único local.
- Os clientes podem gerir permissões de base de dados utilizando grupos externos (Azure AD).
- Pode eliminar o armazenamento de palavras-passe permitindo a autenticação integrada do Windows e outras formas de autenticação suportadas pelo Diretório Ativo do Azure.
- Utiliza utilizadores de bases de dados contidos para autenticar identidades ao nível da base de dados.
- Suporta autenticação baseada em token para aplicações que se ligam à Base de Dados SQL.
- Suporta AD FS (federação de domínio) ou autenticação de utilizador/palavra-passe nativo para uma instância de Diretório Ativo Azure local sem sincronização de domínio.
- A Azure AD suporta ligações do Estúdio de Gestão de Servidores SQL que utilizam a autenticação universal do Diretório Ativo, que inclui a autenticação multi-factor. A Autenticação Multi-Factor fornece uma autenticação forte com um leque de opções de verificação - chamada telefónica, mensagem de texto, cartões inteligentes com PIN ou notificação de aplicação móvel. Para mais informações, consulte o [suporte SSMS para autenticação multi-factor Azure AD com base de dados SQL e Armazém de Dados SQL](../../sql-database/sql-database-ssms-mfa-authentication.md).

Os passos de configuração incluem os seguintes procedimentos para configurar e utilizar a autenticação Azure AD:

- Crie e povoea o Azure AD.
- Opcional: Associar ou alterar a instância de Diretório Ativo que está atualmente associada à sua subscrição Azure.
- Crie um administrador de diretório ativo Azure para a Base de Dados Azure SQL ou para o Armazém de [Dados Azure SQL](https://azure.microsoft.com/services/sql-data-warehouse/).
- Configure os computadores dos seus clientes.
- Crie utilizadores de bases de dados contidos na sua base de dados mapeada para identidades da AD Azure.
- Ligue-se à sua base de dados utilizando identidades AD Azure.

Pode encontrar informações detalhadas na [autenticação do Diretório Ativo Do Azure para autenticação com base de dados SQL, Instância Gerida ou Armazém de Dados SQL](../../sql-database/sql-database-aad-authentication.md).

## <a name="protect-your-data-by-using-encryption-and-row-level-security"></a>Proteja os seus dados utilizando encriptação e segurança ao nível da linha
A encriptação transparente de dados transparente da Base de [Dados Azure SQL](../../sql-database/transparent-data-encryption-azure-sql.md) ajuda a proteger dados no disco e protege contra o acesso não autorizado ao hardware. Realiza a encriptação e desencriptação em tempo real da base de dados, cópias de segurança associadas e ficheiros de registo de transações inativos e não carece de alterações à aplicação. A encriptação transparente de dados encripta o armazenamento de uma base de dados inteira utilizando uma chave simétrica chamada chave de encriptação da base de dados.

Mesmo quando todo o armazenamento está encriptado, é importante também encriptar a própria base de dados. Trata-se de uma implementação da abordagem de defesa aprofundada para a proteção de dados. Se estiver a utilizar a Base de Dados Azure SQL e pretender proteger dados sensíveis (como cartões de crédito ou números de segurança social), pode encriptar bases de dados com encriptação AES validada por 140-2. Esta encriptação satisfaz os requisitos de muitos padrões da indústria (por exemplo, HIPAA e PCI).

Os ficheiros relacionados com a extensão do [pool do tampão (BPE)](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) não são encriptados quando encripta uma base de dados utilizando encriptação de dados transparente. Deve utilizar ferramentas de encriptação ao nível do sistema de ficheiros, como o [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) ou o Sistema de [Ficheiros Encriptadores (EFS)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749610(v%3dws.10)) para ficheiros relacionados com o BPE.

Uma vez que um utilizador autorizado, como um administrador de segurança ou um administrador de base de dados, pode aceder aos dados mesmo que a base de dados esteja encriptada com encriptação transparente de dados, deve também seguir estas recomendações:

- Ativar a autenticação do Servidor SQL ao nível da base de dados.
- Utilize a autenticação Azure AD utilizando [funções RBAC](/azure/role-based-access-control/overview).
- Certifique-se de que os utilizadores e aplicações utilizam contas separadas para autenticar. Desta forma, pode limitar as permissões concedidas aos utilizadores e aplicações e reduzir o risco de atividades maliciosas.
- Implementar a segurança ao nível da base de dados utilizando funções fixas de base de dados (como db_datareader ou db_datawriter). Ou pode criar funções personalizadas para a sua aplicação para conceder permissões explícitas a objetos de base de dados selecionados.

Para outras formas de proteger os seus dados, considere:

- A [encriptação ao nível da célula](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), para encriptar colunas específicas ou até mesmo células de dados com chaves de encriptação diferentes.
- [Sempre Encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine), que permite aos clientes encriptar dados confidenciais dentro das aplicações do cliente e nunca revelar as chaves de encriptação do Motor base de dados (Base de Dados SQL ou Servidor SQL). Como resultado, a Always Encrypted proporciona uma separação entre aqueles que possuem os dados (e podem vê-los) e aqueles que gerem os dados (mas não devem ter acesso).
- [Segurança de Nível de Linha](/sql/relational-databases/security/row-level-security), que permite aos clientes controlar o acesso a linhas numa tabela de bases de dados com base nas características do utilizador que está a executar uma consulta. (Exemplo de características são a adesão ao grupo e o contexto de execução.)

As organizações que não estão a usar encriptação ao nível da base de dados podem ser mais suscetíveis a ataques que comprometem dados localizados nas bases de dados sql.

Pode saber mais sobre encriptação transparente de dados transparente sQL Database lendo o artigo Encriptação de dados transparente com base de [dados Azure SQL](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="enable-database-auditing"></a>Ativar a auditoria da base de dados
A auditoria de uma instância do Motor de Base de Dados do Servidor SQL ou de uma base de dados individual envolve rastreio e registo de eventos. Para o SQL Server, pode criar auditorias que contenham especificações para eventos e especificações ao nível do servidor para eventos ao nível da base de dados. Os eventos auditados podem ser escritos nos registos do evento ou para auditar ficheiros.

Existem vários níveis de auditoria para o SQL Server, dependendo dos requisitos governamentais ou de padrões para a sua instalação. A auditoria do SQL Server fornece ferramentas e processos para permitir, armazenar e visualizar auditorias em vários objetos de servidor e base de dados.

A auditoria da Base de [Dados Azure SQL](/azure/sql-database/sql-database-auditing) rastreia os eventos da base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure.

A auditoria pode ajudá-lo a manter o cumprimento regulamentar, compreender a atividade da base de dados e encontrar discrepâncias e anomalias que possam apontar para preocupações comerciais ou violações de segurança. A auditoria facilita a adesão às normas de conformidade, mas não garante o cumprimento.

Para saber mais sobre a auditoria da base de dados e como o permitir, consulte Iniciar a auditoria da base de [dados SQL](/azure/sql-database/sql-database-auditing).

## <a name="enable-database-threat-detection"></a>Ativar a deteção de ameaças de base de dados
A proteção contra ameaças vai além da deteção. A proteção contra ameaças de base de dados inclui:

- Descobrir e classificar os seus dados mais sensíveis para que possa proteger os seus dados.
- Implementar configurações seguras na sua base de dados para que possa proteger a sua base de dados.
- Detetar e responder a potenciais ameaças à medida que ocorrem para que possa responder e remediar rapidamente.

**Boas práticas**: Descubra, classifique e classifique os dados sensíveis nas suas bases de dados.   
**Detalhe**: Classifique os dados na sua base de dados SQL, permitindo a [descoberta e classificação](/azure/sql-database/sql-database-data-discovery-and-classification) de dados na Base de Dados Azure SQL. Pode monitorizar o acesso aos seus dados sensíveis no painel de instrumentos do Azure ou descarregar relatórios.

**Boas práticas**: Acompanhe as vulnerabilidades da base de dados para que possa melhorar proativamente a segurança da sua base de dados.   
**Detalhe**: Utilize o serviço de [avaliação](/azure/sql-database/sql-vulnerability-assessment) de vulnerabilidade da base de dados Azure SQL, que procura potenciais vulnerabilidades na base de dados. O serviço emprega uma base de conhecimentos de regras que sinalizam vulnerabilidades de segurança e mostram desvios das melhores práticas, tais como configurações erradas, permissões excessivas e dados sensíveis desprotegidos.

As regras baseiam-se nas melhores práticas da Microsoft e focam-se nos problemas de segurança que apresentam os maiores riscos para a sua base de dados e os seus dados valiosos. Eles cobrem problemas de nível de base de dados e problemas de segurança ao nível do servidor, como definições de firewall do servidor e permissões ao nível do servidor. Estas regras representam também muitos dos requisitos dos organismos reguladores para cumprir em conformidade com as suas normas de conformidade.

**Boas práticas**: Permitir a deteção de ameaças.  
**Detalhe**: Enable Azure SQL Database [Threat Detection](/azure/sql-database/sql-database-threat-detection) para obter alertas de segurança e recomendações sobre como investigar e mitigar ameaças. Recebe alertas sobre atividades suspeitas de bases de dados, potenciais vulnerabilidades e ataques de injeção SQL, bem como acesso aano maléfico à base de dados e padrões de consulta.

[Advanced Threat Protection](/azure/sql-database/sql-advanced-threat-protection) é um pacote unificado para capacidades avançadas de segurança SQL. Inclui os serviços mencionados anteriormente: Data Discovery and Classification, Vulnerability Assessment e Threat Detection. Fornece uma única localização para permitir e gerir estas capacidades.

Permitir estas capacidades ajuda-o:

- Cumprir as normas de privacidade dos dados e os requisitos de conformidade regulamentar.
- Controle o acesso às suas bases de dados e endureça a segurança deles.
- Monitorize um ambiente dinâmico de base de dados onde as alterações são difíceis de rastrear.
- Detete e responda a potenciais ameaças.

Além disso, a Deteção de Ameaças integra alertas com o Azure Security Center para uma visão central do estado de segurança de todos os seus recursos Azure.

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.

Os seguintes recursos estão disponíveis para fornecer informações mais gerais sobre segurança Azure e serviços relacionados com a Microsoft:
* [Azure Security Team Blog](https://blogs.msdn.microsoft.com/azuresecurity/) - para informações atualizadas sobre as últimas novidades em Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) - onde as vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser reportadas ou via e-mail parasecure@microsoft.com
