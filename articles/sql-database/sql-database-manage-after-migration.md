---
title: Gerir bases de dados únicos e em pool após a migração - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como gerir a sua base de dados após a migração para a base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: carlrab
manager: craigg
ms.date: 02/13/2019
ms.openlocfilehash: 2b8c35450ab4586f619993aeb9a578c83cafc0c6
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216939"
---
# <a name="new-dba-in-the-cloud--managing-your-single-and-pooled-databases-in-azure-sql-database"></a>Novo DBA na cloud – gerenciamento de seus bancos de dados individuais e agrupados na base de dados do Azure SQL

Mover o tradicional de gestão automática, ambiente Self-controlado para um ambiente PaaS pode parecer um pouco sobrecarregado em primeiro lugar. Como programador de uma aplicação ou um DBA, iria querer saber os principais recursos da plataforma que pode ajudar a manter seu aplicativo disponível, desempenho, seguro e resiliente - sempre. Este artigo tem como objetivo fazer exatamente isso. O artigo organiza os recursos de forma sucinta e dá-lhe algumas orientações sobre como utilizar melhor as principais capacidades de base de dados SQL com bases de dados únicos e em pool para gerir e manter a sua aplicação em execução com eficiência e obter resultados ideais na cloud. Público-alvo típico para este artigo seria aqueles que:

- Estão a avaliar a migração das suas aplicações para a SQL Database do Azure – modernizar as suas aplicações.
- Estão no processo de migrar as suas aplicações – o cenário de migração em curso.
- Foram concluídas recentemente a migração para o Azure SQL DB – novo DBA na cloud.

Este artigo aborda algumas das principais características de base de dados do Azure SQL, como uma plataforma que pode ser aproveitado prontamente ao trabalhar com bases de dados individuais e bases de dados agrupadas em conjuntos elásticos. Eles são os seguintes:

- Negócios continuidade e recuperação após desastre (BCDR)
- Segurança e conformidade
- Monitorização de base de dados inteligente e a manutenção
- Movimento de dados

> [!NOTE]
> Este artigo aplica-se para as seguintes opções de implementação na base de dados do Azure SQL: único conjuntos elásticos e bases de dados. Não é aplicável à opção de implementação de instância gerida na base de dados SQL.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Negócios continuidade e recuperação após desastre (BCDR)

