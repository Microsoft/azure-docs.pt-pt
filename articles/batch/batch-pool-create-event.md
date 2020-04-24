---
title: Piscina de Lote Azure cria evento
description: Referência para o lote de piscina criar evento, que é emitido uma vez que uma piscina é criada. O conteúdo do registo exporá informações gerais sobre a piscina.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 755fbe548b4be729d20788597db05f4d9678ebea
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116558"
---
# <a name="pool-create-event"></a>Evento de criação de conjunto

 Este evento é emitido uma vez que uma piscina foi criada. O conteúdo do registo exporá informações gerais sobre a piscina. Note que se o tamanho alvo da piscina for maior do que 0 nós de computação, um evento de redimensionação da piscina seguir-se-á imediatamente após este evento.

 O exemplo seguinte mostra o corpo de uma piscina `CloudServiceConfiguration` criar evento para uma piscina criada usando a propriedade.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
|`displayName`|String|O nome da exposição da piscina.|
|`vmSize`|String|Do tamanho das máquinas virtuais na piscina. Todas as máquinas virtuais numa piscina têm o mesmo tamanho. <br/><br/> Para obter informações sobre os tamanhos disponíveis de máquinas virtuais para piscinas de Serviços cloud (piscinas criadas com cloudServiceConfiguration), consulte [Sizes for Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). O lote suporta todos os tamanhos vm dos serviços de nuvem `ExtraSmall`exceto .<br/><br/> Para obter informações sobre os tamanhos vm disponíveis para piscinas usando imagens do Mercado de Máquinas Virtuais (piscinas criadas com virtualMachineConfiguration) ver [Tamanhos para Máquinas Virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) ou [Tamanhos para Máquinas Virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). O Batch suporta todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e os do armazenamento premium (séries `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|`imageType`|String|O método de implantação para a imagem. Valores suportados são `virtualMachineConfiguration` ou`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Tipo complexo|A configuração do serviço de nuvem para a piscina.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Tipo complexo|A configuração da máquina virtual para a piscina.|
|[`networkConfiguration`](#bk_netconf)|Tipo complexo|A configuração da rede para a piscina.|
|`resizeTimeout`|Hora|O prazo para a atribuição de nódosos de cálculo à piscina especificado para a última operação de redimensionação na piscina.  (O tamanho inicial quando a piscina é criada conta como um redimensionamento.)|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados que são solicitados para a piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`enableInterNodeCommunication`|Booleano|Especifica se a piscina está configurada para comunicação direta entre nós.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autopool de um trabalho.|
|`maxTasksPerNode`|Int32|O número máximo de tarefas que podem ser executadas simultaneamente num único nó computacional na piscina.|
|`vmFillType`|String|Define como o serviço Batch distribui tarefas entre nós de computação na piscina. Os valores válidos são Spread ou Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfigura

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`osFamily`|String|A família Azure Guest OS será instalada nas máquinas virtuais da piscina.<br /><br /> Os valores possíveis são:<br /><br /> **2** – OS Família 2, equivalente ao Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Família 3, equivalente ao Windows Server 2012.<br /><br /> **4** – OS Família 4, equivalente ao Windows Server 2012 R2.<br /><br /> Para mais informações, consulte [lançamentos do Os](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)Do TS do Azure Guest .|
|`targetOSVersion`|String|A versão O SO do Azure Guest a instalar nas máquinas virtuais da piscina.<br /><br /> O valor **\*** predefinido é o que especifica a versão mais recente do sistema operativo para a família especificada.<br /><br /> Para outros valores permitidos, consulte [lançamentos de Os](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)OS do Hóspede Azure .|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>configuração virtualMáquina

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Tipo complexo|Especifica informações sobre a plataforma ou imagem do Marketplace a utilizar.|
|`nodeAgentId`|String|O sKU do agente do nó batch provisionado no nó computacional.|
|[`windowsConfiguration`](#bk_winconf)|Tipo complexo|Especifica as definições do sistema operativo Windows na máquina virtual. Esta propriedade não deve ser especificada se a imagemReference está a referir uma imagem Do Linux OS.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imagemReferência

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`publisher`|String|O editor da imagem.|
|`offer`|String|A oferta da imagem.|
|`sku`|String|O SKU da imagem.|
|`version`|String|A versão da imagem.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>janelasConfiguração

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Booleano|Indica se a máquina virtual está ativada para atualizações automáticas. Se esta propriedade não for especificada, o valor predefinido é verdadeiro.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>configuração de rede

|Nome do elemento|Tipo|Notas|
|------------------|--------------|----------|
|`subnetId`|String|Especifica o identificador de recursos da subnet na qual são criados os nódosos de cálculo da piscina.|
