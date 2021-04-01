---
title: Interoperabilidade de recurso com grupos de disponibilidade e ouvinte DNN
description: 'Saiba mais sobre as considerações adicionais ao trabalhar com certas funcionalidades do SQL Server e um ouvinte de rede distribuído (DNN) com um grupo de disponibilidade Always On no SQL Server em VMs Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359409"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Interoperabilidade de recurso com ouvinte AG e DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existem certas funcionalidades do SQL Server que dependem de um nome de rede virtual codificado (VNN). Como tal, ao utilizar o ouvinte de rede distribuído (DNN) com o seu grupo de disponibilidade Always On e o SQL Server em VMs Azure, poderá haver algumas considerações adicionais. 

Este artigo detalha as funcionalidades do SQL Server e a interoperabilidade com o ouvinte do grupo de disponibilidade DNN. 


## <a name="client-drivers"></a>Controladores do cliente

Para os controladores ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, os utilizadores precisam de especificar explicitamente o nome e a porta do ouvinte DNN como o nome do servidor na cadeia de ligação. Para garantir uma conectividade rápida após o failover, adicione `MultiSubnetFailover=True` à cadeia de ligação se o cliente SQL a suportar. 

## <a name="tools"></a>Ferramentas

Os utilizadores do [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) precisam de especificar explicitamente o nome e a porta do ouvinte DNN como o nome do servidor na cadeia de ligação para se ligarem ao ouvinte. 

A criação do ouvinte DNN através do SQL Server Management Studio (SSMS) GUI não é suportada atualmente. 


## <a name="availability-groups-and-fci"></a>Grupos de disponibilidade e FCI

Pode configurar um grupo de disponibilidade Always On utilizando uma instância de cluster failover (FCI) como uma das réplicas. Para que esta configuração funcione com o ouvinte DNN, a instância do [cluster de failover também deve usar o DNN,](failover-cluster-instance-distributed-network-name-dnn-configure.md) uma vez que não existe forma de colocar o endereço IP virtual FCI na lista IP AG DNN. 

Nesta configuração, o URL de ponta de espelhamento para a réplica FCI precisa de utilizar o DNN FCI. Da mesma forma, se o FCI for usado como uma réplica apenas de leitura, o encaminhamento apenas de leitura para a réplica DO FCI precisa de usar o DNN FCI. 

O formato para o ponto final espelhante é: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Por exemplo, se o seu nome DENNN FCI é `dnnlsnr` , e é a porta do ponto final `5022` espelhante da FCI, o código Transact-SQL (T-SQL) corta para criar o URL de ponto final parece: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Da mesma forma, o formato para o URL de encaminhamento apenas de leitura é: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Por exemplo, se o seu nome DNN DNS for `dnnlsnr` , e for a porta utilizada pelo alvo de leitura `1444` SQL Server FCI, o corte de código T-SQL para criar o URL de encaminhamento apenas de leitura parece: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Pode omitir a porta no URL se for a porta padrão 1433. Para um caso nomeado, configurar uma porta estática para a instância nomeada e especimentá-la no URL de encaminhamento apenas de leitura.  

## <a name="distributed-availability-group"></a>Grupo de disponibilidade distribuído

Os grupos de disponibilidade distribuídos não são atualmente suportados com o ouvinte DNN. 

## <a name="replication"></a>Replicação

Replicação transacional, de fusão e de replicação instantânea todos os suportes que substituem o ouvinte VNN pelo ouvinte DNN e porta em objetos de replicação que se ligam ao ouvinte. 

Para obter mais informações sobre como utilizar a replicação com grupos de disponibilidade, consulte [Editor e AG,](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server) [Assinante e AG,](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)Distribuidor [e AG.](/sql/relational-databases/replication/configure-distribution-availability-group)

## <a name="msdtc"></a>MSDTC

Tanto o MSDTC local como o agrupado são suportados, mas o MSDTC utiliza uma porta dinâmica, que requer um Balancer de Carga Azure standard para configurar a porta HA. Como tal, ou o VM deve utilizar uma reserva IP padrão, ou não pode ser exposto à internet. 

Defina duas regras, uma para a porta 135 do Mapper endpoint RPC e outra para a porta MSDTC real. Após o failover, modifique a regra LB para a nova porta MSDTC depois de ter alterações no novo nó. 

Se o MSDTC for local, certifique-se de permitir a comunicação de saída. 

## <a name="distributed-query"></a>Consulta distribuída 

A consulta distribuída baseia-se num servidor ligado, que pode ser configurado utilizando o ouvinte e porta AG DNN. Se a porta não for 1433, escolha a opção Utilizar outra opção **de fonte de dados** no SQL Server Management Studio (SSMS) ao configurar o seu servidor ligado. 

## <a name="filestream"></a>FileStream

O Filestream é suportado, mas não para cenários em que os utilizadores acedam à partilha de ficheiros com o âmbito de aplicação utilizando a API do Ficheiro Do Windows. 

## <a name="filetable"></a>Filetable

A filetable é suportada, mas não para cenários em que os utilizadores acedam à partilha de ficheiros com o âmbito de aplicação utilizando a API do Ficheiro Windows. 

## <a name="linked-servers"></a>Servidores ligados

Configure o servidor ligado utilizando o nome e porta do ouvinte AG DNN. Se a porta não for 1433, escolha a opção Utilizar outra opção **de fonte de dados** no SQL Server Management Studio (SSMS) ao configurar o seu servidor ligado. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes


- Qual versão SQL Server traz suporte para ouvintes AG DNN? 

   SQL Server 2019 CU8 e mais tarde.

- Qual é o tempo de insusição esperado quando o ouvinte do DNN é usado?

   Para o ouvinte do DNN, o tempo de execução será apenas o tempo de execução da AG, sem tempo adicional (como o tempo de sonda quando estiver a utilizar o Balançador de Carga Azure).

- Existe algum requisito de versão para os clientes SQL suportarem dNN com OLEDB e ODBC?

   Recomendamos `MultiSubnetFailover=True` suporte de cadeia de ligação para o ouvinte DNN. Está disponível a partir do SQL Server 2012 (11.x).

- São necessárias alterações na configuração do SQL Server para eu utilizar o ouvinte DNN? 

   O SQL Server não requer qualquer alteração de configuração para utilizar o DNN, mas algumas funcionalidades do SQL Server podem exigir mais consideração. 

- A DNN suporta clusters de várias sub-redes?

   Sim. O cluster liga o DNN em DNS com os endereços IP físicos de todas as réplicas na disponibilidade, independentemente da sub-rede. O cliente SQL tenta todos os endereços IP do nome DNS independentemente da sub-rede. 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte: 

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Sempre no grupo de disponibilidade](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

