---
title: Configure o ouvinte DNN para o grupo de disponibilidade
description: Saiba como configurar um ouvinte de rede distribuído (DNN) para substituir o seu ouvinte de rede virtual (VNN) e encaminhar o tráfego para o seu grupo de disponibilidade Always On no SQL Server em Azure VM.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7c3950b1aeae2b4e90b1ae4acfb9439c2cb159ca
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026007"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Configure um ouvinte DNN para um grupo de disponibilidade
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Com o SQL Server em VMs Azure, o nome de rede distribuído (DNN) encaminha o tráfego para o recurso agrupado apropriado. Proporciona uma forma mais fácil de se ligar a um grupo de disponibilidade Always On (AG) do que o ouvinte de rede virtual (VNN), sem a necessidade de um Equilibrador de Carga Azure.

Este artigo ensina-o a configurar um ouvinte DNN para substituir o ouvinte VNN e encaminhar o tráfego para o seu grupo de disponibilidade com o SQL Server em VMs Azure para uma elevada disponibilidade e recuperação de desastres (HADR).

A funcionalidade de ouvinte dNN está atualmente disponível apenas a partir do SQL Server 2019 CU8 no Windows Server 2016 e posteriormente.

Para uma opção de conectividade alternativa, considere um [ouvinte VNN e um Balanceador de Carga Azure.](availability-group-vnn-azure-load-balancer-configure.md)

## <a name="overview"></a>Descrição Geral

Um ouvinte de rede distribuído (DNN) substitui o ouvinte de grupo de disponibilidade de rede virtual tradicional (VNN) quando utilizado com [grupos de disponibilidade Always On em VMs do SQL Server](availability-group-overview.md). Isto anula a necessidade de um Balançador de Carga Azure para encaminhar o tráfego, simplificando a implantação, manutenção e melhorando o failover.

Utilize o ouvinte DNN para substituir um ouvinte VNN existente, ou, em alternativa, utilize-o em conjunto com um ouvinte VNN existente de modo a que o seu grupo de disponibilidade tenha dois pontos de ligação distintos - um utilizando o nome do ouvinte VNN (e porta se não estiver predefinido), e outro utilizando o nome e porta do ouvinte DNN.

