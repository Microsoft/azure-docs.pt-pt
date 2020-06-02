---
title: Perguntas frequentes (FAQ)'
titleSuffix: Azure SQL Managed Instance
description: Azure SQL SQL Gestd Instance frequentemente fez perguntas (FAQ)
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
ms.openlocfilehash: b72952618b2d024bd2c4b445c3ea673ed523866b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247942"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL Gestd Instance frequentemente fez perguntas (FAQ)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo contém as perguntas mais comuns sobre [a Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funcionalidades suportadas

**Onde posso encontrar uma lista de funcionalidades suportadas no SQL Managed Instance?**

Para obter uma lista de funcionalidades suportadas em SQL Managed Instance, consulte [as funcionalidades de Instância Gerida Azure SQL](../database/features-comparison.md).

Para diferenças de sintaxe e comportamento entre Azure SQL Managed Instance e SQL Server, consulte [as diferenças T-SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Limites de recursos de & de especificação tecnológica
 
**Onde posso encontrar características técnicas e limites de recursos para a SQL Managed Instance?**

Para as características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações de hardware](resource-limits.md#hardware-generation-characteristics).
Para os níveis de serviço disponíveis e as suas características, consulte [diferenças técnicas entre os níveis de serviço.](resource-limits.md#service-tier-characteristics)

## <a name="known-issues--bugs"></a>Problemas conhecidos & insetos

**Onde posso encontrar problemas e insetos conhecidos?**

Para insetos e questões conhecidas ver [questões conhecidas](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Novas funcionalidades

**Onde posso encontrar as últimas funcionalidades e as funcionalidades na pré-visualização pública?**

Para funcionalidades novas e de pré-visualização ver [notas de lançamento](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempos de implantação 

**Quanto tempo demora a criar ou atualizar instâncias, ou a restaurar uma base de dados?**

O tempo esperado para criar uma SqL Managed Instance ou alterar o nível de serviço (vCores, armazenamento) depende de vários fatores. Dê uma olhada nas operações de [Gestão](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenção de nomeação

**Um SQL Managed Instance pode ter o mesmo nome que a instância do SqL Server no local?**

Alterar o nome SQL Managed Instance não é suportado.

A zona de DNS padrão de instância gerida SQL *(database.windows.net* pode ser alterada. 

Para utilizar outra zona DNS em vez do padrão, por exemplo, *.contoso.com*: 
- Use o CliConfig para definir um pseudónimo. A ferramenta é apenas um invólucro de definições de registo, por isso também pode ser feito usando a política de grupo ou o script.
- Utilize *CNAME* com *trustServerCertificate=verdadeira* opção.

## <a name="move-db-from-mi"></a>Mover DB do MI 

**Como posso transferir a base de dados do SQL Managed Instance para o SQL Server ou para a Base de Dados Azure SQL?**

Pode [exportar base de dados para BACPAC](../database/database-export.md) e, em seguida, [importar o ficheiro BACPAC.]( ../database/database-import.md) Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB.

A replicação transacional pode ser usada se todas as tabelas da base de dados tiverem chaves primárias.

As `COPY_ONLY` cópias de segurança nativas retiradas do SQL Managed Instance não podem ser restauradas no SQL Managed Server porque o SQL Managed Instance tem uma versão de base de dados mais alta em comparação com o SQL Server.

## <a name="migrate-instance-db"></a>Exemplo de migração DB

**Como posso migrar a minha base de dados de casos para uma única Base de Dados Azure SQL?**

Uma opção é [exportar a base de dados para um BACPAC](../database/database-export.md) e, em seguida, [importar o ficheiro BACPAC.](../database/database-import.md) 

Esta é a abordagem recomendada se a sua base de dados for inferior a 100 GB. A replicação transacional pode ser usada se todas as tabelas da base de dados tiverem chaves primárias.

## <a name="switch-hardware-generation"></a>Mudar geração de hardware 

**Posso mudar a minha geração de hardware SQL Managed Instance entre a Gen 4 e a Gen 5 online?**

A comutação on-line automatizada entre gerações de hardware é possível se ambas as gerações de hardware estiverem disponíveis na região onde o seu SQL Managed Instance é a provisionado. Neste caso, pode consultar [a página geral do modelo vCore](../database/service-tiers-vcore.md) explicando como alternar entre gerações de hardware.

Trata-se de uma operação de longa duração, uma vez que um novo SQL Managed Instance será a provisionado em segundo plano e as bases de dados serão automaticamente transferidas entre a antiga e a nova instância com uma rápida falha no final do processo. 

**E se ambas as gerações de hardware não forem suportadas na mesma região?**

Se ambas as gerações de hardware não forem suportadas na mesma região, a mudança na geração de hardware é possível, mas deve ser feita manualmente. Isto requer que você fornece um novo caso na região onde a geração de hardware procurado está disponível, e manualmente fazer o back up e restaurar os dados entre o antigo e o novo exemplo.

**E se não houver endereços IP suficientes para a realização de operação de atualização?**

Caso não existam endereços IP suficientes na sub-rede onde a sua instância gerida é alocada, terá de criar uma nova sub-rede e uma nova instância gerida no seu interior. Sugerimos também que a nova sub-rede seja criada com mais endereços IP atribuídos para que futuras operações de atualização evitem situações semelhantes (para o tamanho adequado da sub-rede, verifique [como determinar o tamanho da sub-rede Vnet](vnet-subnet-determine-size.md). Após a disponibilização de novas instâncias, pode fazer o back up manual e restaurar os dados entre a instância antiga e nova ou efetuar [a restauração](point-in-time-restore.md?tabs=azure-powershell)pontual transversal . 


## <a name="tune-performance"></a>Otimizar o desempenho

**Como sinto o desempenho do meu SQL Managed Instance?**

Final geral SQL Caso gerido usa armazenamento remoto devido ao tamanho de dados e ficheiros de registo que importam para o desempenho. Para obter mais informações, consulte [o impacto do tamanho do ficheiro de registo no desempenho da sql Managed Instance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)para fins gerais .

Se a sua carga de trabalho consistir em muitas pequenas transações, considere mudar o tipo de ligação de proxy para modo de redirecionamento.

## <a name="maximum-storage-size"></a>Tamanho máximo de armazenamento

**Qual é o tamanho máximo de armazenamento para SQL Managed Instance?**

O tamanho do armazenamento para sql Gestd Instance depende do nível de serviço selecionado (Final Geral ou Critical De Negócio). Para limitações de armazenamento destes níveis de serviço, consulte [característica do nível de serviço](../database/service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Custo de armazenamento de back-up 

**O armazenamento de backup é deduzido do armazenamento do meu SQL Managed Instance?**

Não, o armazenamento de cópias de segurança não é deduzido do seu espaço de armazenamento SQL Managed Instance. O armazenamento de backup é independente do espaço de armazenamento de instância e não é limitado em tamanho. O armazenamento de backup é limitado pelo período de tempo para reter a cópia de segurança das bases de dados do seu caso, configurável de 7 a 35 dias. Para mais informações, consulte [cópias de segurança automatizadas.](../database/automated-backups-overview.md)

## <a name="track-billing"></a>Faturação de faixas

**Há alguma maneira de controlar o meu custo de faturação para o meu SQL Managed Instance?**

Pode fazê-lo utilizando a [solução Azure Cost Management](/azure/cost-management/). Navegue para **subscrições** no [portal Azure](https://portal.azure.com) e selecione **Análise de Custos.** 

Utilize a opção **custos acumulados** e, em seguida, filtre pelo **tipo de recurso** como `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Regras NSG de entrada

**Como posso definir regras NSG de entrada em portos de gestão?**

O avião de controlo SQL SQL Managed Instance mantém as regras NSG que protegem as portas de gestão.

Aqui está para que são utilizados portos de gestão:

Os portos 9000 e 9003 são utilizados pela infraestrutura de tecido de serviço. O papel principal do Tecido de Serviço é manter o cluster virtual saudável e manter o estado de objetivo em termos de número de réplicas de componentes.

As portas 1438, 1440 e 1452 são utilizadas pelo agente de nó. O agente nó é uma aplicação que corre dentro do cluster e é usada pelo plano de controlo para executar comandos de gestão.

Além das regras NSG, a firewall incorporada protege a instância na camada de rede. Na comunicação da camada de aplicação está protegida com os certificados.
  
Para obter mais informações e como verificar a firewall incorporada, consulte [a firewall incorporada Azure SQL.](management-endpoint-verify-built-in-firewall.md)


## <a name="mitigate-data-exfiltration-risks"></a>Mitigar riscos de exfiltração de dados  

**Como posso mitigar os riscos de exfiltração de dados?**

Para mitigar quaisquer riscos de exfiltração de dados, recomenda-se aos clientes a aplicação de um conjunto de definições e controlos de segurança:

- Ligue [encriptação de dados transparentes (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) em todas as bases de dados.
- Desligue o tempo de execução da linguagem comum (CLR). Isto também é recomendado no local.
- Utilize apenas a autenticação do Azure Ative Directory (AAD).
- Instância de acesso com conta DBA privilegiada baixa.
- Configure o acesso à caixa de salto JiT para a conta sysadmin.
- Ligue [a auditoria DO SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integre-a com mecanismos de alerta.
- Ligue a [Deteção de Ameaças](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) a partir da suite Advanced Data Security [(ADS).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Casos de utilização de poupança de custos

**Onde posso encontrar casos de utilização e consequentes economias de custos com a SQL Managed Instance?**

Estudos de caso de caso de caso de caso de caso de casos geridos pela SQL:

- [Rio Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Todos os escritos](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Para se compreender melhor os benefícios, custos e riscos associados à implantação da SQL Managed Instance, há também um estudo de Forrester: [Impacto Económico Total do MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Atualização de DNS 

**Posso refrescar o DNS?**

Atualmente, não fornecemos uma funcionalidade para atualizar a configuração do servidor DNS para SQL Managed Instance.

A configuração do DNS é eventualmente renovada:

- Quando o contrato de arrendamento DHCP expirar.
- No upgrade da plataforma.

Como uma solução alternativa, desclasse a SQL Managed Instance para 4 vCore e atualize-a novamente depois. Isto tem um efeito colateral de refrescar a configuração do DNS.


## <a name="ip-address"></a>Endereço IP

**Posso ligar-me à SQL Managed Instance utilizando o endereço IP?**

A ligação à 22-19 usando o endereço IP não é suportada. SQL Managed Instance host name maps to a load balancer in front of the SQL Managed Instance virtual cluster. Como um cluster virtual poderia hospedar várias Ocorrências geridas SQL, as ligações não podem ser encaminhadas para a adequada SQL Managed Instance sem especificar o nome explicitamente.

Para obter mais informações sobre a arquitetura de cluster virtual SQL Managed Instance, consulte [a arquitetura de conectividade de cluster virtual.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Um SQL Managed Instance pode ter um endereço IP estático?**

Em situações raras mas necessárias, podemos precisar de fazer uma migração on-line de um SQL Managed Instance para um novo cluster virtual. Se necessário, esta migração deve-se a mudanças na nossa pilha de tecnologia destinadas a melhorar a segurança e a fiabilidade do serviço. Migrar para um novo cluster virtual resulta na alteração do endereço IP que está mapeado para o nome de anfitrião SQL Managed Instance. O serviço SQL Managed Instance não reclama suporte a endereço IP estático e reserva-se o direito de alterá-lo sem aviso prévio como parte de ciclos de manutenção regulares.

Por esta razão, desencorajamos vivamente a imutabilidade do endereço IP, uma vez que poderia causar tempo de inatividade desnecessário.

## <a name="change-time-zone"></a>Alterar fuso horário

**Posso alterar o fuso horário para uma ocorrência gerida pelo SQL?**

A configuração do fuso horário pode ser definida quando uma SqL Managed Instance é prevista pela primeira vez. A alteração do fuso horário da atual SQL Managed Instance não é suportada. Para mais detalhes, consulte [as limitações do fuso horário.](timezones-overview.md#limitations)

As soluções alternativas incluem a criação de um novo SQL Managed Instance com o fuso horário adequado e, em seguida, realizar uma cópia de segurança manual e restaurar, ou o que recomendamos, realizar uma [restauração pontual transversal.](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)


## <a name="resolve-performance-issues"></a>Resolver problemas de desempenho

**Como resolvo problemas de desempenho com a minha SQL Managed Instance?**

Para uma comparação de desempenho entre SQL Managed Instance e SQL Server, um bom ponto de partida são [as melhores práticas para comparação de desempenho entre O Azure SQL Managed Instance e o artigo do SQL Server.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

O carregamento de dados é frequentemente mais lento em SQL Managed Instance do que no SQL Server devido ao modelo obrigatório de recuperação total e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) no produção de registo de transações. Por vezes, isto pode ser trabalhado carregando dados transitórios em temporário em vez de base de dados de utilizadores, ou usando colunas agrupadas ou tabelas otimizadas pela memória.


## <a name="restore-encrypted-backup"></a>Restaurar cópias de segurança encriptadas

**Posso restaurar a minha base de dados encriptada para a SQL Managed Instance?**

Sim, não precisa de desencriptar a sua base de dados para a restaurar para a SQL Managed Instance. É necessário fornecer um certificado/chave utilizado como protetor de chave de encriptação no sistema de origem para o SQL Managed Instance para ser capaz de ler dados a partir do ficheiro de backup encriptado. Há duas maneiras possíveis de fazê-lo:

- *Faça o upload do certificado-protector para a SQL Managed Instance*. Pode ser feito apenas com o PowerShell. O [guião da amostra](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descreve todo o processo.
- *Faça o upload assimétrico do protector-chave para o Cofre da Chave Azure (AKV) e aponte para o SQL Managed Instance*. Esta abordagem assemelha-se a uma caixa de utilização de TDE (BYOK) que também utiliza a integração AKV para armazenar a chave de encriptação. Se não quiser utilizar a chave como protetor de chave de encriptação e apenas quiser disponibilizar a chave para a SQL Managed Instance para restaurar a(s) base de dados encriptada, siga as instruções para configurar o [BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption), e não verifique a caixa de verificação *Faça da chave selecionada o protetor TDE predefinido*.

Assim que disponibilizar o protetor de encriptação para a SQL Managed Instance, pode proceder ao procedimento de restauro da base de dados padrão.

## <a name="migrate-from-sql-database"></a>Migrar da Base de Dados SQL 

**Como posso migrar da Base de Dados Azure SQL para a SQL Managing Instance?**

SQL Managed Instance oferece os mesmos níveis de desempenho por computação e tamanho de armazenamento que a Base de Dados Azure SQL. Se pretender consolidar dados num único caso, ou simplesmente precisar de uma funcionalidade suportada exclusivamente em SQL Managed Instance, pode migrar os seus dados utilizando a funcionalidade de exportação/importação (BACPAC).

## <a name="password-policy"></a>Política de senha 

**Que políticas de palavra-passe são aplicadas para os logins SQL De Instância Gerida SQL?**

A política de palavra-passe de sql Managed Instance para logins SQL herda as políticas da plataforma Azure que são aplicadas aos VMs formando cluster virtual mantendo o exemplo gerido. De momento não é possível alterar nenhuma destas definições, uma vez que estas definições são definidas pelo Azure e herdadas por exemplo gerida.

 > [!IMPORTANT]
 > A plataforma Azure pode alterar os requisitos de política sem notificar os serviços que dependem dessas políticas.

**Quais são as políticas atuais da plataforma Azure?**

Cada login deve definir a sua palavra-passe no início do login e alterar a sua palavra-passe depois de atingir a idade máxima.

| **Política** | **Definição de Segurança** |
| --- | --- |
| Idade máxima da senha | 42 dias |
| Idade mínima da senha | 1 dia |
| Comprimento mínimo da palavra-passe | 10 caracteres |
| A palavra-passe deve satisfazer os requisitos de complexidade | Ativado |

**É possível desativar a complexidade e expiração da palavra-passe em SQL Managed Instance no nível de login?**

Sim, é possível controlar campos de CHECK_POLICY e CHECK_EXPIRATION ao nível de login. Pode verificar as definições atuais executando o seguinte comando T-SQL:

```sql
SELECT *
FROM sys.sql_logins
```

Depois disso, pode modificar as definições de login especificadas executando:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(substituir 'teste' pelo nome de login pretendido e ajustar os valores de política e de expiração)
