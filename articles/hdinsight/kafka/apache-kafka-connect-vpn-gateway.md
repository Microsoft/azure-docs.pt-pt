---
title: Ligar ao Kafka através de redes virtuais - Azure HDInsight
description: Saiba como ligar diretamente ao Kafka no HDInsight através de uma rede Virtual do Azure. Saiba como ligar ao Kafka de clientes de desenvolvimento usando um gateway de VPN, ou a partir de clientes na sua rede no local com um dispositivo de gateway VPN.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 0b1ba5c6d342fb0bf6f888af4bc3a4e1c8ef939e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074723"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Ligar ao Apache Kafka no HDInsight através de uma rede Virtual do Azure

Saiba como ligar diretamente ao Apache Kafka no HDInsight através de uma rede Virtual do Azure. Este documento fornece informações sobre como ligar ao Kafka com as seguintes configurações:

* A partir dos recursos numa rede no local. Esta ligação é estabelecida com um dispositivo VPN (software ou hardware) na sua rede local.
* A partir de um ambiente de desenvolvimento com um cliente de software VPN.

## <a name="architecture-and-planning"></a>Planejamento e arquitetura

HDInsight não permite a ligação direta ao Kafka através da internet pública. Em vez disso, os clientes de Kafka (produtores e consumidores) tem de utilizar um dos seguintes métodos de ligação:

* Execute o cliente na mesma rede virtual como o Kafka no HDInsight. Esta configuração é utilizada na [introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md) documento. O cliente executa diretamente em nós de cluster do HDInsight ou em outra máquina virtual na mesma rede.

