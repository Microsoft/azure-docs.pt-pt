---
title: Piscina de Lote Azure cria evento
description: Referência para o lote de piscina criar evento, que é emitido uma vez que uma piscina é criada. O conteúdo do log vai expor informações gerais sobre o pool.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022194"
---
# <a name="pool-create-event"></a>Evento de criação de conjunto

 Esse evento é emitido após a criação de um pool. O conteúdo do log vai expor informações gerais sobre o pool. Observe que, se o tamanho de destino do pool for maior que 0 nós de computação, um evento de início de redimensionamento do pool será seguido imediatamente após esse evento.

 O exemplo a seguir mostra o corpo de um evento de criação de pool para um pool criado usando a propriedade `CloudServiceConfiguration`.

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
|`id`|Cadeia|A ID do pool.|
|`displayName`|Cadeia|O nome de exibição do pool.|
|`vmSize`|Cadeia|O tamanho das máquinas virtuais no pool. Todas as máquinas virtuais em um pool têm o mesmo tamanho. <br/><br/> Para obter informações sobre os tamanhos disponíveis de máquinas virtuais para pools de serviços de nuvem (pools criados com cloudServiceConfiguration), consulte [tamanhos dos serviços de nuvem](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/). O lote dá suporte a todos os tamanhos de VM de serviços de nuvem, exceto `ExtraSmall`.<br/><br/> Para obter informações sobre tamanhos de VM disponíveis para pools usando imagens do Marketplace de máquinas virtuais (pools criados com virtualMachineConfiguration), consulte [tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) ou [tamanhos de máquinas virtuais](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows). O Batch suporta todos os tamanhos de VM do Azure, exceto `STANDARD_A0` e os do armazenamento premium (séries `STANDARD_GS`, `STANDARD_DS` e `STANDARD_DSV2`).|
|`imageType`|Cadeia|O método de implantação para a imagem. Os valores com suporte são `virtualMachineConfiguration` ou `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Tipo complexo|A configuração do serviço de nuvem para o pool.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Tipo complexo|A configuração da máquina virtual para o pool.|
|[`networkConfiguration`](#bk_netconf)|Tipo complexo|A configuração de rede para o pool.|
|`resizeTimeout`|Tempo|O tempo limite para a alocação de nós de computação para o pool especificado para a última operação de redimensionamento no pool.  (O dimensionamento inicial quando o pool é criado conta como um redimensionamento.)|
|`targetDedicatedNodes`|Int32|O número de nós de computação dedicados que são solicitados para o pool.|
|`targetLowPriorityNodes`|Int32|O número de nós de computação de baixa prioridade que são solicitados para o pool.|
|`enableAutoScale`|Bool|Especifica se o tamanho do pool se ajusta automaticamente ao longo do tempo.|
|`enableInterNodeCommunication`|Bool|Especifica se o pool está configurado para comunicação direta entre nós.|
|`isAutoPool`|Bool|Especifica se o pool foi criado por meio do mecanismo de autopool de um trabalho.|
|`maxTasksPerNode`|Int32|O número máximo de tarefas que podem ser executadas simultaneamente em um único nó de computação no pool.|
|`vmFillType`|Cadeia|Define como o serviço de lote distribui tarefas entre nós de computação no pool. Os valores válidos são spread ou Pack.|

###  <a name="bk_csconf"></a>cloudServiceConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`osFamily`|Cadeia|A família do sistema operacional convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> Os valores possíveis são:<br /><br /> **2** – família de sistemas operacionais 2, equivalente ao Windows Server 2008 R2 SP1.<br /><br /> **3** – so família 3, equivalente ao Windows Server 2012.<br /><br /> **4** – so família 4, equivalente ao Windows Server 2012 R2.<br /><br /> Para obter mais informações, consulte [versões do sistema operacional convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|
|`targetOSVersion`|Cadeia|A versão do sistema operacional convidado do Azure a ser instalada nas máquinas virtuais no pool.<br /><br /> O valor padrão é **\*** que especifica a versão mais recente do sistema operacional para a família especificada.<br /><br /> Para outros valores permitidos, consulte [versões do sistema operacional convidado do Azure](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases).|

###  <a name="bk_vmconf"></a>virtualMachineConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Tipo complexo|Especifica informações sobre a plataforma ou a imagem do Marketplace a ser usada.|
|`nodeAgentId`|Cadeia|A SKU do agente de nó do lote provisionada no nó de computação.|
|[`windowsConfiguration`](#bk_winconf)|Tipo complexo|Especifica as configurações do sistema operacional Windows na máquina virtual. Essa propriedade não deve ser especificada se o imageReference estiver fazendo referência a uma imagem do SO Linux.|

###  <a name="bk_imgref"></a> imageReference

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`publisher`|Cadeia|O editor da imagem.|
|`offer`|Cadeia|A oferta da imagem.|
|`sku`|Cadeia|A SKU da imagem.|
|`version`|Cadeia|A versão da imagem.|

###  <a name="bk_winconf"></a>windowsConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Booleano|Indica se a máquina virtual está habilitada para atualizações automáticas. Se essa propriedade não for especificada, o valor padrão será true.|

###  <a name="bk_netconf"></a>networkConfiguration

|Nome do elemento|Tipo|Notas|
|------------------|--------------|----------|
|`subnetId`|Cadeia|Especifica o identificador de recurso da sub-rede na qual os nós de computação do pool são criados.|
