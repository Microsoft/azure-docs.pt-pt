---
title: Ligue-se a Kafka usando redes virtuais - Azure HDInsight
description: Saiba como ligar-se diretamente a Kafka no HDInsight através de uma Rede Virtual Azure. Saiba como ligar-se a Kafka a partir de clientes de desenvolvimento usando um gateway VPN, ou de clientes na sua rede no local utilizando um dispositivo de gateway VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272126"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Connect to Apache Kafka on HDInsight through an Azure Virtual Network (Ligar ao Apache Kafka no HDInsight através de uma Rede Virtual do Azure)

Saiba como ligar-se diretamente a Apache Kafka no HDInsight através de uma Rede Virtual Azure. Este documento fornece informações sobre a ligação a Kafka utilizando as seguintes configurações:

* De recursos numa rede no local. Esta ligação é estabelecida utilizando um dispositivo VPN (software ou hardware) na sua rede local.
* De um ambiente de desenvolvimento usando um cliente de software VPN.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Arquitetura e planeamento

O HDInsight não permite a ligação direta a Kafka através da internet pública. Em vez disso, os clientes kafka (produtores e consumidores) devem utilizar um dos seguintes métodos de ligação:

* Execute o cliente na mesma rede virtual que Kafka no HDInsight. Esta configuração é usada no Início com Apache Kafka no documento [HDInsight.](apache-kafka-get-started.md) O cliente funciona diretamente nos nós do cluster HDInsight ou em outra máquina virtual na mesma rede.