> [!CAUTION]
> O comportamento de encaminhamento ao utilizar um DNN difere quando se utiliza um VNN. Não utilize a porta 1433. Para saber mais, consulte a secção [de consideração](#port-considerations) do Porto mais tarde neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar os passos deste artigo, já deve ter:

- SQL Server 2019 na CU8 ou mais tarde, no Windows Server 2016 e mais tarde
- Decidiu que o nome da rede distribuída é a opção de conectividade adequada [para a sua solução HADR](hadr-cluster-best-practices.md#connectivity).
- Configurar o seu [grupo de disponibilidade Always On](availability-group-overview.md). 
- Instalou a versão mais recente do [PowerShell.](/powershell/azure/install-az-ps) 
- Identificou a porta única que utilizará para o ouvinte do DNN. A porta utilizada para um ouvinte DNN deve ser única em todas as réplicas do grupo de disponibilidade ou da instância de cluster failover.  Nenhuma outra ligação pode partilhar a mesma porta.



## <a name="create-script"></a>Criar script

Utilize o PowerShell para criar o recurso de nome de rede distribuído (DNN) e associá-lo ao seu grupo de disponibilidade.

Para tal, siga estes passos:

1. Abra um editor de texto, como o Bloco de Notas.
1. Copiar e colar o seguinte script:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Guarde o script como um `.ps1` ficheiro, tal como `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Executar script

Para criar o ouvinte DNN, execute o script passando em parâmetros para o nome do grupo de disponibilidade, nome do ouvinte e porta.

Por exemplo, assumindo um nome de grupo de disponibilidade de `ag1` , nome do ouvinte de , e porta ouvinte como , siga estes `dnnlsnr` `6789` passos:

1. Abra uma ferramenta de interface de linha de comando, como o pedido de comando ou o PowerShell.
1. Navegue para onde guardou o `.ps1` script, como c:\Documentos.
1. Execute o guião: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Por exemplo:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Verificar ouvinte

Utilize o SQL Server Management Studio ou o Transact-SQL para confirmar que o seu ouvinte DNN foi criado com sucesso.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Expandir **os ouvintes do Grupo de Disponibilidade** no [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para ver o seu ouvinte DNN:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Ver o ouvinte DNN em disponibilidade de ouvintes do grupo no SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Utilize o Transact-SQL para visualizar o estado do ouvinte DNN:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Um valor de `1` `is_distributed_network_name` indicação de que o ouvinte é um ouvinte de rede distribuído (DNN):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Utilize sys.availability_group_listeners para identificar ouvintes da DNN que têm um valor de 1 em is_distributed_network_name":::

## <a name="update-connection-string"></a>Cadeia de ligação de atualização

Atualize as cadeias de ligação para aplicações de modo a que se conectem ao ouvinte DNN. Para garantir uma conectividade rápida após o failover, adicione `MultiSubnetFailover=True` à cadeia de ligação se o cliente SQL a suportar.

## <a name="test-failover"></a>Ativação pós-falha de teste

Teste de falha do grupo de disponibilidade para garantir a funcionalidade.

Para testar o failover, siga estes passos:

1. Ligue-se ao ouvinte DNN ou a uma das réplicas utilizando o [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Expandir **sempre no grupo de disponibilidade** no Explorador de **Objetos.**
1. Clique com o botão direito no grupo de disponibilidade e escolha **Failover** para abrir o **Assistente de Falha**.
1. Siga as instruções para escolher um alvo de failover e falhe o grupo de disponibilidade para uma réplica secundária.
1. Confirme que a base de dados está sincronizada na nova réplica primária.
1. (Opcional) Falha na primária original, ou noutra réplica secundária.

## <a name="test-connectivity"></a>Testar conectividade

Teste a conectividade ao seu ouvinte DNN com estes passos:

1. Open [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Ligue-se ao seu ouvinte DNN.
1. Abra uma nova janela de consulta e verifique a réplica a que está ligado ao correr `SELECT @@SERVERNAME` .
1. Falhe o grupo de disponibilidade para outra réplica.
1. Após um período de tempo razoável, corra `SELECT @@SERVERNAME` para confirmar que o seu grupo de disponibilidade está agora hospedado em outra réplica.

## <a name="limitations"></a>Limitações

- Atualmente, um ouvinte DNN para um grupo de disponibilidade é suportado apenas para SQL Server 2019 CU8 e mais tarde no Windows Server 2016 e mais tarde. 
- Os ouvintes da DNN **DEVEM** ser configurados com uma porta única.  A porta não pode ser partilhada com qualquer outra ligação em qualquer réplica.
- Pode haver considerações adicionais quando estiver a trabalhar com outras funcionalidades do SQL Server e um grupo de disponibilidade com um DNN. Para obter mais informações, consulte [AG com interoperabilidade DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Considerações portuárias

Os ouvintes do DNN são projetados para ouvir em todos os endereços IP, mas em uma porta específica e única. A entrada dns para o nome do ouvinte deve resolver os endereços de todas as réplicas do grupo de disponibilidade. Isto é feito automaticamente com o script PowerShell fornecido na secção [Criar Script.](#create-script) Uma vez que os ouvintes do DNN aceitam ligações em todos os endereços IP, é fundamental que a porta do ouvinte seja única, e não seja utilizada por qualquer outra réplica no grupo de disponibilidade. Uma vez que o SQL Server ouve sempre na porta 1433, quer diretamente quer através do serviço SQL Browser, a porta 1433 não pode ser utilizada para qualquer ouvinte DNN.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades HADR do SQL Server no Azure, consulte [os grupos Availability](availability-group-overview.md) e [o caso do cluster Failover](failover-cluster-instance-overview.md). Você também pode aprender [as melhores práticas](hadr-cluster-best-practices.md) para configurar o seu ambiente para alta disponibilidade e recuperação de desastres. 
