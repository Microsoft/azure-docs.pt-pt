---
title: Otimizar a entrada da rede VM [ Microsoft Docs
description: Aprenda a otimizar a entrada da rede de máquinas virtuais Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690890"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a entrada da rede para máquinas virtuais Azure

As máquinas virtuais Azure (VM) têm definições de rede padrão que podem ser otimizadas para a entrada de rede. Este artigo descreve como otimizar a entrada de rede para Microsoft Azure Windows e VMs Linux, incluindo grandes distribuições como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se o seu Windows VM suportar [a Rede Acelerada,](create-vm-accelerated-networking-powershell.md)permitir essa funcionalidade seria a configuração ideal para a entrada. Para todos os outros VMs do Windows, a utilização de salvia lateral de receção (RSS) pode atingir uma entrada máxima mais alta do que um VM sem RSS. O RSS pode ser desativado por padrão num VM do Windows. Para determinar se o RSS está ativado e capacitá-lo se está atualmente desativado, complete os seguintes passos:

1. Veja se o RSS está ativado `Get-NetAdapterRss` para um adaptador de rede com o comando PowerShell. No exemplo seguinte, a `Get-NetAdapterRss`saída devolvida do RSS não está ativada.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Para ativar o RSS, introduza o seguinte comando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterou as definições de NIC, causando perda temporária de conectividade durante cerca de um minuto. Uma caixa de diálogo de religação aparece durante a perda de conectividade. A conectividade é tipicamente restaurada após a terceira tentativa.
3. Confirme que o RSS está ativado `Get-NetAdapterRss` no VM, entrando novamente no comando. Se for bem sucedido, a saída de exemplo seguinte é devolvida:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM do Linux

O RSS é sempre ativado por padrão num VM Azure Linux. Os kernels Linux lançados desde outubro de 2017 incluem novas opções de otimização de rede que permitem a um VM Linux obter uma maior entrada de rede.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implementações

O kernel Ubuntu Azure oferece o melhor desempenho de rede no Azure e tem sido o núcleo padrão desde 21 de setembro de 2017. Para obter este núcleo, instale primeiro a versão mais recente suportada de 16.04-LTS, da seguinte forma:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Depois de concluída a criação, insira os seguintes comandos para obter as últimas atualizações. Estes passos também funcionam para os VMs atualmente a executar o kernel Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O seguinte conjunto de comando opcional pode ser útil para as implementações ubuntu existentes que já têm o kernel Azure, mas que não conseguiram novas atualizações com erros.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Upgrade de kernel Ubuntu Azure para VMs existentes

Um desempenho significativo de entrada pode ser alcançado através da atualização para o kernel Azure Linux. Para verificar se tem este núcleo, verifique a sua versão kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se o seu VM não tiver o kernel Azure, o número da versão geralmente começa com "4.4". Se o VM não tiver o núcleo Azure, execute os seguintes comandos como raiz:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as mais recentes otimizações, o melhor é criar um VM com a versão mais recente suportada, especificando os seguintes parâmetros:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Os Novos EMs existentes podem beneficiar da instalação dos mais recentes Serviços de Integração linux (LIS). A otimização da entrada está no LIS, a partir de 4.2.2-2, embora as versões posteriores contenham mais melhorias. Introduza os seguintes comandos para instalar os mais recentes LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter as otimizações, o melhor é criar um VM com a versão mais recente suportada, especificando os seguintes parâmetros:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Os Novos EMs existentes podem beneficiar da instalação dos mais recentes Serviços de Integração linux (LIS). A otimização de entrada está no LIS, a partir de 4.2. Introduza os seguintes comandos para descarregar e instalar o LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre a Versão 4.2 dos Serviços de Integração linux para Hyper-V visualizando a [página de descarregamento](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos seguintes
* Consulte o resultado otimizado com [a largura de banda/teste de saída Azure VM](virtual-network-bandwidth-testing.md) para o seu cenário.
* Leia sobre como a [largura de banda é atribuída a máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com a [Rede Virtual Azure frequentemente colocada seleções (FAQ)](virtual-networks-faq.md)
