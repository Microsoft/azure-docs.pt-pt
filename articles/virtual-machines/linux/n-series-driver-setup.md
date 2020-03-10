---
title: Conjunto de pilotos da série N-GPU da Azure para o Linux
description: Como configurar os pilotos da NVIDIA GPU para VMs da série N que executam o Linux em Azure
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944564"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instale os pilotos da NVIDIA GPU em VMs da série N que executam o Linux

Para tirar partido das capacidades da GPU dos VMs da série N Azure que executam o Linux, os condutores de GPU da NVIDIA devem ser instalados. A Extensão do [Condutor GPU da NVIDIA](../extensions/hpccompute-gpu-linux.md) instala os condutores adequados da NVIDIA CUDA ou grid num VM da série N. Instale ou gerea extensão utilizando o portal Azure ou ferramentas como os modelos Azure CLI ou Azure Resource Manager. Consulte a documentação de extensão do [condutor da NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) para distribuição e etapas de implementação suportadas.

Se optar por instalar manualmente os controladores gpu, este artigo fornece distribuições suportadas, controladores e etapas de instalação e verificação. As informações de configuração manual do controlador também estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para especificações VM da série N, capacidades de armazenamento e detalhes do disco, consulte os [tamanhos de VM GPU Linux](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instale os condutores cuda em VMs da série N

Aqui estão os passos para instalar os controladores CUDA do Kit de Ferramentas NVIDIA CUDA em VMs da série N. 


C C++ e desenvolvedores podem instalar opcionalmente o Toolkit completo para construir aplicações gpu-accelerated. Para mais informações, consulte o Guia de Instalação da [CUDA.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

Para instalar controladores CUDA, faça uma ligação SSH a cada VM. Para verificar se o sistema tem uma GPU capaz de CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Verá a saída semelhante ao seguinte exemplo (mostrando um cartão NVIDIA Tesla K80):

![saída de comando lspci](./media/n-series-driver-setup/lspci.png)

Em seguida, executar comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Descarregue e instale os controladores CUDA a partir do site da NVIDIA. Por exemplo, para Ubuntu 16.04 LTS:
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

2. Para instalar opcionalmente o conjunto completo de ferramentas CUDA, escreva:

   ```bash
   sudo apt-get install cuda
   ```

3. Reinicie o VM e proceda à verificação da instalação.

#### <a name="cuda-driver-updates"></a>Atualizações do condutor da CUDA

Recomendamos que atualize periodicamente os condutores de CUDA após a implantação.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux

1. Atualize o núcleo (recomendado). Se optar por não atualizar o núcleo, certifique-se de que as versões de `kernel-devel` e `dkms` são adequadas para o seu núcleo.

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
 
3. Religue-se ao VM e continue a instalação com os seguintes comandos:

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

4. Para instalar opcionalmente o conjunto completo de ferramentas CUDA, escreva:

   ```bash
   sudo yum install cuda
   ```

5. Reinicie o VM e proceda à verificação da instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Para consultar o estado do dispositivo GPU, SSH para o VM e executar o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador. 

Se o controlador estiver instalado, verá uma saída semelhante à seguinte. Note que a **GPU-Util** mostra 0% a menos que esteja atualmente a executar uma carga de trabalho de GPU no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos mostrados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade da rede RDMA

A conectividade da rede RDMA pode ser ativada em VMs de série N com capacidade RDMA, tais como NC24r implantados no mesmo conjunto de disponibilidade ou num único grupo de colocação num conjunto de escala VM. A rede RDMA suporta o tráfego de Interface de Passagem de Mensagens (MPI) para aplicações em execução com Intel MPI 5.x ou uma versão posterior. Seguem-se requisitos adicionais:

### <a name="distributions"></a>Distribuição

Implemente VMs de série N com capacidade RDMA a partir de uma das imagens no Azure Marketplace que suporta a conectividade RDMA em VMs da série N:
  
* **Ubuntu 16.04 LTS** - Configure os condutores rdma no VM e registe-se com a Intel para descarregar intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* Os controladores **CentOS 7.4 HPC** - RDMA e Intel MPI 5.1 estão instalados no VM.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instale controladores GRID em VMs da série NV ou NVv3

Para instalar os controladores NVIDIA GRID em VMs da série NV ou NVv3, faça uma ligação SSH a cada VM e siga os passos para a sua distribuição Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Execute o comando `lspci`. Verifique se o cartão Ou cartões NVIDIA M60 são visíveis como dispositivos PCI.

2. Instale atualizações.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Desative o condutor do kernel Nouveau, que é incompatível com o condutor da NVIDIA. (Utilize apenas o condutor da NVIDIA em NV ou NVv2 VMs.) Para tal, crie um ficheiro em `/etc/modprobe.d` denominado `nouveau.conf` com os seguintes conteúdos:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Reiniciar o VM e voltar a ligar. Servidor De saída X:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Descarregue e instale o controlador GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Quando lhe perguntarem se pretende executar o utilitário nvidia-xconfig para atualizar o seu ficheiro de configuração X, selecione **Sim**.

7. Após a instalação concluída, copie /etc/nvidia/gridd.conf.template para um novo ficheiro gridd.conf no local /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adicione o seguinte ao `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Retire o seguinte da `/etc/nvidia/gridd.conf` se estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie o VM e proceda à verificação da instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o núcleo e o DKMS (recomendado). Se optar por não atualizar o núcleo, certifique-se de que as versões de `kernel-devel` e `dkms` são adequadas para o seu núcleo.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Desative o condutor do kernel Nouveau, que é incompatível com o condutor da NVIDIA. (Utilize apenas o condutor da NVIDIA em VMs NV ou NV2.) Para tal, crie um ficheiro em `/etc/modprobe.d` denominado `nouveau.conf` com os seguintes conteúdos:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Reinicie o VM, religue e instale os mais recentes Serviços de [Integração Linux para Hyper-V e Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Religue-se ao VM e execute o comando `lspci`. Verifique se o cartão Ou cartões NVIDIA M60 são visíveis como dispositivos PCI.
 
5. Descarregue e instale o controlador GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando lhe perguntarem se pretende executar o utilitário nvidia-xconfig para atualizar o seu ficheiro de configuração X, selecione **Sim**.

7. Após a instalação concluída, copie /etc/nvidia/gridd.conf.template para um novo ficheiro gridd.conf no local /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adicione o seguinte ao `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Retire o seguinte da `/etc/nvidia/gridd.conf` se estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie o VM e proceda à verificação da instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação do condutor


Para consultar o estado do dispositivo GPU, SSH para o VM e executar o utilitário de linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador. 

Se o controlador estiver instalado, verá uma saída semelhante à seguinte. Note que a **GPU-Util** mostra 0% a menos que esteja atualmente a executar uma carga de trabalho de GPU no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos mostrados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se precisar de um servidor X11 para ligações remotas a um VM NV ou NVv2, [o x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque permite a aceleração do hardware dos gráficos. O BusID do dispositivo M60 deve ser adicionado manualmente ao ficheiro de configuração X11 (normalmente, `etc/X11/xorg.conf`). Adicione uma secção `"Device"` semelhante à seguinte:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Além disso, atualize a sua secção `"Screen"` para utilizar este dispositivo.
 
O BusID decimal pode ser encontrado correndo

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
O BusID pode mudar quando um VM é realojado ou reiniciado. Por isso, poderá querer criar um script para atualizar o BusID na configuração X11 quando um VM é reiniciado. Por exemplo, crie um script chamado `busidupdate.sh` (ou outro nome que escolher) com conteúdos semelhantes aos seguintes:

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

Em seguida, crie uma entrada para o seu script de atualização em `/etc/rc.d/rc3.d` para que o script seja invocado como raiz no arranque.

## <a name="troubleshooting"></a>Resolução de Problemas

* Pode definir o modo de persistência utilizando `nvidia-smi` para que a saída do comando seja mais rápida quando precisa de consultar cartões. Para definir o modo de persistência, execute `nvidia-smi -pm 1`. Note que se o VM for reiniciado, a definição do modo desaparece. Pode sempre escrever a definição de modo a executar no arranque.
* Se atualizou os controladores da NVIDIA CUDA para a versão mais recente e descobrir que a conectivcidade RDMA já não está a funcionar, [reinstale os controladores RDMA](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) para reaguizar essa conectividade. 

## <a name="next-steps"></a>Passos Seguintes

* Para capturar uma imagem De VM Linux com os seus controladores NVIDIA instalados, veja [como generalizar e capturar uma máquina virtual Linux](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
