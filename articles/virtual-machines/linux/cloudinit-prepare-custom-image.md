---
title: Preparar a imagem de VM do Azure para utilização com o cloud-init | Documentos da Microsoft
description: Como preparar uma imagem de VM do Azure já existente para a implementação com o cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 1f9f6042b52c722280a8227754960ffb270e94b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668257"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparar uma imagem de VM do Linux do Azure existente para utilização com o cloud-init
Este artigo mostra-lhe como utilizar uma máquina virtual do Azure existente e prepará-lo para ser reimplantados e pronto para utilizar o cloud-init. A imagem resultante pode ser utilizada para implementar uma nova máquina virtual ou conjuntos de dimensionamento de máquinas virtuais - que poderia, em seguida, ser ainda mais personalizadas ao cloud-init no momento da implementação.  Executam estes scripts de inicialização da cloud no primeiro arranque, assim que os recursos foram aprovisionados através do Azure. Para obter mais informações sobre o cloud-init funcionamento nativamente no Azure e distribuições de Linux suportadas, consulte [descrição geral do cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento assume que já tem uma máquina virtual do Azure em execução, executar uma versão suportada do sistema operativo Linux. Já configurou a máquina para se adequar às suas necessidades, instalado os módulos necessários, processar todas as atualizações necessárias e tiver testado de modo a garantir que cumpre os seus requisitos. 

## <a name="preparing-rhel-76--centos-76"></a>A preparar a 7,6 do RHEL / CentOS 7,6
Precisa para encaminhar o SSH à VM do Linux e execute os seguintes comandos para instalar o cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Atualização do `cloud_init_modules` secção `/etc/cloud/cloud.cfg` para incluir os seguintes módulos:
```bash
- disk_setup
- mounts
```

Eis um exemplo do que uma para fins gerais `cloud_init_modules` secção é semelhante.
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
Um número de tarefas relacionadas ao provisionamento e lidar com discos efémeros precisa ser atualizados no `/etc/waagent.conf`. Execute os seguintes comandos para atualizar as definições apropriadas. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Permitir apenas do Azure como uma origem de dados para o agente Linux do Azure através da criação de um novo ficheiro `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` com um editor à sua escolha com a seguinte linha:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Se a sua imagem do Azure existente tiver um ficheiro de comutação configurado e pretender alterar a configuração do ficheiro de troca de novas imagens com o cloud-init, terá de remover o ficheiro de troca existente.

Do Red Hat com base em imagens – siga as instruções em explicar documento Red Hat seguintes como [remova o ficheiro de troca](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Para imagens de CentOS com swapfile ativada, pode executar o comando seguinte para desativar o swapfile:
```bash
sudo swapoff /mnt/resource/swapfile
```

Certifique-se de que a referência de swapfile é removida do `/etc/fstab` -deve ser algo como a seguinte saída:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para economizar espaço e remova o ficheiro de troca pode executar o seguinte comando:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Etapa extra para imagem preparada do cloud-init
> [!NOTE]
> Se a sua imagem era anteriormente uma **cloud-init** imagem preparada e configurada, terá de efetuar os passos seguintes.

Os comandos de três seguintes são utilizados apenas se a VM estiver personalizando a ser uma nova imagem de origem especializadas anteriormente foi aprovisionada pelo cloud-init.  Não é necessário executá-los se a imagem foi configurada com o agente Linux do Azure.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>A finalizar o agente Linux definição 
Todas as imagens de plataforma do Azure têm o agente do Linux do Azure instalada, independentemente se foi configurado pelo cloud-init ou não.  Execute o comando seguinte para concluir o desaprovisionamento do utilizador da máquina Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obter mais informações sobre os comandos de desaprovisionamento do agente Linux do Azure, consulte a [agente Linux do Azure](../extensions/agent-linux.md) para obter mais detalhes.

Saia da sessão SSH e, em seguida, a partir do shell de bash, execute os seguintes comandos de AzureCLI desalocar, generalize e criar uma nova imagem de VM do Azure.  Substitua `myResourceGroup` e `sourceVmName` com as informações apropriadas, refletindo o sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos de cloud-init adicionais das alterações de configuração, consulte o seguinte:
 
- [Adicionar um utilizador de Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gestor de pacotes para atualizar os pacotes existentes no primeiro arranque](cloudinit-update-vm.md)
- [Alterar o nome de anfitrião VM local](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicação, atualizar ficheiros de configuração e injetar chaves](tutorial-automate-vm-deployment.md)
