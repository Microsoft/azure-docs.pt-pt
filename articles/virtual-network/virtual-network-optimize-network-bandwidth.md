---
title: Otimizar o rendimento da rede VM Microsoft Docs
description: Otimizar a produção de rede para o Microsoft Azure Windows e Linux VMs, incluindo grandes distribuições como Ubuntu, CentOS e Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 20403b8c45120a53ea38fbbed60c8f96fd9d55e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812850"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Otimizar a produção de rede para máquinas virtuais Azure

As máquinas virtuais Azure (VM) têm definições de rede predefinidas que podem ser otimizadas para o rendimento da rede. Este artigo descreve como otimizar a produção de rede para o Microsoft Azure Windows e Linux VMs, incluindo grandes distribuições como Ubuntu, CentOS e Red Hat.

## <a name="windows-vm"></a>VM do Windows

Se o seu VM do Windows suportar [rede acelerada,](create-vm-accelerated-networking-powershell.md)ativar essa funcionalidade seria a configuração ideal para a produção. Para todos os outros VMs do Windows, a utilização de Receive Side Scaling (RSS) pode atingir uma produção máxima mais alta do que um VM sem RSS. O RSS pode ser desativado por predefinição num VM do Windows. Para determinar se o RSS está ativado e capacitá-lo se está atualmente desativado, complete os seguintes passos:

1. Veja se o RSS está ativado para um adaptador de rede com o `Get-NetAdapterRss` comando PowerShell. No exemplo seguinte, a saída devolvida do `Get-NetAdapterRss` RSS não está ativada.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Para ativar o RSS, insira o seguinte comando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    O comando anterior não tem saída. O comando alterou as definições de NIC, causando perda temporária de conectividade durante cerca de um minuto. Uma caixa de diálogo de reconectação aparece durante a perda de conectividade. A conectividade é tipicamente restaurada após a terceira tentativa.
3. Confirme se o RSS está ativado no VM, entrando novamente no `Get-NetAdapterRss` comando. Se for bem sucedida, a saída de exemplo a seguir é devolvida:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>VM do Linux

O RSS é sempre ativado por padrão num VM Azure Linux. Os kernels Linux lançados desde outubro de 2017 incluem novas opções de otimização de rede que permitem a um Linux VM alcançar uma maior produção de rede.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu para novas implementações

O kernel Ubuntu Azure é o mais otimizado para o desempenho da rede em Azure. Para obter as mais recentes otimizações, instale primeiro a versão mais recente suportada de 18.04-LTS, da seguinte forma:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

Depois de concluída a criação, insira os seguintes comandos para obter as últimas atualizações. Estes passos também funcionam para VMs atualmente com o kernel Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

O seguinte conjunto de comandos opcional pode ser útil para as implementações ubuntu existentes que já têm o kernel Azure, mas que não conseguiram novas atualizações com erros.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Upgrade do kernel Ubuntu Azure para os VM existentes

Um desempenho significativo de produção pode ser alcançado através da atualização para o núcleo Azure Linux. Para verificar se tem este núcleo, verifique a sua versão kernel. Deve ser o mesmo ou mais tarde do que o exemplo.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Se o seu VM não tiver o núcleo Azure, o número da versão geralmente começa com "4.4". Se o VM não tiver o núcleo Azure, executar os seguintes comandos como raiz:

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
"Sku": "7.7",
"Version": "latest"
```

Os VM novos e existentes podem beneficiar da instalação dos mais recentes Serviços de Integração Linux (LIS). A otimização de produção está no LIS, a partir de 4.2.2-2, embora as versões posteriores contenham melhorias adicionais. Introduza os seguintes comandos para instalar o LIS mais recente:

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

Os VM novos e existentes podem beneficiar da instalação dos mais recentes Serviços de Integração Linux (LIS). A otimização de produção está em LIS, a partir de 4.2. Introduza os seguintes comandos para descarregar e instalar LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Saiba mais sobre a Versão 4.2 dos Serviços de Integração Linux para Hiper-V visualizando a página de [descarregamento](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Passos seguintes
* Implementar VMs próximos uns dos outros para baixa latência com [Grupo de Colocação de Proximidade](../virtual-machines/windows/co-location.md)
* Consulte o resultado otimizado com [bandwidth/Throughput testando Azure VM](virtual-network-bandwidth-testing.md) para o seu cenário.
* Leia sobre como [a largura de banda é atribuída a máquinas virtuais](virtual-machine-network-throughput.md)
* Saiba mais com [a Azure Virtual Network perguntas frequentes (FAQ)](virtual-networks-faq.md)
