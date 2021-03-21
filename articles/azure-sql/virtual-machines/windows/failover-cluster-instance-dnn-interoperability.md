---
title: Interoperabilidade de recurso com SQL Server FCI & DNN
description: 'Saiba mais sobre as considerações adicionais ao trabalhar com certas funcionalidades do SQL Server e um recurso de nome de rede distribuído (DNN) com uma instância de cluster de failover no SQL Server em VMs Azure. '
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
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358219"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Interoperabilidade de recurso com SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existem certas funcionalidades do SQL Server que dependem de um nome de rede virtual codificado (VNN). Como tal, ao utilizar o recurso de nome de rede distribuído (DNN) com a sua instância de cluster de failover e o SQL Server em VMs Azure, existem algumas considerações adicionais. 

Neste artigo, aprenda a configurar o pseudónimo da rede ao utilizar o recurso DNN, bem como quais as funcionalidades do SQL Server que requerem consideração adicional.

## <a name="create-network-alias-fci"></a>Criar pseudónimos de rede (FCI)

Alguns componentes do lado do servidor dependem de um valor VNN codificado e requerem um pseudónimo de rede que mapeie o VNN para o nome DNN DNS para funcionar corretamente. Siga os [passos](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) em Criar um pseudónimo do servidor para criar um pseudónimo que mapeie o VNN para o nome DNN DNS. 

