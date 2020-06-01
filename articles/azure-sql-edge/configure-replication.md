---
title: Configure a replicação para Azure SQL Edge (Pré-visualização)
description: Saiba como configurar a replicação para Azure SQL Edge (Preview)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6249d72ba43bf59a2862595f40adf2d1ac5a6346
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235174"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configure a replicação para Azure SQL Edge (Pré-visualização) 

O exemplo de Azure SQL Edge pode ser configurado como o assinante push para uma replicação transacional de ida ou réplica instantânea. A exemplo de Azure SQL Edge não pode funcionar como o editor ou o distribuidor para uma configuração de replicação transacional. Replicação de fusão, replicação P2P, A Oracle Publishing não são suportados com Azure SQL Edge.

## <a name="supported-configurations"></a>**Configurações suportadas:**
  
- A exemplo de Azure SQL Edge deve ser um assinante de impulso para uma editora.
- O editor e o distribuidor podem ser ambos
   - Um caso de SQL Server a funcionar no local ou um caso de SQL Server a funcionar numa máquina virtual Azure. Para obter mais informações, consulte [o SQL Server na visão geral das máquinas virtuais Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). As instâncias do SQL Server devem estar a utilizar uma versão superior à SQL Server 2016.
   - Um exemplo de Azure SQL Managed Instance. A Managed Instance pode acolher bases de dados de editores, distribuidores e assinantes. Para obter mais informações, consulte [a replicação com a sql Database Managed Instance](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- A base de dados de distribuição e os agentes de replicação não podem ser colocados numa instância Azure SQL Edge.  

> [!NOTE]
> Tentar configurar a replicação utilizando uma versão não apoiada pode resultar em número de erro MSSQL_REPL20084 (O processo não conseguiu ligar-se ao Assinante.) e MSSQL_REPL40532 (Não pode abrir o servidor \<name> solicitado pelo login. O login falhou.).  

Para utilizar todas as funcionalidades do Azure SQL Edge, deve utilizar as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e das Ferramentas de [Dados do SqL Server](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Observações

- A replicação pode ser configurada utilizando [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando declarações Transact-SQL na editora utilizando o SQL Server Management Studio ou o [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
- A replicação só pode utilizar logins de autenticação do SQL Server para se ligar a uma instância Azure SQL Edge.
- As mesas replicadas devem ter uma chave primária.
- Uma única publicação no SQL Server pode suportar tanto os subscritores Azure SQL Edge como SQL Server (no local e o SQL Server numa máquina virtual Azure).  
- A gestão, monitorização e resolução de problemas de replicação devem ser realizadas a partir da instância do SQL Server.  
- Apenas as subscrições push para Azure SQL Edge são suportadas.  
- Apenas `@subscriber_type = 0` é suportado em **sp_addsubscription** para Azure SQL Edge.  
- A Azure SQL Edge não suporta replicação bidirecional, imediata, updatable ou peer to peer.
- O Azure SQL Edge suporta apenas um subconjunto de funcionalidades disponíveis no SQL Server ou SQL Managed Instance, uma vez que tal tentativa de replicar uma base de dados (ou objetos dentro da base de dados) que contenham uma ou mais funcionalidades não apoiadas resultará numa falha. Por exemplo, tentar replicar uma base de dados que contenha objetos com tipos de dados espaciais resultará num erro. Para obter mais informações sobre as funcionalidades suportadas pela Azure SQL Edge, consulte [as funcionalidades suportadas do Azure SQL Edge](features.md).

## <a name="scenarios"></a>Cenários  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inicialização de dados de referência numa instância Edge

Um cenário comum onde a replicação pode ser útil é quando há necessidade de inicializar a instância de borda com dados de referência que mudam ao longo do tempo. Por exemplo, atualizar os modelos ML na instância Edge depois de terem sido treinados numa instância sql Server.

1. Crie uma publicação de replicação transacional numa base de dados do SQL Server.  
2. Na instância do SQL Server, utilize as declarações do Novo Assistente de **Subscrição** ou Transact-SQL para criar um impulso à subscrição do Azure SQL Edge.  
3. A base de dados replicada no Azure SQL Edge pode ser inicializada utilizando um instantâneo gerado pelo agente instantâneo e distribuído e entregue pelo agente de distribuição ou utilizando uma cópia de segurança da base de dados do editor. Mais uma vez, se a cópia de segurança da base de dados contiver objetos/funcionalidades que não são suportados pelo Azure SQL Edge, a operação de restauro falhará.

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
- Copiar tipos de dados, hierárquios ou espaciais.
- Converter hierarquias para tipos de dados MAX  
- Converter o espaço para os tipos de dados MAX  
- Copiar propriedades estendidas  
- Permissões de cópia  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma subscrição push. Para obter mais informações, consulte:
  
- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma Subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) utilizando o nome/IP do servidor Azure SQL Edge como assinante (por exemplo **myEdgeinstance,1433)** e um nome de base de dados na instância Azure SQL Edge como base de dados de destino (por **exemplo, AdventureWorks).**  

## <a name="see-also"></a>Ver também  

- [Criar uma Publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma subscrição push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Types of Replication](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication) (Tipos de Replicação)
- [Monitorização (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Subscrição](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


