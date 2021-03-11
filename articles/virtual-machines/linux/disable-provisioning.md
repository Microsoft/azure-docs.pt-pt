---
title: Desativar ou remover o agente de provisionamento
description: Saiba como desativar ou remover o agente de provisionamento em VMs e imagens Linux.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 7c797957c292b9859ca41951b15f58c3d0be40b2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561076"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Desativar ou remover o Agente Linux de VMs e imagens

Antes de remover o Agente Linux, deve entender o que a VM não poderá fazer depois de o Agente Linux ser removido.

As extensões de máquina virtual Azure (VM) são [pequenas](../extensions/overview.md) aplicações que fornecem tarefas de configuração e automatização pós-implantação em VMs Azure, as extensões são instaladas e geridas pelo plano de controlo Azure. Compete ao Agente [Azure Linux](../extensions/agent-linux.md) processar os comandos de extensão da plataforma e garantir o estado correto da extensão dentro do VM.

A plataforma Azure acolhe muitas extensões que vão desde a configuração de VM, monitorização, segurança e aplicações de utilidade. Há uma grande escolha de extensões de primeira e de terceiros, exemplos de cenários-chave para os seguintes para:
* Serviços de apoio a Azure de primeira parte, tais como Azure Backup, Monitoring, Encriptação de Disco, Segurança, Replicação do Site e outros.
* Resets SSH / Password
* Configuração VM - Executando scripts personalizados, instalando Chef, agentes de marionetas, etc..
* Produtos de terceiros, tais como produtos AV, ferramentas de vulnerabilidade VM, ferramentas de monitorização de VM e App.
* As extensões podem ser agregadas com uma nova implantação em VM. Por exemplo, podem fazer parte de uma implantação maior, configurando aplicações na provisão VM, ou correr contra qualquer sistema operado por extensão suportado após a implementação.

## <a name="disabling-extension-processing"></a>Desativação do processamento de extensão

Existem várias formas de desativar o processamento de extensão, dependendo das suas necessidades, mas antes de continuar, **deve** remover todas as extensões implantadas no VM, por exemplo usando o Azure CLI, pode [listar](/cli/azure/vm/extension#az-vm-extension-list) e [eliminar:](/cli/azure/vm/extension#az-vm-extension-delete)

```azurecli
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```
> [!Note]
> 
> Se não fizer o que precede, a plataforma tentará enviar a configuração de extensão e o tempo limite após 40min.

### <a name="disable-at-the-control-plane"></a>Desativar no avião de controlo
Se não tiver a certeza se irá precisar de extensões no futuro, pode deixar o Agente Linux instalado no VM e, em seguida, desativar a capacidade de processamento de extensão da plataforma. Esta opção está disponível na `Microsoft.Compute` versão api `2018-06-01` ou superior, e não tem uma dependência da versão Linux Agent instalada.

```azurecli
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Pode facilmente reensificar este processamento de extensão a partir da plataforma, com o comando acima, mas defini-lo como "verdadeiro".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Remova o Agente Linux de um VM em execução

Certifique-se de que **removeu** todas as extensões existentes do VM antes, conforme acima.

### <a name="step-1-remove-the-azure-linux-agent"></a>Passo 1: Remover o Agente Azure Linux

Se remover o Agente Linux e não os artefactos de configuração associados, pode reinstalar-se mais tarde. Executar um dos seguintes, como raiz, para remover o Agente Azure Linux:

#### <a name="for-ubuntu-1804"></a>Para Ubuntu >=18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>Para Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>Para SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-2-optional-remove-the-azure-linux-agent-artifacts"></a>Passo 2: (Opcional) Remover os artefactos do Agente Azure Linux
> [!IMPORTANT] 
>
> Pode remover todos os artefactos associados do Agente Linux, mas isso significa que não pode reinstalá-lo mais tarde. Portanto, recomenda-se vivamente que considere desativar primeiro o Agente Linux, removendo o Agente Linux usando apenas o acima. 

Se souber que nunca mais voltará a instalar o Agente Linux, pode executar o seguinte:

#### <a name="for-ubuntu-1804"></a>Para Ubuntu >=18,04
```bash
apt -y purge walinuxagent
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>Para Redhat >= 7.7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>Para SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Preparar uma imagem sem o Agente Linux
Se tiver uma imagem que já contenha init em nuvem, e quiser remover o agente Linux, mas ainda assim providenciar usando cloud-init, executar os passos no Passo 2 (e opcionalmente Passo 3) como raiz para remover o Agente Azure Linux e, em seguida, o seguinte removerá a configuração de cloud-init e dados em cache, e preparará o VM para criar uma imagem personalizada.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Deprovisione e crie uma imagem
O Agente Linux tem a capacidade de limpar alguns dos metadados de imagem existentes, com o passo "waagent -deprovision+user", no entanto, depois de removido, terá de realizar ações como abaixo, e remover quaisquer outros dados sensíveis do mesmo.

- Remova todas as teclas de anfitrião ssh existentes

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Apagar a conta de administração

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Eliminar a palavra-passe raiz

   ```bash
   passwd -d root
   ```

Uma vez concluída a acima, pode criar a imagem personalizada utilizando o Azure CLI.


**Criar uma imagem gerida regular**
```azurecli
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Criar uma versão de imagem numa Galeria de Imagens Partilhadas**

```azurecli
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Criar um VM a partir de uma imagem que não contém um Agente Linux
Quando criar o VM a partir da imagem sem Agente Linux, tem de se certificar de que a configuração de implementação VM indica que as extensões não são suportadas neste VM.

> [!NOTE] 
> 
> Se não fizer o que precede, a plataforma tentará enviar a configuração de extensão e o tempo limite após 40min.

Para implantar o VM com extensões desativadas, pode utilizar o CLI Azure com [--activar-agente](/cli/azure/vm#az-vm-create).

```azurecli
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Em alternativa, pode fazê-lo utilizando modelos Azure Resource Manager (ARM), definindo `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [Provisioning Linux](provisioning.md).
