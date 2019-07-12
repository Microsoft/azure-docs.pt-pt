---
title: Base de dados SQL geridas FAQ de instância | Documentos da Microsoft
description: Perguntas mais frequentes (FAQ) do sobre a instância gerida da base de dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798070"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) do sobre a instância gerida da base de dados SQL

Este artigo contém muitas das questões mais comuns sobre [instância gerida de base de dados SQL](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Onde posso encontrar uma lista de funcionalidades que são suportadas na instância gerida?

Para obter uma lista das funcionalidades suportadas na instância gerida, veja [base de dados do Azure SQL em comparação com o SQL Server](sql-database-features.md).

Para diferenças de sintaxe e o comportamento entre a instância gerida de SQL Database do Azure e SQL Server no local, consulte [diferenças do T-SQL do SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Onde posso encontrar características técnicas e limites de recursos para a instância gerida?

Para as características de geração de hardware disponíveis, consulte [diferenças técnicas no gerações de hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Para os escalões de serviço disponível e suas características, consulte [diferenças técnicas entre escalões de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Onde posso encontrar bugs e problemas conhecidos?

Para bugs e problemas conhecidos, consulte [há alterações comportamentais](sql-database-managed-instance-transact-sql-information.md#Changes) e [problemas conhecidos](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Pode uma instância gerida tem o mesmo nome que o SQL Server no local?

Instância gerida tem de ter um nome que termina com *database.windows.net*. Para utilizar outra zona DNS em vez da predefinição, por exemplo, **mi outro nome**. contoso.com: 
- Utilizar CliConfig para definir um alias (a ferramenta é apenas, um wrapper de definições de registo, para que isso também poderia ser feito através da política de grupo ou script).
- Uso *CNAME* com *TrustServerCertificate = true* opção.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Como posso mover base de dados de instância gerida volta para o SQL Server ou base de dados do Azure SQL?

Pode [exportar a base de dados para bacpac](sql-database-export.md) e, em seguida [importar o ficheiro bacpac]( sql-database-import.md). Essa abordagem é recomendada se a sua base de dados é menor do que 100 GB.

Replicação transacional pode ser utilizada se a todas as tabelas na base de dados tiverem chaves primárias.

Nativo `COPY_ONLY` não não possível restaurar cópias de segurança obtidas a partir da instância gerida do SQL Server porque a instância gerida tem uma versão de base de dados superior em comparação com o SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Como posso migrar meu banco de dados de instância para uma única base de dados do Azure SQL?

Uma opção é [exportar a base de dados para um bacpac](sql-database-export.md) e, em seguida [importar o ficheiro bacpac]( sql-database-import.md). 

Esta é a abordagem recomendada se a sua base de dados é menor do que 100 GB. Replicação transacional pode ser utilizada se a todas as tabelas na base de dados tiverem chaves primárias.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Como escolher entre Gen 4 e 5 de fins de geração de hardware para a instância gerida?

Isso depende da sua carga de trabalho como alguns geração de hardware é melhor para determinados tipos de cargas de trabalho do que o outro. Embora o assunto de desempenho é bastante complexo um para simplificar, as seguintes diferenças entre as gerações de hardware que afetam o desempenho da carga de trabalho:
- Geração 4 fornece um melhor suporte de computação, pois baseia-se nos processadores físicos, em comparação com fins 5, que é baseado em vCore processadores. Isso pode ser mais vantajoso para cargas de trabalho intensivas de computação.
- Suporta a geração 5 acelerada rede resulta numa melhor largura de banda de e/s para o armazenamento remoto. Isso pode ser vantajoso para cargas de trabalho intensivas de e/s nos escalões de serviço de fins gerais. Geração 5 utiliza discos locais mais rápidos do SSD, em comparação comparados a geração 4. Isso pode ser vantajoso para cargas de trabalho intensivas de e/s nos escalões de serviço críticos de negócios.

Os clientes são aconselhados para testar o desempenho de cargas de trabalho reais de produção antes que se destina para determinar qual hardware geração funcionará melhor no seu caso.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Posso mudar a minha instância gerida geração de hardware entre Gen 4 e 5 de fins online? 

Mudança online automatizado entre gerações de hardware é possível se ambas as gerações de hardware estão disponíveis na mesma região onde a sua instância gerida está aprovisionada. Neste caso, terá uma opção na secção do escalão de preços do portal do Azure para alternar entre gerações de hardware.

Esta é uma longa operação de execução como a nova instância gerida será aprovisionada no back-end e as bases de dados automaticamente transferidos entre a instância antiga e nova. Este processo seja perfeito para os clientes.

Se ambas as gerações de hardware não são suportadas na mesma região, alterar a geração de hardware é possível, mas deve ser feito manualmente. Isto requer a aprovisionar uma nova instância na região em que a geração de hardware pretendido está disponível e manualmente fazer backup e restauração de dados entre a instância antigo e novo.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Como otimizar o desempenho da minha instância gerida? 

Instância gerida de fins gerais utiliza armazenamento remoto devido a que o tamanho dos ficheiros de dados e de registo é importante para o desempenho. Para otimizar o desempenho da camada de serviço para fins gerais, siga as instruções nesta postagem no blog.

Para cargas de trabalho intensivas de e/s considere a utilização de hardware de geração 5, e o uso de geração 4 para cargas de trabalho intensivas de computação. Para obter mais informações, consulte a secção de FAQ sobre como escolher entre gerações de hardware.

Se a sua carga de trabalho consiste em vários pequenas transações, considere a mudar o tipo de ligação de proxy para o modo de redirecionamento.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>O que é o tamanho máximo de armazenamento para a instância gerida? 

Tamanho de armazenamento para a instância gerida depende o escalão de serviço selecionado (fins gerais ou críticas para a empresa). Para limitações de armazenamento destes escalões de serviço, consulte [característica de camada de serviço](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>O armazenamento de cópia de segurança é deduzido do meu armazenamento de instância gerida? 

Não, armazenamento de cópia de segurança não é deduzido do seu espaço de armazenamento de instância gerida. O armazenamento de cópia de segurança é independente do espaço de armazenamento de instância e não está limitado no tamanho. Armazenamento de cópia de segurança é limitado pelo tempo para manter a cópia de segurança de sua instância bases de dados, configuráveis do 7 35 dias. Para obter detalhes, consulte [cópias de segurança automatizadas](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Como posso definir regras de NSG de entrada nas portas de gestão?

A funcionalidade de firewall interno configura a firewall do Windows em todas as máquinas virtuais no cluster para permitir ligações de entrada de intervalos de IP associadas apenas ao Microsoft management/implementação as máquinas virtuais e estações de trabalho de administrador seguras com eficiência a impedir invasões por meio da camada de rede.

É aqui que portas são utilizadas para:

As portas 9000 e 9003 são utilizadas pela infraestrutura do Service Fabric. Função principal do Service Fabric é manter o cluster virtual em bom estado de funcionamento e manter o estado de objetivos em termos de número de réplicas de componente.

As portas 1438, 1440 e 1452 são utilizadas pelo agente de nó. Agente de nó é uma aplicação que é executado dentro do cluster e é utilizada pelo painel de controlo para executar comandos de gestão.

Além do firewall interno na camada da rede, a comunicação é também protegida com certificados.
  
Para obter mais informações e como verificar o firewall interno, consulte [gerida de base de dados do Azure SQL firewall interno da instância](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Como pode mitigar os riscos de rede? 

Para atenuar quaisquer riscos de rede, os clientes são recomendados para aplicar um conjunto de controles e configurações de segurança:

- Ative na encriptação de dados transparente (TDE) de todas as bases de dados.
- Ative o desativar tempo de execução do CLR (Common Language). Isso é recomendável no local também.
- Utilize apenas a contas do Azure AD.
- Acesso SQL MI com a conta de baixo privilégio do DBA.
- Configure o acesso da jumpbox JiT para a conta de administrador do sistema.
- Ativar a auditoria de SQL e integrá-lo com mecanismos de alertas.
- Ative a deteção de ameaças do ATS suite.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Onde posso encontrar resultante poupanças de custos e de casos de utilização com a instância gerida?

Estudos de caso de instância gerida:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Para obter uma melhor compreensão sobre os benefícios, custos e riscos associados à implementação de instância gerida da base de dados do Azure SQL, também há estudo da Forrester um: [Total de impacto económico do MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Pode fazer a atualização DNS? 
  
Atualmente, não fornecemos um recurso para atualizar a configuração do servidor DNS para a instância gerida.

Configuração de DNS, eventualmente, é atualizada:

- Quando a concessão de DHCP expira.
- Actualização de plataforma.

Como solução, a instância gerida para 4 vCore mudar para a versão e atualizá-lo novamente mais tarde. Isso tem um efeito colateral de atualizar a configuração de DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Pode uma instância gerida tem um endereço IP estático?

Em situações raras mas necessárias, podemos necessite de realizar uma migração online de uma instância gerida para um novo cluster virtual. Se for necessário, é esta migração devido às alterações na nossa pilha de tecnologia que tem como objetivo aprimorar a segurança e fiabilidade do serviço. Migrar para um novo resultados de virtual cluster alterar o endereço IP que está mapeado para o nome de anfitrião da instância gerida. O serviço de instância gerida não solicitar suporte de endereço IP estático e se reserva o direito de alterá-la sem aviso prévio como parte de ciclos de manutenção regular.

Por esse motivo, é vivamente desencorajar depender de imutabilidade do endereço IP como ela poderia causar períodos de inatividade desnecessários.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Pode alterar o fuso horário para uma instância gerida existente?

Configuração do fuso horário pode ser definida quando uma instância gerida está aprovisionada pela primeira vez. Não é suportada a alteração de fuso horário da instância gerida existente. Para obter detalhes, consulte [limitações de fuso horário](sql-database-managed-instance-timezone.md#limitations).

Soluções alternativas incluem a criação de uma nova instância com o fuso horário adequado e, em seguida, a efetuar uma cópia de segurança manual e restaurar, ou o que é recomendável, efetuar uma [restauro de ponto no tempo de instância para várias](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Como posso resolver problemas de desempenho com a instância gerida

Para obter uma comparação de desempenho entre a instância gerida e SQL Server, é um ponto de partida [melhores práticas para comparação de desempenho entre a instância de SQL do Azure gerido e o SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Cargas de dados costumam ser mais lentas numa instância gerida do que no SQL Server devido ao modelo de recuperação completo obrigatório e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) no débito de escrita de registo de transação. Às vezes, isso pode ser contornado ao carregamento de dados transitórios para tempdb em vez de base de dados do utilizador, ou utilizar o columnstore em cluster ou tabelas com otimização de memória.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Posso restaurar a minha base de dados encriptado para instância gerida?

Sim, não precisa de desencriptar o seu banco de dados para poder restaurá-lo para a instância gerida. É necessário fornecer uma chave/certificado utilizada como um protetor de chave de encriptação no sistema de origem para a instância gerida, ser capaz de ler dados a partir do ficheiro de cópia de segurança encriptado. Existem duas maneiras possíveis de fazer isso:

- Carregar o protetor de certificado para a instância gerida. Isso pode ser feito apenas com o PowerShell. O script de exemplo descreve o processo inteiro.
- Carregar protetor de chave assimétrica para o Azure Key Vault (AKV) e apontar a instância gerida para ela. Essa abordagem é semelhante ao caso de utilização do traga-your-own-key (BYOK) TDE que também utiliza a integração AKV para armazenar a chave de encriptação. Se quiser apenas a chave carregada para AKV disponível para instância gerida para restaurar as bases de dados encriptados sem, na verdade, usar a chave como um protetor de chave de encriptação, siga as instruções para configurar a BYOK TDE e não marque a caixa de verificação marca a chave selecionada a protetor de TDE predefinido.

Depois de efetuar a encriptação protetor disponível para instância gerida, pode continuar com o procedimento de restauro de base de dados padrão.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Como posso migrar de agrupamento de base de dados do Azure SQL único ou elástico para a instância gerida? 

Gerido instância oferece os mesmos níveis de desempenho por tamanho de computação e armazenamento como outras opções de implementação de base de dados do Azure SQL. Se pretende consolidar os dados numa única instância, ou precisar simplesmente de um recurso exclusivamente na instância gerida, pode migrar os dados utilizando a funcionalidade de exportação/importação (BACPAC).
