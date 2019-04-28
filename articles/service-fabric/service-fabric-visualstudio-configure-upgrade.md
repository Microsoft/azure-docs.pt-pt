---
title: Configurar a atualização de uma aplicação do Service Fabric | Documentos da Microsoft
description: Saiba como configurar as definições para atualizar uma aplicação do Service Fabric com o Microsoft Visual Studio.
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
ms.author: mikkelhegn
ms.openlocfilehash: 79120371ca2a62e5ef9f2bf38476635db12e9fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082849"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurar a atualização de uma aplicação do Service Fabric no Visual Studio
O Visual Studio tools para o Azure Service Fabric fornecem suporte de atualização para a publicação de clusters locais ou remotos. Existem três cenários em que pretende atualizar a sua aplicação para uma versão mais recente em vez de substituir o aplicativo durante o teste e depuração:

* Os dados da aplicação não será perdidos durante a atualização.
* Disponibilidade permanece elevada para que não haverá qualquer interrupção do serviço durante a atualização, se existirem instâncias suficientes serviço distribuído por todos os domínios de atualização.
* Testes podem ser executados num aplicativo enquanto ele está a ser atualizado.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Pode escolher entre dois tipos de implementação: regular ou atualização. Uma implantação regular apaga quaisquer informações de implementação anterior e os dados no cluster, enquanto preserva a uma implantação de atualização-lo. Ao atualizar uma aplicação do Service Fabric no Visual Studio, terá de fornecer os parâmetros de atualização de aplicativo e o estado de funcionamento, consulte as políticas. Parâmetros de atualização de aplicação ajudam a controlar a atualização, enquanto as políticas de verificação de estado de funcionamento determinam se a atualização foi concluída com êxito. Ver [atualização da aplicação de Service Fabric: parâmetros de atualização](service-fabric-application-upgrade-parameters.md) para obter mais detalhes.

Existem três modos de atualização: *Monitorizado*, *UnmonitoredAuto*, e *UnmonitoredManual*.

* Uma atualização de monitorizados automatiza a atualização e verificação de estado de funcionamento da aplicação.
* Uma atualização de UnmonitoredAuto automatiza a atualização, mas ignora a verificação de estado de funcionamento da aplicação.
* Ao fazer uma atualização de UnmonitoredManual, terá de atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Ver [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de atualização disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar uma aplicação do Service Fabric no Visual Studio
Se estiver a utilizar as ferramentas do Visual Studio Service Fabric para atualizar uma aplicação do Service Fabric, pode especificar um processo de publicação para ser uma atualização em vez de uma implantação regular, verificando a **atualizar a aplicação** caixa de verificação.

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique nas **definições** botão junto à caixa de verificação. O **Editar parâmetros atualizar** é apresentada a caixa de diálogo. O **Editar parâmetros atualizar** os modos de atualização monitorizados, UnmonitoredAuto e UnmonitoredManual oferece suporte a caixa de diálogo.
2. Selecione o modo de atualização que pretende utilizar e, em seguida, preencha a grade de parâmetro.

    Cada parâmetro tem valores predefinidos. O parâmetro opcional *DefaultServiceTypeHealthPolicy* precisar de uma entrada de tabela de hash. Eis um exemplo de formato de entrada da tabela hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que usa uma entrada de tabela de hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Eis um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se selecionar o modo de atualização UnmonitoredManual, tem de iniciar manualmente a consola do PowerShell para continuar e concluir o processo de atualização. Consulte a [atualização da aplicação de Service Fabric: Tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber a atualização manual como funciona.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualizar uma aplicação com o PowerShell
Pode utilizar cmdlets do PowerShell para atualizar uma aplicação do Service Fabric. Ver [tutorial de atualização de aplicação do Service Fabric](service-fabric-application-upgrade-tutorial.md) e [início ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especifique uma política de verificação de estado de funcionamento no arquivo de manifesto do aplicativo
Todos os serviços num aplicativo de Service Fabric podem ter seus próprio parâmetros de política de estado de funcionamento que substituem os valores predefinidos. Pode fornecer estes valores de parâmetro no arquivo de manifesto do aplicativo.

O exemplo seguinte mostra como aplicar uma política de verificação de estado de funcionamento exclusivo para cada serviço no manifesto do aplicativo.

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
Para obter mais informações sobre como atualizar uma aplicação, consulte [atualizar uma aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md).
