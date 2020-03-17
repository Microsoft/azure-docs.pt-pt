---
title: Gerir bases de dados individuais e reunidas após a migração
description: Saiba como gerir a sua base de dados após a migração para a Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: ebb512fee0186bed3cc7f49f0525dac43e57da3a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256188"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Novo DBA na nuvem – Gerir as suas bases de dados individuais e reunidas na Base de Dados Azure SQL

Passar do ambiente tradicional auto-gerido e auto-controlado para um ambiente PaaS pode parecer um pouco esmagador no início. Como desenvolvedor de aplicações ou DBA, gostaria de saber as capacidades centrais da plataforma que o ajudaria a manter a sua aplicação disponível, performante, segura e resiliente - sempre. Este artigo pretende fazer exatamente isso. O artigo organiza sucintamente recursos e dá-lhe algumas orientações sobre como utilizar melhor as principais capacidades da Base de Dados SQL com bases de dados únicas e reunidas para gerir e manter a sua aplicação a funcionar de forma eficiente e obter os melhores resultados na nuvem. O público típico para este artigo seria aqueles que:

- Estão a avaliar a migração das suas aplicações para a Base de Dados Azure SQL – Modernização da sua aplicação( s).
- Estão em processo de migração da sua(s) aplicação(s) – Cenário de migração em curso.
- Concluíram recentemente a migração para o Azure SQL DB – Novo DBA na nuvem.

Este artigo discute algumas das características fundamentais da Base de Dados Azure SQL como uma plataforma que você pode facilmente alavancar quando trabalha com bases de dados únicas e bases de dados em piscinas elásticas. São os seguintes:

- Monitorizar base de dados utilizando o portal Azure
- Continuidade de negócio e recuperação após desastre (BCDR)
- Segurança e conformidade
- Monitorização e manutenção de bases de dados inteligentes
- Movimento de dados

> [!NOTE]
> Este artigo aplica-se às seguintes opções de implantação na Base de Dados Azure SQL: bases de dados únicas e piscinas elásticas. Não se aplica à opção de implementação de instânciagerida na Base de Dados SQL.

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorizar bases de dados com o portal do Azure

