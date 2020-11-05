---
title: Configure a replicação para Azure SQL Edge
description: Saiba como configurar a replicação para Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 3b424ece9207328d87068160f78ebc78a3bd1a8d
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395228"
---
# <a name="configure-replication-to-azure-sql-edge"></a>Configure a replicação para Azure SQL Edge 

Pode configurar uma instância de Azure SQL Edge como assinante push para replicação transacional de ida ou replicação instantânea. Este caso não pode funcionar como o editor ou o distribuidor para uma configuração de replicação transacional. Note que a Azure SQL Edge não suporta a replicação de fusão, a replicação entre pares ou a publicação da Oracle.

## <a name="supported-configurations"></a>Configurações suportadas
  
- O caso do Azure SQL Edge deve ser um assinante de impulso para uma editora.
- O editor e o distribuidor podem ser:
   - Um caso de SQL Server a funcionar no local, ou um caso de SQL Server a funcionar numa máquina virtual Azure. Para obter mais informações, consulte [o SQL Server na visão geral das máquinas virtuais Azure](../azure-sql/virtual-machines/index.yml). As instâncias do SQL Server devem estar a usar uma versão mais tarde do que o SQL Server 2016.
   - Um exemplo de Azure SQL Managed Instance. A SQL Managed Instance pode acolher bases de dados de editores, distribuidores e assinantes. Para obter mais informações, consulte [a replicação com a sql Database Managed Instance](/azure/sql-database/replication-with-sql-database-managed-instance/).

- A base de dados de distribuição e os agentes de replicação não podem ser colocados num caso de Azure SQL Edge.  

> [!NOTE]
> Se tentar configurar a replicação utilizando uma versão não apoiada, poderá receber os seguintes dois erros: MSSQL_REPL20084 ("O processo não conseguiu ligar-se ao Assinante.") e MSSQL_REPL40532 ("Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.").  

## <a name="remarks"></a>Observações

Os seguintes requisitos e boas práticas são importantes para entender à medida que configura a replicação:

- Pode configurar a replicação utilizando [o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Também pode fazê-lo executando declarações Transact-SQL na editora, utilizando o SQL Server Management Studio ou [o Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
- Para replicar a uma instância de Azure SQL Edge, tem de utilizar a autenticação do SQL Server para iniciar sedições.
- As mesas replicadas devem ter uma chave primária.
- Uma única publicação no SQL Server pode suportar tanto os subscritores Azure SQL Edge como SQL Server (no local e o SQL Server numa máquina virtual Azure).  
- A gestão, monitorização e resolução de problemas de replicação devem ser realizadas a partir da instância do SQL Server.  
- Apenas as subscrições push para Azure SQL Edge são suportadas.  
- Apenas `@subscriber_type = 0` é suportado no procedimento armazenado para `sp_addsubscription` Azure SQL Edge.  
- Azure SQL Edge não suporta replicação bi-direcional, imediata, updatable ou peer-to-peer.
- O Azure SQL Edge suporta apenas um subconjunto de funcionalidades disponíveis no SQL Server ou na SQL Managed Instance. Se tentar replicar uma base de dados (ou objetos dentro da base de dados) que contenha uma ou mais funcionalidades não apoiadas, a tentativa falha. Por exemplo, se tentar replicar uma base de dados que contenha objetos com tipos de dados espaciais, receberá um erro. Para obter mais informações, consulte [funcionalidades suportadas do Azure SQL Edge](features.md).

## <a name="initialize-reference-data-on-an-instance-of-azure-sql-edge"></a>Inicialize dados de referência sobre uma instância de Azure SQL Edge

É melhor rubricar o seu caso com dados de referência que mudam ao longo do tempo. Por exemplo, é melhor atualizar os modelos de machine learning no seu exemplo de Azure SQL Edge, depois de terem sido treinados numa instância sql Server. Eis como inicializar o seu caso num cenário destes:

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server.  
2. Na instância do SQL Server, utilize as declarações do Novo Assistente de **Subscrição** ou Transact-SQL para criar um impulso à subscrição do Azure SQL Edge.  
3. Pode inicializar a base de dados replicada no Azure SQL Edge utilizando um instantâneo gerado pelo agente instantâneo, e distribuído e entregue pelo agente de distribuição. Em alternativa, pode inicializar utilizando uma cópia de segurança da base de dados do editor. Lembre-se que se a cópia de segurança da base de dados contiver objetos ou funcionalidades não suportadas pelo Azure SQL Edge, a operação de restauro falha.

## <a name="limitations"></a>Limitações

As seguintes opções não são suportadas para subscrições Azure SQL Edge:

- Associação de grupos de ficheiros de cópia  
- Esquemas de partição de mesa de cópia  
- Esquemas de partição de índices de cópia  
- Copiar estatísticas definidas pelo utilizador  
- Vinculações padrão de cópia  
- Vinculações de regras de cópia  
- Copiar índices de texto completo  
- Cópia XML XSD  
- Copiar índices XML  
- Permissões de cópia  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Atributo de compressão de dados de cópia  
- Atributo de coluna de cópias esparsa  
- Copy filestream, `hierarchyid` ou tipos de dados espaciais
- Converter `hierarchyid` para tipos de dados MAX  
- Converter o espaço para os tipos de dados MAX  
- Copiar propriedades estendidas  
- Permissões de cópia  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição push. Para obter mais informações, consulte:
  
- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma subscrição push](/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome do servidor Azure SQL Edge e IP como assinante (por exemplo, **myEdgeinstance,1433),** e um nome de base de dados na instância Azure SQL Edge como base de dados de destino (por exemplo, **AdventureWorks).**  

## <a name="next-steps"></a>Passos seguintes  

- [Criar uma publicação](/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de replicação](/sql/relational-databases/replication/types-of-replication)
- [Monitorização (replicação)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma subscrição](/sql/relational-databases/replication/initialize-a-subscription)