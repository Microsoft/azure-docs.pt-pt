---
title: Prepare a imagem VM Azure para uso com cloud-init
description: Como preparar uma imagem VM Azure pré-existente para implantação com cloud-init
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066807"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Prepare uma imagem VM Linux Azure existente para uso com cloud-init
Este artigo mostra-lhe como pegar numa máquina virtual Azure existente e prepará-la para ser reimplantada e pronta para usar cloud-init. A imagem resultante pode ser usada para implantar uma nova máquina virtual ou conjuntos de escala de máquinavirtual - qualquer um dos quais poderia ser mais personalizado por cloud-init no momento de implantação.  Estes scripts de cloud-init funcionam na primeira bota uma vez que os recursos foram aprovisionados pelo Azure. Para mais informações sobre como o cloud-init funciona nativamente em Azure e os distros linux suportados, consulte a [visão geral cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que já tem uma máquina virtual Azure em execução com uma versão suportada do sistema operativo Linux. Já configurou a máquina de acordo com as suas necessidades, instalou todos os módulos necessários, processou todas as atualizações necessárias e testou-a para garantir que satisfaz os seus requisitos. 

## <a name="preparing-rhel-76--centos-76"></a>Preparação RHEL 7.6 / CentOS 7.6
Você precisa sSH no seu VM Linux e executar os seguintes comandos para instalar cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Atualize `cloud_init_modules` a `/etc/cloud/cloud.cfg` secção para incluir os seguintes módulos:

```bash
- disk_setup
- mounts
```

Aqui está uma amostra de `cloud_init_modules` como uma secção de propósito geral se parece.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

É necessário atualizar uma série de tarefas relativas ao fornecimento e `/etc/waagent.conf`manuseamento de discos efémeros . Executar os seguintes comandos para atualizar as definições apropriadas.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Permitir apenas o Azure como fonte de dados para `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` o Agente Azure Linux, criando um novo ficheiro utilizando um editor à sua escolha com a seguinte linha:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Se a sua imagem Azure existente tiver um ficheiro de swap configurado e pretender alterar a configuração do ficheiro swap para novas imagens utilizando o cloud-init, precisa de remover o ficheiro de swap existente.

Para imagens baseadas em Chapéu Vermelho - siga as instruções no seguinte documento do Chapéu Vermelho explicando como [remover o ficheiro de troca](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Para imagens CentOS com swapfile ativado, pode executar o seguinte comando para desligar o swapfile:

```bash
sudo swapoff /mnt/resource/swapfile
```

Certifique-se de que `/etc/fstab` a referência do swap file é removida - deve parecer algo como a seguinte saída:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para poupar espaço e remover o ficheiro de troca pode executar o seguinte comando:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Passo extra para imagem preparada em nuvem
> [!NOTE]
> Se a sua imagem foi previamente uma imagem preparada e configurada em **nuvem,** tem de fazer os seguintes passos.

Os seguintes três comandos só são utilizados se o VM que está a personalizar para ser uma nova imagem de origem especializada foi previamente fornecido por cloud-init.  Não precisa de executá-las se a sua imagem foi configurada utilizando o Agente Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizando a definição do agente Linux 
Todas as imagens da plataforma Azure têm o Agente Azure Linux instalado, independentemente de ter sido configurado por cloud-init ou não.  Executar o seguinte comando para terminar a desprovisionamento do utilizador da máquina Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para mais informações sobre os comandos de deprovisionamento do Agente Azure Linux, consulte o [Agente Azure Linux](../extensions/agent-linux.md) para obter mais detalhes.

Saia da sessão SSH, depois da sua concha de bash, execute os seguintes comandos AzureCLI para desalocar, generalizar e criar uma nova imagem Azure VM.  `myResourceGroup` Substitua `sourceVmName` e com as informações apropriadas que reflitam a sua fonteVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicação, atualize ficheiros de configuração e injete chaves](tutorial-automate-vm-deployment.md)
