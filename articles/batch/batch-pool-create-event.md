---
title: Piscina Azure Batch criar evento
description: Referência para o lote de lote criar evento, que é emitido uma vez que uma piscina foi criada. O conteúdo do registo exporá informações gerais sobre a piscina.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: dea0e04fa506274e645ba7a578039a0d6f021043
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850953"
---
# <a name="pool-create-event"></a>Evento de criação de conjunto

 Este evento é emitido uma vez que uma piscina foi criada. O conteúdo do registo exporá informações gerais sobre a piscina. Note que se o tamanho alvo da piscina for superior a 0 nós compute, um evento de início de redimensione piscina seguir-se-á imediatamente após este evento.

 O exemplo a seguir mostra o corpo de uma piscina criar evento para uma piscina criada usando a `CloudServiceConfiguration` propriedade.

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Elemento|Tipo|Notas|
|-------------|----------|-----------|
|`id`|String|A identificação da piscina.|
|`displayName`|String|O nome de exibição da piscina.|
|`vmSize`|String|O tamanho das máquinas virtuais na piscina. Todas as máquinas virtuais numa piscina têm o mesmo tamanho. <br/><br/> Para obter informações sobre os tamanhos disponíveis de máquinas virtuais para piscinas de Serviços cloud (piscinas criadas com cloudServiceConfiguration), consulte [Tamanhos para Serviços cloud.](../cloud-services/cloud-services-sizes-specs.md) O lote suporta todos os tamanhos VM dos Serviços cloud, `ExtraSmall` exceto.<br/><br/> Para obter informações sobre tamanhos VM disponíveis para piscinas utilizando imagens do Mercado de Máquinas Virtuais (piscinas criadas com configuração virtual) consulte [tamanhos para máquinas virtuais](../virtual-machines/linux/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json) (Linux) ou [Tamanhos para Máquinas Virtuais](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) (Windows). O Batch suporta todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e os do armazenamento premium (séries `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|`imageType`|String|O método de implantação para a imagem. Valores suportados são `virtualMachineConfiguration` ou `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Tipo Complexo|A configuração de serviço em nuvem para a piscina.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Tipo Complexo|A configuração da máquina virtual para a piscina.|
|[`networkConfiguration`](#bk_netconf)|Tipo Complexo|A configuração da rede para a piscina.|
|`resizeTimeout`|Hora|O tempo limite para a atribuição de nós de computação à piscina especificado para a última operação de redimensionação na piscina.  (O tamanho inicial quando a piscina é criada conta como um redimensionamento.)|
|`targetDedicatedNodes`|Int32|O número de nós computacional dedicados que são solicitados para a piscina.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para a piscina.|
|`enableAutoScale`|Booleano|Especifica se o tamanho da piscina se ajusta automaticamente ao longo do tempo.|
|`enableInterNodeCommunication`|Booleano|Especifica se a piscina está configurada para comunicação direta entre nós.|
|`isAutoPool`|Booleano|Especifica se a piscina foi criada através do mecanismo autoPool de um trabalho.|
|`taskSlotsPerNode`|Int32|O número máximo de tarefas que podem ser executadas simultaneamente num único nó de computação na piscina.|
|`vmFillType`|String|Define como o serviço Batch distribui tarefas entre nós de computação na piscina. Os valores válidos são Spread ou Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`osFamily`|String|A família Azure Guest OS será instalada nas máquinas virtuais da piscina.<br /><br /> Os valores possíveis são:<br /><br /> **2** – OS Family 2, equivalente ao Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, equivalente ao Windows Server 2012.<br /><br /> **4** – OS Family 4, equivalente ao Windows Server 2012 R2.<br /><br /> Para mais informações, consulte [as versões do Azure Guest OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|
|`targetOSVersion`|String|A versão Azure Guest OS a instalar nas máquinas virtuais da piscina.<br /><br /> O valor predefinido é **\*** que especifica a versão mais recente do sistema operativo para a família especificada.<br /><br /> Para outros valores permitidos, consulte [lançamentos do Azure Guest OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases).|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Tipo Complexo|Especifica informações sobre a plataforma ou a imagem do Marketplace para usar.|
|`nodeAgentId`|String|O SKU do agente de nó de lote previsto no nó de computação.|
|[`windowsConfiguration`](#bk_winconf)|Tipo Complexo|Especifica as definições do sistema operativo Windows na máquina virtual. Esta propriedade não deve ser especificada se a imagemReference estiver a fazer referência a uma imagem linux OS.|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imagemReferência

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`publisher`|String|O editor da imagem.|
|`offer`|String|A oferta da imagem.|
|`sku`|String|O SKU da imagem.|
|`version`|String|A versão da imagem.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> janelasConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Booleano|Indica se a máquina virtual está ativada para atualizações automáticas. Se esta propriedade não for especificada, o valor padrão é verdadeiro.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> redeConfiguação

|Nome do elemento|Tipo|Notas|
|------------------|--------------|----------|
|`subnetId`|String|Especifica o identificador de recursos da sub-rede em que são criados os nós computacional da piscina.|
