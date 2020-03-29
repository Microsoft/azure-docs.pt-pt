---
title: Dados personalizados e Máquinas Virtuais Azure
description: Detalhes sobre a utilização de dados personalizados e Cloud-Init em Máquinas Virtuais Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109631"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Dados personalizados e Cloud-Init em Máquinas Virtuais Azure

## <a name="what-is-custom-data"></a>O que são dados personalizados?

Os clientes perguntam frequentemente como podem injetar um script ou outros metadados numa máquina virtual do Microsoft Azure no momento da provisionio.  Noutras nuvens, este conceito é muitas vezes referido como dados do utilizador.  No Microsoft Azure, temos uma funcionalidade semelhante chamada dados personalizados. 

Os dados personalizados só são disponibilizados ao VM durante a primeira configuração/configuração inicial, chamamos a isto "provisionamento". O provisionamento é o processo em que os parâmetros VM Create (por exemplo, nome de anfitrião, nome de utilizador, palavra-passe, certificados, dados personalizados, chaves, etc.) são disponibilizados ao VM e um agente de provisionamento os processa, como o [Agente Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) e o [cloud-init.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>Passar dados personalizados para o VM
Para utilizar dados personalizados, deve codificar primeiro o conteúdo antes de o passar para a API, a não ser que esteja a utilizar uma ferramenta CLI que faça a conversão para si, como o AZ CLI. O tamanho não pode exceder 64 KB.

No CLI, pode passar os seus dados personalizados como um ficheiro, e será convertido para base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

No Azure Resource Manager (ARM), existe uma [função base64](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

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
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Processamento de dados personalizados
Os agentes de provisionamento instalados nos VMs lidam com a plataforma e colocam-no no sistema de ficheiros. 

### <a name="windows"></a>Windows
Os dados personalizados são colocados em *%SYSTEMDRIVE%\AzureData\CustomData.bin* como um ficheiro binário, mas não é processado. Se desejar processar este ficheiro, terá de construir uma imagem personalizada e escrever código para processar o CustomData.bin.

### <a name="linux"></a>Linux  
No Linux OS's, os dados personalizados são transmitidos para o VM através do ficheiro ovf-env.xml, que é copiado para o diretório */var/lib/waagent* durante o provisionamento.  As versões mais recentes do Agente Microsoft Azure Linux também copiarão os dados codificados base64 para */var/lib/waagent/CustomData,* bem como para conveniência.

A Azure apoia atualmente dois agentes de provisionamento:
* Agente Linux - Por padrão, o agente não irá processar dados personalizados, terá de construir uma imagem personalizada com ela ativada. As configurações relevantes, de acordo com a [documentação](https://github.com/Azure/WALinuxAgent#configuration) são:
    * Provisionamento.DecodeCustomData
    * Provisionamento.ExecuteCustomData

Quando activaos dados personalizados e executares um script, irá atrasar o relatório vm que está pronto ou que o fornecimento tenha sido bem sucedido até que o script esteja concluído. Se o guião exceder o subsídio total de tempo de fornecimento de VM de 40 minutos, o VM Create falhará. Note que, se o script não executar, ou erros durante a execução, não for considerado uma falha fatal de provisionamento, terá de criar um caminho de notificação para alertá-lo para o estado de conclusão do script.

Para resolução de problemas na execução de dados personalizados, reveja */var/log/waagent.log*

* cloud-init - Por padrão, processará dados personalizados por padrão, o cloud-init aceita [vários formatos](https://cloudinit.readthedocs.io/en/latest/topics/format.html) de dados personalizados, tais como configuração de cloud-init, scripts etc. Semelhante ao Agente Linux, quando o cloud-init processa os dados personalizados. Se houver erros durante a execução do processamento de configuração ou scripts, não é considerado uma falha fatal de provisionamento, e terá de criar um caminho de notificação para alertá-lo para o estado de conclusão do script. No entanto, diferente do Agente Linux, o cloud-init não aguarda as configurações de dados personalizadas do utilizador para completar antes de informar para a plataforma que o VM está pronto. Para obter mais informações sobre cloud-init no azure, reveja a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Para resolver a execução de dados personalizados, reveja a [documentação](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)de resolução de problemas.


## <a name="faq"></a>FAQ
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Posso atualizar dados personalizados depois da criação do VM?
Para VMs individuais, os dados personalizados no modelo VM não podem ser atualizados, mas para VMSS, pode atualizar dados personalizados VMSS via REST API (não aplicável aos clientes PS ou AZ CLI). Quando atualiza os dados personalizados no modelo VMSS:
* As instâncias existentes no VMSS não obterão os dados personalizados atualizados, apenas até serem reimagemdos.
* As instâncias existentes no VMSS que são atualizadas não obterão os dados personalizados atualizados.
* Novos casos receberão os novos dados personalizados.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Posso colocar valores sensíveis em dados personalizados?
Aconselhamos a **não** armazenar dados sensíveis em dados personalizados. Para mais informações, consulte [as melhores práticas de Segurança Azure e encriptação.](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)


### <a name="is-custom-data-made-available-in-imds"></a>Os dados personalizados são disponibilizados no IMDS?
Não, esta funcionalidade não está disponível atualmente.
