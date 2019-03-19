---
title: Configuração de controladores GPU da série N do Azure para Linux | Documentos da Microsoft
description: Como configurar controladores de NVIDIA GPU para VMs de série N que executem o Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb597edc676fbb7b63c6a07849551cc21f69b354
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58015009"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instalar controladores NVIDIA GPU em VMs de série N que executem o Linux

Para tirar partido das capacidades GPU do Azure VMs de série N que executem o Linux, tem de estar instalados controladores NVIDIA GPU. O [extensão de controladores de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instala os controladores adequados NVIDIA CUDA ou GRADE numa VM de série N. Instalar ou gerir a extensão com o portal do Azure ou ferramentas, como os modelos da CLI do Azure ou Azure Resource Manager. Consulte a [documentação da extensão de controladores de GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) para as distribuições suportadas e passos de implementação.

Se optar por instalar manualmente os controladores de GPU, este artigo fornece as distribuições suportadas, drivers e passos de instalação e verificação. Informações de configuração manual de driver também estão disponíveis para [Windows VMs](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para VMS de série N especificações, as capacidades de armazenamento e detalhes do disco, consulte [tamanhos de VM do Linux de GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instalar controladores CUDA em VMs de série N

Eis os passos para instalar controladores CUDA do Toolkit NVIDIA CUDA em VMs de série N. 


Os desenvolvedores de C e C++, opcionalmente, podem instalar o Kit de ferramentas completo para criar aplicativos acelerado de GPU. Para obter mais informações, consulte a [guia de instalação CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Para instalar controladores CUDA, efetue uma ligação SSH a cada VM. Para verificar que o sistema tem uma GPU compatível com CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Verá o resultado semelhante ao seguinte exemplo (mostrando um cartão de NVIDIA Tesla K80):

![saída do comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, os comandos de instalação de execução específico para a sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Baixe e instale os controladores CUDA a partir do site da NVIDIA. Por exemplo, para o Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   A instalação pode demorar vários minutos.

2. Opcionalmente, instale o Kit de ferramentas completo do CUDA, escreva:

   ```bash
   sudo apt-get install cuda
   ```

3. Reinicie a VM e continue para verificar a instalação.

#### <a name="cuda-driver-updates"></a>Atualizações de controlador CUDA

Recomendamos que atualize periodicamente controladores CUDA após a implementação.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux

1. Atualize o kernel (recomendado). Se optar por não atualizar o kernel, certifique-se de que as versões do `kernel-devel` e `dkms` são adequadas para o kernel.

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
 
3. Voltar a ligar à VM e prosseguir com a instalação com os seguintes comandos:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A instalação pode demorar vários minutos. 

4. Opcionalmente, instale o Kit de ferramentas completo do CUDA, escreva:

   ```bash
   sudo yum install cuda
   ```

5. Reinicie a VM e continue para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de driver

Para consultar o estado do dispositivo GPU, SSH à VM e execute o [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver o utilitário da linha de comandos. 

Se o controlador está instalado, verá um resultado semelhante ao seguinte. Tenha em atenção que **GPU Util** mostra 0%, a menos que estiver executando atualmente uma carga de trabalho GPU na VM. Sua versão do controlador e detalhes GPU podem ser diferentes daqueles mostrados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade de rede RDMA

Conectividade de rede RDMA pode ser ativada em VMs de série N com capacidade RDMA como NC24r implementadas no mesmo conjunto de disponibilidade ou num único grupo de colocação num conjunto de dimensionamento VM. A rede RDMA suporta tráfego de Message Passing Interface (MPI) para aplicações em execução com Intel MPI 5.x ou uma versão posterior. Siga a requisitos adicionais:

### <a name="distributions"></a>Distribuições

Implemente VMs de série N com capacidade RDMA a partir de uma das imagens no Azure Marketplace que suporta a conetividade RDMA em VMs de série N:
  
* **Ubuntu 16.04 LTS** - configurar os controladores RDMA na VM e registar com o Intel para transferir a MPI Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **Com base no centOS 7.4 HPC** -drivers RDMA e Intel MPI 5.1 estão instaladas na VM.

## <a name="install-grid-drivers-on-nv-or-nvv2-series-vms"></a>Instalar controladores GRID em NV ou VMs da série NVv2

Para instalar controladores de GRID da NVIDIA em NV ou VMs da série NVv2, efetue uma ligação SSH a cada VM e siga os passos para a distribuição de Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Execute o comando `lspci`. Certifique-se de que a placa M60 da NVIDIA ou cartões ficam visíveis como dispositivos PCI.

2. Instale atualizações.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   ```
3. Desabilite o driver do kernel Nouveau, que é incompatível com o driver da NVIDIA. (Utilize apenas os controladores NVIDIA no NV ou NVv2 VMs.) Para tal, crie um ficheiro na `/etc/modprobe.d `com o nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Reinicie a VM e voltar a ligar. Servidor de saída X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Transfira e instale o controlador de GRADE:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Quando lhe for pedido que pretende executar o utilitário de nvidia xconfig para atualizar o ficheiro de configuração de X, selecione **Sim**.

7. Depois de concluída a instalação, copie /etc/nvidia/gridd.conf.template para um novo gridd.conf de ficheiro na localização /etc/nvidia /

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adicione o seguinte ao `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. Reinicie a VM e continue para verificar a instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o kernel e DKMS (recomendado). Se optar por não atualizar o kernel, certifique-se de que as versões do `kernel-devel` e `dkms` são adequadas para o kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   ```

2. Desabilite o driver do kernel Nouveau, que é incompatível com o driver da NVIDIA. (Utilize apenas os controladores NVIDIA no NV ou NV2 VMs.) Para tal, crie um ficheiro na `/etc/modprobe.d `com o nome `nouveau.conf` com o seguinte conteúdo:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Reinicie a VM, voltar a ligar e instalar a versão mais recente [serviços de integração do Linux para Hyper-V e o Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Voltar a ligar à VM e execute o `lspci` comando. Certifique-se de que a placa M60 da NVIDIA ou cartões ficam visíveis como dispositivos PCI.
 
5. Transfira e instale o controlador de GRADE:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando lhe for pedido que pretende executar o utilitário de nvidia xconfig para atualizar o ficheiro de configuração de X, selecione **Sim**.

7. Depois de concluída a instalação, copie /etc/nvidia/gridd.conf.template para um novo gridd.conf de ficheiro na localização /etc/nvidia /
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adicione o seguinte ao `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. Reinicie a VM e continue para verificar a instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação de driver


Para consultar o estado do dispositivo GPU, SSH à VM e execute o [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o driver o utilitário da linha de comandos. 

Se o controlador está instalado, verá um resultado semelhante ao seguinte. Tenha em atenção que **GPU Util** mostra 0%, a menos que estiver executando atualmente uma carga de trabalho GPU na VM. Sua versão do controlador e detalhes GPU podem ser diferentes daqueles mostrados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 server
Se precisar de um tipo de X11 servidor para ligações remotas para uma VM de NVv2, ou NV [x11vnc](http://www.karlrunge.com/x11vnc/) é recomendada porque permite a aceleração de hardware de gráficos. BusID do dispositivo M60 deve ser adicionado manualmente para o tipo de X11 ficheiro de configuração (normalmente, `etc/X11/xorg.conf`). Adicionar um `"Device"` secção semelhante ao seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Além disso, atualizar sua `"Screen"` secção para utilizar este dispositivo.
 
O decimal BusID pode ser encontrado ao executar

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
O BusID pode alterar quando uma VM obtém realocada ou reiniciada. Por conseguinte, pode querer criar um script para atualizar o BusID no X11 configuração quando uma VM é reiniciada. Por exemplo, criar um script chamado `busidupdate.sh` (ou outro nome que escolher) com conteúdo semelhante ao seguinte:

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

Em seguida, crie uma entrada para o seu script de atualização no `/etc/rc.d/rc3.d` para que o script for chamado como raiz no arranque.

## <a name="troubleshooting"></a>Resolução de problemas

* Pode definir a utilizar o modo de persistência `nvidia-smi` , de modo a saída do comando é mais rápida quando precisar de cartões de consulta. Para definir o modo de persistência, execute `nvidia-smi -pm 1`. Tenha em atenção que se a VM é reiniciada, a definição modo desaparece. Pode sempre criar um script a definição de modo a executar na inicialização.

## <a name="next-steps"></a>Passos Seguintes

* Para capturar uma imagem de VM do Linux com seus controladores NVIDIA instalados, consulte [como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
