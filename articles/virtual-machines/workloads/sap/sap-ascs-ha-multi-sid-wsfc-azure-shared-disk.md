---
title: SAP ASCS/SCS multi-SID HA com WSFC e Azure disco compartilhado Microsoft Docs
description: Disponibilidade alta multi-SID para uma instância SAP ASCS/SCS com disco compartilhado WSFC e Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4dfbffcaedb6c544a34e347633d5adc173fab33e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655990"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>SAP ASCS/SCS exemplo multi-SID alta disponibilidade com cluster de failover do servidor Windows e disco compartilhado Azure

> ![SO Windows][Logo_Windows] Windows
>

Este artigo centra-se em como passar de uma única instalação ASCS/SCS para uma configuração multi-SID SAP, instalando instâncias adicionais agrupadas SAP ASCS/SCS num cluster existente do Windows Server Failover Clustering (WSFC) com disco partilhado Azure. Quando este processo estiver concluído, configuraste um cluster SAP multi-SID.

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

Atualmente pode utilizar discos SSD Azure Premium como disco partilhado Azure para a instância SAP ASCS/SCS. Estão em vigor as seguintes limitações:

-  [O disco Azure Ultra](../../disks-types.md#ultra-disk) não é suportado como Disco Partilhado Azure para cargas de trabalho SAP. Atualmente não é possível colocar VMs Azure, usando Azure Ultra Disk em Conjunto de Disponibilidade
-  [O disco Azure Shared](../../disks-shared.md) com discos SSD Premium só é suportado com VMs no Conjunto de Disponibilidade. Não é suportado na implantação de Zonas de Disponibilidade. 
-  O [maxShares,](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) valor do disco partilhado Azure, determina quantos nós de cluster podem usar o disco partilhado. Normalmente para a instância SAP ASCS/SCS, irá configurar dois nós no Windows Failover Cluster, pelo que o valor para `maxShares` deve ser definido para dois.
-  Todos os VMs do cluster SAP ASCS/SCS devem ser implantados no mesmo grupo de colocação de [proximidade Azure](../../windows/proximity-placement-groups.md).   
   Embora possa implementar VMs de cluster Windows em Conjunto de Disponibilidade com disco partilhado Azure sem PPG, o PPG garantirá uma proximidade física próxima dos discos partilhados do Azure e dos VMs do cluster, conseguindo assim uma menor latência entre os VMs e a camada de armazenamento.    

Para mais detalhes sobre as limitações para o disco partilhado Azure, reveja cuidadosamente a secção [limitações](../../disks-shared.md#limitations) da documentação do Disco Partilhado Azure.  

> [!IMPORTANT]
> Ao implementar o cluster SAP ASCS/SCS Windows Failover com o disco partilhado Azure, esteja ciente de que a sua implementação estará a funcionar com um único disco partilhado num único cluster de armazenamento. A sua instância SAP ASCS/SCS será impactada, em caso de problemas com o cluster de armazenamento, onde o disco partilhado Azure é implantado.  

> [!IMPORTANT]
> A configuração deve satisfazer as seguintes condições:
> * Cada sistema de gestão de bases de dados (DBMS) SID deve ter o seu próprio cluster WSFC dedicado.  
> * Os servidores de aplicações SAP que pertencem a um sistema SAP SID devem ter os seus próprios VMs dedicados.  
> * Não é suportada uma mistura de Enqueue Replication Server 1 e Enqueue Replication Server 2 no mesmo cluster.  


## <a name="supported-os-versions"></a>Versões de SO suportadas

Tanto o Windows Server 2016 como o Windows Server 2019 são suportados (utilize as imagens mais recentes do data center).

Recomendamos vivamente a utilização do **Datacenter 2019 do Windows Server 2019,** como:
- Windows 2019 Failover Cluster Service está ciente de Azure
- Há uma maior integração e consciencialização da Manutenção do Anfitrião Azure e uma melhor experiência através da monitorização de eventos de agenda do Azure.
- É possível utilizar o nome de rede distribuído (é a opção predefinida). Portanto, não há necessidade de ter um endereço IP dedicado para o nome da rede de cluster. Além disso, não há necessidade de configurar este endereço IP no Azure Internal Load Balancer. 

## <a name="architecture"></a>Arquitetura

Tanto o servidor de replicação Enqueue 1 (ERS1) como o servidor de replicação Enqueue 2 (ERS2) são suportados na configuração multi-SID.  Uma mistura de ERS1 e ERS2 não é suportada no mesmo cluster. 

1. O primeiro exemplo mostra dois SIDs SAP, ambos com arquitetura ERS1 onde:

   - O SAP SID1 é implantado em disco partilhado, com ERS1. A instância ERS é instalada no hospedeiro local e na unidade local.
     O SAP SID1 tem o seu próprio endereço IP (virtual) (SID1 (A)SCS IP1), que está configurado no equilibrador de carga interna Azure.

   - O SAP SID2 é implantado em disco partilhado, com ERS1. A instância ERS é instalada no hospedeiro local e na unidade local.
     O SAP SID2 tem um endereço IP próprio (virtual) (SID2 (A)SCS IP2), que está configurado também no equilibrador de carga interna Azure.

![Caso SAP ASCS/SCS de alta disponibilidade - dois casos com configuração ERS1][sap-ha-guide-figure-6007]

2. O segundo exemplo mostra dois SIDs SAP, ambos com arquitetura ERS2 onde: 

   - SAP SID1 com ERS2, é que também está agrupado e é implantado na unidade local.  
     O SAP SID1 tem um endereço IP próprio (virtual) (SID1 (A)SCS IP1), que está configurado no equilibrador de carga interna Azure.
     O SAP ERS2, utilizado pelo sistema SAP SID1, tem o seu próprio endereço IP (virtual) (SID1 ERS2 IP2), que está configurado no equilibrador de carga interna Azure.

   - SAP SID2 com ERS2, é que também está agrupado e é implantado na unidade local.  
     O SAP SID2 tem um endereço IP próprio (virtual) (SID2 (A)SCS IP3), que está configurado no equilibrador de carga interna Azure.
     O SAP ERS2, utilizado pelo sistema SAP SID2, tem o seu próprio endereço IP (virtual) (SID2 ERS2 IP4), que está configurado no equilibrador de carga interna Azure.

   Aqui temos um total de quatro endereços IP virtuais:  
   - SID1 (A)SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A)SCS IP3
   - SID2 ERS2 IP4

![Caso SAP ASCS/SCS de alta disponibilidade - dois casos com configuração ERS1 e ERS2][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Preparação de infraestruturas

Instalaremos um novo SAP SID **PR2,** para além da instância SAP **PR1** ASCS/SCS **agrupada existente.**  

### <a name="host-names-and-ip-addresses"></a>Nomes de anfitrião e endereços IP

| Papel de nome de anfitrião | Nome do anfitrião | Endereço IP estático | Conjunto de disponibilidade | Grupo de colocação de proximidade |
| --- | --- | --- |---| ---|
| 1º cluster nó ASCS/SCS cluster |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 2º cluster nó ASCS/SCS cluster |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| Nome da rede de cluster | pr1clust |10.0.0.42(**apenas** para o conjunto Win 2016) | n/a | n/a |
| **SID1** Nome da rede de cluster ASCS | pr1-ascscl |10.0.0.43 | n/a | n/a |
| **SID1** Nome da rede de cluster ERS **(apenas** para ERS2) | pr1-erscl |10.0.0.44 | n/a | n/a |
| **SID2** Nome da rede de cluster ASCS | pr2-ascscl |10.0.0.45 | n/a | n/a |
| **SID2** Nome da rede de cluster ERS **(apenas** para ERS2) | pr1-erscl |10.0.0.46 | n/a | n/a |

### <a name="create-azure-internal-load-balancer"></a>Criar esquilibrador interno de carga Azure

SAP ASCS, SAP SCS e o novo SAP ERS2, utilizem o nome de hospedeiro virtual e endereços IP virtuais. No Azure é necessário um [equilibrador](../../../load-balancer/load-balancer-overview.md) de carga para utilizar um endereço IP virtual. Recomendamos vivamente a utilização [do balanceador de carga Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

Será necessário adicionar a configuração ao equilibrador de carga existente para a segunda instância **PR2** DO SAP SID ASCS/SCS/ERS . A configuração para o primeiro SAP SID **PR1** já deve estar no lugar.  

**a (A) SCS PR2 [instância número 02]**
- Configuração frontend
    - Endereço IP ascs/SCS estático **10.0.0.45**
- Configuração de backend  
    Já estão no lugar - os VMs já foram adicionados à piscina de backend, enquanto configuram para SAP SID **PR1**
- Porto de Sonda
    - Porta 620 **nr** [**62002**] Deixe a opção por defeito para Protocolo (TCP), Intervalo (5), limiar pouco saudável (2)
- Regras de equilíbrio de carga
    - Se utilizar o Balanceador de Carga Padrão, selecione portas HA
    - Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
        - 32 **nr** TCP [**3202**]
        - 36 **nr** TCP [**3602**]
        - 39 **nr** TCP [**3902**]
        - 81 **nr** TCP [**8102**]
        - 5 **nr** 13 TCP [**50213**]
        - 5 **nr** 14 TCP [**50214**]
        - 5 **nr** 16 TCP [**50216**]
        - Associe-se ao **PR2** ASCS Frontend IP, à sonda de saúde e à piscina de backend existente.  

    - Certifique-se de que o tempo limite de marcha lenta (minutos) está definido para o valor máximo 30 e que o IP flutuante (retorno direto do servidor) está ativado.

**ERS2 PR2 [instância número 12]** 

Uma vez que o Enqueue Replication Server 2 (ERS2) também está agrupado, o endereço IP virtual ERS2 também deve ser configurado no Azure ILB, além de acima de SAP ASCS/SCS IP. Esta secção aplica-se apenas, se utilizar a arquitetura do servidor de replicação Enqueue 2 para **PR2**.  
- Nova configuração frontend
    - Endereço IP ESTÁTICO SAP ERS2 **10.0.0.46**

- Configuração de backend  
  Os VMs já foram adicionados à piscina de backend ILB.  

- Novo Porto de Sonda
    - Porta 621 **nr**  [**62112**] Deixe a opção por defeito para Protocolo (TCP), Intervalo (5), limiar pouco saudável (2)

- Novas regras de equilíbrio de carga
    - Se utilizar o Balanceador de Carga Padrão, selecione portas HA
    - Se utilizar o Balanceador de Carga Básica, crie regras de equilíbrio de carga para as seguintes portas
        - 32 **nr** TCP [**3212**]
        - 33 **nr** TCP [**3312**]
        - 5 **nr** 13 TCP [**51212**]
        - 5 **nr** 14 TCP [**51212**]
        - 5 **nr** 16 TCP [**51212**]
        - Associe-se ao **PR2** ERS2 Frontend IP, à sonda de saúde e à piscina de backend existente.  

    - Certifique-se de que o tempo limite de marcha lenta (minutos) está definido para o valor máximo, por exemplo, 30, e que o IP flutuante (retorno direto do servidor) está ativado.


### <a name="create-and-attach-second-azure-shared-disk"></a>Criar e anexar segundo disco compartilhado Azure

Executar este comando num dos nós do cluster. Terá de ajustar os valores para o seu grupo de recursos, região de Azure, SAPSID, e assim por diante.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formato do disco compartilhado com PowerShell
1. Pegue o número do disco. Executar os comandos PowerShell num dos nóns de cluster:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formate o disco. Neste exemplo é o disco número 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Verifique se o disco está agora visível como um disco de cluster.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Registe o disco no cluster.  
   ```powershell
     # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output 
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Criar um nome de hospedeiro virtual para a instância SAP ASCS/SCS agrupada

1. Crie uma entrada DNS para o nome de anfitrião virtual para o novo caso SAP ASCS/SCS no gestor DNS do Windows.  
   O endereço IP que atribui ao nome de anfitrião virtual em DNS deve ser o mesmo que o endereço IP que atribuiu no Azure Load Balancer.  

   ![_Define a entrada de DNS para o nome virtual do cluster SAP ASCS/SCS e endereço IP][sap-ha-guide-figure-6009]
 
   _Definir a entrada dns para o nome virtual do cluster SAP ASCS/SCS e endereço IP_

2. Se utilizar o SERVIDOR de Replicação de Enqueue 2 da SAP, que também é um exemplo agrupado, então também precisa de reservar no DNS um nome de anfitrião virtual para ERS2. 
   O endereço IP que atribui ao nome de anfitrião virtual para ERS2 em DNS deve ser o mesmo que o endereço IP que atribuiu no Azure Load Balancer.  

   ![_Define a entrada dns para o nome virtual do cluster SAP ERS2 e endereço IP][sap-ha-guide-figure-6010]
 
   _Defina a entrada DNS para o nome virtual do cluster SAP ERS2 e endereço IP_

3. Para definir o endereço IP atribuído ao nome de anfitrião virtual, selecione **DNS Manager**  >  **Domain**.

   ![Novo nome virtual e endereço IP para configuração de cluster SAP ASCS/SCS e ERS2][sap-ha-guide-figure-6011]

   _Novo nome virtual e endereço TCP/IP para configuração de cluster SAP ASCS/SCS e ERS2_

## <a name="sap-installation"></a>Instalação de SAP 

### <a name="install-the-sap-first-cluster-node"></a>Instale o primeiro nó de cluster SAP

Siga o procedimento de instalação descrito pela SAP. Certifique-se de que na opção de instalação inicial "Primeiro Nó de Cluster", e para escolher "Cluster Shared Disk" como opção de configuração.  
Escolha o disco partilhado recém-criado.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Modificar o perfil SAP da instância ASCS/SCS

Se estiver a executar o Enqueue Replication Server 1, adicione o parâmetro de perfil `enque/encni/set_so_keepalive` SAP, conforme descrito abaixo. O parâmetro de perfil evita que as ligações entre os processos de trabalho SAP e o servidor de enqueue se fechem quando estão inativas por muito tempo. O parâmetro SAP não é necessário para o ERS2. 

1. Adicione este parâmetro de perfil ao perfil de instância SAP ASCS/SCS, se utilizar o ERS1.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Para o ERS1 e ERS2, certifique-se de que os `keepalive` parâmetros de SO estão definidos como descrito na nota [SAP 1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Para aplicar as alterações do parâmetro de perfil SAP, reinicie a instância SAP ASCS/SCS.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Configure a porta da sonda no recurso cluster

Utilize a funcionalidade da sonda do balançador interno para fazer com que toda a configuração do cluster funcione com o Balançador de Carga Azure. O balançador interno de carga Azure distribui normalmente a carga de trabalho de entrada igualmente entre máquinas virtuais participantes.

No entanto, isto não funcionará em algumas configurações de cluster porque apenas um caso está ativo. A outra instância é passiva e não pode aceitar nenhuma carga de trabalho. Uma funcionalidade da sonda ajuda quando o equilibrador interno de carga Azure deteta qual a instância está ativa, e apenas direciona a instância ativa.  

> [!IMPORTANT]
> Nesta configuração de exemplo, o **ProbePort** está definido para 620 **Nr**. Para a caixa ASCS sap com o número **02** é 620 **02**.
> Terá de ajustar a configuração para corresponder aos números de instância SAP e ao seu SAP SID.

Para adicionar uma porta de sonda, executar este Módulo PowerShell num dos VMs do cluster:

- No caso do CASO SAP ASC/SCS com o número **02** da instância 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Se utilizar o ERS2, com o número **12,** que está agrupado. Não há necessidade de configurar a porta de sonda para a ERS1, uma vez que não está agrupada.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 O código para a função `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` seria:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manually restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Continue com a instalação SAP

1. Instale a caixa de dados, seguindo o processo descrito no guia de instalação SAP.  
2. Instale o SAP no segundo nó de cluster seguindo os passos descritos no guia de instalação SAP.  
3. Instale a instância DOE (PAS) no dispositivo virtual designado para hospedar o PAS.   
   Siga o processo descrito no guia de instalação SAP. Não há dependências do Azure.
4. Instale servidores de aplicações SAP adicionais nas máquinas virtuais, designadas para hospedar instâncias do servidor DA APLICAÇÃO SAP.  
   Siga o processo descrito no guia de instalação SAP. Não há dependências do Azure. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testar a falha da instância SAP ASCS/SCS
Para os testes de failover delineados, assumimos que o SAP ASCS está ativo no nó A.  

1. Verifique se o sistema SAP pode falhar com sucesso do nó A para o nó B. Neste exemplo, o teste é feito para SAPSID **PR2**.  
   Certifique-se de que cada um dos SAPSID pode mover-se com sucesso para o outro nó de cluster.   
   Escolha uma destas opções para iniciar uma falha do grupo de cluster SAP \<SID\> do nó de cluster A ao nó de cluster B:
    - Gestor de Cluster Failover  
    - Falha no cluster PowerShell

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Reinicie o nó de cluster A dentro do sistema operativo de hóspedes Windows. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.  
3. Reinicie o nó de cluster A do portal Azure. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.  
4. Reinicie o nó de cluster A utilizando a Azure PowerShell. Isto inicia uma falha automática do grupo de cluster SAP \<SID\> do nó A ao nó B.

## <a name="next-steps"></a>Passos seguintes

* [Prepare a infraestrutura Azure para o SAP HA utilizando um cluster de failover do Windows e disco partilhado para uma instância SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]
* [Instale o SAP NetWeaver HA num cluster de falha do Windows e disque partilhe o disco para uma instância SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md