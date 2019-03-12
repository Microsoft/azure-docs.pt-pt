---
title: Ligar um serviço em nuvem a um controlador de domínio personalizado | Documentos da Microsoft
description: Saiba como ligar as suas funções da web/de trabalho para um domínio AD personalizado com o PowerShell e a extensão de domínio do AD
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 8bee2e2038ee39c777e1ca09994ad21872d2029a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530063"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ligar funções de serviços do Azure na nuvem a um controlador de domínio do AD alojado no Azure de personalizado
Vamos primeiro configurar uma rede Virtual (VNet) no Azure. Em seguida, vamos adicionar um controlador de domínio do Active Directory (alojada numa máquina Virtual do Azure) para a VNet. Em seguida, podemos irá adicionar as funções de serviço cloud existentes para a VNet previamente criada, em seguida, ligá-los para o controlador de domínio.

Antes de começar, alguns dos aspetos a ter em mente:

1. Este tutorial utiliza o PowerShell, por isso, certifique-se de que tem o Azure PowerShell instalado e pronto para começar. Para obter ajuda com a configuração do Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. As instâncias de controlador de domínio do AD e a função da Web/trabalho tem de ser na VNet.

Siga este guia passo a passo e caso se depare com quaisquer problemas, deixe-num comentário no final do artigo. Alguém irá entrar em contacto (Sim, vamos ler comentários).

A rede que é referenciada pelo serviço de nuvem tem de ser um **rede virtual clássica**.

## <a name="create-a-virtual-network"></a>Criar uma rede Virtual
Pode criar uma rede Virtual no Azure com o portal do Azure ou o PowerShell. Para este tutorial, o PowerShell é utilizado. Para criar uma rede virtual com o portal do Azure, veja [criar uma rede virtual](../virtual-network/quick-create-portal.md). O artigo abrange a criação de uma rede virtual (Resource Manager), mas tem de criar uma rede virtual (clássico) para serviços em nuvem. Para tal, no portal, selecione **criar um recurso**, tipo *rede virtual* no **pesquisa** caixa e, em seguida, prima **Enter**. Nos resultados da pesquisa, sob **tudo**, selecione **rede Virtual**. Sob **selecionar um modelo de implementação**, selecione **clássico**, em seguida, selecione **criar**. Em seguida, pode seguir os passos no artigo.

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual
Depois de concluir a configuração da rede Virtual, terá de criar um controlador de domínio do AD. Para este tutorial, estamos será possível configurar um controlador de domínio do AD numa máquina Virtual do Azure.

Para tal, crie uma máquina virtual através do PowerShell com os comandos seguintes:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promova a sua máquina Virtual para um controlador de domínio
Para configurar a Máquina Virtual como um controlador de domínio do AD, terá de iniciar sessão VM e configurá-la.

Para iniciar sessão na VM, pode obter o ficheiro RDP através do PowerShell, utilize os seguintes comandos:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Uma vez que tem sessão iniciada na VM, configurar a sua máquina Virtual como um controlador de domínio do AD basta seguir o guia passo a passo [como configurar seu controlador de domínio do AD do cliente](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar o seu serviço Cloud para a rede Virtual
Em seguida, terá de adicionar a sua implementação do serviço cloud para a nova VNet. Para fazer isso, modifique seu cscfg de serviço cloud adicionando as secções relevantes ao seu cscfg usando o Visual Studio ou o editor da sua preferência.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Em seguida, compila o projeto de serviços cloud e implementá-la para o Azure. Para obter ajuda com a implantação de seu pacote de serviços cloud para o Azure, consulte [como criar e implementar um serviço Cloud](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ligue as suas funções da web/trabalho ao domínio
Assim que o seu projeto de serviço em nuvem é implementado no Azure, ligar as instâncias de função ao domínio do AD personalizado com a extensão de domínio do AD. Para adicionar a extensão de domínio do AD para a implementação de serviços cloud existentes e associar o domínio personalizado, execute os seguintes comandos do PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E isso é tudo.

Os serviços cloud devem ser associados ao seu controlador de domínio personalizado. Se gostaria de saber mais sobre as diferentes opções disponíveis para saber como configurar a extensão de domínio do AD, utilize a ajuda do PowerShell. Alguns exemplos seguintes:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
