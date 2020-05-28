---
title: Perguntas frequentes (FAQ)'
titleSuffix: Azure SQL Managed Instance
description: Instância gerida Azure SQL SQL frequentemente feita perguntas (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: c1a7f22314af472037194150b78e881395c14c2e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117385"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Instância gerida Azure SQL frequentemente colocada seleções (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as questões mais comuns sobre a [Instância Gerida Azure SQL](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funcionalidades suportadas

**Onde posso encontrar uma lista de funcionalidades suportadas no SQL Managed Instance?**

Para obter uma lista de funcionalidades suportadas em Instância Gerida SQL, consulte [as funcionalidades de Instância Gerida SQL](../database/features-comparison.md).

Para diferenças na sintaxe e no comportamento entre o Azure SQL Managed Instance e o In-premises SQL Server, consulte [as diferenças T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Especificação tecnológica & limites de recursos
 
**Onde posso encontrar características técnicas e limites de recursos para a Instância Gerida SQL?**

Para obter características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações](resource-limits.md#hardware-generation-characteristics)de hardware .
Para os níveis de serviço disponíveis e as suas características, consulte [diferenças técnicas entre os níveis](resource-limits.md#service-tier-characteristics)de serviço .

## <a name="known-issues--bugs"></a>Questões conhecidas & bugs

**Onde posso encontrar problemas e insetos conhecidos?**

Para bugs e questões conhecidas veja [questões conhecidas](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novas funcionalidades

**Onde posso encontrar as últimas funcionalidades e as funcionalidades na pré-visualização pública?**

Para novas funcionalidades de pré-visualização, consulte [as notas](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)de lançamento .

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo demora a criar ou atualizar casos, ou a restaurar uma base de dados?**

O tempo esperado para criar um SQL Managed Instance ou alterar o nível de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas operações de [Gestão](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenção de nomeação

**Um SQL Managed Instance pode ter o mesmo nome que o SQL Server no local?**

A alteração do nome SQL Managed Instance não é suportada.

Zona dNS padrão de instância gerida sQL *.database.windows.net* pode ser alterada. 

Utilizar outra zona DNS em vez do predefinido, por exemplo, *.contoso.com:* 
- Use cliConfig para definir um pseudónimo. A ferramenta é apenas um invólucro de definições de registo, por isso pode ser feito usando a política de grupo ou script também.
- Utilize *O CNAME* com *TrustServerCertificate=verdadeira* opção.

## <a name="move-db-from-mi"></a>Mover DB do MI 

**Como posso mover a base de dados da SQL Managed Instance de volta para O Servidor SQL ou base de dados Azure SQL?**

Pode [exportar base de dados para o BACPAC](../database/database-export.md) e, em seguida, [importar o ficheiro BACPAC]( ../database/database-import.md). Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB.

A replicação transacional pode ser utilizada se todas as tabelas da base de dados tiverem chaves primárias.

As `COPY_ONLY` cópias de segurança nativas retiradas da SQL Managed Instance não podem ser restauradas no Servidor SQL porque a SQL Managed Instance tem uma versão de base de dados mais alta em comparação com o Servidor SQL.

## <a name="migrate-instance-db"></a>Migrar instância DB

**Como posso migrar a minha base de dados para uma única base de dados Azure SQL?**

Uma opção é [exportar a base de dados para um BACPAC](../database/database-export.md) e, em seguida, importar o ficheiro [BACPAC](../database/database-import.md). 

Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB. A replicação transacional pode ser utilizada se todas as tabelas da base de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Mudar geração de hardware 

**Posso trocar online a minha geração de hardware SQL Managed Instance entre a Gen 4 e a Gen 5?**

A troca automática online entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região onde o seu SQL Managed Instance é provisionado. Neste caso, pode verificar a página de visão geral do [modelo vCore](../database/service-tiers-vcore.md) explicando como alternar entre gerações de hardware.

Esta é uma operação de longa duração, uma vez que uma nova Instância Gerida sQL será aprovisionada em segundo plano e bases de dados automaticamente transferidas entre a antiga e a nova instância com uma rápida falha no final do processo. 

**E se ambas as gerações de hardware não forem apoiadas na mesma região?**

Se ambas as gerações de hardware não forem suportadas na mesma região, a alteração da geração de hardware é possível, mas deve ser feita manualmente. Isto requer que você ofereci de uma nova instância na região onde a geração de hardware procurada está disponível, e manualmente fazer cópias de segurança e restaurar dados entre a antiga e a nova instância.

**E se não houver endereços IP suficientes para a realização da operação de atualização?**

No caso de não existirem endereços IP suficientes na sub-rede onde a sua instância gerida é aprovisionada, terá de criar uma nova subnet e uma nova instância gerida no seu interior. Sugerimos também que seja criada uma nova subnet com mais endereços IP atribuídos para que futuras operações de atualização evitem situações semelhantes (para o tamanho adequado da sub-rede, verifique [como determinar o tamanho da subnet vnet](vnet-subnet-determine-size.md). Depois de aprovisionada nova instância, pode fazer o repor e restaurar manualmente os dados entre a antiga e a nova instância ou efetuar a restauração de [pontos a tempo.](point-in-time-restore.md?tabs=azure-powershell) 


## <a name="tune-performance"></a>Otimizar o desempenho

**Como posso sintonizar o desempenho do meu SQL Managed Instance?**

General Purpose SQL Managed Instance utiliza armazenamento remoto devido ao tamanho de dados e ficheiros de registo que são importantes para o desempenho. Para mais informações, consulte impacto do tamanho do ficheiro de registo no desempenho da [Instância Gerida SQL](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se a sua carga de trabalho for constituída por muitas pequenas transações, considere mudar o tipo de ligação de proxy para modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo do armazenamento

**Qual é o tamanho máximo de armazenamento para a Instância Gerida SQL?**

O tamanho do armazenamento para a Instância Gerida sQL depende do nível de serviço selecionado (Propósito Geral ou Business Critical). Para limitações de armazenamento destes níveis de serviço, consulte a [característica do nível de serviço](../database/service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Custo de armazenamento de volta 

**O armazenamento de reserva é deduzido do meu armazenamento SQL Managed Instance?**

Não, o armazenamento de reserva não é deduzido do seu espaço de armazenamento SQL Managed Instance. O armazenamento de reserva é independente do espaço de armazenamento da instância e não é limitado em tamanho. O armazenamento de cópia de segurança é limitado pelo período de tempo para reter a cópia de segurança das bases de dados da sua instância, configurável de 7 a 35 dias. Para mais detalhes, consulte [cópias de segurança automatizadas](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Faturação de pista

**Há alguma maneira de rastrear o meu custo de faturação para o meu Caso Gerido SQL?**

Pode fazê-lo utilizando a [solução Azure Cost Management](/azure/cost-management/). Navegue para **Assinaturas** no [portal Azure](https://portal.azure.com) e selecione Análise de **Custos**. 

Utilize a opção **custos acumulados** e, em seguida, filtrar pelo **tipo Recurso** como `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Regras de NSG de entrada

**Como posso estabelecer regras de entrada de NSG em portos de gestão?**

O plano de controlo de instâncias gerida sQL SQL SQL mantém regras de NSG que protegem as portas de gestão.

Aqui está o que as portas de gestão são usadas para:

Os portos 9000 e 9003 são utilizados pela infraestrutura de tecido de serviço. Serviço O papel primário do Tecido é manter o cluster virtual saudável e manter o estado objetivo em termos de número de réplicas de componentes.

Os portos 1438, 1440 e 1452 são usados pelo agente do nó. O agente nó é uma aplicação que funciona dentro do cluster e é usada pelo plano de controlo para executar comandos de gestão.

Além das regras da NSG, a firewall incorporada protege a instância na camada de rede. Na comunicação da camada de aplicação está protegida com os certificados.
  
Para obter mais informações e como verificar a firewall incorporada, consulte a [firewall integrada do Azure SQL.](management-endpoint-verify-built-in-firewall.md)


## <a name="mitigate-data-exfiltration-risks"></a>Mitigar riscos de exfiltração de dados  

**Como posso mitigar os riscos de exfiltração de dados?**

Para mitigar quaisquer riscos de exfiltração de dados, recomenda-se aos clientes que apliquem um conjunto de definições e controlos de segurança:

- Ligue a [Encriptação transparente de dados (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todas as bases de dados.
- Desligue o tempo de execução da linguagem comum (CLR). Isto também é recomendado no local.
- Utilize apenas a autenticação do Diretório Ativo Azure (AAD).
- Caso de acesso com conta DBA privilegiada baixa.
- Configure o acesso da caixa de acesso jit para a conta de sysadmin.
- Ligue [a auditoria da SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integre-a com mecanismos de alerta.
- Ligue a [Deteção](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) de Ameaças da suite [Advanced Data Security (ADS).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Casos de utilização de poupança de custos

**Onde posso encontrar casos de uso e economiade custos resultantes com Instância Gerida SQL?**

Estudos de caso sql geridos por exemplo:

- [Rio Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [Detalhes de Energia](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Para obter uma melhor compreensão dos benefícios, custos e riscos associados à implantação de SQL Managed Instance, há também um estudo de Forrester: [Impacto Económico Total do MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização dNS 

**Posso refrescar o DNS?**

Atualmente, não fornecemos uma funcionalidade para atualizar a configuração do servidor DNS para a Instância Gerida SQL.

A configuração DNS é eventualmente renovada:

- Quando o contrato de arrendamento dHCP expirar.
- Na atualização da plataforma.

Como uma suver, desaperte o SQL Managed Instance para 4 vCore e atualize-o novamente depois. Isto tem um efeito colateral de refrescar a configuração DNS.


## <a name="ip-address"></a>Endereço IP

**Posso ligar-me à Instância Gerida sQL utilizando o endereço IP?**

A ligação à Instância Gerida SQL utilizando o endereço IP não é suportada. SQL Managed Instance mapas de nome sonorizador para um equilibrador de carga em frente ao cluster virtual SQL Managed Instance. Como um cluster virtual poderia acolher várias instâncias geridas pela SQL, as ligações não podem ser encaminhadas para a instância gerida adequada sql sem especificar explicitamente o nome.

Para obter mais informações sobre a arquitetura de cluster virtual SQL Managed Instance, consulte [a arquitetura de conectividade de cluster virtual.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Um Caso Gerido SQL pode ter um endereço IP estático?**

Em situações raras mas necessárias, podemos precisar de fazer uma migração on-line de um SQL Managed Instance para um novo cluster virtual. Se necessário, esta migração deve-se a mudanças na nossa pilha de tecnologia destinadas a melhorar a segurança e a fiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome anfitrião SQL Managed Instance. O serviço SQL Managed Instance não reclama suporte de endereço IP estático e reserva-se o direito de alterá-lo sem aviso prévio como parte de ciclos regulares de manutenção.

Por esta razão, desencorajamos fortemente a immutabilidade do endereço IP, uma vez que poderia causar tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Alterar o fuso horário

**Posso mudar o fuso horário para uma instância gerida pela SQL?**

A configuração do fuso horário pode ser definida quando uma Instância Gerida SQL é aprovisionada pela primeira vez. A alteração do fuso horário da instância gerida sQL existente não é suportada. Para mais detalhes, consulte as limitações do [fuso horário](timezones-overview.md#limitations).

As sintetizações incluem a criação de um novo SQL Managed Instance com o fuso horário adequado e, em seguida, ou executar uma cópia de segurança manual e restaurar, ou o que recomendamos, executar uma [restauração de ponto-a-tempo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)transversal .


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como posso resolver problemas de desempenho com a minha Instância Gerida SQL?**

Para uma comparação de desempenho entre a Instância Gerida sQL e o Servidor SQL, um bom ponto de partida são [as melhores práticas para a comparação de desempenho entre o Artigo Azure SQL Managed Instance e SQL Server.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

O carregamento de dados é muitas vezes mais lento em SQL Managed Instance do que no SQL Server devido ao modelo de recuperação completa obrigatório e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na entrada de escrita de log de transação. Por vezes, isto pode ser trabalhado ao redor, carregando dados transitórios em tempdb em vez de base de dados do utilizador, ou usando colunas agrupadas, ou tabelas otimizadas pela memória.


## <a name="restore-encrypted-backup"></a>Restaurar a cópia de segurança encriptada

**Posso restaurar a minha base de dados encriptada para a SQL Managed Instance?**

Sim, não precisa de desencriptar a sua base de dados para restaurá-la para a SQL Managed Instance. É necessário fornecer um certificado/chave utilizado como protetor de chave de encriptação no sistema de origem para a Instância Gerida SQL para poder ler os dados do ficheiro de backup encriptado. Há duas maneiras possíveis de fazê-lo:

- *Faça upload de proteção de certificados para a instância gerida pela SQL*. Pode ser feito usando apenas powerShell. O [script da amostra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- Carregue o *protetor-chave assimétrico para o Cofre de Chaves Azure (AKV) e o ponto SQL Managed Instance para ele*. Esta abordagem assemelha-se ao caso de utilização do TDE bring-your-your-key (BYOK) que também utiliza a integração akv para armazenar a chave de encriptação. Se não quiser utilizar a chave como protetor de chaves de encriptação e apenas pretender disponibilizar a chave para a SQL Managed Instance para restaurar a(s) base de dados encriptada), siga as instruções para [configurar O BYOK TDE,](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e não verifique a caixa de verificação *Faça da chave selecionada o protetor TDE predefinido*.

Assim que disponibilizar o protetor de encriptação para a Instância Gerida SQL, pode prosseguir com o procedimento padrão de restauro da base de dados.

## <a name="migrate-from-sql-database"></a>Migrar da Base de Dados SQL 

**Como posso migrar da Base de Dados Azure SQL para a Instância Gerida SQL?**

A SQL Managed Instance oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que a Base de Dados Azure SQL. Se pretender consolidar dados numa única instância, ou simplesmente precisar de uma funcionalidade suportada exclusivamente em Instância Gerida SQL, pode migrar os seus dados utilizando a funcionalidade de exportação/importação (BACPAC).
