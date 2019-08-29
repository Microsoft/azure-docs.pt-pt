---
title: Instalação do driver de GPU da série N do Azure para Linux | Microsoft Docs
description: Como configurar Drivers NVIDIA GPU para VMs da série N que executam o Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5ef060127840838778a00fdabd2d56b2ef23d6f4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082687"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar drivers NVIDIA GPU em VMs da série N executando Linux

Para aproveitar os recursos de GPU das VMs da série N do Azure que executam o Linux, os drivers NVIDIA GPU devem ser instalados. A [extensão de Driver Nvidia GPU](../extensions/hpccompute-gpu-linux.md) instala drivers NVIDIA CUDA ou Grid apropriados em uma VM da série N. Instale ou gerencie a extensão usando o portal do Azure ou ferramentas como os modelos CLI do Azure ou Azure Resource Manager. Consulte a [documentação de extensão do Driver Nvidia GPU](../extensions/hpccompute-gpu-linux.md) para obter as distribuições com suporte e as etapas de implantação.

Se você optar por instalar os drivers de GPU manualmente, este artigo fornecerá as distribuições, drivers e etapas de instalação e verificação com suporte. As informações de configuração manual do driver também estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter as especificações de VM da série N, as capacidades de armazenamento e os detalhes do disco, consulte [tamanhos de VM Linux da GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalar drivers do CUDA em VMs da série N

Aqui estão as etapas para instalar os drivers do CUDA do kit de ferramentas NVIDIA CUDA em VMs da série N. 


O C C++ e os desenvolvedores podem, opcionalmente, instalar o kit de ferramentas completo para criar aplicativos acelerados por GPU. Para obter mais informações, consulte o [Guia de instalação do CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Para instalar os drivers do CUDA, faça uma conexão SSH com cada VM. Para verificar se o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Você verá uma saída semelhante ao exemplo a seguir (mostrando um cartão NVIDIA Tesla K80):

![saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, execute os comandos de instalação específicos para sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Baixe e instale os drivers do CUDA do site do NVIDIA. Por exemplo, para o Ubuntu 16, 4 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   A instalação pode levar vários minutos.

2. Para instalar opcionalmente o kit de ferramentas CUDA completo, digite:

   ```bash
   sudo apt-get install cuda
   ```

3. Reinicialize a VM e prossiga para verificar a instalação.

#### <a name="cuda-driver-updates"></a>Atualizações do driver CUDA

É recomendável que você atualize periodicamente os drivers do CUDA após a implantação.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux

1. Atualize o kernel (recomendado). Se você optar por não atualizar o kernel, verifique se as versões do `kernel-devel` e `dkms` do são apropriadas para o kernel.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. Reconecte-se à VM e continue a instalação com os seguintes comandos:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A instalação pode levar vários minutos. 

4. Para instalar opcionalmente o kit de ferramentas CUDA completo, digite:

   ```bash
   sudo yum install cuda
   ```

5. Reinicialize a VM e prossiga para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação do driver

Para consultar o estado do dispositivo GPU, use o SSH para a VM e execute o utilitário de linha de comando [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída semelhante à seguinte. Observe que **GPU-util** mostra 0%, a menos que você esteja executando uma carga de trabalho de GPU na VM no momento. A versão do driver e os detalhes da GPU podem ser diferentes dos mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

A conectividade de rede RDMA pode ser habilitada em VMs da série N compatíveis com RDMA, como o NC24r implantado no mesmo conjunto de disponibilidade ou em um único grupo de posicionamento em um conjunto de dimensionamento de VM. A rede RDMA dá suporte ao tráfego de interface de transmissão de mensagens (MPI) para aplicativos executados com o Intel MPI 5. x ou uma versão posterior. Os requisitos adicionais são os seguintes:

### <a name="distributions"></a>Distribuições

Implante VMs da série N compatíveis com RDMA de uma das imagens no Azure Marketplace que dá suporte à conectividade RDMA em VMs da série N:
  
* **Ubuntu 16, 4 LTS** -configure os drivers RDMA na VM e registre-se na Intel para baixar o Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* Os drivers HPC-RDMA **7,4 baseados em CentOS** e o Intel MPI 5,1 estão instalados na VM.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instalar drivers de grade em VMs do NV ou da série NVv3

Para instalar os drivers NVIDIA GRID nas VMs do NV ou da série NVv3, faça uma conexão SSH com cada VM e siga as etapas para sua distribuição do Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Execute o comando `lspci`. Verifique se a placa ou os cartões NVIDIA M60 estão visíveis como dispositivos PCI.

2. Instalar atualizações.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use apenas o driver NVIDIA em VMs NV ou NVv2.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Reinicialize a VM e reconecte. Saia do servidor X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Baixe e instale o driver de grade:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Quando for perguntado se você deseja executar o utilitário NVIDIA-xconfig para atualizar o arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie/etc/NVIDIA/gridd.conf.Template para um novo arquivo grided. conf no local/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se ele estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicialize a VM e prossiga para verificar a instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o kernel e o DKMS (recomendado). Se você optar por não atualizar o kernel, verifique se as versões do `kernel-devel` e `dkms` do são apropriadas para o kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Desabilite o driver de kernel Nouveau, que é incompatível com o driver NVIDIA. (Use apenas o driver NVIDIA em VMs NV ou NV2.) Para fazer isso, crie um arquivo em `/etc/modprobe.d` nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Reinicialize a VM, reconecte e instale o [Integration Services Linux mais recente para o Hyper-V e o Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Reconecte-se à VM e execute `lspci` o comando. Verifique se a placa ou os cartões NVIDIA M60 estão visíveis como dispositivos PCI.
 
5. Baixe e instale o driver de grade:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando for perguntado se você deseja executar o utilitário NVIDIA-xconfig para atualizar o arquivo de configuração X, selecione **Sim**.

7. Após a conclusão da instalação, copie/etc/NVIDIA/gridd.conf.Template para um novo arquivo grided. conf no local/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adicione o seguinte a `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Remova o seguinte de `/etc/nvidia/gridd.conf` se ele estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicialize a VM e prossiga para verificar a instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação do driver


Para consultar o estado do dispositivo GPU, use o SSH para a VM e execute o utilitário de linha de comando [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver. 

Se o driver estiver instalado, você verá uma saída semelhante à seguinte. Observe que **GPU-util** mostra 0%, a menos que você esteja executando uma carga de trabalho de GPU na VM no momento. A versão do driver e os detalhes da GPU podem ser diferentes dos mostrados.

![Status do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se você precisar de um servidor X11 para conexões remotas a uma VM NV ou NVv2, o [x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque permite a aceleração de hardware de gráficos. O BusID do dispositivo M60 deve ser adicionado manualmente ao arquivo de configuração do X11 (geralmente, `etc/X11/xorg.conf`). Adicione uma `"Device"` seção semelhante à seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Além disso, atualize `"Screen"` sua seção para usar este dispositivo.
 
O BusID decimal pode ser encontrado executando

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
O BusID pode ser alterado quando uma VM é realocada ou reinicializada. Portanto, talvez você queira criar um script para atualizar o BusID na configuração de X11 quando uma VM é reinicializada. Por exemplo, crie um script chamado `busidupdate.sh` (ou outro nome que você escolher) com o conteúdo semelhante ao seguinte:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Em seguida, crie uma entrada para o script de `/etc/rc.d/rc3.d` atualização no para que o script seja invocado como raiz na inicialização.

## <a name="troubleshooting"></a>Resolução de problemas

* Você pode definir o modo de `nvidia-smi` persistência usando, portanto, a saída do comando é mais rápida quando você precisa consultar os cartões. Para definir o modo de persistência `nvidia-smi -pm 1`, execute. Observe que, se a VM for reiniciada, a configuração de modo desaparecerá. Você sempre pode criar um script da configuração de modo para executar na inicialização.

## <a name="next-steps"></a>Passos Seguintes

* Para capturar uma imagem de VM do Linux com os drivers NVIDIA instalados, consulte [como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
