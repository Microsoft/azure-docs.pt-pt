---
title: FAQ de instância gerida
description: SQL Database gerido instância frequentemente feita perguntas (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 99fbda6f6d5e8fc88f9f4f34c6e194412a120057
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598516"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database gerido instância frequentemente feita perguntas (FAQ)

Este artigo contém muitas das questões mais comuns sobre a instância gerida pela Base de [Dados SQL.](sql-database-managed-instance.md)

## <a name="supported-features"></a>Funcionalidades suportadas

**Onde posso encontrar uma lista de funcionalidades suportadas em instâncias geridas?**

Para obter uma lista de funcionalidades suportadas em instância gerida, consulte a Base de [Dados Azure SQL versus SQL Server](sql-database-features.md).

Para diferenças na sintaxe e no comportamento entre a Base de Dados Azure SQL gerida por instância sql e no local SQL Server, consulte [as diferenças T-SQL do SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Especificação tecnológica & limites de recursos
 
**Onde posso encontrar características técnicas e limites de recursos para instâncias geridas?**

Para obter características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)de hardware .
Para os níveis de serviço disponíveis e as suas características, consulte [diferenças técnicas entre os níveis](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)de serviço .

## <a name="known-issues--bugs"></a>Questões conhecidas & bugs

**Onde posso encontrar problemas e insetos conhecidos?**

Para bugs e questões conhecidas veja [questões conhecidas](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Novas funcionalidades

**Onde posso encontrar as últimas funcionalidades e as funcionalidades na pré-visualização pública?**

Para novas funcionalidades de pré-visualização, consulte [as notas](sql-database-release-notes.md?tabs=managed-instance)de lançamento .

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo demora a criar ou atualizar casos, ou a restaurar uma base de dados?**

O tempo esperado para criar um novo nível de instância gerido ou alterar o nível de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas operações de [Gestão](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenção de nomeação

**Uma instância gerida pode ter o mesmo nome que o SQL Server no local?**

A alteração do nome de instância gerida não é suportada.

Zona dNS padrão de instância gerida *.database.windows.net* poderia ser alterada. 

Utilizar outra zona DNS em vez do predefinido, por exemplo, *.contoso.com:* 
- Use cliConfig para definir um pseudónimo. A ferramenta é apenas um invólucro de definições de registo, por isso pode ser feito usando a política de grupo ou script também.
- Utilize *O CNAME* com *TrustServerCertificate=verdadeira* opção.

## <a name="move-db-from-mi"></a>Mover DB do MI 

**Como posso mover a base de dados de instância gerida de volta para SQL Server ou Azure SQL Database?**

Pode [exportar base de dados para o BACPAC](sql-database-export.md) e, em seguida, [importar o ficheiro BACPAC]( sql-database-import.md). Esta é uma abordagem recomendada se a sua base de dados for inferior a 100 GB.

A replicação transacional pode ser utilizada se todas as tabelas da base de dados tiverem chaves primárias.

As `COPY_ONLY` cópias de segurança nativas retiradas da instância gerida não podem ser restauradas ao SQL Server porque a instância gerida tem uma versão de base de dados mais alta em comparação com o SQL Server.

## <a name="migrate-instance-db"></a>Migrar instância DB

**Como posso migrar a minha base de dados para uma única base de dados Azure SQL?**

Uma opção é [exportar a base de dados para um BACPAC](sql-database-export.md) e, em seguida, importar o ficheiro [BACPAC](sql-database-import.md). 

Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB. A replicação transacional pode ser utilizada se todas as tabelas da base de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Mudar geração de hardware 

**Posso mudar a minha geração de hardware de instância gerida entre a Gen 4 e a Gen 5 online?**

A troca automática online entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região onde a sua instância gerida é aprovisionada. Neste caso, pode verificar a página de visão geral do [modelo vCore](sql-database-service-tiers-vcore.md) explicando como alternar entre gerações de hardware.

Trata-se de uma operação de longa duração, uma vez que uma nova instância gerida será aprovisionada em segundo plano e bases de dados automaticamente transferidas entre a antiga e a nova instância com uma rápida falha no final do processo. 

**E se ambas as gerações de hardware não forem apoiadas na mesma região?**

Se ambas as gerações de hardware não forem suportadas na mesma região, a alteração da geração de hardware é possível, mas deve ser feita manualmente. Isto requer que você ofereci de uma nova instância na região onde a geração de hardware procurada está disponível, e manualmente fazer cópias de segurança e restaurar dados entre a antiga e a nova instância.

**E se não houver endereços IP suficientes para a realização da operação de atualização?**

No caso de não existirem endereços IP suficientes na sub-rede onde a sua instância gerida é aprovisionada, terá de criar uma nova subnet e uma nova instância gerida no seu interior. Sugerimos também que seja criada uma nova subrede com mais endereços IP alocadas para que futuras operações de atualização evitem situações semelhantes (para o tamanho da subnet do propper, verifique [como determinar o tamanho da subnet vnet](sql-database-managed-instance-determine-size-vnet-subnet.md). Depois de aprovisionada nova instância, pode fazer o repor e restaurar manualmente os dados entre a antiga e a nova instância ou efetuar a restauração de [pontos a tempo.](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell) 


## <a name="tune-performance"></a>Otimizar o desempenho

**Como posso sintonizar o desempenho do meu caso gerido?**

A instância gerida pelo General Purpose utiliza armazenamento remoto devido ao tamanho dos dados e ficheiros de registo que são importantes para o desempenho. Para mais informações, consulte [impacto do tamanho do ficheiro de registo no desempenho](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)da Instância Gerida por Propósito Geral .

Se a sua carga de trabalho for constituída por muitas pequenas transações, considere mudar o tipo de ligação de proxy para modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo do armazenamento

**Qual é o tamanho máximo de armazenamento para o caso gerido?**

O tamanho do armazenamento para a instância gerida depende do nível de serviço selecionado (Propósito Geral ou Business Critical). Para limitações de armazenamento destes níveis de serviço, consulte a [característica do nível de serviço](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Custo de armazenamento de volta 

**O armazenamento de reserva é deduzido do meu armazenamento de instância gerido?**

Não, o armazenamento de reserva não é deduzido do seu espaço de armazenamento de instância gerido. O armazenamento de reserva é independente do espaço de armazenamento da instância e não é limitado em tamanho. O armazenamento de cópia de segurança é limitado pelo período de tempo para reter a cópia de segurança das bases de dados da sua instância, configurável de 7 a 35 dias. Para mais detalhes, consulte [cópias de segurança automatizadas](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Faturação de pista

**Há alguma maneira de controlar o meu custo de faturação para o meu caso gerido?**

Pode fazê-lo utilizando a [solução Azure Cost Management](/azure/cost-management/). Navegue para **Assinaturas** no [portal Azure](https://portal.azure.com) e selecione Análise de **Custos**. 

Utilize a opção **custos acumulados** e, em seguida, filtrar pelo **tipo Recurso** como `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Regras de NSG de entrada

**Como posso estabelecer regras de entrada de NSG em portos de gestão?**

O plano de controlo de instâncias gerido mantém as regras da NSG que protegem as portas de gestão.

Aqui está o que as portas de gestão são usadas para:

Os portos 9000 e 9003 são utilizados pela infraestrutura de tecido de serviço. Serviço O papel primário do Tecido é manter o cluster virtual saudável e manter o estado objetivo em termos de número de réplicas de componentes.

Os portos 1438, 1440 e 1452 são usados pelo agente do nó. O agente nó é uma aplicação que funciona dentro do cluster e é usada pelo plano de controlo para executar comandos de gestão.

Além das regras da NSG, a firewall incorporada protege a instância na camada de rede. Na comunicação da camada de aplicação está protegida com os certificados.
  
Para obter mais informações e como verificar a firewall incorporada, consulte a Base de [Dados Azure SQL gerida por instância incorporada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


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

**Onde posso encontrar casos de utilização e consequente poupança de custos com instância gerida?**

Estudos de caso geridos por exemplo:

- [Rio Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [Detalhes de Energia](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Para obter uma melhor compreensão dos benefícios, custos e riscos associados à implementação de caso gerido pela Azure SQL Database, há também um estudo de Forrester: [Impacto Económico Total do MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização dNS 

**Posso refrescar o DNS?**

Atualmente, não fornecemos uma funcionalidade para atualizar a configuração do servidor DNS para instância gerida.

A configuração DNS é eventualmente renovada:

- Quando o contrato de arrendamento dHCP expirar.
- Na atualização da plataforma.

Como uma suver, desaperte a instância gerida para 4 vCore e atualize-a novamente depois. Isto tem um efeito colateral de refrescar a configuração DNS.


## <a name="ip-address"></a>Endereço IP

**Posso ligar-me à instância gerida usando o endereço IP?**

A ligação à instância gerida utilizando o endereço IP não é suportada. Mapas de nomes de anfitriões geridos para carregar o equilibrador em frente ao cluster virtual de instância gerido. Como um cluster virtual poderia acolher múltiplas instâncias geridas a ligação não poderia ser encaminhada para instância gerida adequada sem especificar o seu nome.

Para obter mais informações sobre arquitetura de cluster virtual gerida, consulte a arquitetura de conectividade de [cluster virtual.](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)

**Um caso gerido pode ter um endereço IP estático?**

Em situações raras, mas necessárias, talvez tenhamos de fazer uma migração online de um exemplo gerido para um novo aglomerado virtual. Se necessário, esta migração deve-se a mudanças na nossa pilha de tecnologia destinadas a melhorar a segurança e a fiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que é mapeado para o nome do anfitrião da instância gerida. O serviço de instância gerido não reclama suporte de endereço IP estático e reserva-se o direito de alterá-lo sem aviso prévio como parte de ciclos regulares de manutenção.

Por esta razão, desencorajamos fortemente a immutabilidade do endereço IP, uma vez que poderia causar tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Alterar o fuso horário

**Posso mudar o fuso horário para um caso gerido existente?**

A configuração do fuso horário pode ser definida quando uma instância gerida é aprovisionada pela primeira vez. Mudar o fuso horário da instância gerida existente não é suportado. Para mais detalhes, consulte as limitações do [fuso horário](sql-database-managed-instance-timezone.md#limitations).

As sintetizações incluem a criação de uma nova instância gerida com o fuso horário adequado e, em seguida, ou executa uma cópia de segurança manual e restaure, ou o que recomendamos, executar uma [restauração de ponto-a-tempo](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)transversal .


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como posso resolver problemas de desempenho com o meu caso gerido?**

Para uma comparação de desempenho entre a instância gerida e o SQL Server, um bom ponto de partida são [as melhores práticas para a comparação de desempenho entre a instância gerida pelo Azure SQL e](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) o artigo do SQL Server.

O carregamento de dados é muitas vezes mais lento em caso gerido do que no SQL Server devido ao modelo de recuperação total obrigatório e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) na entrada de escrita de registo de transações. Por vezes, isto pode ser trabalhado ao redor, carregando dados transitórios em tempdb em vez de base de dados do utilizador, ou usando colunas agrupadas, ou tabelas otimizadas pela memória.


## <a name="restore-encrypted-backup"></a>Restaurar a cópia de segurança encriptada

**Posso restaurar a minha base de dados encriptada para a ocorrência gerida?**

Sim, não precisa de desencriptar a sua base de dados para poder restaurá-la para a instância gerida. É necessário fornecer um certificado/chave utilizado como protetor de chave de encriptação no sistema de origem para a instância gerida para poder ler dados do ficheiro de backup encriptado. Há duas maneiras possíveis de fazê-lo:

- *Faça upload do protetor de certificados para a instância gerida*. Pode ser feito usando apenas powerShell. O [script da amostra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- Carregue o *protetor-chave assimétrico para o Cofre de Chaves Azure (AKV) e*o exemplo gerido por pontos . Esta abordagem assemelha-se ao caso de utilização do TDE bring-your-your-key (BYOK) que também utiliza a integração akv para armazenar a chave de encriptação. Se não quiser utilizar a chave como protetor de chaves de encriptação e apenas pretender disponibilizar a chave para a instância gerida para restaurar a base de dados encriptada, siga as instruções para [a configuração do BYOK TDE,](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)e não verifique a caixa de verificação *Faça da chave selecionada o protetor TDE predefinido*.

Assim que disponibilizar o protetor de encriptação para a instância gerida, pode prosseguir com o procedimento padrão de restauro da base de dados.

## <a name="migrate-from-single-db"></a>Migrar de db único 

**Como posso migrar da base de dados Azure SQL, única ou elástica piscina para a ocorrência gerida?**

A instância gerida oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que outras opções de implementação da Base de Dados Azure SQL. Se pretender consolidar dados numa única instância, ou simplesmente precisar de uma funcionalidade suportada exclusivamente em instância gerida, pode migrar os seus dados utilizando a funcionalidade exportação/importação (BACPAC).