* Ligar uma rede privada, como sua rede no local, para a rede virtual. Esta configuração permite que os clientes na sua rede no local para trabalhar diretamente com o Kafka. Para ativar esta configuração, execute as seguintes tarefas:

  1. Crie uma rede virtual
  2. Crie um gateway VPN que utiliza uma configuração site a site. A configuração usada neste documento liga-se a um dispositivo de gateway VPN na sua rede no local.
  3. Crie um servidor DNS na rede virtual.
  4. Configure o reencaminhamento entre o servidor DNS em cada rede.
  5. Crie um Kafka num cluster do HDInsight na rede virtual.

     Para obter mais informações, consulte a [ligar ao Apache Kafka a partir de uma rede no local](#on-premises) secção. 

* Ligar máquinas individuais a rede virtual através de um gateway de VPN e o cliente VPN. Para ativar esta configuração, execute as seguintes tarefas:

  1. Crie uma rede virtual
  2. Crie um gateway VPN que utiliza uma configuração de ponto a site. Esta configuração pode ser utilizada com clientes Windows e MacOS.
  3. Crie um Kafka num cluster do HDInsight na rede virtual.
  4. Configure o Kafka para fins de publicidade de IP. Esta configuração permite que o cliente estabeleçam ligação através do Mediador de endereços IP em vez de nomes de domínio.
  5. Transferir e utilizar o cliente VPN no sistema de desenvolvimento.

     Para obter mais informações, consulte a [ligar ao Apache Kafka com um cliente VPN](#vpnclient) secção.

     > [!WARNING]  
     > Esta configuração apenas é recomendada para fins de desenvolvimento devido às seguintes limitações:
     >
     > * Cada cliente tem de ligar a utilizar um cliente de software VPN.
     > * O cliente VPN não transmite pedidos de resolução de nome para a rede virtual, pelo que deverá utilizar para comunicar com o Kafka de endereçamento IP. Comunicação do IP requer configuração adicional no cluster do Kafka.

Para obter mais informações sobre como utilizar o HDInsight numa rede virtual, consulte [expandir HDInsight ao utilizar redes virtuais do Azure](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Ligar ao Apache Kafka a partir de uma rede no local

Para criar um cluster de Kafka que comunica com a sua rede no local, siga os passos a [ligar o HDInsight à sua rede no local](./../connect-on-premises-network.md) documento.

> [!IMPORTANT]  
> Ao criar o cluster do HDInsight, selecione o __Kafka__ tipo de cluster.

Estes passos, crie a seguinte configuração:

* Rede Virtual do Azure
* Gateway de VPN de site a site
* Conta de armazenamento do Azure (utilizada pelo HDInsight)
* Kafka no HDInsight

Para verificar que um cliente de Kafka pode ligar ao cluster no local, utilize os passos no [exemplo: Cliente Python](#python-client) secção.

## <a id="vpnclient"></a> Ligar ao Apache Kafka com um cliente VPN

Utilize os passos nesta secção para criar a seguinte configuração:

* Rede Virtual do Azure
* Gateway de VPN ponto a site
* Conta de armazenamento do Azure (utilizado pelo HDInsight)
* Kafka no HDInsight

1. Siga os passos a [trabalhar com certificados autoassinados para ligações ponto a site](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) documento. Este documento cria os certificados necessários para o gateway.

2. Abra uma linha de comandos do PowerShell e utilize o seguinte código para iniciar sessão na sua subscrição do Azure:

    ```powershell
    Connect-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Utilize o seguinte código para criar variáveis que contêm informações de configuração:

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

4. Utilize o seguinte código para criar o grupo de recursos do Azure e a rede virtual:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
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
    > Pode demorar alguns minutos até que este processo ser concluído.

5. Utilize o seguinte código para criar o contentor de BLOBs e de conta de armazenamento do Azure:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. Utilize o seguinte código para criar o cluster do HDInsight:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
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
   > Este processo demora cerca de 15 minutos a concluir.

### <a name="configure-kafka-for-ip-advertising"></a>Configurar o Kafka para fins de publicidade de IP

Por predefinição, o Apache Zookeeper devolve o nome de domínio de mediadores Kafka para clientes. Esta configuração não funciona com o cliente de software VPN, pois não poderá utilizar a resolução de nomes de entidades na rede virtual. Para esta configuração, utilize os seguintes passos para configurar o Kafka para anunciar os endereços IP em vez de nomes de domínio:

1. Um browser, aceda à https://CLUSTERNAME.azurehdinsight.net. Substitua __CLUSTERNAME__ com o nome do cluster do Kafka no HDInsight.

    Quando lhe for pedido, utilize o nome de utilizador HTTPS e a palavra-passe para o cluster. A interface do Usuário de Web do Ambari do cluster é apresentado.

2. Para ver informações sobre o Kafka, selecione __Kafka__ da lista à esquerda.

    ![Lista de serviço com o Kafka realçado](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Para ver a configuração do Kafka, selecione __configurações__ da parte superior ao centro.

    ![Links de configurações para o Kafka](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Para localizar os __kafka env__ configuração, introduza `kafka-env` no __filtro__ campo no canto superior direito.

    ![Configuração do Kafka, para env do kafka](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar os endereços IP, adicione o seguinte texto para o fim dos __env de kafka-modelo__ campo:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface que escuta Kafka, introduza `listeners` no __filtro__ campo no canto superior direito.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor na __serviços de escuta__ campo `PLAINTEXT://0.0.0.0:9092`.

8. Para guardar as alterações de configuração, utilize o __guardar__ botão. Introduza uma mensagem de texto que descreve as alterações. Selecione __OK__ assim que as alterações foram guardadas.

    ![Configuração botão Guardar](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Para evitar erros quando reiniciar o Kafka, utilize o __ações de serviço__ e selecione __ativar no modo de manutenção__. Selecione OK para concluir esta operação.

    ![Ações de serviço, com ative a manutenção realçada](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, utilize o __reinicie__ e selecione __reiniciar todos os afetados__. Confirme o reinício e, em seguida, utilize o __OK__ botão após a conclusão da operação.

    ![Reinicie o botão ao reiniciar todos os afetados realçado](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Para desativar o modo de manutenção, utilize o __ações de serviço__ e selecione __Ativar desativar modo de manutenção__. Selecione **OK** para concluir esta operação.

### <a name="connect-to-the-vpn-gateway"></a>Ligar ao gateway de VPN

Para ligar ao gateway de VPN, utilize o __ligar ao Azure__ secção a [configurar uma ligação ponto a Site](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) documento.

## <a id="python-client"></a> Exemplo: Cliente Python

Para validar a conectividade ao Kafka, utilize os seguintes passos para criar e executar um Python produtor e consumidor:

1. Utilize um dos seguintes métodos para recuperar o nome de domínio completamente qualificado (FQDN) e endereços IP de nós do cluster de Kafka:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

    Este script presume que `$resourceGroupName` é o nome do grupo de recursos do Azure que contém a rede virtual.

    Guarde as informações retornadas para utilização nos passos seguintes.

2. Utilize o seguinte para instalar o [kafka-python](https://kafka-python.readthedocs.io/) cliente:

        pip install kafka-python

3. Para enviar dados para o Kafka, utilize o seguinte código de Python:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Substitua o `'kafka_broker'` entradas com os endereços retornados do passo 1 nesta secção:

   * Se estiver a utilizar um __cliente VPN de Software__, substitua o `kafka_broker` entradas com o endereço IP do seu nós de trabalho.

   * Se tiver __ativada a resolução de nomes através de um servidor DNS personalizado__, substitua o `kafka_broker` entradas com o FQDN de nós de trabalho.

     > [!NOTE]
     > Este código envia a cadeia de caracteres `test message` para o tópico `testtopic`. A configuração predefinida do Kafka no HDInsight é criar o tópico, se não existir.

4. Para obter as mensagens do Kafka, utilize o seguinte código de Python:

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

    Substitua o `'kafka_broker'` entradas com os endereços retornados do passo 1 nesta secção:

    * Se estiver a utilizar um __cliente VPN de Software__, substitua o `kafka_broker` entradas com o endereço IP do seu nós de trabalho.

    * Se tiver __ativada a resolução de nomes através de um servidor DNS personalizado__, substitua o `kafka_broker` entradas com o FQDN de nós de trabalho.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como utilizar o HDInsight com uma rede virtual, consulte a [expandir o Azure HDInsight com uma rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md) documento.

Para obter mais informações sobre como criar uma rede Virtual do Azure com o gateway de VPN ponto a Site, consulte os seguintes documentos:

* [Configurar uma ligação de ponto a Site com o portal do Azure](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Configurar uma ligação de ponto a Site com o Azure PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Para obter mais informações sobre como trabalhar com o Apache Kafka no HDInsight, consulte os seguintes documentos:

* [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md)
* [Usar o espelhamento do Apache kafka no HDInsight](apache-kafka-mirroring.md)
