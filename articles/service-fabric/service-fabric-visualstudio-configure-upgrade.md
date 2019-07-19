---
title: Configurar a atualização de um aplicativo Service Fabric | Microsoft Docs
description: Saiba como definir as configurações para atualizar um aplicativo Service Fabric usando Microsoft Visual Studio.
services: service-fabric
documentationcenter: na
author: mikkelhegn
manager: mfussell
editor: tglee
ms.assetid: 1757ba85-0b7b-4f16-8a23-2ddaa61c86c6
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: 5979541146b7cd7b854f35c5bf204e71208f066b
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876764"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de um aplicativo Service Fabric no Visual Studio
As ferramentas do Visual Studio para Azure Service Fabric fornecem suporte de atualização para a publicação em clusters locais ou remotos. Há três cenários nos quais você deseja atualizar seu aplicativo para uma versão mais recente em vez de substituir o aplicativo durante o teste e a depuração:

* Os dados do aplicativo não serão perdidos durante a atualização.
* A disponibilidade permanece alta para que não haja interrupção do serviço durante a atualização, se houver instâncias de serviço suficientes espalhadas entre domínios de atualização.
* Os testes podem ser executados em um aplicativo enquanto estão sendo atualizados.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Você pode escolher entre dois tipos de implantação: regular ou atualizar. Uma implantação regular apaga todas as informações e dados de implantação anteriores no cluster, enquanto uma implantação de atualização a preserva. Ao atualizar um aplicativo Service Fabric no Visual Studio, você precisa fornecer os parâmetros de atualização do aplicativo e as políticas de verificação de integridade. Os parâmetros de atualização de aplicativo ajudam a controlar a atualização, enquanto as políticas de verificação de integridade determinam se a atualização foi bem-sucedida. Consulte [Service Fabric atualização de aplicativo: parâmetros de atualização](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Há três modos de atualização: *Monitorado*, *UnmonitoredAuto*e *UnmonitoredManual*.

* Uma atualização monitorada automatiza a atualização e a verificação de integridade do aplicativo.
* Uma atualização do UnmonitoredAuto automatiza a atualização, mas ignora a verificação de integridade do aplicativo.
* Ao fazer uma atualização do UnmonitoredManual, você precisa atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer conjuntos diferentes de parâmetros. Consulte [parâmetros de atualização de aplicativos](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar um aplicativo Service Fabric no Visual Studio
Se você estiver usando as ferramentas de Service Fabric do Visual Studio para atualizar um aplicativo Service Fabric, poderá especificar um processo de publicação para ser uma atualização em vez de uma implantação regular marcando a caixa de seleção **atualizar o aplicativo** .

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique no botão **configurações** ao lado da caixa de seleção. A caixa de diálogo **Editar parâmetros de atualização** é exibida. A caixa de diálogo **Editar parâmetros de atualização** dá suporte aos modos de atualização Monitored, UnmonitoredAuto e UnmonitoredManual.
2. Selecione o modo de atualização que você deseja usar e, em seguida, preencha a grade de parâmetros.

    Cada parâmetro tem valores padrão. O parâmetro opcional *DefaultServiceTypeHealthPolicy* usa uma entrada de tabela de hash. Aqui está um exemplo do formato de entrada da tabela de hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que usa uma entrada de tabela de hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Veja um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se você selecionar o modo de atualização UnmonitoredManual, deverá iniciar manualmente um console do PowerShell para continuar e concluir o processo de atualização. Consulte [Service Fabric atualização do aplicativo: Tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber como a atualização manual funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar um aplicativo usando o PowerShell
Você pode usar os cmdlets do PowerShell para atualizar um aplicativo Service Fabric. Consulte [Service Fabric tutorial de atualização de aplicativo](service-fabric-application-upgrade-tutorial.md) e [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar uma política de verificação de integridade no arquivo de manifesto do aplicativo
Cada serviço em um aplicativo Service Fabric pode ter seus próprios parâmetros de diretiva de integridade que substituem os valores padrão. Você pode fornecer esses valores de parâmetro no arquivo de manifesto do aplicativo.

O exemplo a seguir mostra como aplicar uma política de verificação de integridade exclusiva para cada serviço no manifesto do aplicativo.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como atualizar um aplicativo, consulte [atualizar um aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md).
