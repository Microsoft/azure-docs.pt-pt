---
title: Prepare a imagem Azure VM para uso com cloud-init
description: Como preparar uma imagem VM Azure pré-existente para implantação com cloud-init
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 6785bb1f5ebde4f746dc8aa1ea9f586f406f8431
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373679"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Prepare uma imagem VM Linux Azure existente para uso com cloud-init
Este artigo mostra-lhe como pegar numa máquina virtual Azure existente e prepará-la para ser redistribuída e pronta para usar a cloud-init. A imagem resultante pode ser usada para implantar uma nova máquina virtual ou conjuntos de escala de máquinas virtuais - qualquer um dos quais poderia ser personalizado por cloud-init no tempo de implementação.  Estes scripts de nuvem funcionam na primeira bota uma vez que os recursos foram a provisionados pela Azure. Para obter mais informações sobre como o cloud-init funciona de forma nativa em Azure e os distros de Linux suportados, consulte [a visão geral de cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que já tem uma máquina virtual Azure em funcionamento a executar uma versão suportada do sistema operativo Linux. Já configura a máquina de acordo com as suas necessidades, instalou todos os módulos necessários, processou todas as atualizações necessárias e testou-a para garantir que satisfaz os seus requisitos. 

## <a name="preparing-rhel-76--centos-76"></a>Preparação RHEL 7.6 / CentOS 7.6
Você precisa de SSH no seu Linux VM e executar os seguintes comandos para instalar cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Atualize a `cloud_init_modules` secção para incluir os `/etc/cloud/cloud.cfg` seguintes módulos:

```bash
- disk_setup
- mounts
```

Aqui está uma amostra de como é uma secção de propósito `cloud_init_modules` geral.

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

Uma série de tarefas relacionadas com o fornecimento e manuseamento de discos efémeros têm de ser atualizadas em `/etc/waagent.conf` . Executar os seguintes comandos para atualizar as definições apropriadas.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Permita apenas a Azure como fonte de dados para o Agente Azure Linux, criando um novo ficheiro `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` utilizando um editor à sua escolha com a seguinte linha:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Se a sua imagem Azure existente tiver um ficheiro de troca configurado e pretender alterar a configuração do ficheiro swap para novas imagens utilizando o cloud-init, tem de remover o ficheiro de troca existente.

Para imagens baseadas em Chapéu Vermelho - siga as instruções no documento do Chapéu Vermelho seguinte, explicando como [remover o ficheiro de troca](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Para imagens CentOS com swapfile ativada, pode executar o seguinte comando para desligar o ficheiro de troca:

```bash
sudo swapoff /mnt/resource/swapfile
```

Certifique-se de que a referência do ficheiro de swap é removida `/etc/fstab` - deve parecer algo como a seguinte saída:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para guardar espaço e remover o ficheiro swap pode executar o seguinte comando:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Passo extra para imagem preparada em nuvem
> [!NOTE]
> Se a sua imagem foi previamente uma imagem preparada e configurada **em nuvem,** tem de fazer os seguintes passos.

Os três comandos seguintes só são utilizados se o VM que está a personalizar para ser uma nova imagem de origem especializada foi previamente a provisionado por cloud-init.  NÃO precisa de executá-las se a sua imagem foi configurada usando o Agente Azure Linux.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Definição de agente linux finalizador 
Todas as imagens da plataforma Azure têm o Agente Azure Linux instalado, independentemente de ter sido configurado por cloud-init ou não.  Executar o seguinte comando para terminar de desprovisionar o utilizador da máquina Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obter mais informações sobre os comandos de desprovisionamento do Agente Azure Linux, consulte o [Agente Azure Linux](../extensions/agent-linux.md) para obter mais detalhes.

Saia da sessão SSH, em seguida, a partir da sua concha de bash, executar os seguintes comandos AzureCLI para negociar, generalizar e criar uma nova imagem Azure VM.  Substitua `myResourceGroup` e `sourceVmName` pelas informações adequadas que reflitam a sua fonteVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais de alterações de configuração, consulte o seguinte:
 
- [Adicione um utilizador Linux adicional a um VM](cloudinit-add-user.md)
- [Executar um gestor de pacotes para atualizar os pacotes existentes na primeira bota](cloudinit-update-vm.md)
- [Alterar nome de anfitrião local VM](cloudinit-update-vm-hostname.md) 
- [Instale um pacote de aplicações, atualize ficheiros de configuração e injete teclas](tutorial-automate-vm-deployment.md)
