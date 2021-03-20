---
title: Ligue um Serviço de Nuvem (clássico) a um controlador de domínio personalizado | Microsoft Docs
description: Saiba como ligar as suas funções web/trabalhador a um domínio AD personalizado utilizando extensão de domínio PowerShell e AD
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8c2c8377944caa7ad28f6b379531e6d5bf44c9e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98742510"
---
# <a name="connecting-azure-cloud-services-classic-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Conectar os serviços de nuvem de Azure (clássico) a um controlador de domínio AD personalizado hospedado em Azure

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Primeiro criaremos uma Rede Virtual (VNet) em Azure. Em seguida, adicionaremos um Controlador de Domínio de Diretório Ativo (hospedado numa Máquina Virtual Azure) ao VNet. Em seguida, adicionaremos as funções de serviço de nuvem existentes ao VNet pré-criado e, em seguida, conectá-las-emos ao Controlador de Domínio.

Antes de começarmos, algumas coisas a ter em mente:

1. Este tutorial utiliza o PowerShell, por isso certifique-se de que tem o Azure PowerShell instalado e pronto para partir. Para obter ajuda na configuração do Azure PowerShell, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/).
2. O seu Controlador de Domínio AD e as instâncias de função Web/Worker precisam de estar no VNet.

Siga este guia passo a passo e se encontrar algum problema, deixe-nos um comentário no final do artigo. Alguém vai entrar em sua casa (sim, nós lemos comentários).

A rede que é referenciada pelo serviço de nuvem deve ser uma **rede virtual clássica.**

## <a name="create-a-virtual-network"></a>Criar uma Rede Virtual
Pode criar uma Rede Virtual em Azure utilizando o portal Azure ou PowerShell. Para este tutorial, o PowerShell é usado. Para criar uma rede virtual utilizando o portal Azure, consulte [criar uma rede virtual.](../virtual-network/quick-create-portal.md) O artigo abrange a criação de uma rede virtual (Gestor de Recursos), mas tem de criar uma rede virtual (Classic) para serviços na nuvem. Para tal, no portal, selecione **Criar um recurso,** digitar *rede virtual* na caixa **'Procurar'** e, em seguida, **premir Enter**. Nos resultados da pesquisa, em **'Tudo'** **(rede Virtual)** selecione. Em **Selecione um modelo de implementação,** selecione **Classic,** em seguida, selecione **Criar**. Em seguida, pode seguir os passos do artigo.

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
Uma vez concluída a configuração da Rede Virtual, terá de criar um Controlador de Domínio AD. Para este tutorial, vamos criar um Controlador de Domínio AD numa Máquina Virtual Azure.

Para tal, crie uma máquina virtual através do PowerShell utilizando os seguintes comandos:

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

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Promover a sua Máquina Virtual a um Controlador de Domínio
Para configurar a Máquina Virtual como controlador de domínio AD, terá de iniciar sessão no VM e configurá-la.

Para iniciar sessão no VM, pode obter o ficheiro RDP através do PowerShell, utilizar os seguintes comandos:

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Assim que tiver assinado no VM, crie a sua Máquina Virtual como Controlador de Domínio AD seguindo o guia passo a passo sobre [como configurar o controlador de domínio AD do seu cliente](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Adicione o seu Serviço cloud à Rede Virtual
Em seguida, precisa adicionar a sua implementação de serviço em nuvem ao novo VNet. Para isso, modifique o seu serviço de cloud cscfg adicionando as secções relevantes ao seu cscfg utilizando o Visual Studio ou o editor à sua escolha.

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

Em seguida, construa o seu projeto de serviços em nuvem e desloque-o para Azure. Para obter ajuda na implementação do seu pacote de serviços na nuvem para a Azure, consulte [como criar e implementar um serviço de cloud](cloud-services-how-to-create-deploy-portal.md)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Ligue as suas funções web/trabalhador ao domínio
Assim que o seu projeto de serviço na nuvem for implementado no Azure, ligue as suas instâncias de função ao domínio AD personalizado utilizando a Extensão de Domínio AD. Para adicionar a Extensão de Domínio AD à sua implementação de serviços em nuvem existente e juntar-se ao domínio personalizado, execute os seguintes comandos em PowerShell:

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

Os seus serviços na nuvem devem ser associados ao seu controlador de domínio personalizado. Se quiser saber mais sobre as diferentes opções disponíveis para configurar a extensão de domínio AD, utilize a ajuda PowerShell. Seguem-se alguns exemplos:

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```



