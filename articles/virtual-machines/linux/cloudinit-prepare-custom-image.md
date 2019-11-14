---
title: Preparar a imagem de VM do Azure para uso com Cloud-init
description: Como preparar uma imagem de VM do Azure pré-existente para implantação com Cloud-init
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
ms.openlocfilehash: a75bceebe584522ee999f86664b8afb9fa00f17b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036738"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Preparar uma imagem de VM do Azure do Linux existente para uso com Cloud-init
Este artigo mostra como usar uma máquina virtual do Azure existente e prepará-la para ser reimplantada e pronta para usar Cloud-init. A imagem resultante pode ser usada para implantar uma nova máquina virtual ou conjuntos de dimensionamento de máquinas virtuais – qualquer uma delas pode ser personalizada ainda mais pela Cloud-init no momento da implantação.  Esses scripts de Cloud-init são executados na primeira inicialização depois que os recursos são provisionados pelo Azure. Para obter mais informações sobre como a Cloud-init funciona nativamente no Azure e o distribuições do Linux com suporte, consulte [visão geral de Cloud-init](using-cloud-init.md)

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você já tem uma máquina virtual do Azure em execução que executa uma versão com suporte do sistema operacional Linux. Você já configurou o computador para atender às suas necessidades, instalou todos os módulos necessários, processou todas as atualizações necessárias e o testou para garantir que atende às suas necessidades. 

## <a name="preparing-rhel-76--centos-76"></a>Preparando o RHEL 7,6/CentOS 7,6
Você precisa de SSH em sua VM do Linux e executar os seguintes comandos para instalar o Cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Atualize a seção `cloud_init_modules` no `/etc/cloud/cloud.cfg` para incluir os seguintes módulos:
```bash
- disk_setup
- mounts
```

Aqui está um exemplo de como é uma seção de `cloud_init_modules` de uso geral.
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
Várias tarefas relacionadas ao provisionamento e ao manuseio de discos efêmeras precisam ser atualizadas no `/etc/waagent.conf`. Execute os comandos a seguir para atualizar as configurações apropriadas. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Permita apenas o Azure como uma fonte de arquivos para o agente Linux do Azure criando um novo arquivo `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` usando um editor de sua escolha com a seguinte linha:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Se a imagem do Azure existente tiver um arquivo de permuta configurado e você quiser alterar a configuração do arquivo de permuta para novas imagens usando Cloud-init, será necessário remover o arquivo de permuta existente.

Para imagens baseadas em Red Hat-siga as instruções no seguinte documento do Red Hat explicando como [remover o arquivo de permuta](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

Para imagens CentOS com o Swapfile habilitado, você pode executar o seguinte comando para desativar o Swapfile:
```bash
sudo swapoff /mnt/resource/swapfile
```

Verifique se a referência de Swapfile foi removida do `/etc/fstab`-ela deve ser semelhante à seguinte saída:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Para economizar espaço e remover o arquivo de permuta, você pode executar o seguinte comando:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Etapa adicional para imagem preparada de Cloud-init
> [!NOTE]
> Se sua imagem era anteriormente uma imagem configurada e preparada **por Cloud-init** , você precisa executar as etapas a seguir.

Os três comandos a seguir só serão usados se a VM que você está Personalizando para ser uma nova imagem de origem especializada tiver sido provisionada anteriormente por Cloud-init.  Você não precisará executá-los se a imagem tiver sido configurada usando o agente Linux do Azure.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Finalizando a configuração do agente do Linux 
Todas as imagens da plataforma Azure têm o agente Linux do Azure instalado, independentemente de ter sido configurada por Cloud-init ou não.  Execute o comando a seguir para concluir o desprovisionamento do usuário do computador Linux. 

```bash
sudo waagent -deprovision+user -force
```

Para obter mais informações sobre os comandos de desprovisionamento do agente Linux do Azure, consulte o [agente Linux do Azure](../extensions/agent-linux.md) para obter mais detalhes.

Saia da sessão SSH e, em seguida, em seu shell bash, execute os seguintes comandos AzureCLI para desalocar, generalizar e criar uma nova imagem de VM do Azure.  Substitua `myResourceGroup` e `sourceVmName` com as informações apropriadas refletindo seu sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Passos seguintes
Para obter exemplos adicionais de inicialização em nuvem de alterações de configuração, consulte o seguinte:
 
- [Adicionar um usuário Linux adicional a uma VM](cloudinit-add-user.md)
- [Executar um Gerenciador de pacotes para atualizar os pacotes existentes na primeira inicialização](cloudinit-update-vm.md)
- [Alterar nome de host local da VM](cloudinit-update-vm-hostname.md) 
- [Instalar um pacote de aplicativos, atualizar arquivos de configuração e inserir chaves](tutorial-automate-vm-deployment.md)
