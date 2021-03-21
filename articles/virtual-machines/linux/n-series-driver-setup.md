---
title: Azure N-series GPU configuração de piloto para Linux
description: Como configurar os pilotos da NVIDIA GPU para VMs da série N que executam o Linux em Azure
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: c4c6bee6d3f9e423d83458ad48d213fe65223514
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551765"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Instale os controladores da NVIDIA GPU em VMs da série N que executam o Linux

Para tirar partido das capacidades da GPU de VMs da série Azure N apoiados pelos GPUs da NVIDIA, tem de instalar os controladores gpu da NVIDIA. A [extensão do condutor do GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instala controladores NVIDIA CUDA ou GRID apropriados num VM da série N. Instale ou gere a extensão utilizando o portal Azure ou ferramentas como os modelos Azure CLI ou Azure Resource Manager. Consulte a documentação da [extensão do condutor do GPU da NVIDIA](../extensions/hpccompute-gpu-linux.md) para distribuição e etapas de implantação suportadas.

Se optar por instalar manualmente os controladores da NVIDIA GPU, este artigo fornece distribuições suportadas, controladores e etapas de instalação e verificação. Informações de configuração manual do controlador também estão disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md).

Para especificações VM da série N, capacidades de armazenamento e detalhes do disco, consulte [os tamanhos VM da GPU Linux](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Instale os controladores CUDA em VMs da série N

Aqui estão os passos para instalar os controladores CUDA do Kit de Ferramentas CUDA NVIDIA em VMs da série N. 

Os desenvolvedores C e C++ podem opcionalmente instalar o Kit de Ferramentas completo para construir aplicações aceleradas pela GPU. Para mais informações, consulte o [Guia de Instalação da CUDA.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

Para instalar os controladores CUDA, faça uma ligação SSH a cada VM. Para verificar se o sistema tem uma GPU com capacidade para a CUDA, execute o seguinte comando:

```bash
lspci | grep -i NVIDIA
```
Verá uma saída semelhante ao seguinte exemplo (mostrando uma carta NVIDIA Tesla K80):

![Saída de comando lspci](./media/n-series-driver-setup/lspci.png)

O ISPCI lista os dispositivos PCIe no VM, incluindo o InfiniBand NIC e GPUs, se houver. Se o LSPCI não regressar com sucesso, poderá ter de instalar o LIS no CentOS/RHEL (instruções abaixo).
Em seguida, executar comandos de instalação específicos para a sua distribuição.

### <a name="ubuntu"></a>Ubuntu 

1. Faça o download e instale os controladores CUDA a partir do site da NVIDIA. 
    > [!NOTE]
   >  O exemplo abaixo mostra o caminho do pacote CUDA para Ubuntu 16.04. Substitua o caminho específico da versão que pretende utilizar. 
   >  
   >  Visite o [Nvidia Download Center] https://developer.download.nvidia.com/compute/cuda/repos/) (para o percurso completo específico de cada versão. 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

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

Recomendamos que atualize periodicamente os controladores CUDA após a implantação.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux

1. Atualize o núcleo (recomendado). Se optar por não atualizar o núcleo, certifique-se de que as versões `kernel-devel` e `dkms` são apropriadas para o seu núcleo.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Instale os mais recentes [Serviços de Integração Linux para Hiper-V e Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Verifique se o LIS é necessário verificando os resultados do lspci. Se todos os dispositivos GPU estiverem listados como esperado (e documentados acima), não é necessário instalar LIS.

   Por favor, note que o LIS é aplicável à Red Hat Enterprise Linux, CentOS e ao Oracle Linux Red Hat Compatible Kernel 5.2-5.11, 6.0-6.10 e 7.0-7.7. Consulte a documentação [Linux Integration Services] https://www.microsoft.com/en-us/download/details.aspx?id=55106) (para mais detalhes. 
   Ignore este passo se planeia utilizar CentOS/RHEL 7.8 (ou versões mais altas) uma vez que o LIS já não é necessário para estas versões.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Voltar a ligar-se ao VM e continuar a instalação com os seguintes comandos:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A instalação pode demorar vários minutos. 

4. Para instalar opcionalmente o conjunto completo de ferramentas CUDA, escreva:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Se vir uma mensagem de erro relacionada com pacotes em falta como o sistema de ficheiros vulkan, então poderá ter de editar /etc/yum.repos.d/rh-cloud, procure opcionais-rpms e esteja ativado para 1
   >  

5. Reinicie o VM e proceda à verificação da instalação.

### <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Para consultar o estado do dispositivo GPU, SSH para o VM e executar o utilitário da linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador. 

Se o controlador estiver instalado, verá uma saída semelhante à seguinte. Note que **a GPU-Util** mostra 0% a menos que esteja atualmente a executar uma carga de trabalho gpu no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos apresentados.

![Estado do dispositivo NVIDIA](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>Conectividade da rede RDMA

A conectividade da rede RDMA pode ser ativada em VMs da série N com capacidade RDMA, tais como NC24r implantados no mesmo conjunto de disponibilidade ou num único grupo de colocação num conjunto de escala de máquina virtual (VM). A rede RDMA suporta o tráfego de Interface de Passagem de Mensagens (MPI) para aplicações em execução com Intel MPI 5.x ou uma versão posterior. Os requisitos adicionais seguem-se:

### <a name="distributions"></a>Distribuições

Implementar VMs da série N capaz de RDMA a partir de uma das imagens no Azure Marketplace que suporta a conectividade RDMA em VMs da série N:
  
* **Ubuntu 16.04 LTS** - Configurar os controladores RDMA no VM e registar-se com a Intel para descarregar a Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-based 7.4 HPC** - RdMA drivers e Intel MPI 5.1 estão instalados no VM.

* **HPC baseado em CentOS** - CentOS-HPC 7.6 e posteriormente (para SKUs onde a InfiniBand é suportada sobre SR-IOV). Estas imagens têm bibliotecas Mellanox OFED e MPI pré-instaladas.

> [!NOTE]
> CX3-Pro cartões são suportados apenas através de versões LTS de Mellanox OFED. Utilize a versão LTS Mellanox OFED (4.9-0.1.7.0) nos VMs da série N com cartões ConnectX3-Pro. Para mais informações, consulte [a Linux Drivers.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)
>
> Além disso, algumas das imagens mais recentes do Azure Marketplace HPC têm Mellanox OFED 5.1 e mais tarde, que não suportam ConnectX3-Pro cartões. Verifique a versão Mellanox OFED na imagem HPC antes de a utilizar em VMs com cartões ConnectX3-Pro.
>
> As imagens a seguir são as imagens centos-HPC mais recentes que suportam ConnectX3-Pro cartões:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Instale controladores GRID em VMs da série NV ou NVv3

Para instalar os controladores NVIDIA GRID em VMs da série NV ou NVv3, faça uma ligação SSH a cada VM e siga os passos para a sua distribuição Linux. 

### <a name="ubuntu"></a>Ubuntu 

1. Execute o comando `lspci`. Verifique se o cartão ou cartões NVIDIA M60 são visíveis como dispositivos PCI.

2. Instale atualizações.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Desative o condutor do núcleo Nouveau, que é incompatível com o condutor da NVIDIA. (Utilize apenas o controlador NVIDIA em NV ou NVv2 VMs.) Para tal, crie um ficheiro `/etc/modprobe.d` com o nome com os `nouveau.conf` seguintes conteúdos:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Reinicie o VM e reconecte-se. Servidor de saída X:

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

7. Após a instalação concluída, copiar /etc/nvidia/gridd.conf.template para um novo ficheiro gridd.conf no local /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adicione o seguinte `/etc/nvidia/gridd.conf` a:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Retire o seguinte de `/etc/nvidia/gridd.conf` se estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie o VM e proceda à verificação da instalação.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS ou Red Hat Enterprise Linux 

1. Atualize o núcleo e o DKMS (recomendado). Se optar por não atualizar o núcleo, certifique-se de que as versões `kernel-devel` e `dkms` são apropriadas para o seu núcleo.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Desative o condutor do núcleo Nouveau, que é incompatível com o condutor da NVIDIA. (Utilize apenas o controlador NVIDIA em NV ou NV3 VMs.) Para tal, crie um ficheiro `/etc/modprobe.d` com o nome com os `nouveau.conf` seguintes conteúdos:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Reinicie o VM, reconecte e instale os mais recentes [Serviços de Integração Linux para Hiper-V e Azure.](https://www.microsoft.com/download/details.aspx?id=55106) Verifique se o LIS é necessário verificando os resultados do lspci. Se todos os dispositivos GPU estiverem listados como esperado (e documentados acima), não é necessário instalar LIS. 

   Ignore este passo se planeia utilizar CentOS/RHEL 7.8 (ou versões mais altas) uma vez que o LIS já não é necessário para estas versões.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Voltar a ligar-se ao VM e executar o `lspci` comando. Verifique se o cartão ou cartões NVIDIA M60 são visíveis como dispositivos PCI.
 
5. Descarregue e instale o controlador GRID:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Quando lhe perguntarem se pretende executar o utilitário nvidia-xconfig para atualizar o seu ficheiro de configuração X, selecione **Sim**.

7. Após a instalação concluída, copiar /etc/nvidia/gridd.conf.template para um novo ficheiro gridd.conf no local /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adicione o seguinte `/etc/nvidia/gridd.conf` a:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Retire o seguinte de `/etc/nvidia/gridd.conf` se estiver presente:
 
   ```
   FeatureType=0
   ```
10. Reinicie o VM e proceda à verificação da instalação.


### <a name="verify-driver-installation"></a>Verificar a instalação do condutor


Para consultar o estado do dispositivo GPU, SSH para o VM e executar o utilitário da linha de comando [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) instalado com o controlador. 

Se o controlador estiver instalado, verá uma saída semelhante à seguinte. Note que **a GPU-Util** mostra 0% a menos que esteja atualmente a executar uma carga de trabalho gpu no VM. A versão do seu condutor e os detalhes da GPU podem ser diferentes dos apresentados.

![Screenshot que mostra a saída quando o estado do dispositivo GPU é questionado.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Servidor X11
Se necessitar de um servidor X11 para ligações remotas a um NV ou VM NVv2, [o x11vnc](http://www.karlrunge.com/x11vnc/) é recomendado porque permite a aceleração de hardware de gráficos. O BusID do dispositivo M60 deve ser adicionado manualmente ao ficheiro de configuração X11 (normalmente, `etc/X11/xorg.conf` ). Adicione uma `"Device"` secção semelhante às seguintes:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Além disso, atualize a sua `"Screen"` secção para utilizar este dispositivo.
 
O BusID decimal pode ser encontrado correndo

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
O BusID pode mudar quando um VM é realojado ou reiniciado. Portanto, é melhor criar um script para atualizar o BusID na configuração X11 quando um VM é reiniciado. Por exemplo, crie um script nomeado `busidupdate.sh` (ou outro nome que escolher) com conteúdos semelhantes aos seguintes:

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

Em seguida, crie uma entrada para o seu script de atualização `/etc/rc.d/rc3.d` para que o script seja invocado como raiz no arranque.

## <a name="troubleshooting"></a>Resolução de problemas

* Pode definir o modo de persistência utilizando `nvidia-smi` para que a saída do comando seja mais rápida quando necessitar de consultar cartões. Para definir o modo de persistência, execute `nvidia-smi -pm 1` . Note que se o VM for reiniciado, a definição de modo desaparece. Pode sempre escrever a definição de modo para executar no arranque.
* Se atualizou os controladores da NVIDIA CUDA para a versão mais recente e descobriu que a conectividade RDMA já não está a funcionar, [reinstale os controladores RDMA](#rdma-network-connectivity) para restabelecer essa conectividade. 
* Durante a instalação do LIS, se uma determinada versão CentOS/RHEL OS (ou kernel) não for suportada para LIS, é lançado um erro "versão kernel não suportada". Por favor, reporte este erro juntamente com as versões SO e kernel.

## <a name="next-steps"></a>Passos seguintes

* Para capturar uma imagem Linux VM com os seus controladores NVIDIA instalados, consulte [Como generalizar e capturar uma máquina virtual Linux](capture-image.md).