No [portal Azure,](https://portal.azure.com/)pode monitorizar a utilização de uma base de dados individual, selecionando a sua base de dados e clicando na tabela **de Monitorização.** É apresentada a janela **Métricas** que pode alterar ao clicar no botão **Editar gráfico**. Adicione as métricas seguintes:

- Percentagem de CPU
- Percentagem de DTU
- Percentagem de ES de Dados
- Percentagem de tamanho da Base de Dados

Depois de adicionar estas métricas, pode continuar a vê-las na tabela **de Monitorização** com mais informações sobre a janela **Métrica.** As quatro métricas mostram a percentagem de utilização média relativa à **DTU** da base de dados. Consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e os [artigos de modelode compra baseados em VCore](sql-database-service-tiers-vcore.md) para obter mais informações sobre os níveis de serviço.  

![Monitorização da camada de serviços do desempenho da base de dados.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Também pode configurar alertas para as métricas de desempenho. Clique no botão **Adicionar alerta** na janela **Métricas**. Siga o assistente para configurar o alerta. Tem a opção de alertar se as métricas excederem um determinado limiar ou se a métrica descer abaixo de um determinado limiar.

Por exemplo, se espera que a carga de trabalho na sua base de dados aumente, pode optar por configurar um alerta por e-mail sempre que a base de dados atingir 80% em qualquer uma das métricas de desempenho. Pode usar isto como um aviso precoce para descobrir quando poderá ter de mudar para o tamanho da computação mais alta.

As métricas de desempenho também podem ajudá-lo a determinar se você é capaz de desvalorizar para um tamanho de computação mais baixo. Parta do princípio de que está a utilizar uma base de dados Standard S2 e que todas as métricas de desempenho mostram que, em média, a base de dados não utiliza mais de 10% em qualquer momento. É provável que a base de dados funcione corretamente no Standard S1. No entanto, esteja atento a cargas de trabalho que aumentam ou flutuam antes de tomar a decisão de passar para um tamanho de computação mais baixo.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Continuidade de negócio e recuperação após desastre (BCDR)

A continuidade do negócio e as capacidades de recuperação de desastres permitem-lhe continuar o seu negócio, como de costume, em caso de desastre. O desastre pode ser um evento de nível de base de dados (por exemplo, alguém erroneamente deixa cair uma tabela crucial) ou um evento de nível de data-center (catástrofe regional, por exemplo, um tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como crio e geria backups na Base de Dados SQL

Não se criam backups no Azure SQL DB e isso é porque não é preciso. A Base de Dados SQL faz automaticamente cópias de dados para si, pelo que já não se deve preocupar em agendar, tirar e gerir cópias de segurança. A plataforma recebe uma cópia de segurança completa todas as semanas, backup diferencial a cada poucas horas e uma cópia de segurança a cada 5 minutos para garantir que a recuperação do desastre é eficiente, e a perda de dados é mínima. O primeiro backup completo acontece assim que criar uma base de dados. Estes backups estão disponíveis para si durante um determinado período chamado "Período de Retenção" e variam de acordo com o nível de serviço que escolher. A Base de Dados SQL fornece-lhe a capacidade de restaurar a qualquer ponto do tempo neste período de retenção utilizando [o Ponto de Recuperação do Tempo (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Camada de serviços|Período de retenção em dias|
|---|:---:|
|Básica|7|
|Standard|35|
|Premium|35|
|||

Além disso, a função [de Retenção a Longo Prazo (LTR) permite-lhe](sql-database-long-term-retention.md) manter os seus ficheiros de backup por um período muito mais longo especificamente, por um período máximo de 10 anos, e restaurar os dados destas cópias de segurança em qualquer ponto desse período. Além disso, as cópias de segurança da base de dados são mantidas em armazenamento geo-replicado para garantir a resiliência da catástrofe regional. Também pode restaurar estes backups em qualquer região de Azure em qualquer momento dentro do período de retenção. Ver [visão geral da continuidade do negócio.](sql-database-business-continuity.md)

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Como posso garantir a continuidade do negócio em caso de desastre ao nível de datacenter ou catástrofe regional

Como as cópias de segurança da sua base de dados são armazenadas em armazenamento geo-replicado para garantir que, em caso de desastre regional, você pode restaurar o backup para outra região de Azure. Isto chama-se geo-restauro. O RPO (Objetivo do Ponto de Recuperação) para este é geralmente < 1 hora e o ERT (Tempo estimado de recuperação – poucos minutos a horas).

Para bases de dados críticas de missão, o Azure SQL DB oferece uma geo-replicação ativa. O que isto essencialmente faz é criar uma cópia secundária geo-replicada da sua base de dados original noutra região. Por exemplo, se a sua base de dados estiver inicialmente alojada na região de Azure West US e quiser resiliência regional de desastres. Criaria uma réplica geoactiva ativa da base de dados nos EUA ocidentais para dizer Leste dos EUA. Quando a calamidade atingir os EUA Ocidentais, podes falhar na região leste dos EUA. Configurá-los num grupo de auto-failover é ainda melhor porque isto garante que a base de dados falha automaticamente no secundário nos EUA Oriental em caso de desastre. O RPO para isto é < 5 segundos e o ERT < 30 segundos.

Se um grupo de falha automática não estiver configurado, então a sua aplicação precisa de monitorizar ativamente para uma catástrofe e iniciar uma falha no secundário. Pode criar até 4 georéplicas ativas em diferentes regiões do Azure. Fica ainda melhor. Também pode aceder a estas georéplicas ativas secundárias para acesso apenas a leitura. Isto vem muito útil para reduzir a latência para um cenário de aplicação geo-distribuído.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Como é que o meu plano de recuperação de desastres muda de instalações para base de dados SQL

Em resumo, a configuração tradicional do Servidor SQL no local exigia que gerisse ativamente a sua Disponibilidade utilizando funcionalidades como Clustering Failover, Database Mirroring, Replication transaction ou Log Shipping e manter e gerir backups para garantir que as cópias de segurança Continuidade do Negócio. Com a Base de Dados SQL, a plataforma gere-as para si, para que possa focar-se no desenvolvimento e otimização da sua aplicação de base de dados e não se preocupe tanto com a gestão de desastres. Pode ter planos de recuperação de backup e desastres configurados e trabalhar com apenas alguns cliques no portal Azure (ou alguns comandos usando as APIs PowerShell).

Para saber mais sobre a recuperação de desastres, consulte: [Azure SQL Db Disaster Recovery 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Segurança e conformidade

A Base de Dados SQL leva a Segurança e a Privacidade muito a sério. A segurança dentro da Base de Dados SQL está disponível ao nível da base de dados e ao nível da plataforma e é melhor compreendida quando categorizada em várias camadas. Em cada camada você pode controlar e fornecer a segurança ideal para a sua aplicação. As camadas são:

- Identidade e autenticação[(autenticação SQL e autenticação Azure Ative Directory [AAD]).](sql-database-manage-logins.md)
- Atividade de monitorização[(Auditoria](sql-database-auditing.md) e [deteção de ameaças).](sql-database-threat-detection.md)
- Proteger dados reais[(Encriptação de Dados Transparentes [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Sempre Encriptado [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Controlar o acesso a dados sensíveis e privilegiados[(segurança de nível de linha](/sql/relational-databases/security/row-level-security) e [máscara de dados dinâmicos).](/sql/relational-databases/security/dynamic-data-masking)

[O Azure Security Center](https://azure.microsoft.com/services/security-center/) oferece uma gestão de segurança centralizada através de cargas de trabalho em funcionamento em Azure, no local e em outras nuvens. Pode ver se a proteção essencial da Base de Dados SQL, como [auditoria](sql-database-auditing.md) e [encriptação transparente de dados [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) estão configuradas em todos os recursos e criar políticas baseadas nos seus próprios requisitos.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Que métodos de autenticação do utilizador são oferecidos na Base de Dados SQL

Existem dois métodos de autenticação oferecidos na Base de Dados SQL:

- [Autenticação de diretório ativo Azure](sql-database-aad-authentication.md)
- [Autenticação SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

A autenticação tradicional das janelas não é suportada. Azure Ative Directory (AD) é um serviço centralizado de gestão de identidade e acesso. Com isto pode fornecer convenientemente um Acesso único de acesso de sign-on (SSO) a todo o pessoal da sua organização. O que isto significa é que as credenciais são partilhadas em todos os serviços azure para uma autenticação mais simples. O AAD suporta [o MFA (Multi Fator Authentication)](sql-database-ssms-mfa-authentication.md) e com [alguns cliques](../active-directory/hybrid/how-to-connect-install-express.md) a AAD pode ser integrada com o Diretório Ativo do Servidor do Windows. A autenticação SQL funciona exatamente como tem usado no passado. Fornece um nome de utilizador/palavra-passe e pode autenticar os utilizadores em qualquer base de dados de um determinado servidor de Base de Dados SQL. Isto também permite que a Base de Dados SQL e o SQL Data Warehouse ofereçam contas de autenticação multifactor e de utilizadores de hóspedes dentro de um domínio DaD Azure. Se já tem um Diretório Ativo no local, pode federar o diretório com o Azure Ative Directory para estender o seu diretório ao Azure.

|**Se...**|**Base de Dados SQL / Armazém de Dados SQL**|
|---|---|
|Prefira não utilizar o Diretório Ativo Azure (AD) em Azure|Utilizar [a autenticação SQL](sql-database-security-overview.md)|
|AD usado no SQL Server no local|[Federate AD com Azure AD,](../active-directory/hybrid/whatis-hybrid-identity.md)e use autenticação Azure AD. Com isto, pode utilizar o Single Sign-On.|
|Necessidade de impor a autenticação multifactor (MFA)|Exija mFA como uma política através do [Microsoft Conditional Access](sql-database-conditional-access.md), e use a [autenticação Azure AD Universal com suporte MFA](sql-database-ssms-mfa-authentication.md).|
|Ter contas de hóspedes a partir de contas da Microsoft (live.com, outlook.com) ou outros domínios (gmail.com)|Utilize [a autenticação Universal Azure AD](sql-database-ssms-mfa-authentication.md) na Base de Dados/Armazém de Dados SQL, que aproveita a [Colaboração Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Estão ligados ao Windows usando as suas credenciais de AD Azure a partir de um domínio federado|Utilize [a autenticação integrada Azure AD](sql-database-aad-authentication-configure.md).|
|Estão registados no Windows usando credenciais de um domínio não federado com Azure|Utilize [a autenticação integrada Azure AD](sql-database-aad-authentication-configure.md).|
|Disponha de serviços de nível médio que precisam de se ligar à Base de Dados SQL ou ao SQL Data Warehouse|Utilize [a autenticação integrada Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Como posso limitar ou controlar o acesso à conectividade à minha base de dados

Existem múltiplas técnicas à sua disposição que pode usar para obter uma organização de conectividade ideal para a sua aplicação.

- Regras da Firewall
- Pontos finais do serviço VNet
- IPs Reservados

#### <a name="firewall"></a>Firewall

Uma firewall impede o acesso ao seu servidor a partir de uma entidade externa, permitindo apenas acesso a entidades específicas ao seu servidor de Base de Dados SQL. Por predefinição, todas as ligações e bases de dados dentro do servidor de base de dados SQL são proibidas, exceto ligações provenientes de outros Serviços Azure. Com uma regra de firewall, pode abrir acesso ao seu servidor apenas a entidades (por exemplo, uma máquina de desenvolvimento) que aprove, permitindo o endereço IP desse computador através da firewall. Também lhe permite especificar uma gama de IPs que pretende permitir o acesso ao servidor de base de dados SQL. Por exemplo, os endereços IP da máquina de desenvolvedor na sua organização podem ser adicionados de uma só vez, especificando um intervalo na página de definições de Firewall.

Pode criar regras de firewall ao nível do servidor ou ao nível da base de dados. As regras de firewall IP de nível de servidor podem ser criadas usando o portal Azure ou com SSMS. Para saber mais sobre como definir uma regra de firewall ao nível do servidor e do nível de base de dados, consulte: Criar regras de firewall IP na Base de [Dados SQL](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Pontos finais de serviço

Por predefinição, a sua base de dados SQL está configurada para "Permitir que os serviços Do Azure acedam ao servidor" – o que significa que qualquer Máquina Virtual em Azure pode tentar ligar-se à sua base de dados. Estas tentativas ainda têm de ser autenticadas. No entanto, se não quiser que a sua base de dados seja acessível por quaisquer IPs Azure, pode desativar "Permitir que os serviços Do Azure acedam ao servidor". Além disso, pode configurar [pontos finais](sql-database-vnet-service-endpoint-rule-overview.md)do serviço VNet .

Os pontos finais de serviço (SE) permitem-lhe expor os seus recursos Azure críticos apenas à sua própria rede virtual privada em Azure. Ao fazê-lo, elimina essencialmente o acesso público aos seus recursos. O tráfego entre a sua rede virtual para o Azure permanece na rede de espinha dorsal Azure. Sem se tem um túnel forçado. A sua rede virtual obriga o tráfego de internet à sua organização e ao tráfego do Serviço Azure a percorrer a mesma rota. Com os Pontos Finais de Serviço, pode otimizar isto uma vez que os pacotes fluem diretamente da sua rede virtual para o serviço na rede de espinha dorsal Azure.

![Pontos finais de serviço de VNet](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>IPs Reservados

Outra opção é fornecer [IPs reservados](../virtual-network/virtual-networks-reserved-public-ip.md) para os seus VMs e adicionar esses endereços IP VM específicos nas definições de firewall do servidor. Ao atribuir IPs reservados, poupa-se ao trabalho de ter de atualizar as regras de firewall com a alteração dos endereços IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Em que porta ligo à Base de Dados SQL em

Porto 1433. A Base de Dados SQL comunica sobre esta porta. Para se ligar a partir de dentro de uma rede corporativa, tem de adicionar uma regra de saída nas definições de firewall da sua organização. Como orientação, evite expor a porta 1433 fora da fronteira do Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Como posso monitorizar e regular a atividade no meu servidor e base de dados na Base de Dados SQL

#### <a name="sql-database-auditing"></a>Auditoria de Base de Dados SQL

Com a Base de Dados SQL, pode ligar a Auditoria ON para rastrear eventos de base de dados. [SQL Database Auditing registra](sql-database-auditing.md) eventos de base de dados e os escreve num ficheiro de registo de auditoria na sua Conta de Armazenamento Azure. A auditoria é especialmente útil se pretender obter informações sobre possíveis violações de segurança e políticas, manter o cumprimento regulamentar, etc. Permite-lhe definir e configurar certas categorias de eventos que acha que precisam de ser auditados e com base nisso pode obter relatórios pré-configurados e um dashboard para obter uma visão geral dos eventos que ocorrem na sua base de dados. Pode aplicar estas políticas de auditoria ao nível da base de dados ou ao nível do servidor. Um guia sobre como ativar a auditoria para o seu servidor/base de dados, consulte: Ativar a Auditoria da Base de [Dados SQL](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Deteção de ameaças

Com a [deteção de ameaças,](sql-database-threat-detection.md)obtém-se a capacidade de agir sobre a segurança ou violações de políticas descobertas pela Auditoria muito facilmente. Não precisa de ser um perito em segurança para lidar com potenciais ameaças ou violações no seu sistema. A deteção de ameaças também tem algumas capacidades incorporadas, como a deteção de injeção SQL. A Injeção SQL é uma tentativa de alterar ou comprometer os dados e uma forma bastante comum de atacar uma aplicação de base de dados em geral. A deteção de ameaças executa vários conjuntos de algoritmos que detetam potenciais vulnerabilidades e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados (como acesso a partir de uma localização incomum ou por um principal desconhecido). Oficiais de segurança ou outros administradores designados recebem uma notificação por e-mail se uma ameaça for detetada na base de dados. Cada notificação fornece detalhes sobre a atividade suspeita e recomendações sobre como investigar e mitigar a ameaça. Para aprender a ligar a deteção de ameaças, consulte: Ativar a [deteção de ameaças](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Como protejo os meus dados em geral na Base de Dados SQL

A encriptação fornece um mecanismo forte para proteger e proteger os seus dados sensíveis de intrusos. Os seus dados encriptados não são de utilidade para o intruso sem a chave de desencriptação. Assim, adiciona uma camada extra de proteção em cima das camadas de segurança existentes construídas na Base de Dados SQL. Existem dois aspetos para proteger os seus dados na Base de Dados SQL:

- Os seus dados que estão em repouso nos dados e ficheiros de registo
- Os seus dados que estão a bordo

Na Base de Dados SQL, por padrão, os seus dados em repouso nos dados e ficheiros de registo no subsistema de armazenamento são completamente e sempre encriptados através de [Encriptação transparente de dados [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). As suas cópias de segurança também estão encriptadas. Com o TDE não são necessárias alterações no lado da sua aplicação que esteja a aceder a estes dados. A encriptação e a desencriptação acontecem de forma transparente; daí o nome.
Para proteger os seus dados sensíveis a bordo e em repouso, a Base de Dados SQL fornece uma funcionalidade chamada [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE é uma forma de encriptação do lado do cliente que encripta colunas sensíveis na sua base de dados (por isso estão em cifra para administradores de bases de dados e utilizadores não autorizados). O servidor recebe os dados encriptados para começar. A chave para Sempre Encriptado também é armazenada no lado do cliente, pelo que apenas os clientes autorizados podem desencriptar as colunas sensíveis. O servidor e os administradores de dados não conseguem ver os dados sensíveis uma vez que as chaves de encriptação são armazenadas no cliente. A E encripta colunas sensíveis na tabela de ponta a ponta, desde clientes não autorizados até ao disco físico. A AE suporta comparações de igualdade hoje em dia, para que os DBAs possam continuar a consultar colunas encriptadas como parte dos seus comandos SQL. Sempre Encriptado pode ser usado com uma variedade de opções de loja chave, tais como [Cofre chave Azure,](sql-database-always-encrypted-azure-key-vault.md)loja de certificados Windows e módulos de segurança de hardware locais.

|**Características**|**Always Encrypted**|**Encriptação de Dados Transparente**|
|---|---|---|
|**Extensão de encriptação**|De ponta a ponta|Dados de descanso|
|**Servidor de base de dados pode aceder a dados sensíveis**|Não|Sim, já que a encriptação é para os dados em repouso|
|**Operações T-SQL permitidas**|Comparação da igualdade|Toda a área de superfície T-SQL está disponível|
|**Alterações na aplicação necessárias para usar a funcionalidade**|Mínimo|Muito Mínimo|
|**Granularidade de encriptação**|Nível de coluna|Nível de base de dados|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Como posso limitar o acesso a dados sensíveis na minha base de dados

Cada aplicação tem um certo pedaço de dados sensíveis na base de dados que precisa de ser protegido de ser visível para todos. Certos funcionários da organização precisam de ver estes dados, no entanto outros não devem ser capazes de ver estes dados. Um exemplo é o salário dos empregados. No entanto, um gestor precisaria de acesso à informação salarial para os seus relatórios diretos, no entanto, os membros da equipa não devem ter acesso à informação salarial dos seus pares. Outro cenário são os desenvolvedores de dados que podem estar interagindo com dados sensíveis durante as fases de desenvolvimento ou testes, por exemplo, SSNs de clientes. Esta informação novamente não precisa de ser exposta ao desenvolvedor. Nesses casos, os seus dados sensíveis precisam de ser mascarados ou não ser expostos. A Base de Dados SQL oferece duas dessas abordagens para evitar que utilizadores não autorizados possam ver dados sensíveis:

[A Máscara de Dados Dinâmicos](sql-database-dynamic-data-masking-get-started.md) é uma funcionalidade de máscara de dados que lhe permite limitar a exposição sensível de dados, mascarando-a a utilizadores não privilegiados na camada de aplicação. Define uma regra de mascaramento que pode criar um padrão de mascaramento (por exemplo, apenas para mostrar os últimos quatro dígitos de um ID SSN nacional: XXX-XX-0000 e marcar a maior parte como Xs) e identificar quais os utilizadores que devem ser excluídos da regra de mascaramento. A máscara acontece no voo e existem várias funções de mascaramento disponíveis para várias categorias de dados. A máscara de dados dinâmicos permite-lhe detetar automaticamente dados sensíveis na sua base de dados e aplicar a máscara.

[A segurança do Nível de Linha](/sql/relational-databases/security/row-level-security) permite controlar o acesso ao nível da linha. Ou seja, certas linhas numa tabela de bases de dados baseadas no utilizador que executa a consulta (membro do grupo ou contexto de execução) estão escondidas. A restrição de acesso é feita no nível de base de dados em vez de num nível de aplicação, para simplificar a lógica da sua aplicação. Começa-se por criar um filtro predicado, filtrando filas que não estão expostas e a política de segurança a seguir definir quem tem acesso a estas linhas. Finalmente, o utilizador final executa a sua consulta e, dependendo do privilégio do utilizador, ou vê essas linhas restritas ou não consegue vê-las.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Como posso gerir as chaves de encriptação na nuvem

Existem opções-chave de gestão tanto para a Encriptação Encriptada do Cliente (encriptação do lado do cliente) como para a encriptação transparente de dados (encriptação em repouso). Recomenda-se que rode regularmente as chaves de encriptação. A frequência de rotação deve alinhar-se tanto com os regulamentos internos da organização como com os requisitos de conformidade.

#### <a name="transparent-data-encryption-tde"></a>Encriptação de Dados Transparente (TDE)

Existe uma hierarquia de duas teclas no TDE – os dados em cada base de dados de utilizadores são encriptados por uma chave de encriptação de base de dados exclusiva a base de dados AES-256 (DEK), que por sua vez é encriptada por uma chave-mestra assimétrica RSA 2048. A chave principal também pode ser gerida:

- Automaticamente pela plataforma - Base de Dados SQL.
- Ou usando o [Cofre chave Azure](sql-database-always-encrypted-azure-key-vault.md) como loja chave.

Por padrão, a chave principal para encriptação de dados transparente é gerida pelo serviço Debase de dados SQL por conveniência. Se a sua organização quiser controlar a chave principal, existe uma opção de usar o Cofre chave Azure](sql-database-sempre encriptado-azure-key-vault.md) como a loja-chave. Ao utilizar o Cofre de Chaves Azure, a sua organização assume o controlo sobre o fornecimento de chaves, rotação e controlos de permissão. [Rotação ou troca do tipo de chave master TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápido, uma vez que apenas encripta o DEK. Para organizações com separação de funções entre segurança e gestão de dados, um administrador de segurança poderia fornecer o material-chave para a chave principal do TDE no Cofre de Chaves Azure e fornecer uma chave Azure Key Vault identificador para o administrador de base de dados para usar para encriptação em repouso num servidor. O Key Vault foi concebido de tal forma que a Microsoft não vê nem extrai chaves de encriptação. Também obtém uma gestão centralizada das chaves para a sua organização.

#### <a name="always-encrypted"></a>Sempre Encriptado

Existe também uma [hierarquia de duas teclas](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) em Always Encrypted - uma coluna de dados sensíveis é encriptada por uma chave de encriptação de colunaS AES 256 (CEK), que por sua vez é encriptada por uma chave master de colunas (CMK). Os condutores clientes fornecidos por Always Encrypted não têm limitações no comprimento das CMKs. O valor encriptado do CEK é armazenado na base de dados, e o CMK é armazenado numa loja de chaves de confiança, como windows Certificate Store, Azure Key Vault ou um módulo de segurança de hardware.

- Tanto o CEK como o [CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) podem ser rodados.
- A rotação do CEK é um tamanho de operação de dados e pode ser intensiva em função do tamanho das tabelas que contêm as colunas encriptadas. Por conseguinte, é prudente planear rotações cek em conformidade.
- A rotação cmk, no entanto, não interfere com o desempenho da base de dados, e pode ser feita com funções separadas.

O diagrama seguinte mostra as opções da loja chave para as teclas principais da coluna em Sempre Encriptado

![Fornecedores de lojas CMK sempre encriptados](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Como posso otimizar e proteger o tráfego entre a minha organização e a Base de Dados SQL

O tráfego de rede entre a sua organização e a Base de Dados SQL seria geralmente encaminhado para a rede pública. No entanto, se optar por otimizar este caminho e torná-lo mais seguro, pode olhar para a Rota expresso. A rota expressa permite essencialmente estender a sua rede corporativa à plataforma Azure através de uma ligação privada. Ao fazê-lo, não se passa pela Internet pública. Você também obtém maior segurança, fiabilidade e otimização de encaminhamento que se traduz em tardios de rede mais baixos e velocidades muito mais rápidas do que normalmente experimentaria através da internet pública. Se está a planear transferir uma parte significativa dos dados entre a sua organização e o Azure, a utilização da Rota Expresso pode gerar benefícios de custo. Pode escolher entre três modelos de conectividade diferentes para a ligação da sua organização ao Azure:

- [Co-localização de Cloud Exchange](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Qualquer um-para-qualquer](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Ponto a ponto](../expressroute/expressroute-connectivity-models.md#Ethernet)

A Rota Expressa também permite que você rebente até 2x o limite de largura de banda que compra sem custoadicional. Também é possível configurar a conectividade transversal utilizando a rota Express. Para ver uma lista de fornecedores de conectividade ER, consulte: [Express Route Partners e Peering Locations](../expressroute/expressroute-locations.md). Os seguintes artigos descrevem a Rota expresso com mais detalhes:

- [Introdução na Rota Expresso](../expressroute/expressroute-introduction.md)
- [Pré-requisitos](../expressroute/expressroute-prerequisites.md)
- [Fluxos de Trabalho](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>A Base de Dados SQL está em conformidade com quaisquer requisitos regulamentares, e como é que isso ajuda com a conformidade da minha própria organização.

A Base de Dados SQL está em conformidade com uma série de compliancies regulamentares. Para ver o mais recente conjunto de compliancies que foram cumpridas pela SQL Database, visite o [Microsoft Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) e desempene as compliancies que são importantes para a sua organização para ver se a Base de Dados SQL está incluída nos serviços De base de dados DoML em conformidade. É importante notar que, embora a Base de Dados SQL possa ser certificada como um serviço conforme, ajuda no cumprimento do serviço da sua organização, mas não o garante automaticamente.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitorização e manutenção de bases de dados inteligentes após migração

Depois de migrar a sua base de dados para a Base de Dados SQL, vai querer monitorizar a sua base de dados (por exemplo, verificar como é a utilização de recursos ou verificações de DBCC) e realizar manutenção regular (por exemplo, reconstruir ou reorganizar índices, estatísticas, etc.). Felizmente, a Base de Dados SQL é inteligente no sentido em que utiliza as tendências históricas e as métricas e estatísticas gravadas para o ajudar proativamente a monitorizar e manter a sua base de dados, de modo a que a sua aplicação corra da melhor forma para sempre. Em alguns casos, o Azure SQL DB pode executar automaticamente tarefas de manutenção dependendo da configuração da configuração. Existem três facetas para monitorizar a sua base de dados na Base de Dados SQL:

- Monitorização e otimização de desempenho.
- Otimização de segurança.
- Otimização de custos.

### <a name="performance-monitoring-and-optimization"></a>Monitorização e otimização de desempenho

Com o QueRy Performance Insights, pode obter recomendações personalizadas para a sua carga de trabalho na base de dados para que as suas aplicações possam continuar a funcionar a um nível ideal - sempre. Também pode configurar para que estas recomendações se apliquem automaticamente e não tenha de se preocupar em realizar tarefas de manutenção. Com o Index Advisor, pode implementar automaticamente recomendações de índice com base na sua carga de trabalho - isto chama-se Auto-Tuning. As recomendações evoluem à medida que a sua carga de trabalho de aplicação muda para lhe fornecer as sugestões mais relevantes. Você também tem a opção de rever manualmente estas recomendações e aplicá-las a seu critério.  

### <a name="security-optimization"></a>Otimização de segurança

A Base de Dados SQL fornece recomendações de segurança acionáveis para ajudá-lo a proteger os seus dados e deteção de ameaças para identificar e investigar atividades suspeitas de base de dados que podem representar um potencial fio para a base de dados. [A avaliação](sql-vulnerability-assessment.md) da vulnerabilidade é um serviço de digitalização e reporte de bases de dados que permite monitorizar o estado de segurança das suas bases de dados em escala e identificar riscos de segurança e derivar de uma linha de base de segurança definida por si. Após cada digitalização, é fornecida uma lista personalizada de passos atos e scripts de reparação, bem como um relatório de avaliação que pode ser usado para ajudar a satisfazer os requisitos de conformidade.

Com o Azure Security Center, identifica as recomendações de segurança em todo o tabuleiro e aplica-as com um único clique.

### <a name="cost-optimization"></a>Otimização de custos

A plataforma Azure SQL analisa o histórico de utilização através das bases de dados num servidor para avaliar e recomendar opções de otimização de custos para si. Esta análise geralmente leva uma quinzena para analisar e construir recomendações acionáveis. Piscina elástica é uma dessas opções. A recomendação aparece no portal como um banner:

![recomendações elásticas da piscina](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Pode também ver esta análise na secção "Advisor":

![elástico pool recomendações-conselheiro](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Como posso monitorizar o desempenho e a utilização de recursos na Base de Dados SQL

Na Base de Dados SQL pode aproveitar as ideias inteligentes da plataforma para monitorizar o desempenho e sintonizar em conformidade. Pode monitorizar o desempenho e a utilização de recursos na Base de Dados SQL utilizando os seguintes métodos:

#### <a name="azure-portal"></a>Portal do Azure

O portal Azure mostra a utilização de uma base de dados selecionando a base de dados e clicando no gráfico no painel de visão geral. Pode modificar o gráfico para mostrar múltiplas métricas, incluindo percentagem de CPU, percentagem de DTU, percentagem de Dados IO, percentagem de Sessões e Percentagem de tamanho de base de dados.

![Gráfico de monitorização](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Gráfico de monitorização2](./media/sql-database-manage-after-migration/chart.png)

A partir deste gráfico, também pode configurar alertas por recurso. Estes alertas permitem-lhe responder às condições de recurso com um e-mail, escrever para um ponto final HTTPS/HTTP ou realizar uma ação. Para mais informações, consulte [Criar alertas](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Pontos de vista de gestão dinâmica

Pode consultar a visão dinâmica de gestão do [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) para devolver o histórico de estatísticas de consumo de recursos a partir da última hora e a visão do catálogo do sistema [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) para devolver a história nos últimos 14 dias.

#### <a name="query-performance-insight"></a>Query Performance Insight

[A Consulta Performance Insight](sql-database-query-performance.md) permite-lhe ver um histórico das consultas de maior consumo de recursos e consultas de longa duração para uma base de dados específica. Pode identificar rapidamente as consultas TOP por utilização de recursos, duração e frequência de execução. Pode rastrear consultas e detetar regressão. Esta funcionalidade requer que a [Consulta Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) seja ativada e ativa para a base de dados.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (Pré-visualização) em registos do Monitor Azure

[Os registos do Azure Monitor](../azure-monitor/insights/azure-sql.md) permitem-lhe recolher e visualizar as principais métricas de desempenho da base de dados Azure SQL, suportando até 150.000 Bases de Dados SQL e 5.000 piscinas elásticas SQL por espaço de trabalho. Pode usá-lo para monitorizar e receber notificações. Você pode monitorizar a Base de Dados SQL e métricas elásticas do pool através de várias subscrições Azure e piscinas elásticas e pode ser usado para identificar problemas em cada camada de uma pilha de aplicações.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Estou a notar problemas de desempenho: Como é que a minha metodologia de resolução de problemas da Base de Dados SQL difere do SQL Server

Uma grande parte das técnicas de resolução de problemas que utilizaria para diagnosticar problemas de consulta e desempenho na base de dados permanece a mesma. Afinal, o mesmo motor SQL Server alimenta a nuvem. No entanto, a plataforma - Azure SQL DB construiu em 'inteligência'. Pode ajudá-lo a resolver problemas e diagnosticar problemas de desempenho ainda mais facilmente. Também pode executar algumas destas ações corretivas em seu nome e, em alguns casos, corrigi-las proativamente - automaticamente.

A sua abordagem para problemas de resolução de problemas pode beneficiar significativamente utilizando funcionalidades inteligentes como [Query Performance Insight (QPI)](sql-database-query-performance.md) e [Database Advisor](sql-database-advisor.md) em conjunto e, por isso, a diferença de metodologia difere nesse aspeto – já não precisa de fazer o trabalho manual de moer os detalhes essenciais que podem ajudá-lo a resolver o problema em questão. A plataforma faz-te o trabalho árduo. Um exemplo disso é o QPI. Com o QPI, você pode perfurar todo o caminho até o nível de consulta e olhar para as tendências históricas e descobrir quando exatamente a consulta regrediu. O Database Advisor dá-lhe recomendações sobre coisas que podem ajudá-lo a melhorar o seu desempenho geral em geral como - índices em falta, queda de índices, parametrização das suas consultas, etc.

Com a resolução de problemas de desempenho, é importante identificar se é apenas a aplicação ou a base de dados que a apoia, que está a afetar o desempenho da sua aplicação. Muitas vezes o problema de desempenho reside na camada de aplicação. Pode ser a arquitetura ou o padrão de acesso a dados. Por exemplo, considere que tem uma aplicação tagarela sensível à latência da rede. Neste caso, a sua aplicação sofre porque haveria muitos pedidos curtos indo e vindo ("chatty") entre a aplicação e o servidor e numa rede congestionada, estas rodadas somam-se rapidamente. Para melhorar o desempenho neste caso, pode utilizar consultas de [lote](sql-database-performance-guidance.md#batch-queries). A utilização de lotes ajuda-o tremendamente porque agora os seus pedidos são processados num lote; assim, ajudando-o a reduzir a latência de ida e volta e a melhorar o seu desempenho de candidatura.

Além disso, se notar uma degradação no desempenho geral da sua base de dados, pode monitorizar as visões de gestão dinâmica [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dinâmicas de forma a compreender o CPU, a IO e o consumo de memória. O seu desempenho talvez tenha sido impactado porque a sua base de dados está esfomeada de recursos. Pode ser que possa ter de alterar o tamanho e/ou o nível de serviço da computação com base nas exigências crescentes e cada vez mais reduzidas da carga de trabalho.

Para obter um conjunto abrangente de recomendações para afinar problemas de desempenho, consulte: [Sintonize a sua base](sql-database-performance-guidance.md#tune-your-database)de dados .

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Como posso garantir que estou usando o nível de serviço apropriado e tamanho da computação

A Base de Dados SQL oferece vários níveis de serviço Básico, Standard e Premium. Cada nível de serviço obtém um desempenho previsível garantido ligado a esse nível de serviço. Dependendo da sua carga de trabalho, pode ter explosões de atividade onde a utilização do seu recurso pode atingir o limite do tamanho atual do cálculo em que se encontra. Nesses casos, é útil começar por avaliar se qualquer afinação pode ajudar (por exemplo, adicionar ou alterar um índice, etc.). Se ainda encontrar problemas de limite, considere mudar-se para um nível de serviço mais elevado ou tamanho de computação.

|**Camada de serviços**|**Cenários de caso de uso comum**|
|---|---|
|**Básica**|Aplicações com um punhado de utilizadores e uma base de dados que não tem alta conmoedação, escala e requisitos de desempenho. |
|**Standard**|Aplicações com uma conmoeda, escala e requisitos de desempenho consideráveis, juntamente com exigências de IO baixas a médias. |
|**Premium**|Aplicações com muitos utilizadores simultâneos, alta CPU/memória e elevadas exigências da IO. Aplicações sensíveis à alta condivisa, alta suver e latência podem alavancar o nível Premium. |
|||

Para se certificar de que está no tamanho certo da computação, pode monitorizar o seu consumo de recursos de consulta e base de dados através de uma das formas acima mencionadas em "Como monitorizar o desempenho e a utilização de recursos na Base de Dados SQL". Se descobrir que as suas consultas/bases de dados estão constantemente a aquecer em CPU/Memória, etc. pode considerar a escala até um tamanho de computação mais elevado. Da mesma forma, se notar que mesmo durante as suas horas de ponta, não parece usar tanto os recursos; considerar a escala para baixo a partir do tamanho atual do cálculo.

Se tiver um padrão de aplicação SaaS ou um cenário de consolidação de bases de dados, considere utilizar um pool elástico para otimização de custos. O pool elástico é uma ótima maneira de alcançar a consolidação da base de dados e a otimização de custos. Para ler mais sobre a gestão de várias bases de dados usando piscina elástica, consulte: [Gerir piscinas e bases](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases)de dados .

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Quantas vezes preciso de verificar a integridade da base de dados para a minha base de dados?

A Base de Dados SQL utiliza algumas técnicas inteligentes que lhe permitem lidar automaticamente com certas classes de corrupção de dados e sem qualquer perda de dados. Estas técnicas são incorporadas ao serviço e são alavancadas pelo serviço quando a necessidade surge. Regularmente, as cópias de segurança da sua base de dados em todo o serviço são testadas restaurando-as e executando o DBCC CHECKDB nele. Se houver problemas, a Base de Dados SQL aborda-os proativamente. [A reparação automática](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) de páginas é alavancada para a fixação de páginas corruptas ou com problemas de integridade de dados. As páginas de base de dados são sempre verificadas com a definição padrão CHECKUM que verifica a integridade da página. A Base de Dados SQL monitoriza e analisa proativamente a integridade dos dados da sua base de dados e, se surgirem problemas, aborda-os com a maior prioridade. Além destes, pode optar por fazer opcionalmente os seus próprios controlos de integridade à sua vontade.  Para mais informações, consulte a Integridade dos Dados na Base de [Dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Movimento de dados após migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Como exporto e importa os dados como ficheiros BACPAC da Base de Dados SQL

- **Exportação**: Pode exportar a sua base de dados Azure SQL como ficheiro BACPAC do portal Azure

   ![exportação de bases de dados](./media/sql-database-export/database-export1.png)

- **Importação**: Também pode importar dados como ficheiro BACPAC na base de dados utilizando o portal Azure.

   ![importação de bases de dados](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Como sincronizei dados entre a Base de Dados SQL e o Servidor SQL

Tem várias formas de o conseguir:

- **[Data Sync](sql-database-sync-data.md)** – Esta funcionalidade ajuda-o a sincronizar os dados bidireccionalmente entre várias bases de dados do SQL Server no local e a Base de Dados SQL. Para sincronizar com as bases de dados do SQL Server no local, é necessário instalar e configurar o agente de sincronização num computador local e abrir a porta TCP de saída 1433.
- **[Replicação de Transações](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** – Com a replicação de transações pode sincronizar os seus dados desde as instalações até ao Azure SQL DB, sendo o in-local o editor e o Azure SQL DB a ser o assinante. Por enquanto, só esta configuração é apoiada. Para obter mais informações sobre como migrar os seus dados do local para o Azure SQL com o mínimo de tempo de inatividade, consulte: Utilize a Replicação de [Transações](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a [Base de Dados SQL](sql-database-technical-overview.md).
