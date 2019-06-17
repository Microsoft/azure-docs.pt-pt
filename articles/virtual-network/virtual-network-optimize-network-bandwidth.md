---
title: Otimizar o débito de rede VM | Documentos da Microsoft
description: Saiba como otimizar o débito de rede de máquina virtual do Azure.
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
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125785"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar o débito de rede para máquinas virtuais do Azure

Máquinas virtuais do Azure (VM) tem as definições de rede predefinido que podem ser ainda mais otimizadas para débito de rede. Este artigo descreve como otimizar o débito de rede para o Microsoft Azure Windows e VMs do Linux, incluindo as principais distribuições como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se suportar a sua VM do Windows [redes aceleradas](create-vm-accelerated-networking-powershell.md), permitindo que essa funcionalidade seria a configuração ideal para a taxa de transferência. Para todas as outras VMs do Windows, usando a receber do lado da receção (RSS) pode chegar a maior débito máximo que uma VM sem RSS. RSS pode ser desabilitado por padrão numa VM do Windows. Para determinar se RSS está ativado e ativá-lo se ele está atualmente desabilitado, conclua os seguintes passos:

1. Se o RSS está ativado para um adaptador de rede com o `Get-NetAdapterRss` comando do PowerShell. No exemplo seguinte devolvido do `Get-NetAdapterRss`, RSS não está ativada.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Para ativar o RSS, introduza o seguinte comando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem uma saída. O comando alterar as definições da NIC, provocando a perda de conectividade temporário para cerca de um minuto. É apresentada uma caixa de diálogo Reconnecting durante a perda de conectividade. Normalmente, a conectividade é restaurada após a terceira tentativa.
3. Confirme que o RSS está ativado na VM ao introduzir o `Get-NetAdapterRss` novamente o comando. Se tiver êxito, é devolvido o resultado de exemplo seguinte:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM do Linux

RSS está sempre ativada por predefinição na VM Linux do Azure. Kernels de Linux liberados desde Outubro de 2017 incluem novas opções de otimizações de rede que permitem uma VM do Linux alcançar um débito de rede mais elevado.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implementações

O kernel de Ubuntu Azure fornece o melhor desempenho de rede no Azure e desde o kernel de padrão de 21 de Setembro de 2017. Para obter esta kernel, primeiro instale a versão mais recente suportada de 16.04-LTS, da seguinte forma:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Depois de concluída a criação, introduza os comandos seguintes para obter as atualizações mais recentes. Estes passos também funcionam para as VMs em execução o kernel de Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O seguinte conjunto de comando opcional pode ser útil para implementações de Ubuntu existentes que já tenham o kernel do Azure, mas que não foram ainda mais atualiza com erros.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Atualização de kernel do Ubuntu Azure de VMs existentes

Desempenho da produtividade significativa pode ser obtido com a atualização para o kernel de Linux do Azure. Para verificar se tem este kernel, verificar a sua versão de kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se a VM não tem o kernel do Azure, o número de versão normalmente começa com "4.4." Se a VM não tem o kernel do Azure, execute os seguintes comandos como raiz:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Para obter as mais recentes otimizações, é melhor criar uma VM com a versão mais recente suportada ao especificar os seguintes parâmetros:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

VMs novas e existentes podem se beneficiar de instalar os serviços de integração de Linux (LIS) mais recente. A otimização de débito é no LIS, a partir de 4.2.2-2, embora as versões posteriores contém ainda outras melhorias. Introduza os seguintes comandos para instalar o LIS mais recente:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Para obter as otimizações, é melhor criar uma VM com a versão mais recente suportada ao especificar os seguintes parâmetros:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

VMs novas e existentes podem se beneficiar de instalar os serviços de integração de Linux (LIS) mais recente. A otimização de débito é no LIS, a partir de 4.2. Introduza os comandos seguintes para transferir e instalar o LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre o Linux Integration Services versão 4.2 para Hyper-V, visualizando o [página de transferência](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos Seguintes
* Ver o resultado otimizado com [VM do Azure de teste de largura de banda/débito](virtual-network-bandwidth-testing.md) para o seu cenário.
* Saiba mais sobre como [largura de banda atribuída às máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com [rede Virtual do Azure, perguntas freqüentes (FAQ sobre)](virtual-networks-faq.md)