* Ligue uma rede privada, como a sua rede no local, à rede virtual. Esta configuração permite que os clientes da sua rede no local trabalhem diretamente com kafka. Para ativar esta configuração, execute as seguintes tarefas:

  1. Crie uma rede virtual
  2. Crie um gateway VPN que utilize uma configuração site-to-site. A configuração utilizada neste documento liga-se a um dispositivo de gateway VPN na sua rede no local.
  3. Crie um servidor DNS na rede virtual.
  4. Configure o reencaminhamento entre o servidor DNS em cada rede.
  5. Crie um cluster Kafka no HDInsight na rede virtual.

     Para mais informações, consulte o Connect to Apache Kafka a partir de uma secção [de rede no local.](#on-premises)

* Ligue as máquinas individuais à rede virtual utilizando um gateway VPN e um cliente VPN. Para ativar esta configuração, execute as seguintes tarefas:

  1. Crie uma rede virtual
  2. Crie um gateway VPN que utilize uma configuração ponto-a-local. Esta configuração pode ser utilizada tanto com clientes Windows como MacOS.
  3. Crie um cluster Kafka no HDInsight na rede virtual.
  4. Configure Kafka para publicidade IP. Esta configuração permite ao cliente ligar usando endereços IP do corretor em vez de nomes de domínio.
  5. Descarregue e use o cliente VPN no sistema de desenvolvimento.

     Para mais informações, consulte o Connect to Apache Kafka com uma secção de [clientes VPN.](#vpnclient)

     > [!WARNING]  
     > Esta configuração só é recomendada para fins de desenvolvimento devido às seguintes limitações:
     >
     > * Cada cliente deve ligar-se através de um cliente de software VPN.
     > * O cliente VPN não passa pedidos de resolução de nomes para a rede virtual, pelo que deve utilizar endereçoip para comunicar com kafka. A comunicação IP requer uma configuração adicional no cluster Kafka.

Para obter mais informações sobre a utilização do HDInsight numa rede virtual, consulte [Plan uma rede virtual para clusters Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Ligue-se a Apache Kafka a partir de uma rede no local

Para criar um cluster Kafka que comunica com a sua rede no local, siga os passos no [Connect HDInsight até ao documento de rede no local.](./../connect-on-premises-network.md)

> [!IMPORTANT]  
> Ao criar o cluster HDInsight, selecione o tipo de cluster __Kafka.__

Estes passos criam a seguinte configuração:

* Rede Virtual do Azure
* Gateway VPN site-to-site
* Conta de Armazenamento Azure (utilizada pelo HDInsight)
* Kafka no HDInsight

Para verificar se um cliente Kafka pode ligar-se ao cluster a partir do local, utilize os passos na secção de [cliente Exemplo: Python.](#python-client)

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Ligue-se a Apache Kafka com um cliente VPN

Utilize os passos nesta secção para criar a seguinte configuração:

* Rede Virtual do Azure
* Gateway VPN ponto-a-local
* Conta de Armazenamento Azure (utilizada pelo HDInsight)
* Kafka no HDInsight

1. Siga os passos no Trabalho com certificados auto-assinados para o documento de [ligações Ponto-a-local.](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) Este documento cria os certificados necessários para o portal.

2. Abra um pedido PowerShell e utilize o seguinte código para iniciar sessão na subscrição do Azure:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. Utilize o seguinte código para criar variáveis que contenham informações de configuração:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Utilize o seguinte código para criar o grupo de recursos Azure e a rede virtual:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > Pode levar vários minutos para que este processo esteja concluído.

5. Utilize o seguinte código para criar a Conta de Armazenamento Azure e o recipiente de bolha:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Utilize o seguinte código para criar o cluster HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Este processo leva cerca de 15 minutos para ser concluído.

### <a name="configure-kafka-for-ip-advertising"></a>Configure Kafka para publicidade IP

Por padrão, o Apache Zookeeper devolve aos clientes o nome de domínio dos corretores Kafka. Esta configuração não funciona com o cliente de software VPN, uma vez que não pode utilizar a resolução de nomes para entidades da rede virtual. Para esta configuração, utilize os seguintes passos para configurar Kafka para anunciar endereços IP em vez de nomes de domínio:

1. Num browser, aceda a `https://CLUSTERNAME.azurehdinsight.net`. Substitua-o `CLUSTERNAME` pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, utilize o nome de utilizador HTTPS e a palavra-passe para o cluster. A Ambari Web UI para o cluster é exibida.

2. Para ver informações sobre Kafka, __selecione Kafka__ da lista à esquerda.

    ![Lista de serviçocom Kafka em destaque](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para ver a configuração kafka, selecione __Configs__ do meio superior.

    ![Configuração dos serviços Apache Ambari](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. Para encontrar a configuração __kafka-env,__ introduza `kafka-env` no campo __Filter__ na parte superior direita.

    ![Configuração kafka, para kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar kafka para anunciar endereços IP, adicione o seguinte texto na parte inferior do campo __kafka-env-template:__

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que Kafka `listeners` ouve, introduza no campo __Filter__ na parte superior direita.

7. Para configurar kafka para ouvir todas as interfaces de `PLAINTEXT://0.0.0.0:9092`rede, altere o valor no campo dos __ouvintes__ para .

8. Para evitar as alterações de configuração, utilize o botão __Guardar.__ Introduza uma mensagem de texto descrevendo as alterações. Selecione __OK__ uma vez que as alterações tenham sido guardadas.

    ![Configuração de salvamento Apache Ambari](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. Para evitar erros ao reiniciar o Kafka, utilize o botão __Desviação__ de Ação de Serviço e selecione __modo de manutenção Ligar__. Selecione OK para completar esta operação.

    ![Ações de serviço, com a entrada em destaque na manutenção](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, utilize o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme o reinício e, em seguida, utilize o botão __OK__ depois de concluída a operação.

    ![Botão de reiniciar com reiniciar todos os afetados destacados](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. Para desativar o modo de manutenção, utilize o botão "Ação de __Serviço"__ e selecione desligar o __modo de manutenção__. Selecione **OK** para completar esta operação.

### <a name="connect-to-the-vpn-gateway"></a>Ligue-se ao gateway VPN

Para ligar ao gateway VPN, utilize a secção __Connect to Azure__ do documento de [ligação Ponto-a-Local.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Exemplo: Cliente Python

Para validar a conectividade com kafka, use os seguintes passos para criar e executar um produtor e consumidor Python:

1. Utilize um dos seguintes métodos para recuperar os endereços de domínio totalmente qualificados (FQDN) e IP dos nós do cluster Kafka:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Este script pressupõe que `$resourceGroupName` é o nome do grupo de recursos Azure que contém a rede virtual.

    Guarde as informações devolvidas para utilização nos próximos passos.

2. Utilize o seguinte para instalar o cliente [kafka-python:](https://kafka-python.readthedocs.io/)

    ```bash
    pip install kafka-python
    ```

3. Para enviar dados para Kafka, utilize o seguinte código Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Substitua `'kafka_broker'` as entradas com os endereços devolvidos do passo 1 nesta secção:

   * Se estiver a utilizar um cliente `kafka_broker` VPN de __software,__ substitua as entradas pelo endereço IP dos seus nós de trabalhador.

   * Se tiver ativado a resolução de nomeatravés `kafka_broker` de um servidor __DNS personalizado,__ substitua as entradas pelo FQDN dos nós dos trabalhadores.

     > [!NOTE]
     > Este código envia `test message` a `testtopic`corda para o tópico. A configuração padrão de Kafka no HDInsight é criar o tópico se não existir.

4. Para recuperar as mensagens de Kafka, utilize o seguinte código Python:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Substitua `'kafka_broker'` as entradas com os endereços devolvidos do passo 1 nesta secção:

    * Se estiver a utilizar um cliente `kafka_broker` VPN de __software,__ substitua as entradas pelo endereço IP dos seus nós de trabalhador.

    * Se tiver ativado a resolução de nomeatravés `kafka_broker` de um servidor __DNS personalizado,__ substitua as entradas pelo FQDN dos nós dos trabalhadores.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do HDInsight com uma rede virtual, consulte o Plano uma implementação de rede virtual para o documento de [clusters Azure HDInsight.](../hdinsight-plan-virtual-network-deployment.md)

Para obter mais informações sobre a criação de uma Rede Virtual Azure com gateway VPN Ponto-a-Local, consulte os seguintes documentos:

* [Configure uma ligação Ponto-a-Local utilizando o portal Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configure uma ligação Ponto-a-Local utilizando o Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para obter mais informações sobre o trabalho com Apache Kafka no HDInsight, consulte os seguintes documentos:

* [Começa com Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Use espelhamento com Apache Kafka no HDInsight](apache-kafka-mirroring.md)
