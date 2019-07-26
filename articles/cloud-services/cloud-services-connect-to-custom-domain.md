---
title: Conectar um serviço de nuvem a um controlador de domínio personalizado | Microsoft Docs
description: Saiba como conectar suas funções Web/de trabalho a um domínio do AD personalizado usando o PowerShell e a extensão de domínio do AD
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 97a24720e65539a68745a5a1bb3f13ce1cafb9be
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359179"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Conectando funções de serviços de nuvem do Azure a um controlador de domínio do AD personalizado hospedado no Azure
Primeiro, configuraremos uma VNet (rede virtual) no Azure. Em seguida, adicionaremos um controlador de Domínio do Active Directory (hospedado em uma máquina virtual do Azure) à VNet. Em seguida, adicionaremos funções de serviço de nuvem existentes à VNet criada previamente e as conectaremos ao controlador de domínio.

Antes de começar, algumas coisas devem ser consideradas:

1. Este tutorial usa o PowerShell, portanto, verifique se você tem Azure PowerShell instalado e pronto para começar. Para obter ajuda com a configuração de Azure PowerShell, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
2. O controlador de domínio do AD e as instâncias da função Web/de trabalho precisam estar na VNet.

Siga este guia passo a passo e, se você encontrar problemas, deixe-nos um comentário no final do artigo. Alguém vai voltar para você (Sim, temos comentários para ler).

A rede que é referenciada pelo serviço de nuvem deve ser uma **rede virtual clássica**.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Você pode criar um Entrada na Rede virtual do Azure usando o portal do Azure ou o PowerShell. Para este tutorial, o PowerShell é usado. Para criar uma rede virtual usando o portal do Azure, consulte [criar uma rede virtual](../virtual-network/quick-create-portal.md). O artigo aborda a criação de uma rede virtual (Resource Manager), mas você deve criar uma rede virtual (clássica) para serviços de nuvem. Para fazer isso, no portal, selecione **criar um recurso**, digite *rede virtual* na caixa de **pesquisa** e pressione **Enter**. Nos resultados da pesquisa, em **tudo**, selecione **rede virtual**. Em **selecionar um modelo de implantação**, selecione **clássico**e, em seguida, selecione **criar**. Em seguida, você pode seguir as etapas no artigo.

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

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Depois de concluir a configuração da rede virtual, será necessário criar um controlador de domínio do AD. Para este tutorial, iremos configurar um controlador de domínio do AD em uma máquina virtual do Azure.

Para fazer isso, crie uma máquina virtual por meio do PowerShell usando os seguintes comandos:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promover sua máquina virtual a um controlador de domínio
Para configurar a máquina virtual como um controlador de domínio do AD, será necessário fazer logon na VM e configurá-la.

Para fazer logon na VM, você pode obter o arquivo RDP por meio do PowerShell, use os seguintes comandos:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Depois de entrar na VM, configure sua máquina virtual como um controlador de domínio do AD seguindo o guia passo a passo sobre [como configurar o controlador de domínio do AD do cliente](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicionar seu serviço de nuvem à rede virtual
Em seguida, você precisa adicionar a implantação do serviço de nuvem à nova VNet. Para fazer isso, modifique seu cscfg do serviço de nuvem adicionando as seções relevantes ao cscfg usando o Visual Studio ou o editor de sua escolha.

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

Em seguida, crie seu projeto de serviços de nuvem e implante-o no Azure. Para obter ajuda com a implantação do pacote de serviços de nuvem no Azure, consulte [como criar e implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Conectar suas funções Web/de trabalho ao domínio
Depois que seu projeto de serviço de nuvem for implantado no Azure, conecte suas instâncias de função ao domínio personalizado do AD usando a extensão de domínio do AD. Para adicionar a extensão de domínio do AD à sua implantação de serviços de nuvem existente e ingressar no domínio personalizado, execute os seguintes comandos no PowerShell:

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

E já está!

Seus serviços de nuvem devem ser associados ao seu controlador de domínio personalizado. Se você quiser saber mais sobre as diferentes opções disponíveis para configurar a extensão de domínio do AD, use a ajuda do PowerShell. Alguns exemplos A seguir:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