Capacidades de recuperação de desastre e continuidade do negócio permitem-lhe continuar a sua empresa, como de costume, em caso de desastre. Após o desastre pode ser um evento de nível de base de dados (por exemplo, alguém por engano ignora uma tabela crucial) ou um evento de nível de centro de dados (catástrofe regional, por exemplo, um tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Como criar e gerir cópias de segurança na base de dados SQL

Não crie cópias de segurança no Azure SQL DB e isso é porque não precisa. Base de dados SQL cria automaticamente uma cópia das bases de dados para si, para que já não deve se preocupar sobre agendamento, retirando e gerir cópias de segurança. A plataforma demora um backup completo de todas as semanas, cópia de segurança intervalos de poucas horas e um registo de cópia de segurança a cada 5 minutos para garantir a recuperação após desastre é eficiente e o mínimo de perda de dados diferencial. A primeira cópia de segurança completa ocorre quando criar uma base de dados. Estas cópias de segurança estão disponíveis para por um determinado período chamado "Período de retenção" e varia de acordo com o escalão de serviço que escolher. Base de dados SQL fornece-lhe a capacidade de restaurar para qualquer ponto anterior no tempo dentro com do período de retenção [ponto no tempo de recuperação (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore).

|Camada de serviços|Período de retenção em dias|
|---|:---:|
|Básica|7|
|Standard|35|
|Premium|35|
|||

Além disso, o [retenção de longo prazo (LTR)](sql-database-long-term-retention.md) funcionalidade permite-lhe manter os ficheiros de cópia de segurança durante um período muito mais especificamente, para até 10 anos e restaurar dados a partir destas cópias de segurança em qualquer momento dentro desse período. Além disso, as cópias de segurança da base de dados são mantidas no armazenamento georreplicado para garantir a resiliência de catástrofes regional. Também pode restaurar essas cópias de segurança em qualquer região do Azure em qualquer altura dentro do período de retenção. Ver [descrição geral da continuidade de negócio](sql-database-business-continuity.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Como garantir a continuidade do negócio em caso de um desastre ao nível do datacenter ou catástrofe regional

Como as cópias de segurança da base de dados são armazenadas no armazenamento georreplicado para se certificar de que no caso de um desastre regional, pode restaurar a cópia de segurança para outra região do Azure. Isso é chamado o restauro geográfico. O RPO (objetivo de ponto de recuperação) para que isso é geralmente < 1 hora e o ERT (estimado de tempo de recuperação – alguns minutos a horas).

Bases de dados de missão crítica, BD SQL do Azure oferece, georreplicação ativa. O que ele faz, essencialmente, é que ele cria uma cópia secundária georreplicada da base de dados original noutra região. Por exemplo, se a base de dados inicialmente está alojado na região do Azure-oeste e pretender resiliência a desastres regional. Deverá criar uma réplica de georreplicação da base de dados em E.U.A. oeste, E.U.A. leste de dizer. Quando a calamidade com acontece no Oeste dos E.U.A., pode efetuar a ativação pós-falha para a região E.U.A. Leste. Configurando-as num grupo de ativação pós-falha automática é ainda melhor, pois isso garante que a base de dados automaticamente, buscará socorro para o secundário, na região E.U.A. leste, em caso de desastre. O RPO para isso é < 5 segundos e os segundos do ERT < 30.

Se não estiver configurado um grupo de ativação pós-falha automática, em seguida, seu aplicativo precisa ativamente monitorizar a existência de um desastre e inicie uma ativação pós-falha para o secundário. Pode criar até 4 tais active geo-réplicas em diferentes regiões do Azure. Pode ficar ainda melhor. Também pode aceder a estes active geo-réplicas secundárias para acesso só de leitura. Isso é muito útil para reduzir a latência para um cenário de aplicação distribuída geograficamente.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Como meu plano de recuperação após desastre altera no local para a base de dados SQL

Em resumo, as tradicional no local do SQL Server, precisa de configuração gerir a disponibilidade com funcionalidades como o Clustering de ativação pós-falha, espelhamento de banco de dados, replicação de transações ou envio de registo ativamente e manter e gerir cópias de segurança para garantir Continuidade do negócio. Base de dados SQL, a plataforma gere automaticamente, para que possa concentrar-se no desenvolvimento e na Otimização do aplicativo de banco de dados e não se preocupar sobre gestão de desastre o máximo. Pode fazer a cópia de segurança e os planos de recuperação após desastre configurados e trabalhar com apenas alguns cliques no portal do Azure (ou alguns comandos usando as APIs do PowerShell).

Para saber mais sobre a recuperação após desastre, veja: [Recuperação de desastres de BD SQL do Azure 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Segurança e conformidade

Base de dados SQL leva segurança e privacidade muito a sério. Security na base de dados SQL está disponível ao nível da base de dados e ao nível da plataforma e é mais bem entendido quando categorizadas em várias camadas. Em cada camada tem de controlar e fornecer segurança ideal para a sua aplicação. As camadas são:

- Identidade e autenticação ([autenticação do Windows/SQL e a autenticação do Azure Active Directory [AAD]](sql-database-control-access.md)).
- Monitorização da atividade ([auditoria](sql-database-auditing.md) e [deteção de ameaças](sql-database-threat-detection.md)).
- Proteção de dados reais ([criptografia transparente de dados [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) e [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Controlar o acesso a dados confidenciais e com privilégios ([segurança ao nível da linha](/sql/relational-databases/security/row-level-security) e [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)).

[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) oferece gerenciamento de segurança centralizado em cargas de trabalho em execução no Azure, no local e noutras Clouds. Pode ver se essencial proteção de base de dados SQL, tais como [auditoria](sql-database-auditing.md) e [encriptação transparente de dados [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) estão configurados em todos os recursos e criar políticas com base nos seus próprios requisitos.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Os métodos de autenticação de utilizador são oferecidos na base de dados SQL

Existem [dois métodos de autenticação](sql-database-control-access.md#authentication) oferecidos na base de dados SQL:

- [Autenticação do Active Directory do Azure](sql-database-aad-authentication.md)
- Autenticação do SQL

Não é suportada a autenticação tradicional do windows. O Azure Active Directory (AD) é um serviço centralizado de gestão de identidades e acessos. Com isso muito convenientemente pode fornecer um único início de sessão acesso (SSO) a todas as pessoas na sua organização. Isso significa que as credenciais são partilhadas entre todos os serviços do Azure para a autenticação mais simples. Oferece suporte a AAD [MFA (Multi-factor Authentication)](sql-database-ssms-mfa-authentication.md) e com um [alguns cliques](../active-directory/hybrid/how-to-connect-install-express.md) AAD pode ser integrado no Windows Server Active Directory. Autenticação do SQL funciona exatamente da mesma forma que utilizado no passado. Fornecer uma nome de utilizador/palavra-passe e pode autenticar os utilizadores a qualquer base de dados num determinado servidor de base de dados SQL. Isso também permite que a base de dados do SQL e SQL Data Warehouse oferecer autenticação multifator e contas de utilizador convidado dentro de um domínio do Azure AD. Se já tiver um Active Directory no local, pode federar o diretório com o Azure Active Directory para expandir o seu diretório para o Azure.

|**Se...**|**Base de dados SQL / do SQL Data Warehouse**|
|---|---|
|Prefere não utilizar o Azure Active Directory (AD) no Azure|Utilize [autenticação do SQL](sql-database-security-overview.md)|
|AD utilizados no SQL Server no local|[Federar o AD com o Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)e utilizar a autenticação do Azure AD. Com isso, pode utilizar o início de sessão único.|
|Precisa de impor o multi-factor authentication (MFA)|Exigir a MFA como uma política através de [acesso condicional do Microsoft](sql-database-conditional-access.md)e utilize [autenticação Universal do Azure AD com o suporte MFA](sql-database-ssms-mfa-authentication.md).|
|Ter contas de convidado de contas Microsoft (live.com, outlook.com) ou de outros domínios (gmail.com)|Uso [autenticação do Azure AD Universal](sql-database-ssms-mfa-authentication.md) no armazém de dados/base de dados SQL, que tira partido [colaboração B2B do Azure AD](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).|
|Sessão iniciada no Windows com as suas credenciais do Azure AD a partir de um domínio federado|Uso [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|Sessão iniciada no Windows com as credenciais de um domínio não Federado com o Azure|Uso [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|Tem os serviços de camada intermediária que precisam de ligar a base de dados SQL ou SQL Data Warehouse|Uso [autenticação integrada do Azure AD](sql-database-aad-authentication-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Como limitar ou controlar o acesso de conectividade ao meu banco de dados

Existem várias técnicas à sua disposição que pode utilizar para a competência de organização de conectividade ideal para a sua aplicação.

- Regras da Firewall
- Pontos finais de serviço de VNet
- IPs Reservados

#### <a name="firewall"></a>Firewall

Uma firewall impede o acesso ao seu servidor de uma entidade externa ao permitir apenas acesso de entidades específicas ao seu servidor de base de dados SQL. Por predefinição, todas as ligações e bases de dados contidas no servidor de base de dados SQL não são permitidas, exceto ligações proveniente de outros serviços do Azure. Com uma regra de firewall é possível abrir o acesso ao seu servidor apenas para entidades (por exemplo, uma máquina de programador), aprovar, ao permitir que o endereço IP do computador através da firewall. Ele também permite-lhe especificar um intervalo de IPs que gostaria de permitir o acesso ao servidor de base de dados SQL. Por exemplo, endereços IP de máquinas do programador na sua organização podem ser adicionados ao mesmo tempo, especificando um intervalo na página de definições de Firewall.

Pode criar regras de firewall ao nível do servidor ou ao nível da base de dados. Regras de firewall IP ao nível do servidor podem ser criadas através do Azure no portal ou com o SSMS. Para saber mais sobre como definir uma regra de firewall ao nível do servidor e ao nível da base de dados, consulte: [Criar regras de firewall IP na base de dados SQL](sql-database-security-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Pontos finais de serviço

Por predefinição, a base de dados SQL está configurado para "Permitir que os serviços do Azure ao servidor de acesso" – que significa que qualquer máquina Virtual no Azure pode tentar ligar à base de dados. Estas tentativas ainda é necessário obter autenticado. No entanto, se não deseja sua base de dados acessíveis por qualquer IPs do Azure, pode desabilitar "Permitir que os serviços do Azure ao servidor de acesso". Além disso, pode configurar [pontos finais de serviço de VNet](sql-database-vnet-service-endpoint-rule-overview.md).

Pontos finais de serviço (SE) permitem-lhe expor os seus recursos críticos do Azure apenas para sua própria rede privada virtual no Azure. Ao fazê-lo, eliminar, essencialmente, acesso público aos seus recursos. O tráfego entre a rede virtual para o Azure permanece na rede backbone do Azure. Sem SE obtém o encaminhamento de pacotes de imposição de túnel. A rede virtual força o tráfego de internet para a sua organização e o tráfego de serviço do Azure para go sobre a mesma rota. Com pontos finais de serviço, pode otimizá-lo desde o fluxo de pacotes diretamente a partir da sua rede virtual para o serviço na rede backbone do Azure.

![Pontos finais de serviço de VNet](./media/sql-database-manage-after-migration/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>IPs Reservados

Outra opção consiste em aprovisionar [reservado IPs](../virtual-network/virtual-networks-reserved-public-ip.md) para as suas VMs e a lista de permissões específicas aos endereços IP de VM no servidor de definições da firewall. Ao atribuir IPs reservados, guarde o trabalho de ter de atualizar as regras de firewall com a alteração dos endereços IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Porta em que posso ligar a base de dados SQL no

Porta 1433. Base de dados SQL comunica através desta porta. Para ligar a partir de uma rede empresarial, terá de adicionar uma regra de saída nas definições de firewall da sua organização. Como uma diretriz, evite a expor a porta 1433 fora do limite do Azure. Pode executar o SSMS no Azure com [do Azure RemoteApp](https://www.microsoft.com/cloud-platform/azure-remoteapp-client-apps). Isso não requer a abertura de ligações de saída para a porta 1433, o IP estático, para que o banco de dados pode ser aberto para apenas o RemoteApp e oferece suporte a várias-factor Authentication (MFA).

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Como monitorizar e regular a atividade no meu servidor e base de dados na base de dados SQL

#### <a name="sql-database-auditing"></a>A auditoria de base de dados SQL

Base de dados SQL, pode ativar a auditoria para controlar eventos de base de dados. [A auditoria de base de dados SQL](sql-database-auditing.md) registra os eventos de base de dados e escreve-as num arquivo de log de auditoria na sua conta de armazenamento do Azure. A auditoria é especialmente útil se pretende obter informações sobre potenciais violações de segurança e a política, manter a conformidade regulamentar etc. Permite-lhe definir e configurar determinadas categorias de eventos que acredita que precisa de auditoria e com base no que pode obter relatórios pré-configurados e um dashboard para obter uma descrição geral dos eventos que ocorrem na sua base de dados. Pode aplicar estas políticas de auditorias ao nível da base de dados ou ao nível do servidor. Um guia sobre como ativar a auditoria para o servidor/base de dados, consulte: [Ativar a base de dados SQL auditoria](sql-database-security-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Deteção de ameaças

Com o [deteção de ameaças](sql-database-threat-detection.md), tem a capacidade de agir de acordo com as violações de política de segurança ou detetadas pela auditoria muito facilmente. Não precisa de ser um especialista em para resolver potenciais ameaças ou violações de seu sistema de segurança. Deteção de ameaças também tem algumas capacidades incorporadas, como deteção de Injeção de SQL. Injeção de SQL é uma tentativa de alterar ou comprometer os dados e uma maneira bastante comum de atacar um aplicativo de banco de dados em geral. Deteção de ameaças executa vários conjuntos de algoritmos que detetar potenciais vulnerabilidades e ataques de injeção de SQL, bem como os padrões de acesso anómalos da base de dados (por exemplo, o acesso a partir de uma localização invulgar ou por um principal invulgar). Responsáveis pela segurança ou outros administradores designados recebem uma notificação por e-mail, se uma ameaça é detetada na base de dados. Cada notificação fornece detalhes sobre a atividade suspeita e recomendações sobre como investigar e mitigar a ameaça. Para saber como ativar a deteção de ameaças, consulte: [Ativar deteção de ameaças](sql-database-security-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Como posso proteger meus dados em geral na base de dados SQL

Encriptação fornece um mecanismo forte para proteger e proteger os seus dados confidenciais de invasores. Os dados criptografados são sem uso ao intruso sem a chave de desencriptação. Portanto, ele adiciona uma camada extra de proteção sobre as camadas existentes de segurança incorporada na base de dados SQL. Há dois aspectos para proteger os dados na base de dados SQL:

- Os dados que está em repouso nos arquivos de dados e de registo
- Os dados que está em trânsito

Na base de dados SQL, por predefinição, os dados Inativos nos ficheiros de dados e de registo no subsistema de armazenamento é completamente e sempre criptografados via [criptografia transparente de dados [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). As cópias de segurança também são encriptadas. Com o TDE, não há nenhuma alteração necessária no seu lado de aplicação que está a aceder a estes dados. A criptografia e descriptografia acontecem de forma transparente; Por conseguinte, o nome.
Para proteger os dados confidenciais em trânsito e em inatividade, a base de dados SQL fornece um recurso chamado [sempre encriptado (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE é uma forma de encriptação do lado do cliente que encripta colunas confidenciais na sua base de dados (para que eles estão em texto cifrado para administradores de banco de dados e os utilizadores não autorizados). O servidor recebe os dados encriptados para começar. A chave para sempre encriptados também é armazenada no lado do cliente, para que os clientes autorizados apenas podem desencriptar as colunas confidenciais. O servidor e os administradores de dados não é possível ver os dados confidenciais, uma vez que as chaves de encriptação são armazenadas no cliente. AE encripta confidenciais colunas na tabela de ponta a ponta, de clientes não autorizados para o disco físico. AE suporta comparações de igualdade hoje em dia, para que os DBAs podem continuar a consultar colunas encriptadas como parte dos seus comandos SQL. Sempre encriptado pode de ser utilizado com uma variedade de opções de armazenamento de chaves, como [do Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md), arquivo de certificados do Windows e módulos de segurança de local hardware.

|**Características**|**Sempre encriptado**|**Encriptação de dados transparente**|
|---|---|---|
|**Intervalo de encriptação**|-A-ponto|Dados Inativos|
|**Servidor de base de dados pode aceder a dados confidenciais**|Não|Sim, uma vez que a encriptação é para os dados Inativos|
|**Operações permitidas de T-SQL**|Comparação de igualdade|Todas as área de superfície de T-SQL está disponível|
|**Alterações de aplicação necessárias para utilizar a funcionalidade**|Mínimo|Mínimo|
|**Granularidade de encriptação**|Nível de coluna|Nível de base de dados|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Como posso limitar o acesso a dados confidenciais em meu banco de dados

Cada aplicativo tem um determinado bits de dados confidenciais na base de dados que tem de ser protegidos contra a ser visível para todos os utilizadores. Determinados funcionários dentro da organização precisam para ver estes dados, no entanto outros não devem ser capazes de ver estes dados. Um exemplo é salários do funcionário. Gerente precisaria acesso às informações de salário de seu relatórios diretos no entanto, os membros da Equipe individuais não devem ter acesso às informações de salário de seus colegas. Outro cenário é que os desenvolvedores de dados que podem interagir com dados confidenciais durante as fases de desenvolvimento ou teste, por exemplo, números da segurança social dos clientes. Estas informações novamente não precisem de ser expostos ao desenvolvedor. Nesses casos, seus dados confidenciais ou tem de ser mascarado ou não ser exposto em todos os. Base de dados SQL oferece duas dessas abordagens para impedir que os utilizadores não autorizados a possibilidade de visualizar os dados confidenciais:

[Dynamic Data Masking](sql-database-dynamic-data-masking-get-started.md) é uma funcionalidade de máscara de dados que permite-lhe limitar a exposição de dados confidenciais ao mascará-los para utilizadores não privilegiados na camada da aplicação. Definir uma regra de máscara que pode criar um padrão de máscara (por exemplo, para mostrar apenas últimos quatro dígitos de um nacional SSN de ID: XXX-XX-0000 e marcar o máximo proveito dele como Xs) e identificar os utilizadores a serem excluídos da regra de máscara. A máscara ocorre no momento e existem várias funções de máscara disponíveis para várias categorias de dados. Máscara de dados dinâmica permite-lhe detetar dados confidenciais na base de dados e aplicar máscara à mesma automaticamente.

[Segurança ao nível da linha](/sql/relational-databases/security/row-level-security) permite-lhe controlar o acesso ao nível da linha. Ou seja, determinadas linhas numa tabela de base de dados com base no utilizador que executa a consulta (grupo associação ou contexto de execução) estão ocultas. A restrição de acesso é feita na camada de base de dados em vez de numa camada de aplicação, para simplificar a lógica de aplicação. Comece por criar um predicado de filtro, filtrar linhas que não são expostos e a segurança política seguinte definição de quem tem acesso a essas linhas. Por fim, o utilizador final executa sua consulta e, dependendo de privilégio do usuário, eles ver essas linhas restritas ou não é possível vê-los em todos os.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Como posso gerir as chaves de encriptação na cloud

Existem opções de gestão de chaves para sempre encriptados (encriptação do lado do cliente) e encriptação de dados transparente (encriptação em repouso). Recomenda-se que regularmente Rodar chaves de encriptação. A frequência de rotação deve alinhar com as suas normas da organização interna e os requisitos de conformidade.

#### <a name="transparent-data-encryption-tde"></a>Encriptação de Dados Transparente (TDE)

Não existe uma hierarquia de chave de dois TDE – os dados em cada base de dados do utilizador são encriptados por uma simétrica AES-256 da base de dados exclusivo para a base de dados chave de encriptação (DEK), que por sua vez, é encriptada por uma chave de mestre de RSA 2048 assimétrica exclusivo para o servidor. A chave mestra podem ser geridos a:

- Automaticamente, a plataforma - base de dados SQL.
- Ou por si através de [do Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md) como o armazenamento de chaves.

Por predefinição, a chave mestra para encriptação de dados transparente é gerida pelo serviço de base de dados SQL para sua comodidade. Se a organização gostaria de controle sobre a chave mestra, há uma opção para utilizar Vault](sql-database-always-encrypted-azure-key-vault.md) de chave do Azure como o armazenamento de chaves. Ao utilizar o Azure Key Vault, sua organização assume o controlo sobre controles de aprovisionamento, a rotação e permissão de chave. [Rotação ou mudar o tipo de uma chave mestra do TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) é rápido, como ele apenas encripta novamente o DEK. Para organizações com separação de funções entre segurança e gestão de dados, pode aprovisionar o material de chave para a chave mestra TDE no Azure Key Vault e fornecer um identificador de chave do Azure Key Vault para o administrador de banco de dados a utilizar para um administrador de segurança encriptação em repouso num servidor. O Key Vault foi concebido de forma a que o Microsoft não veja ou extraia as chaves de encriptação. Também obtém um gerenciamento centralizado de chaves para a sua organização.

#### <a name="always-encrypted"></a>Sempre Encriptado

Há também uma [hierarquia de chave de dois](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) no Always Encrypted - uma coluna de dados confidenciais é encriptada por uma chave de encriptação de 256 colunas AES (CEK), que por sua vez, é encriptada por uma chave mestra de coluna (CMK). Os controladores de cliente fornecidos para sempre encriptados têm sem limitações, o comprimento de CMKs. O valor encriptado do CEK é armazenado na base de dados e o CMK é armazenado num armazenamento de chave confiável, como Windows Store de certificado, o Azure Key Vault ou um módulo de segurança de hardware.

- Ambas as [CEK e CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) pode ser girado.
- Rotação de CEK é um tamanho de operação de dados e podem ser intensivas em termos de tempo dependendo do tamanho das tabelas contendo colunas criptografadas. Por conseguinte, é prudente planejar rotações CEK em conformidade.
- Rotação de CMK, no entanto, não interfere com desempenho de base de dados e pode ser feita com funções separadas.

O diagrama seguinte mostra as opções de armazenamento de chaves para as chaves de mestre de coluna no Always Encrypted

![Sempre encriptado CMK armazenar fornecedores](./media/sql-database-manage-after-migration/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Como posso otimizar e proteger o tráfego entre a minha organização e a base de dados SQL

O tráfego de rede entre a sua organização e a base de dados SQL seria geralmente é roteado através da rede pública. No entanto, se optar por otimizar este caminho e torná-lo mais seguro, pode examinar Express Route. Essencialmente, o expressroute permite-lhe expandir a sua rede corporativa na plataforma do Azure através de uma ligação privada. Ao fazer isso, não passam pela Internet pública. Também obtém uma maior segurança, confiabilidade e otimização de encaminhamento que se traduz em menores latências de rede e muito mais rápidos 1000x maiores do que normalmente teriam vai através da internet pública. Se estiver a planear sobre a transferência de uma parte significativa de dados entre a organização e o Azure, através de Express Route podem resultar em benefícios de custos. Pode escolher entre três modelos de conectividade diferentes para a ligação da sua organização para o Azure:

- [Localização conjunta do Exchange na nuvem](../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Qualquer para qualquer](../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Point-to-Point](../expressroute/expressroute-connectivity-models.md#Ethernet)

Também o Expressroute permite-lhe ultrapassar 2x até o limite de largura de banda que compra sem nenhum custo adicional. Também é possível configurar cruzada com o Express route de conectividade de região. Para ver uma lista de fornecedores de conectividade de ER, consulte: [Express Route parceiros e localizações de Peering](../expressroute/expressroute-locations.md). Os seguintes artigos descrevem o Express Route mais detalhadamente:

- [Introdução sobre o Expressroute](../expressroute/expressroute-introduction.md)
- [Pré-requisitos](../expressroute/expressroute-prerequisites.md)
- [Fluxos de trabalho](../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>É a base de dados SQL em conformidade com quaisquer requisitos de regulamentação e como o que ajuda a conformidade do meu próprio da organização

Base de dados SQL está em conformidade com uma gama de normas regulamentares. Para ver o conjunto mais recente de regulamentares que tiverem sido cumpridos, visite o [Microsoft Trust Center](https://microsoft.com/trustcenter/compliance/complianceofferings) e desagregar as regulamentares que são importantes para a sua organização para ver se a base de dados SQL está incluído em serviços do Azure em conformidade. É importante observar que embora a base de dados SQL pode ser certificada como um serviço em conformidade, ele auxilia a conformidade de serviço da sua organização, mas não automaticamente garante que ele.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Monitorização de base de dados inteligente e a manutenção após a migração

Assim que tiver migrado sua base de dados para a base de dados SQL, está a vai querer monitorizar a sua base de dados (por exemplo, como a utilização de recursos é como de verificação ou DBCC verifica) e realizar a manutenção regular (por exemplo, reconstrua ou reorganize indexa, estatísticas etc.). Felizmente, a base de dados SQL é Intelligent no sentido de que ela usa o tendências históricas derivadas e métricas gravadas e estatísticas para proativamente ajudar a monitorizar e manter a sua base de dados, para que seu aplicativo seja executado de forma ideal sempre. Em alguns casos, o BD SQL do Azure pode executar automaticamente tarefas de manutenção consoante a configuração de configuração. Existem três facetas para monitorização de base de dados na base de dados SQL:

- Monitorização de desempenho e otimização.
- Otimização de segurança.
- Otimização de custos.

### <a name="performance-monitoring-and-optimization"></a>Monitorização de desempenho e Otimização

Com informações de desempenho de consulta, pode obter recomendações personalizadas para a sua carga de trabalho de base de dados para que seus aplicativos podem continuar a executar num nível de ideal – sempre. Pode também configurá-lo para que estas recomendações são aplicadas automaticamente e não é necessário se preocupar com a executar tarefas de manutenção. Com o Assistente de índice, pode implementar automaticamente as recomendações de índice com base na carga de trabalho – isso é chamado de otimização automática. As recomendações de evoluam como as alterações de carga de trabalho de aplicação para lhe fornecer as sugestões mais relevantes. Também obtém a opção de manualmente rever estas recomendações e aplicá-las a seu critério.  

### <a name="security-optimization"></a>Otimização de segurança

Base de dados SQL fornece recomendações de segurança acionáveis para ajudar a proteger os seus dados e deteção de ameaças para identificar e investigar atividades suspeitas da base de dados que possam representar um thread potencial para a base de dados. [Avaliação de vulnerabilidade](sql-vulnerability-assessment.md) é uma base de dados, análise e relatórios de serviço que permite-lhe monitorizar o estado de segurança das bases de dados em escala e identifique riscos de segurança e se desviam de uma linha de base de segurança definida por si. Depois de cada análise, é fornecida uma lista personalizada de passos acionáveis e scripts de remediação, bem como um relatório de avaliação que pode ser utilizado para ajudar a cumprir requisitos de conformidade.

Com o Centro de segurança do Azure, identifique as recomendações de segurança em geral e aplicá-las com um único clique.

### <a name="cost-optimization"></a>Otimização de custos

Plataforma SQL do Azure analisa o histórico de utilização entre bases de dados num servidor para avaliar e recomendar opções de otimização de custos para. Esta análise demora um fortnight para analisar e criar recomendações acionáveis. Conjunto elástico é uma dessas opções. A recomendação é apresentada no portal como uma faixa:

![recomendações de conjunto elástico](./media/sql-database-manage-after-migration/elastic-pool-recommendations.png)

Também pode ver esta análise na seção "Consultor":

![recomendações de conjunto elástico-advisor](./media/sql-database-manage-after-migration/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Como posso monitorizar o desempenho e a utilização de recursos na base de dados SQL

Na base de dados SQL, pode aproveitar as informações inteligentes da plataforma para monitorizar o desempenho e otimizar o em conformidade. Pode monitorizar o desempenho e utilização de recursos na base de dados SQL através dos seguintes métodos:

#### <a name="azure-portal"></a>Portal do Azure

O portal do Azure mostra a utilização de uma base de dados ao selecionar a base de dados e clicar no gráfico no painel de descrição geral. Pode modificar o gráfico para mostrar várias métricas, incluindo a percentagem de CPU, percentagem de DTU, percentagem de es de dados, percentagem de sessões e percentagem do tamanho da base de dados.

![Gráfico de monitorização](./media/sql-database-manage-after-migration/monitoring-chart.png)

![Monitorização gráfico 2](./media/sql-database-manage-after-migration/chart.png)

Este gráfico, também pode configurar alertas por recurso. Estes alertas permitem-lhe responder às condições de recursos com uma mensagem de e-mail, escrever um ponto final HTTPS/HTTP ou executar uma ação. Para obter mais informações, consulte [criar alertas](sql-database-insights-alerts-portal.md).

#### <a name="dynamic-management-views"></a>Vistas de gestão dinâmica

Pode consultar o [DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) vista de gestão dinâmica para retornar o histórico de estatísticas de consumo de recursos de última hora e o [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vista de catálogo de sistema para retornar histórico dos últimos 14 dias.

#### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) permite-lhe ver um histórico das consultas de consumo de recursos principais e consultas de execução longa para uma base de dados específico. Pode identificar rapidamente as consultas principais por utilização de recursos, a duração e a frequência de execução. Pode controlar as consultas e detetar regressão. Esta funcionalidade requer [Query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) para ser ativado e Active Directory para a base de dados.

![Query Performance Insight](./media/sql-database-manage-after-migration/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Registos de análise de SQL do Azure (pré-visualização) no Azure Monitor

[Registos de Monitor do Azure](../azure-monitor/insights/azure-sql.md) permite-lhe recolher e visualizar as principais métricas de desempenho de SQL Azure do Azure, suporta até 150 000 bases de dados SQL e 5000 conjuntos elásticos SQL por área de trabalho. Pode usá-lo para monitorizar e receber notificações. Pode monitorizar métricas de conjunto elástico e de base de dados SQL em várias subscrições do Azure e conjuntos elásticos e pode ser utilizado para identificar problemas em cada camada de uma pilha de aplicação.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Eu estou observar problemas de desempenho: Meu metodologia de resolução de problemas de base de dados SQL difere do SQL Server

Uma grande parte das técnicas de resolução de problemas que utilizaria para diagnosticar a consulta e problemas de desempenho da base de dados permanecem inalterados. Depois de todos os mesmo SQL Server engine capacita a cloud. No entanto, a plataforma - BD SQL do Azure foi criado em "inteligência". Ele pode ajudar a solucionar problemas e diagnosticar problemas de desempenho ainda mais facilmente. Pode também efetuar algumas destas ações corretivas em seu nome e, em alguns casos, proativamente corrigi-los - a automaticamente.

Sua abordagem para a resolução de problemas de desempenho significativamente pode se beneficiar usando as funcionalidades inteligentes, como [Insight(QPI) de desempenho de consulta](sql-database-query-performance.md) e [Database Advisor](sql-database-advisor.md) em conjunto e para que a diferença na metodologia é diferente nesse aspecto – já não terá de fazer o trabalho manual de grinding dos detalhes essenciais que podem ajudá-lo a resolver o problema em questão. A plataforma faz o trabalho pesado para. Um exemplo disso é QPI. Com QPI, pode desagregar todo o caminho para o nível de consulta e ver as tendências históricas derivadas e descobrir quando exatamente a consulta regredido. O Assistente de base de dados dá-lhe recomendações em coisas que podem ajudar a melhorar o desempenho geral em geral, como - índices ausentes, remover índices, parametrização suas consultas etc.

Resolução de problemas de desempenho, é importante identificar se é apenas o aplicativo ou a base de dados suporta ele, que está a afetar o desempenho da aplicação. Muitas vezes o problema de desempenho está na camada de aplicativo. É possível a arquitetura ou o padrão de acesso de dados. Por exemplo, considere que terá uma aplicação de chatty sensível a latência de rede. Neste caso, a sua aplicação sofre porque poderia haver muitos pedidos curtos vai e volta ("conversadoras") entre o aplicativo e o servidor e numa rede congestionada, adicione estes idas e voltas rapidamente. Para melhorar o desempenho nesse caso, pode usar [consultas em lote](sql-database-performance-guidance.md#batch-queries). Lotes de utilização ajuda-o a enormemente porque agora os pedidos são processados num lote; portanto, ajudando-o a fim de reduzir a latência de ida e volta e melhorar o desempenho da aplicação.

Além disso, se observar uma degradação no desempenho geral do seu banco de dados, pode monitorizar o [DM db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) e [resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) vistas de gestão dinâmica para compreenda o consumo de CPU, IO e memória. O desempenho da sua afetada, talvez, porque a base de dados é fica sem recursos. É possível que poderá ter de alterar o tamanho de computação e/ou com base na aumentar e reduzir as exigências de carga de trabalho de camada de serviço.

Para um conjunto abrangente de recomendações de otimização de problemas de desempenho, consulte: [Otimizar a sua base de dados](sql-database-performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Como garantir que eu estou usando o escalão de serviço apropriado e tamanho de computação

Base de dados SQL oferece vários escalões de serviço básico, Standard e Premium. Cada escalão de serviço, obtém um desempenho previsível garantido vinculado a essa camada de serviço. Dependendo da sua carga de trabalho, pode ter picos de atividade em que a utilização de recursos pode atingir o limite do tamanho de computação atual, que se encontra. Nesses casos, é útil começar primeiro ao avaliar se qualquer ajuste pode ajudar a (por exemplo, adicionar ou alterar um índice etc.). Se ainda ocorrerem problemas de limite, considere mover para um escalão de serviço mais elevado ou tamanho de computação.

|**Camada de serviços**|**Cenários de casos de utilização comuns**|
|---|---|
|**Básica**|Aplicativos com utilizadores de uma mão-cheia e uma base de dados que não tem requisitos elevados de simultaneidade, dimensionamento e desempenho. |
|**Standard**|Aplicativos com um requisitos de simultaneidade, dimensionamento e desempenho considerável juntamente com baixa a média de e/s de demanda. |
|**Premium**|Aplicativos com muitos utilizadores em simultâneo, da CPU/memória elevada e alta demanda de e/s. Alta simultaneidade, alto débito e latência às aplicações sensíveis podem aproveitar o nível Premium. |
|||

Para certificar-se de que está no tamanho de computação certas, pode monitorizar o consumo de recursos de consulta e a base de dados por meio de uma das formas mencionados anteriormente em "Como posso monitorizar a utilização de desempenho e de recursos na base de dados SQL". Deve encontrar que suas consultas/bases de dados são consistentemente em execução frequentes em etc. da CPU/memória que pode considerar aumentar verticalmente para um tamanho de computação mais elevado. Da mesma forma, se observar que, mesmo durante as horas de pico, parece que não use os recursos muito; Considere reduzir o tempo limite de tamanho de computação atual.

Se tiver um padrão de aplicação SaaS ou de um cenário de consolidação de base de dados, considere utilizar um conjunto elástico para otimização de custos. Conjunto elástico é uma excelente forma de alcançar a consolidação de base de dados e a otimização de custos. Para ler mais sobre como gerir várias bases de dados com o conjunto elástico, consulte: [Gerir conjuntos e bases de dados](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>A frequência com que é necessário executar verificações de integridade da base de dados para meu banco de dados

Base de dados SQL utiliza algumas técnicas inteligentes que lhe permitem lidar com determinadas classes de Corrupção de dados automaticamente e sem perda de dados. Essas técnicas estão incorporadas no serviço e são utilizadas pelo serviço quando precisar surge. Regularmente, as cópias de segurança da base de dados em todo o serviço são testadas, restaurá-los e executando DBCC CHECKDB nele. Se existirem problemas, base de dados SQL proativamente resolve-los. [Reparo de página automático](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) é utilizado para corrigir páginas que estão danificados ou que têm problemas de integridade de dados. As páginas de base de dados sempre são verificadas com a configuração de soma de verificação padrão que verifica a integridade da página. Base de dados SQL proativamente monitora e analisa a integridade dos dados da base de dados e, se surgir um problema, resolve-os com a prioridade mais alta. Além destas, pode optar por pode optar por executar verificações de integridade de seus próprios à sua vontade.  Para obter mais informações, consulte [integridade dos dados na base de dados SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Movimento de dados após a migração

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database"></a>Como exportar e importar dados como ficheiros BACPAC da base de dados SQL

- **Exportar**: Pode exportar a base de dados SQL do Azure como um ficheiro BACPAC do portal do Azure

   ![exportação da base de dados](./media/sql-database-export/database-export1.png)

- **Importar**: Também pode importar dados como um ficheiro BACPAC para a base de dados com o portal do Azure.

   ![importação de base de dados](./media/sql-database-import/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Como posso sincronizar dados entre a base de dados SQL e o SQL Server

Tem várias formas de fazer isso:

- **[Sincronização de dados](sql-database-sync-data.md)**  – esta funcionalidade ajuda-o a sincronizar dados bidirecional entre várias bases de dados no local do SQL Server e base de dados SQL. Para sincronizar com bases de dados do SQL Server no local, terá de instalar e configurar o agente de sincronização num computador local, abra a porta de saída de TCP 1433.
- **[Replicação de transações](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)**  – com a replicação de transações pode sincronizar os dados no local para a BD SQL do Azure com no local que está a ser Editor e BD SQL do Azure que está a ser subscritor. Por enquanto, apenas esta configuração é suportada. Para obter mais informações sobre como migrar os seus dados no local para o Azure SQL com o tempo de inatividade mínimo, consulte: [Utilizar a replicação de transações](sql-database-single-database-migrate.md#method-2-use-transactional-replication)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [base de dados SQL](sql-database-technical-overview.md).
