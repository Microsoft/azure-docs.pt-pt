---
title: Dados personalizados e máquinas virtuais Azure
description: Detalhes sobre a utilização de dados personalizados e Cloud-Init em Máquinas Virtuais Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87284919"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dados e Cloud-Init personalizados em Azure Virtual Machines

Poderá ter de injetar um script ou outros metadados numa máquina virtual do Microsoft Azure no tempo de provisionamento.  Noutras nuvens, este conceito é frequentemente referido como dados do utilizador.  No Microsoft Azure, temos uma funcionalidade similar chamada dados personalizados. 

Os dados personalizados só são disponibilizados ao VM durante a primeira configuração inicial, chamamos a isto 'provisionamento'. O provisionamento é o processo em que os parâmetros VM Create (por exemplo, nome de hospedeiro, nome de utilizador, palavra-passe, certificados, dados personalizados, chaves, etc.) são disponibilizados ao VM e um agente de provisionamento processa-os, como o [Agente Linux](./extensions/agent-linux.md) e o [cloud-init.](./linux/using-cloud-init.md#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>Passar dados personalizados para o VM
Para utilizar dados personalizados, tem de codificar o conteúdo primeiro antes de o passar para a API, a não ser que esteja a utilizar uma ferramenta CLI que faça a conversão para si, como o AZ CLI. O tamanho não pode exceder 64 KB.

No CLI, pode passar os seus dados personalizados como um ficheiro, e será convertido para base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

No Azure Resource Manager (ARM), existe uma [função base64](../azure-resource-manager/templates/template-functions-string.md#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Processamento de dados personalizados
Os agentes de provisionamento instalados nos VMs lidam com a interação com a plataforma e colocam-no no sistema de ficheiros. 

### <a name="windows"></a>Windows
Os dados personalizados são colocados em *%SYSTEMDRIVE%\AzureData\CustomData.bin* como um ficheiro binário, mas não são processados. Se desejar processar este ficheiro, terá de construir uma imagem personalizada e escrever código para processar o CustomData.bin.

### <a name="linux"></a>Linux  
No Linux OS's, os dados personalizados são transmitidos para o VM através do ficheiro ovf-env.xml, que é copiado para o diretório */var/lib/waagent* durante o provisionamento.  As versões mais recentes do Agente Linux do Microsoft Azure também copiarão os dados codificados de base64 para */var/lib/waagent/CustomData,* bem como para conveniência.

A Azure apoia atualmente dois agentes de provisionamento:
* Agente Linux - Por padrão, o agente não irá processar dados personalizados, terá de construir uma imagem personalizada com ela ativada. As definições relevantes, de acordo com a [documentação,](https://github.com/Azure/WALinuxAgent#configuration) são:
    * Provisioning.DecodeCustomData
    * Provisioning.ExebonitoCustomData

Quando ativar dados personalizados e executar um script, atrasará a reportagem VM que está pronta ou que o provisionamento foi bem sucedido até que o script esteja concluído. Se o script exceder o prazo total de 40 minutos de provisão de VM, o VM Create falhará. Note que se o script não executar, ou erros durante a execução, não é considerado uma falha fatal de provisão, você precisará criar um caminho de notificação para alertá-lo para o estado de conclusão do script.

Para resolver problemas na execução de dados personalizados, reveja */var/log/waagent.log*

* cloud-init - Por padrão irá processar dados personalizados por padrão, o cloud-init aceita [vários formatos de dados personalizados,](https://cloudinit.readthedocs.io/en/latest/topics/format.html) tais como configuração de cloud-init, scripts etc. Semelhante ao Agente Linux, quando o cloud-init processa os dados personalizados. Se houver erros durante a execução do processamento de configuração ou scripts, não é considerado uma falha fatal de provisão, e você precisará criar um caminho de notificação para alertá-lo para o estado de conclusão do script. No entanto, diferente do Agente Linux, o cloud-in não espera que as configurações de dados personalizadas do utilizador completem antes de reportar à plataforma que o VM está pronto. Para obter mais informações sobre cloud-init on azure, reveja a [documentação](./linux/using-cloud-init.md).


Para resolver problemas na execução de dados personalizados, reveja a [documentação](./linux/using-cloud-init.md#troubleshooting-cloud-init)de resolução de problemas .


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Posso atualizar os dados personalizados após a criação do VM?
Para os VMs individuais, os dados personalizados no modelo VM não podem ser atualizados, mas para vMSS, pode atualizar os dados personalizados VMSS através [da REST API](/rest/api/compute/virtualmachinescalesets/update) (não aplicável aos clientes PS ou AZ CLI). Ao atualizar dados personalizados no modelo VMSS:
* As instâncias existentes no VMSS não receberão os dados personalizados atualizados, apenas até que sejam reesimagem.
* As instâncias existentes no VMSS que são atualizadas não receberão os dados personalizados atualizados.
* Novas instâncias receberão os novos dados personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Posso colocar valores sensíveis em dados personalizados?
Aconselhamos a **não** armazenar dados sensíveis em dados personalizados. Para mais informações, consulte [a Azure Security e as melhores práticas de encriptação.](../security/fundamentals/data-encryption-best-practices.md)


### <a name="is-custom-data-made-available-in-imds"></a>Os dados personalizados são disponibilizados no IMDS?
Não, esta funcionalidade não está disponível atualmente.
