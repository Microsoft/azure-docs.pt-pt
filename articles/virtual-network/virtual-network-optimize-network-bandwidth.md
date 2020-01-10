---
title: Otimizar a taxa de transferência da rede VM | Microsoft Docs
description: Saiba como otimizar a taxa de transferência da rede de máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690890"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a taxa de transferência de rede para máquinas virtuais do Azure

As máquinas virtuais (VM) do Azure têm configurações de rede padrão que podem ser otimizadas para a taxa de transferência de rede. Este artigo descreve como otimizar a taxa de transferência de rede para Microsoft Azure VMs Windows e Linux, incluindo distribuições principais, como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se a sua VM do Windows der suporte [à rede acelerada](create-vm-accelerated-networking-powershell.md), a habilitação desse recurso seria a configuração ideal para a taxa de transferência. Para todas as outras VMs do Windows, o uso do RSS (recebimento de recepção) pode alcançar uma taxa de transferência máxima maior do que uma VM sem RSS. O RSS pode ser desabilitado por padrão em uma VM do Windows. Para determinar se o RSS está habilitado e habilitá-lo se ele estiver desabilitado no momento, conclua as seguintes etapas:

1. Veja se o RSS está habilitado para um adaptador de rede com o comando `Get-NetAdapterRss` PowerShell. Na saída de exemplo a seguir retornada da `Get-NetAdapterRss`, o RSS não está habilitado.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Para habilitar o RSS, digite o seguinte comando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterou as configurações de NIC, causando perda de conectividade temporária por cerca de um minuto. Uma caixa de diálogo reconexão aparece durante a perda de conectividade. A conectividade normalmente é restaurada após a terceira tentativa.
3. Confirme se o RSS está habilitado na VM inserindo o comando `Get-NetAdapterRss` novamente. Se for bem-sucedido, a seguinte saída de exemplo será retornada:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM do Linux

O RSS é sempre habilitado por padrão em uma VM Linux do Azure. Os kernels do Linux lançados desde outubro de 2017 incluem novas opções de otimização de rede que permitem que uma VM Linux alcance uma taxa de transferência de rede maior.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implantações

O kernel do Ubuntu Azure fornece o melhor desempenho de rede no Azure e tem sido o kernel padrão desde 21 de setembro de 2017. Para obter esse kernel, primeiro instale a versão mais recente com suporte do 16, 4-LTS, da seguinte maneira:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Após a conclusão da criação, insira os seguintes comandos para obter as atualizações mais recentes. Essas etapas também funcionam para VMs que atualmente executam o kernel do Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O conjunto de comandos opcionais a seguir pode ser útil para implantações do Ubuntu existentes que já têm o kernel do Azure, mas que falharam em atualizações adicionais com erros.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Atualização do kernel Ubuntu Azure para VMs existentes

O desempenho significativo da taxa de transferência pode ser obtido Atualizando para o kernel do Linux do Azure. Para verificar se você tem esse kernel, verifique a versão do kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se sua VM não tiver o kernel do Azure, o número de versão geralmente começa com "4,4". Se a VM não tiver o kernel do Azure, execute os seguintes comandos como raiz:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as otimizações mais recentes, é melhor criar uma VM com a versão mais recente com suporte, especificando os seguintes parâmetros:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

As VMs novas e existentes podem se beneficiar da instalação do LIS (Linux Integration Services mais recente). A otimização da taxa de transferência está no LIS, a partir de 4.2.2-2, embora as versões posteriores contenham aprimoramentos adicionais. Insira os seguintes comandos para instalar a LIS mais recente:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter as otimizações, é melhor criar uma VM com a versão mais recente com suporte, especificando os seguintes parâmetros:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

As VMs novas e existentes podem se beneficiar da instalação do LIS (Linux Integration Services mais recente). A otimização da taxa de transferência está no LIS, a partir de 4,2. Insira os seguintes comandos para baixar e instalar o LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4,2 para Hyper-V exibindo a [página de download](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos seguintes
* Consulte o resultado otimizado com [largura de banda/teste de taxa de transferência de VM do Azure](virtual-network-bandwidth-testing.md) para seu cenário.
* Leia sobre como [a largura de banda é alocada para máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com as perguntas frequentes sobre a [rede virtual do Azure](virtual-networks-faq.md)
