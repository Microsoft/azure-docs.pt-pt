---
title: Gerir após a migração
titleSuffix: Azure SQL Database
description: Saiba como gerir as suas bases de dados individuais e agrêmidas após a migração para a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: b34ac24cb26bf5db4a49a5ad5b531deb252f4695
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446122"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Novo DBA na nuvem – Gestão da Base de Dados Azure SQL após migração
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Passar do ambiente tradicional auto-gerido e auto-controlado para um ambiente PaaS pode parecer um pouco esmagador no início. Como desenvolvedor de aplicações ou DBA, gostaria de conhecer as capacidades centrais da plataforma que o ajudariam a manter a sua aplicação disponível, performante, segura e resiliente - sempre. Este artigo pretende fazer exatamente isso. O artigo organiza sucintamente recursos e dá-lhe algumas orientações sobre como utilizar melhor as capacidades-chave da Base de Dados Azure SQL com bases de dados únicas e agrizadas para gerir e manter a sua aplicação a funcionar de forma eficiente e obter os melhores resultados na nuvem. O público típico deste artigo seria aqueles que:

- Estão a avaliar a migração das suas aplicações para a Base de Dados Azure SQL – Modernizando a sua(s) aplicação.s.
- Estão em processo de migração das suas aplicações (s) – Cenário de migração em curso.
- Concluíram recentemente a migração para a Base de Dados Azure SQL – Nova DBA na nuvem.

Este artigo discute algumas das características fundamentais da Azure SQL Database como uma plataforma que pode facilmente aproveitar ao trabalhar com bases de dados únicas e bases de dados em piscinas elásticas. São os seguintes:

- Monitorizar bases de dados com o portal do Azure
- Continuidade de negócio e recuperação após desastre (BCDR)
- Segurança e conformidade
- Monitorização e manutenção de bases de dados inteligentes
- Movimento de dados

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados com o portal do Azure

