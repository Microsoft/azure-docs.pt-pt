---
title: Configure a replicação para Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre a configuração da replicação ao Azure SQL Edge (Pré-visualização)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596947"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configure a replicação para Azure SQL Edge (Pré-visualização) 

A instância Azure SQL Edge pode ser configurada como o subscritor push para uma replicação transacional de ida ou de instantâneo. A instância Azure SQL Edge não pode funcionar como editora ou distribuidora para uma configuração de replicação transacional. A replicação de fusão, a replicação P2P, a Oracle Publishing não são suportadas com a Azure SQL Edge.

## <a name="supported-configurations"></a>**Configurações suportadas:**
  
- A instância Azure SQL Edge deve ser um subscritor push para uma editora.
- O editor e o distribuidor podem ser ambos
   - Uma instância de SQL Server a funcionar no local ou uma instância de SQL Server a funcionar numa máquina virtual Azure. Para mais informações, consulte [o SQL Server na visão geral das Máquinas Virtuais Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). As instâncias do SQL Server devem estar a utilizar uma versão maior do que o SQL Server 2016.
   - Uma instância de Azure SQL Database Managed Instance. A Managed Instance pode hospedar bases de dados de editores, distribuidores e assinantes. Para mais informações, consulte Replicação com A Instância Gerida pela Base de [Dados SQL](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- A base de dados de distribuição e os agentes de replicação não podem ser colocados numa instância Azure SQL Edge.  

> [!NOTE]
> Tentar configurar a replicação utilizando uma versão não suportada pode resultar num número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQL_REPL40532 (Não pode abrir o nome do servidor \<> solicitado pelo login. O login falhou.).  

Para utilizar todas as funcionalidades do Azure SQL Edge, deve utilizar as versões mais recentes do Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de Dados do [Servidor SQL](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Observações

- A replicação pode ser configurada utilizando o Estúdio de Gestão de [Servidores SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando declarações transact-SQL na editora utilizando o SQL Server Management Studio ou [o Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
- A replicação só pode utilizar logins de autenticação do Servidor SQL para se ligar a uma instância De SQL Edge Azure.
- As mesas replicadas devem ter uma chave primária.
- Uma única publicação no SQL Server pode suportar tanto os subscritores do Azure SQL Edge como do SQL Server (no local e do SQL Server numa máquina virtual Azure).  
- A gestão da replicação, monitorização e resolução de problemas devem ser realizadas a partir do Servidor SQL no local.  
- Apenas são suportadas as subscrições push para o Azure SQL Edge.  
- Apenas `@subscriber_type = 0` é suportado em **sp_addsubscription** para Azure SQL Edge.  
- O Azure SQL Edge não suporta replicação bidirecional, imediata, atualizável ou peer to peer to peer.
- O Azure SQL Edge apenas suporta um subconjunto de funcionalidades disponíveis no SQL Server ou na Azure SQL Database Managed Instance, como tal uma tentativa de replicar uma base de dados (ou objetos dentro da base de dados) que contenham uma ou mais funcionalidades não suportadas resultará numa falha. Por exemplo, tentar replicar uma base de dados que contenha objetos com tipos de dados espaciais resultará num erro. Para obter mais informações sobre as funcionalidades suportadas pelo Azure SQL Edge, consulte [as funcionalidades suportadas do Azure SQL Edge](features.md).

## <a name="scenarios"></a>Cenários  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inicializar dados de referência numa instância edge

Um cenário comum em que a replicação pode ser útil é quando há necessidade de inicializar a instância de borda com dados de referência que mudam ao longo do tempo. Por exemplo, atualizar os modelos ML na instância Edge depois de terem sido treinados numa instância de SQL Server no local.

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server no local.  
2. No sql server no local, utilize o **Novo Assistente de Subscrição** ou declarações Transact-SQL para criar um impulso para a subscrição do Azure SQL Edge.  
3. A base de dados replicada no Azure SQL Edge pode ser inicializada utilizando um instantâneo gerado pelo agente instantâneo e distribuído e entregue pelo agente de distribuição ou utilizando uma cópia de segurança da base de dados do editor. Mais uma vez, se a cópia de segurança da base de dados contiver objetos/funcionalidades que não são suportadas pelo Azure SQL Edge, a operação de restauro falhará.

## <a name="limitations"></a>Limitações

As seguintes opções não são suportadas para subscrições Azure SQL Edge:

- Copy file groups association  
- Esquemas de partilha de tabelas de cópias  
- Esquemas de partilha de índices de cópia  
- Copiar estatísticas definidas pelo utilizador  
- Encadernações por predefinição de cópia  
- Encadernações de regras de cópia  
- Copiar índices de texto completo  
- Cópia XML XSD  
- Copiar índices XML  
- Cópia de permissões  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Atribuir a compressão de dados de cópia  
- Copiar atributo de coluna escassa  
- Copiar tipos de dados, hieráides ou espaciais.
- Converter hieráides em tipos de dados MAX  
- Converter tipos de dados espaciais para MAX  
- Copiar propriedades estendidas  
- Cópia de permissões  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição push. Para obter mais informações, consulte:
  
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma Subscrição Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome/IP do servidor Azure SQL Edge como assinante (por **exemplo, myEdgeinstance,1433**) e um nome de base de dados na instância Azure SQL Edge como base de dados de destino (por **exemplo, AdventureWorks).**  

## <a name="see-also"></a>Ver também  

- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