Para um caso padrão, pode mapear o VNN para o nome DNN DNS diretamente, de modo que VNN = nome DNS DNN.
Por exemplo, se o nome VNN for `FCI1` , o nome da instância é , e o `MSSQLSERVER` DNN é `FCI1DNN` (clientes anteriormente ligados `FCI` a , e agora se ligam a `FCI1DNN` - então mapear o VNN `FCI1` para o DNN `FCI1DNN` . 

Para um caso nomeado, o mapeamento de pseudónimos da rede deve ser feito para o caso completo, de modo que `VNN\Instance`  =  `DNN\Instance` . Por exemplo, se o nome VNN for `FCI1` , o nome da instância é , e o `instA` DNN é `FCI1DNN` (clientes anteriormente ligados `FCI1\instA` a , e agora se ligam a `FCI1DNN\instaA` - então mapear o VNN `FCI1\instaA` para o DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Controladores do cliente

Para os controladores ODBC, OLEDB, ADO.NET, JDBC, PHP e Node.js, os utilizadores precisam de especificar explicitamente o nome DNN DNS como o nome do servidor na cadeia de ligação. Para garantir uma conectividade rápida após o failover, adicione `MultiSubnetFailover=True` à cadeia de ligação se o cliente SQL a suportar. 

## <a name="tools"></a>Ferramentas

Os utilizadores do [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)e [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) precisam de especificar explicitamente o nome DNN DNS como o nome do servidor na cadeia de ligação. 

## <a name="availability-groups-and-fci"></a>Grupos de disponibilidade e FCI

Pode configurar um grupo de disponibilidade Always On utilizando uma instância de cluster de failover como uma das réplicas. Nesta configuração, o URL de ponta de espelhamento para a réplica FCI precisa de utilizar o DNN FCI. Da mesma forma, se o FCI for usado como uma réplica apenas de leitura, o encaminhamento apenas de leitura para a réplica DO FCI precisa de usar o DNN FCI. 

O formato para o ponto final espelhante é: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Por exemplo, se o seu nome DNN DNS é `dnnlsnr` , e é a porta do ponto final `5022` espelhante da FCI, o código Transact-SQL (T-SQL) corta para criar o URL de ponto final parece: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Da mesma forma, o formato para o URL de encaminhamento apenas de leitura é: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Por exemplo, se o seu nome DNN DNS for `dnnlsnr` , e for a porta utilizada pelo alvo de leitura `1444` SQL Server FCI, o corte de código T-SQL para criar o URL de encaminhamento apenas de leitura parece: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Pode omitir a porta no URL se for a porta padrão 1433. Para um caso nomeado, configurar uma porta estática para a instância nomeada e especimentá-la no URL de encaminhamento apenas de leitura.  

## <a name="replication"></a>Replicação

A replicação tem três componentes: Editor, Distribuidor, Assinante. Qualquer um destes componentes pode ser um caso de cluster de falha. Como o FCI VNN é fortemente utilizado na configuração de replicação, tanto explicitamente como implicitamente, um pseudónimo de rede que mapeia o VNN para o DNN pode ser necessário para a replicação funcionar. 

Continue a usar o nome VNN como o nome FCI dentro da replicação, mas crie um pseudónimo de rede nas seguintes situações remotas *antes de configurar a replicação:*

| **Componente de replicação (FCI com DNN)** | **Componente remoto** | **Mapa de pseudónimos de rede** | **Servidor com mapa de rede**| 
|---------|---------|---------|-------- | 
|Publisher | Distribuidor | Editor VNN para Editor DNN| Distribuidor| 
|Distribuidor|Assinante |Distribuidor VNN ao Distribuidor DNN| Assinante | 
|Distribuidor|Publisher | Distribuidor VNN ao Distribuidor DNN | Publisher| 
|Assinante| Distribuidor| VNN assinante para Assinante DNN | Distribuidor| 

Por exemplo, assuma que tem um Editor configurado como um FCI usando DNN numa topologia de replicação, e o Distribuidor é remoto. Neste caso, crie um pseudónimo de rede no servidor distribuidor para mapear a Editora VNN para o Editor DNN: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Configure o nome DNN DNS como pseudónimo de rede utilizando o SqL Server Configuration Manager." :::

Utilize o nome completo para um caso nomeado, como o seguinte exemplo de imagem: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Utilize o nome completo do caso ao configurar um pseudónimo de rede para uma instância nomeada." :::

## <a name="database-mirroring"></a>Espelhamento da base de dados

Pode configurar a base de dados espelhada com um FCI como parceiro de espelhamento de base de dados. Configure-o utilizando [o Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) em vez do SQL Server Management Studio GUI. A utilização do T-SQL assegurará que o ponto final espelhante da base de dados seja criado utilizando o DNN em vez do VNN. 

Por exemplo, se o nome DNN DNS for `dnnlsnr` , e o ponto final espelhado da base de dados é 7022, o seguinte código T-SQL configura o parceiro de espelhamento da base de dados: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Para o acesso ao cliente, a propriedade **Failover Partner** pode lidar com a falha de espelhamento da base de dados, mas não a falha da FCI. 

## <a name="msdtc"></a>MSDTC

O FCI pode participar em transações distribuídas coordenadas pelo Coordenador de Transações Distribuídas da Microsoft (MSDTC). Embora tanto o MSDTC agrupado como o MSDTC local sejam suportados com a FCI DNN, em Azure, um equilibrador de carga ainda é necessário para o MSDTC agrupado. O DNN definido na FCI não substitui o requisito do Balançador de Carga Azure para o MSDTC agrupado em Azure. 

## <a name="filestream"></a>FileStream

Embora o FileStream seja suportado para uma base de dados num FCI, o acesso ao FileStream ou ao FileTable utilizando APIs do Sistema de Ficheiros com DNN não é suportado. 

## <a name="linked-servers"></a>Servidores ligados

É suportado um servidor ligado com um DNN FCI. Ou utiliza o DNN diretamente para configurar um servidor ligado ou usa um pseudónimo de rede para mapear o VNN para o DNN. 


Por exemplo, para criar um servidor ligado com o nome DNN DNS `dnnlsnr` para o `insta1` nome, utilize o seguinte comando Transact-SQL (T-SQL):

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Em alternativa, pode criar o servidor ligado utilizando o nome de rede virtual (VNN), mas terá de definir um pseudónimo de rede para mapear o VNN para o DNN. 

Por exemplo, por exemplo, nome `insta1` VNN `vnnname` , e nome DNN , use o `dnnlsnr` seguinte comando Transact-SQL (T-SQL) para criar um servidor ligado utilizando o VNN:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Em seguida, crie um pseudónimo de rede `vnnname\insta1` para mapear. `dnnlsnr\insta1` 



## <a name="frequently-asked-questions"></a>Perguntas mais frequentes


- Qual versão SQL Server traz suporte DNN? 

   SQL Server 2019 CU2 e mais tarde.

- Qual é o tempo de insusição esperado quando o DNN é usado?

   Para o DNN, o tempo de execução será apenas o tempo de execução do FCI, sem qualquer tempo adicionado (como o tempo de sonda quando estiver a utilizar o Azure Load Balancer).

- Existe algum requisito de versão para os clientes SQL suportarem dNN com OLEDB e ODBC?

   Recomendamos `MultiSubnetFailover=True` suporte de cadeia de ligação para DNN. Está disponível a partir do SQL Server 2012 (11.x).

- São necessárias alterações na configuração do SQL Server para eu utilizar o DNN? 

   O SQL Server não requer qualquer alteração de configuração para utilizar o DNN, mas algumas funcionalidades do SQL Server podem exigir mais consideração. 

- A DNN suporta clusters de várias sub-redes?

   Sim. O cluster liga o DNN em DNS com os endereços IP físicos de todos os nós no cluster, independentemente da sub-rede. O cliente SQL tenta todos os endereços IP do nome DNS independentemente da sub-rede. 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte: 

- [Tecnologias de cluster windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server falha casos de cluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