No [portal Azure,](https://portal.azure.com/)pode monitorizar a utilização de uma base de dados individual selecionando a sua base de dados e clicando na tabela **de Monitorização.** É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

- Percentagem de CPU
- Percentagem de DTU
- Percentagem de ES de Dados
- Percentagem de tamanho da Base de Dados

Depois de adicionar estas métricas, pode continuar a vê-las na tabela **de monitorização** com mais informações na janela **métrica.** As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte o [modelo de compra baseado em DTU](service-tiers-dtu.md) e artigos de modelo de compra [baseados em vCore](service-tiers-vcore.md) para obter mais informações sobre os níveis de serviço.  

![Monitorização da camada de serviços do desempenho da base de dados.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Pode usar isto como um aviso precoce para descobrir quando poderá ter de mudar para o próximo tamanho de computação mais alto.

As métricas de desempenho também podem ajudá-lo a determinar se é capaz de reduzir para um tamanho de cálculo mais baixo. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, esteja atento às cargas de trabalho que aumentam ou flutuam antes de tomar a decisão de passar para um tamanho de cálculo mais baixo.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Continuidade de negócio e recuperação após desastre (BCDR)

As capacidades de continuidade do negócio e recuperação de desastres permitem-lhe continuar o seu negócio, como de costume, em caso de desastre. O desastre pode ser um evento de nível de base de dados (por exemplo, alguém por engano deixa cair uma tabela crucial) ou um evento de nível de data-center (catástrofe regional, por exemplo, um tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como posso criar e gerir backups na Base de Dados SQL

Não cria cópias de segurança na Base de Dados Azure SQL e isso é porque não precisa. A SQL Database faz automaticamente o backup das bases de dados para si, pelo que já não deve preocupar-se em agendar, tirar e gerir cópias de segurança. A plataforma recebe uma cópia de segurança completa todas as semanas, backup diferencial a cada poucas horas e uma cópia de segurança de registo a cada 5 minutos para garantir que a recuperação de desastres é eficiente, e a perda mínima de dados. A primeira cópia de segurança completa acontece assim que criar uma base de dados. Estas cópias de segurança estão disponíveis para si durante um determinado período chamado "Período de Retenção" e variam de acordo com o nível de serviço que escolher. A Base de Dados SQL fornece-lhe a capacidade de restaurar a qualquer ponto no tempo dentro deste período de retenção utilizando [Point in Time Recovery (PITR)](recovery-using-backups.md#point-in-time-restore).

|Escalão de serviço|Período de retenção em dias|
|---|:---:|
|Básico|7|
|Standard|35|
|Premium|35|
|||

Além disso, a funcionalidade [de Retenção a Longo Prazo (LTR) permite-lhe](long-term-retention-overview.md) manter os seus ficheiros de backup por um período muito mais longo especificamente, por um período máximo de 10 anos, e restaurar os dados destas cópias de segurança em qualquer ponto desse período. Além disso, as cópias de segurança da base de dados são mantidas em armazenamento geo-replicado para garantir a resiliência da catástrofe regional. Também pode restaurar estes backups em qualquer região de Azure em qualquer momento dentro do período de retenção. Consulte [a visão geral da continuidade do Negócio](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Como posso assegurar a continuidade do negócio em caso de desastre ao nível do datacenter ou catástrofe regional

Como as cópias de segurança da sua base de dados estão armazenadas em armazenamento geo-replicado para garantir que, em caso de desastre regional, pode restaurar a cópia de segurança para outra região do Azure. Isto chama-se geo-restauro. O RPO (Objetivo do Ponto de Recuperação) para este facto é geralmente < 1 hora e o ERT (Tempo estimado de recuperação – poucos minutos a horas).

Para bases de dados críticas à missão, a Base de Dados Azure SQL oferece uma geo-replicação ativa. O que isto essencialmente faz é criar uma cópia secundária geo-replicada da sua base de dados original noutra região. Por exemplo, se a sua base de dados estiver inicialmente hospedada na região de Azure West US e quiser resiliência regional de desastres. Criaria uma réplica geoactiva da base de dados no Oeste dos EUA para dizer Leste dos EUA. Quando a calamidade atacar os EUA Ocidentais, podes falhar na região leste dos EUA. A sua configuração num grupo de auto-failover é ainda melhor, porque isto garante que a base de dados falha automaticamente para o secundário nos EUA em caso de catástrofe. O RPO para isto é < 5 segundos e o ERT < 30 segundos.

Se um grupo de falha automática não estiver configurado, então a sua aplicação precisa de monitorizar ativamente para uma catástrofe e iniciar uma falha no secundário. Pode criar até 4 geo-réplicas ativas em diferentes regiões do Azure. Fica ainda melhor. Também pode aceder a estas réplicas de geo-réplicas ativas secundárias para acesso apenas à leitura. Isto vem muito útil para reduzir a latência para um cenário de aplicação geo-distribuído.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Como é que o meu plano de recuperação de desastres muda de local para base de dados SQL

Em resumo, a configuração do SQL Server requer que você gere ativamente a sua Disponibilidade utilizando funcionalidades como Clustering Failover, Cloud Mirroring, Replicação de Transações ou Envio de Registos e manter e gerir backups para garantir a continuidade do negócio. Com a SQL Database, a plataforma gere-as para si, para que possa focar-se em desenvolver e otimizar a sua aplicação de base de dados e não se preocupar tanto com a gestão de desastres. Pode configurar planos de backup e recuperação de desastres e trabalhar com apenas alguns cliques no portal Azure (ou alguns comandos utilizando as APIs powerShell).

Para saber mais sobre a recuperação de desastres, consulte: [Azure SQL Database Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Segurança e conformidade

A Base de Dados SQL leva a Segurança e Privacidade muito a sério. A segurança dentro da BASE de Dados SQL está disponível ao nível da base de dados e ao nível da plataforma e é melhor compreendida quando categorizada em várias camadas. Em cada camada você pode controlar e fornecer a segurança ideal para a sua aplicação. As camadas são:

- Autenticação & de identidade[(autenticação SQL e autenticação do Azure Ative Directory [Azure AD]](logins-create-manage.md)).
- Atividade de[monitorização (Auditoria](../../azure-sql/database/auditing-overview.md) e [deteção de ameaças).](threat-detection-configure.md)
- Proteger dados reais[(Encriptação de Dados Transparentes [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Sempre Encriptado [AE]).](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- Controlo do Acesso a dados sensíveis e privilegiados[(Segurança de nível de linha](/sql/relational-databases/security/row-level-security) e [máscara de dados dinâmicos).](/sql/relational-databases/security/dynamic-data-masking)

[O Azure Security Center](https://azure.microsoft.com/services/security-center/) oferece uma gestão centralizada de segurança através de cargas de trabalho em Azure, no local e em outras nuvens. Pode ver se a proteção essencial da Base de Dados SQL, como [a Auditoria](../../azure-sql/database/auditing-overview.md) e a [Encriptação de Dados Transparentes [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) estão configuradas em todos os recursos e criar políticas baseadas nos seus próprios requisitos.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Quais os métodos de autenticação do utilizador oferecidos na Base de Dados SQL

Existem dois métodos de autenticação oferecidos na Base de Dados SQL:

- [Autenticação do Azure Active Directory](authentication-aad-overview.md)
- [Autenticação SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

A autenticação tradicional das janelas não é suportada. O Azure Ative Directory (Azure AD) é um serviço centralizado de gestão de identidade e acesso. Com isto pode fornecer convenientemente um Único Acesso De Acesso (SSO) a todo o pessoal da sua organização. O que isto significa é que as credenciais são partilhadas em todos os serviços da Azure para uma autenticação mais simples. 

O Azure AD suporta [a autenticação multi-factor Azure AD](authentication-mfa-ssms-overview.md) e com [alguns cliques](../../active-directory/hybrid/how-to-connect-install-express.md) o Azure AD pode ser integrado com o Windows Server Ative Directory. A autenticação SQL funciona exatamente como a tens usado no passado. Fornece um nome de utilizador/palavra-passe e pode autenticar os utilizadores em qualquer base de dados num determinado servidor. Isto também permite que a SQL Database e a Azure Synapse Analytics ofereçam autenticação multi-factor e contas de utilizadores de hóspedes dentro de um domínio AD Azure. Se já tem um Diretório Ativo no local, pode federar o diretório com o Azure Ative Directory para estender o seu diretório ao Azure.

|**Se...**|**Base de Dados SQL / Azure Synapse Analytics**|
|---|---|
|Prefere não usar O Diretório Ativo Azure (Azure AD) em Azure|Use [a autenticação SQL](security-overview.md)|
|AD usado no SQL Server no local|[Federate AD com Azure AD,](../../active-directory/hybrid/whatis-hybrid-identity.md)e use a autenticação AZure AD. Com isto, pode utilizar o Single Sign-On.|
|Necessidade de impor a autenticação multi-factor|Requera a autenticação multi-factor como uma política através do [Microsoft Conditional Access](conditional-access-configure.md), e utilize [a autenticação universal Azure AD com suporte à autenticação multi-factor](authentication-mfa-ssms-overview.md).|
|Ter contas de hóspedes a partir de contas da Microsoft (live.com, outlook.com) ou outros domínios (gmail.com)|Utilize [a autenticação universal Azure AD](authentication-mfa-ssms-overview.md) na Base de Dados SQL/Data Warehouse, que aproveita [a colaboração Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md).|
|Estão registados no Windows utilizando as suas credenciais AD Azure a partir de um domínio federado|Utilize [a autenticação integrada Azure AD](authentication-aad-configure.md).|
|São iniciados no Windows usando credenciais de um domínio não federado com Azure|Utilize [a autenticação integrada Azure AD](authentication-aad-configure.md).|
|Disponha de serviços de nível médio que precisam de se ligar à BASE de Dados SQL ou ao Azure Synapse Analytics|Utilize [a autenticação integrada Azure AD](authentication-aad-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Como limito ou controlo o acesso à conectividade à minha base de dados

Existem múltiplas técnicas à sua disposição que pode usar para alcançar uma organização de conectividade ideal para a sua aplicação.

- Regras da Firewall
- Pontos Finais de Serviço da VNet
- IPs Reservados

#### <a name="firewall"></a>Firewall

Uma firewall impede o acesso ao seu servidor a partir de uma entidade externa, permitindo apenas acesso a entidades específicas ao seu servidor. Por predefinição, todas as ligações às bases de dados dentro do servidor são proibidas, exceto (opcionalmente7) ligações provenientes de outros Serviços Azure. Com uma regra de firewall, pode abrir o acesso ao seu servidor apenas a entidades (por exemplo, uma máquina de programador) que aprova, permitindo o endereço IP desse computador através da firewall. Também permite especificar uma gama de IPs que gostaria de permitir o acesso ao servidor. Por exemplo, os endereços IP da máquina de desenvolvedor na sua organização podem ser adicionados de uma só vez especificando um intervalo na página de definições de Firewall.

Pode criar regras de firewall ao nível do servidor ou ao nível da base de dados. As regras de firewall IP de nível do servidor podem ser criadas usando o portal Azure ou com SSMS. Para saber mais sobre como definir uma regra de firewall ao nível do servidor e nível de base de dados, consulte: Criar regras de [firewall IP na Base de Dados SQL](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Pontos finais de serviço

Por predefinição, a sua base de dados está configurada para "Permitir que os serviços do Azure acedam ao servidor" – o que significa que qualquer Máquina Virtual em Azure pode tentar ligar-se à sua base de dados. Estas tentativas ainda têm de ser autenticadas. No entanto, se não pretender que a sua base de dados esteja acessível por quaisquer IPs Azure, pode desativar "Permitir que os serviços do Azure acedam ao servidor". Além disso, pode configurar [pontos finais de serviço VNet](vnet-service-endpoint-rule-overview.md).

Os pontos finais de serviço (SE) permitem-lhe expor os seus recursos Azure críticos apenas à sua própria rede virtual privada em Azure. Ao fazê-lo, elimina essencialmente o acesso do público aos seus recursos. O tráfego entre a sua rede virtual para a Azure permanece na rede de espinha dorsal Azure. Sem a SE, recebes um encaminhamento de pacotes de túneis forçados. A sua rede virtual força o tráfego de internet para a sua organização e o tráfego do Serviço Azure para percorrer a mesma rota. Com o Service Endpoints, pode otimizá-lo uma vez que os pacotes fluem diretamente da sua rede virtual para o serviço na rede de espinha dorsal Azure.

![Pontos finais de serviço da VNet](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>IPs Reservados

Outra opção é fornecer [IPs reservados](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) para os seus VMs e adicionar esses endereços IP VM específicos nas definições de firewall do servidor. Ao atribuir IPs reservados, poupa o trabalho de ter de atualizar as regras de firewall com a alteração de endereços IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Que porta faço a ligação à Base de Dados SQL

Porto 1433. A SQL Database comunica sobre esta porta. Para se conectar a partir de uma rede corporativa, você tem que adicionar uma regra de saída nas definições de firewall da sua organização. Como orientação, evite expor a porta 1433 fora da fronteira de Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Como posso monitorizar e regular a atividade no meu servidor e base de dados na Base de Dados SQL

#### <a name="sql-database-auditing"></a>Auditoria de Base de Dados SQL

Com a BASE de Dados SQL, pode ligar a AUDITORIA ON para rastrear eventos de base de dados. [SQL Base de Dados Auditando](../../azure-sql/database/auditing-overview.md) registos de eventos de base de dados e escreve-os num ficheiro de registo de auditoria na sua Conta de Armazenamento Azure. A auditoria é especialmente útil se pretender obter informações sobre possíveis violações de segurança e políticas, manter a conformidade regulamentar, etc. Permite-lhe definir e configurar certas categorias de eventos que acha que precisam de ser auditados e com base nisso pode obter relatórios pré-configurados e um dashboard para obter uma visão geral dos eventos que ocorrem na sua base de dados. Pode aplicar estas políticas de auditoria ao nível da base de dados ou ao nível do servidor. Um guia sobre como ativar a auditoria para o seu servidor/base de dados, consulte: [Ative SQL Database Auditing](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Deteção de ameaças

Com a [deteção de ameaças,](threat-detection-configure.md)obtém-se a capacidade de agir sobre violações de segurança ou políticas descobertas pela Auditoria com muita facilidade. Não precisa de ser perito em segurança para lidar com potenciais ameaças ou violações no seu sistema. A deteção de ameaças também tem algumas capacidades incorporadas, como a deteção de injeção SQL. A Injeção SQL é uma tentativa de alterar ou comprometer os dados e uma forma bastante comum de atacar uma aplicação de base de dados em geral. A deteção de ameaças executa vários conjuntos de algoritmos que detetam potenciais vulnerabilidades e ataques de injeção de SQL, bem como padrões anómalos de acesso à base de dados (como o acesso a partir de um local incomum ou por um principal desconhecido). Os oficiais de segurança ou outros administradores designados recebem uma notificação por e-mail se for detetada uma ameaça na base de dados. Cada notificação fornece detalhes da atividade suspeita e recomendações sobre como investigar e mitigar a ameaça. Para aprender a ativar a deteção de ameaças, consulte: [Ative a deteção de ameaças](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Como protejo os meus dados em geral na Base de Dados SQL

A encriptação fornece um mecanismo forte para proteger e proteger os seus dados sensíveis de intrusos. Os seus dados encriptados não são de utilidade para o intruso sem a chave de desencriptação. Assim, adiciona uma camada extra de proteção em cima das camadas de segurança existentes construídas na Base de Dados SQL. Existem dois aspetos para proteger os seus dados na Base de Dados SQL:

- Os seus dados que estão em repouso nos ficheiros de dados e registo
- Os seus dados que estão a bordo

Na Base de Dados SQL, por padrão, os seus dados em repouso nos dados e ficheiros de registo no subsistema de armazenamento são completamente e sempre encriptados através da [Encriptação de Dados Transparente [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). As suas cópias de segurança também estão encriptadas. Com o TDE não são necessárias alterações no seu lado da aplicação que esteja a aceder a estes dados. A encriptação e a desencriptação acontecem de forma transparente; daí o nome.
Para proteger os seus dados sensíveis em voo e em repouso, a SQL Database fornece uma funcionalidade chamada [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE é uma forma de encriptação do lado do cliente que encripta colunas sensíveis na sua base de dados (por isso estão em cifração para administradores de bases de dados e utilizadores não autorizados). O servidor recebe os dados encriptados para começar. A chave para Always Encrypted também é armazenada no lado do cliente, pelo que apenas os clientes autorizados podem desencriptar as colunas sensíveis. O servidor e os administradores de dados não conseguem ver os dados sensíveis uma vez que as chaves de encriptação são armazenadas no cliente. A AE encripta colunas sensíveis na mesa de ponta a ponta, de clientes não autorizados ao disco físico. A AE suporta comparações de igualdade hoje em dia, para que os DBAs possam continuar a consultar colunas encriptadas como parte dos seus comandos SQL. Sempre encriptado pode ser usado com uma variedade de opções de loja chave, tais como [Azure Key Vault,](always-encrypted-azure-key-vault-configure.md)loja de certificados Windows e módulos de segurança de hardware locais.

|**Características**|**Always Encrypted**|**Encriptação de Dados Transparente**|
|---|---|---|
|**Extensão de encriptação**|De ponta a ponta|Dados de repouso|
|**O servidor pode aceder a dados sensíveis**|Não|Sim, já que a encriptação é para os dados em repouso|
|**Operações T-SQL permitidas**|Comparação da igualdade|Toda a área de superfície T-SQL está disponível|
|**Alterações de aplicação necessárias para usar a funcionalidade**|Mínimo|Muito mínimo|
|**Granularidade de encriptação**|Nível de coluna|Ao nível da base de dados|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Como posso limitar o acesso a dados sensíveis na minha base de dados

Cada aplicação tem um certo número de dados sensíveis na base de dados que precisa de ser protegido de ser visível para todos. Alguns funcionários dentro da organização precisam de ver estes dados, no entanto outros não devem ser capazes de ver estes dados. Um exemplo são os salários dos empregados. Um gestor precisaria de ter acesso à informação salarial para os seus relatórios diretos, no entanto, os membros da equipa individuais não deveriam ter acesso à informação salarial dos seus pares. Outro cenário são os desenvolvedores de dados que podem estar a interagir com dados sensíveis durante fases de desenvolvimento ou testes, por exemplo, SSNs de clientes. Esta informação não precisa de ser exposta ao desenvolvedor. Nesses casos, os seus dados sensíveis ou precisam de ser mascarados ou não ser expostos de todo. A SQL Database oferece duas abordagens para impedir que utilizadores não autorizados possam visualizar dados sensíveis:

[A Dynamic Data Masking](dynamic-data-masking-overview.md) é uma funcionalidade de mascaramento de dados que permite limitar a exposição de dados sensíveis, mascarando-os a utilizadores não privilegiados na camada da aplicação. Você define uma regra de mascaramento que pode criar um padrão de mascaramento (por exemplo, para mostrar apenas os últimos quatro dígitos de um ID SSN nacional: XXX-XX-0000 e marcar a maior parte como Xs) e identificar quais os utilizadores que devem ser excluídos da regra de mascaramento. A máscara acontece no voo e existem várias funções de máscara disponível para várias categorias de dados. A máscara dinâmica de dados permite-lhe detetar automaticamente dados sensíveis na sua base de dados e aplicar-lhe máscaras.

[A segurança do nível da linha](/sql/relational-databases/security/row-level-security) permite-lhe controlar o acesso ao nível da linha. Ou seja, certas linhas numa tabela de bases de dados com base no utilizador que executa a consulta (membro do grupo ou contexto de execução) são ocultadas. A restrição de acesso é feita no nível de base de dados em vez de num nível de aplicação, para simplificar a lógica da sua aplicação. Começa-se por criar um predicado de filtro, filtrando linhas que não estão expostas e a política de segurança definindo a seguir quem tem acesso a estas linhas. Finalmente, o utilizador final executa a sua consulta e, dependendo do privilégio do utilizador, ou vê essas linhas restritas ou não consegue vê-las.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Como posso gerir chaves de encriptação na nuvem

Existem opções de gestão chave tanto para a Encriptação Sempre Encriptada (encriptação do lado do cliente) como para a Encriptação de Dados Transparente (encriptação em repouso). Recomenda-se que rode regularmente as chaves de encriptação. A frequência de rotação deve alinhar-se tanto com os regulamentos da organização interna como com os requisitos de conformidade.

#### <a name="transparent-data-encryption-tde"></a>Encriptação de Dados Transparente (TDE)

Existe uma hierarquia de duas chaves no TDE – os dados em cada base de dados de utilizadores são encriptados por uma chave de encriptação única da base de dados AES-256 (DEK), que por sua vez é encriptada por uma chave principal rsa 2048 única para servidores. A chave principal pode ser gerida também:

- Automaticamente pela plataforma - SQL Database.
- Ou ao usar [o Azure Key Vault](always-encrypted-azure-key-vault-configure.md) como loja de chaves.

Por predefinição, a chave principal para encriptação de dados transparentes é gerida pelo serviço sql Database para conveniência. Se a sua organização quiser controlar a chave principal, existe uma opção para usar o Cofre da Chave Azure(sempre encriptado-azure-key-vault-configure.md) como a loja-chave. Ao utilizar o Azure Key Vault, a sua organização assume o controlo sobre os controlos de provisão, rotação e permissão. [A rotação ou a mudança do tipo de tecla master TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápida, uma vez que apenas reencripta o DEK. Para organizações com separação de funções entre segurança e gestão de dados, um administrador de segurança poderia fornecer o material chave para a chave principal TDE no Cofre chave Azure e fornecer um identificador de chave Azure Key Vault ao administrador da base de dados para usar para encriptação em repouso em um servidor. O Key Vault foi concebido de forma a que a Microsoft não veja ou extraa chaves de encriptação. Você também tem uma gestão centralizada de chaves para a sua organização.

#### <a name="always-encrypted"></a>Sempre Encriptado

Há também uma [hierarquia de duas chaves](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) em Always Encrypted - uma coluna de dados sensíveis é encriptada por uma chave de encriptação de colunas AES 256 (CEK), que por sua vez é encriptada por uma chave principal de coluna (CMK). Os motoristas de clientes fornecidos para Sempre Encriptados não têm limitações no comprimento dos CMKs. O valor encriptado do CEK é armazenado na base de dados, e o CMK está armazenado numa loja de chaves fidedigna, como Windows Certificate Store, Azure Key Vault ou um módulo de segurança de hardware.

- Tanto o [CEK como](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) o CMK podem ser rodados.
- A rotação CEK é um tamanho de operação de dados e pode ser intensiva em tempo, dependendo do tamanho das tabelas que contêm as colunas encriptadas. Por conseguinte, é prudente planear as rotações do CEK em conformidade.
- A rotação cmk, no entanto, não interfere com o desempenho da base de dados, e pode ser feito com funções separadas.

O diagrama seguinte mostra as opções de loja chave para as teclas principais da coluna em Always Encrypted

![Sempre encriptado fornecedores de lojas CMK](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Como posso otimizar e proteger o tráfego entre a minha organização e a BASE de Dados SQL

O tráfego de rede entre a sua organização e a SQL Database seria geralmente encaminhado para a rede pública. No entanto, se optar por otimizar este caminho e torná-lo mais seguro, pode olhar para o Azure ExpressRoute. O ExpressRoute permite-lhe essencialmente estender a sua rede corporativa à plataforma Azure através de uma ligação privada. Ao fazê-lo, não se passa pela Internet pública. Você também obtém maior segurança, fiabilidade e otimização de encaminhamento que se traduz em latências de rede mais baixas e velocidades muito mais rápidas do que você normalmente experimentaria através da internet pública. Se está a planear transferir uma parte significativa de dados entre a sua organização e a Azure, a utilização do ExpressRoute pode trazer benefícios de custos. Pode escolher entre três modelos de conectividade diferentes para a ligação da sua organização ao Azure:

- [Co-localização de troca de nuvens](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Conectividade any-to-any](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Ponto a Ponto](../../expressroute/expressroute-connectivity-models.md#Ethernet)

O ExpressRoute também permite que rebente até 2x o limite de largura de banda que compra sem custos adicionais. Também é possível configurar a conectividade da região transversal utilizando o ExpressRoute. Para ver uma lista de fornecedores de conectividade ExpressRoute, consulte: [ExpressRoute Partners e Peering Locations](../../expressroute/expressroute-locations.md). Os seguintes artigos descrevem a Rota Expressa com mais pormenor:

- [Introdução na Rota Expressa](../../expressroute/expressroute-introduction.md)
- [Pré-requisitos](../../expressroute/expressroute-prerequisites.md)
- [Fluxos de trabalho](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>A SQL Database está em conformidade com quaisquer requisitos regulamentares, e como é que isso ajuda com o cumprimento da minha própria organização

A BASE de Dados SQL está em conformidade com uma gama de compliancies regulamentares. Para ver o mais recente conjunto de compliancies que foram cumpridas pela SQL Database, visite o [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e registe as compliancies que são importantes para a sua organização para ver se a SQL Database está incluída nos serviços de Azure em conformidade. É importante notar que, embora a SQL Database possa ser certificada como um serviço conforme, ajuda no cumprimento do serviço da sua organização, mas não o garante automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitorização e manutenção de bases de dados inteligentes após migração

Depois de migrar a sua base de dados para a Base de Dados SQL, vai querer monitorizar a sua base de dados (por exemplo, verificar como é a utilização do recurso ou verificação de DBCC) e efetuar uma manutenção regular (por exemplo, reconstruir ou reorganizar índices, estatísticas, etc.). Felizmente, a SQL Database é Inteligente no sentido em que utiliza as tendências históricas e as métricas e estatísticas registadas para ajudá-lo a monitorizar e manter a sua base de dados de forma otimizada sempre. Em alguns casos, o Azure SQL Database pode executar automaticamente tarefas de manutenção dependendo da configuração da sua configuração. Existem três facetas para monitorizar a sua base de dados na Base de Dados SQL:

- Monitorização e otimização de desempenho.
- Otimização de segurança.
- Otimização de custos.

### <a name="performance-monitoring-and-optimization"></a>Monitorização e otimização de desempenho

Com o Questionry Performance Insights, pode obter recomendações personalizadas para a carga de trabalho da sua base de dados para que as suas aplicações possam continuar a funcionar a um nível ideal - sempre. Também pode configurar para que estas recomendações seja aplicada automaticamente e não tenha que se preocupar em executar tarefas de manutenção. Com o SQL Database Advisor, pode implementar automaticamente recomendações de índice com base na sua carga de trabalho - isto chama-se Auto-Sintonização. As recomendações evoluem à medida que a sua aplicação muda de carga de trabalho para lhe fornecer as sugestões mais relevantes. Você também tem a opção de rever manualmente estas recomendações e aplicá-las a seu critério.  

### <a name="security-optimization"></a>Otimização de segurança

A SQL Database fornece recomendações de segurança accibilizáveis para ajudá-lo a proteger os seus dados e a deteção de ameaças para identificar e investigar atividades de base de dados suspeitas que possam representar um potencial fio para a base de dados. [A avaliação de vulnerabilidades](sql-vulnerability-assessment.md) é um serviço de pesquisa e reporte de bases de dados que lhe permite monitorizar o estado de segurança das suas bases de dados em escala e identificar riscos de segurança e derivar de uma linha de segurança definida por si. Após cada digitalização, é fornecida uma lista personalizada de etapas acccáveis e scripts de reparação, bem como um relatório de avaliação que pode ser usado para ajudar a cumprir os requisitos de conformidade.

Com o Azure Security Center, identifica as recomendações de segurança em todo o tabuleiro e aplica-as com um simples clique.

### <a name="cost-optimization"></a>Otimização de custos

A plataforma Azure SQL analisa o histórico de utilização através das bases de dados num servidor para avaliar e recomendar opções de otimização de custos para si. Esta análise geralmente leva uma quinzena para analisar e construir recomendações accitivas. Piscina elástica é uma dessas opções. A recomendação aparece no portal como um banner:

![recomendações de piscina elástica](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Também pode ver esta análise na secção "Advisor":

![elástico pool recomendações-conselheiro](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Como monitorar o desempenho e a utilização de recursos na Base de Dados SQL

Na Base de Dados SQL pode aproveitar as ideias inteligentes da plataforma para monitorizar o desempenho e sintonizar em conformidade. Pode monitorizar a utilização de desempenho e recursos na Base de Dados SQL utilizando os seguintes métodos:

#### <a name="azure-portal"></a>Portal do Azure

O portal Azure mostra a utilização de uma base de dados selecionando a base de dados e clicando no gráfico no painel de visão geral. Pode modificar o gráfico para mostrar várias métricas, incluindo percentagem de CPU, percentagem de DTU, percentagem de IO de dados, percentagem de sessões e percentagem de tamanho da base de dados.

![Gráfico de monitorização](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Gráfico de monitorização2](./media/manage-data-after-migrating-to-database/chart.png)

A partir deste gráfico, também pode configurar alertas por recurso. Estes alertas permitem-lhe responder às condições de recursos com um e-mail, escrever para um ponto final HTTPS/HTTP ou realizar uma ação. Para mais informações, consulte [Criar alertas.](alerts-insights-configure-portal.md)

#### <a name="dynamic-management-views"></a>Vistas de gestão dinâmicas (DMV)

Pode consultar [a](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) sys.dm_db_resource_stats vista dinâmica de gestão para devolver o histórico de estatísticas de consumo de recursos da última hora e a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) visão do catálogo do sistema para devolver a história dos últimos 14 dias.

#### <a name="query-performance-insight"></a>Query Performance Insight

[O Insight de Desempenho](query-performance-insight-use.md) de Consulta permite-lhe ver um histórico das principais consultas que consomem recursos e consultas de longa duração para uma base de dados específica. Pode identificar rapidamente consultas TOP através da utilização, duração e frequência de execução. Pode rastrear consultas e detetar regressão. Esta funcionalidade requer que a [Loja de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) seja ativada e ativa para a base de dados.

![Query Performance Insight](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (Pré-visualização) em registos do Monitor Azure

[Os registos do Azure Monitor](../../azure-monitor/insights/azure-sql.md) permitem recolher e visualizar as principais métricas de desempenho da Base de Dados Azure SQL, suportando até 150.000 bases de dados e 5.000 piscinas elásticas SQL por espaço de trabalho. Pode usá-lo para monitorizar e receber notificações. Você pode monitorizar a Base de Dados SQL e métricas elásticas de piscina em várias subscrições Azure e piscinas elásticas e pode ser usado para identificar problemas em cada camada de uma pilha de aplicações.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou a notar problemas de desempenho: Como é que a minha metodologia de resolução de problemas da BASE de Dados SQL difere do SQL Server

Uma grande parte das técnicas de resolução de problemas que usaria para diagnosticar questões de consulta e desempenho da base de dados permanece a mesma. Afinal, o mesmo motor de base de dados alimenta a nuvem. No entanto, a plataforma - Azure SQL Database construiu em 'inteligência'. Pode ajudá-lo a resolver problemas e diagnosticar problemas de desempenho ainda mais facilmente. Também pode executar algumas destas ações corretivas em seu nome e, em alguns casos, corrigi-las proativamente - automaticamente.

A sua abordagem para resolver problemas de desempenho pode beneficiar significativamente utilizando funcionalidades inteligentes como [o Query Performance Insight (QPI)](query-performance-insight-use.md) e o [Database Advisor](database-advisor-implement-performance-recommendations.md) em conjunto e, por isso, a diferença de metodologia difere nesse aspeto – já não precisa de fazer o trabalho manual de moer os detalhes essenciais que podem ajudá-lo a resolver o problema em questão. A plataforma faz o trabalho duro para si. Um exemplo disso é o QPI. Com o QPI, você pode perfurar todo o caminho até o nível de consulta e olhar para as tendências históricas e descobrir quando exatamente a consulta regrediu. O Database Advisor dá-lhe recomendações sobre coisas que podem ajudá-lo a melhorar o seu desempenho geral em geral como - índices em falta, índices de queda, parametrização das suas consultas, etc.

Com a resolução de problemas de desempenho, é importante identificar se é apenas a aplicação ou a base de dados que a apoia, que está a afetar o desempenho da sua aplicação. Muitas vezes, o problema de desempenho reside na camada de aplicação. Pode ser a arquitetura ou o padrão de acesso a dados. Por exemplo, considere que tem uma aplicação de chatty que é sensível à latência da rede. Neste caso, a sua aplicação sofre porque haveria muitos pedidos curtos indo e vindo ("chatty") entre a aplicação e o servidor e numa rede congestionada, estas velhagens acumulam-se rapidamente. Para melhorar o desempenho neste caso, pode utilizar [As Consultas de Lote](performance-guidance.md#batch-queries). A utilização de lotes ajuda-o tremendamente porque agora os seus pedidos são processados num lote; assim, ajudá-lo a reduzir a latência de ida e volta e melhorar o desempenho da sua aplicação.

Além disso, se notar uma degradação no desempenho geral da sua base de dados, pode monitorizar os [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) pontos de vista dinâmicos de gestão para compreender o consumo de CPU, IO e memória. O teu desempenho pode ter impacto porque a tua base de dados está esfomeada de recursos. Pode ser que precise de alterar o tamanho do cálculo e/ou o nível de serviço com base nas crescentes e diminuídas exigências de carga de trabalho.

Para obter um conjunto abrangente de recomendações para afinar problemas de desempenho, consulte: [Afinar a sua base de dados](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Como posso garantir que estou a usar o nível de serviço adequado e o tamanho do cálculo

A SQL Database oferece vários níveis de serviço Básico, Standard e Premium. Cada nível de serviço obtém um desempenho previsível garantido ligado a esse nível de serviço. Dependendo da sua carga de trabalho, pode ter explosões de atividade onde a sua utilização de recursos pode atingir o teto do tamanho atual do cálculo em que se encontra. Nestes casos, é útil começar primeiro por avaliar se qualquer sintonização pode ajudar (por exemplo, adicionar ou alterar um índice, etc.). Se ainda encontrar problemas de limite, considere mudar-se para um nível de serviço mais elevado ou tamanho de computação.

|**Escalão de serviço**|**Cenários de casos de uso comum**|
|---|---|
|**Básica**|Aplicações com um punhado de utilizadores e uma base de dados que não tem elevados requisitos de concordância, escala e desempenho. |
|**Standard**|Aplicações com uma considerável concordância, escala e requisitos de desempenho associados a exigências de IO baixas a médias. |
|**Premium**|Aplicações com muitos utilizadores simultâneos, CPU/memória alta e elevadas exigências de IO. Altas aplicações sensíveis à concência, alta produção e latência podem alavancar o nível Premium. |
|||

Para se certificar de que está no tamanho certo do cálculo, pode monitorizar a sua consulta e o consumo de recursos de base de dados através de uma das formas acima mencionadas em "Como monitorizar o desempenho e a utilização de recursos na Base de Dados SQL". Caso descubra que as suas consultas/bases de dados estão constantemente a aquecer no CPU/Memória, etc. pode considerar a expansão para um tamanho de computação mais elevado. Da mesma forma, se notar que mesmo durante as suas horas de ponta, parece não usar tanto os recursos; considerar a escala para baixo do tamanho atual do cálculo.

Se tiver um padrão de aplicação SaaS ou um cenário de consolidação de bases de dados, considere usar uma piscina elástica para otimização de custos. A piscina elástica é uma ótima maneira de alcançar a consolidação da base de dados e a otimização de custos. Para ler mais sobre a gestão de várias bases de dados utilizando piscina elástica, consulte: [Gerir piscinas e bases de dados.](elastic-pool-manage.md#azure-portal)

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Quantas vezes preciso de verificar a integridade da base de dados para a minha base de dados?

A SQL Database utiliza algumas técnicas inteligentes que lhe permitem lidar com determinadas classes de corrupção de dados automaticamente e sem qualquer perda de dados. Estas técnicas são integradas ao serviço e são alavancadas pelo serviço quando surge a necessidade. Regularmente, as cópias de segurança da sua base de dados em todo o serviço são testadas restaurando-as e executando o DBCC CHECKDB nele. Em caso de problemas, a SQL Database aborda-os proativamente. [A reparação automática de páginas](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) é alavancada para corrigir páginas que são corruptas ou têm problemas de integridade dos dados. As páginas de base de dados são sempre verificadas com a definição padrão do CHECKSUM que verifica a integridade da página. A SQL Database monitoriza e analisa proativamente a integridade dos dados da sua base de dados e, se surgirem problemas, aborda-os com a maior prioridade. Além destes, pode optar por realizar opcionalmente os seus próprios controlos de integridade à sua vontade.  Para mais informações, consulte [a Integridade dos Dados na Base de Dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Movimento de dados após migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Como exportar e importar dados como ficheiros BACPAC da Base de Dados SQL utilizando o portal Azure

- **Exportação**: Pode exportar a sua base de dados na Base de Dados Azure SQL como um ficheiro BACPAC a partir do portal Azure

   ![exportação de bases de dados](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Importação**: Também pode importar dados como um ficheiro BACPAC na sua base de dados na Base de Dados Azure SQL utilizando o portal Azure.

   ![importação de base de dados](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Como posso sincronizar dados entre a Base de Dados SQL e o SQL Server

Tem várias formas de o conseguir:

- **[Data Sync](sql-data-sync-data-sql-server-sql-database.md)** – Esta funcionalidade ajuda-o a sincronizar os dados bi-direccionalmente entre várias bases de dados do SQL Server e a BASE de Dados SQL. Para sincronizar com as bases de dados do SQL Server, é necessário instalar e configurar o agente de sincronização num computador local ou numa máquina virtual e abrir a porta TCP de saída 1433.
- **[Replicação de transações](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – Com a replicação de transações pode sincronizar os seus dados de uma base de dados do SQL Server para a Base de Dados Azure SQL, sendo a instância do SQL Server o editor e a Base de Dados Azure SQL o assinante. Por enquanto, apenas esta configuração é suportada. Para obter mais informações sobre como migrar os seus dados de uma base de dados do SQL Server para Azure SQL com o mínimo de tempo de inatividade, consulte: [Use a replicação de transações](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a Base de Dados SQL.](sql-database-paas-overview.md)