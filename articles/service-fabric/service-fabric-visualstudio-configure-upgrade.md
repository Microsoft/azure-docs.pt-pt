---
title: Configure a atualização de uma aplicação de Tecido de Serviço
description: Aprenda a configurar as definições para atualizar uma aplicação de Service Fabric utilizando o Microsoft Visual Studio.
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 11df0387db07669ebacdba2e3d850e32903da133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573685"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configure a atualização de uma aplicação de Tecido de Serviço no Estúdio Visual
As ferramentas visual Studio para Azure Service Fabric fornecem suporte de upgrade para publicação em clusters locais ou remotos. Existem três cenários em que pretende atualizar a sua aplicação para uma versão mais recente em vez de substituir a aplicação durante os testes e depurações:

* Os dados da aplicação não serão perdidos durante a atualização.
* A disponibilidade permanece elevada para que não haja qualquer interrupção de serviço durante a atualização, se houver casos de serviço suficientes espalhados por domínios de upgrade.
* Os testes podem ser executados contra uma aplicação enquanto está a ser atualizado.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Pode escolher entre dois tipos de implementação: regular ou upgrade. Uma implantação regular apaga quaisquer informações e dados de implantação anteriores no cluster, enquanto uma implementação de upgrade o preserva. Ao atualizar uma aplicação de Tecido de Serviço no Estúdio Visual, precisa de fornecer parâmetros de atualização de aplicações e políticas de verificação de saúde. Os parâmetros de atualização da aplicação ajudam a controlar a atualização, enquanto as políticas de verificação de saúde determinam se a atualização foi bem sucedida. Consulte [a atualização da aplicação Do Tecido de Serviço: atualizar os parâmetros](service-fabric-application-upgrade-parameters.md) para mais detalhes.

Existem três modos de atualização: *Monitorado,* *UnmonitoredAuto* e *UnmonitoredManual*.

* Uma atualização monitorizada automatiza o exame de saúde de upgrade e aplicação.
* Uma atualização UnmonitoredAuto automatiza a atualização, mas ignora o exame de saúde da aplicação.
* Quando fizer uma atualização UnmonitoredManual, tem de atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Consulte [os parâmetros de atualização](service-fabric-application-upgrade-parameters.md) da Aplicação para saber mais sobre as opções de upgrade disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar uma aplicação de Tecido de Serviço em Estúdio Visual
Se estiver a utilizar as ferramentas Visual Studio Service Fabric para atualizar uma aplicação de Tecido de Serviço, pode especificar um processo de publicação para ser uma atualização em vez de uma implementação regular, verificando a caixa de verificação **de atualização da aplicação.**

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique no botão **Definições** ao lado da caixa de verificação. Aparece a caixa de diálogo **de parâmetros de atualização de edição.** A caixa de diálogo **de parâmetros de atualização** de edição suporta os modos de atualização Monitorado, UnmonitoredAuto e UnmonitoredManual.
2. Selecione o modo de atualização que pretende utilizar e, em seguida, preencha a grelha de parâmetros.

    Cada parâmetro tem valores predefinidos. O parâmetro opcional *DefaultServiceTypeHealthPolicy* requer uma entrada de tabela de haxixe. Aqui está um exemplo do formato de entrada de tabela de haxixe para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que leva uma entrada de tabela de haxixe no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Aqui está um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se selecionar o modo de atualização UnmonitoredManual, tem de iniciar manualmente uma consola PowerShell para continuar e terminar o processo de atualização. Consulte a [atualização da aplicação do Service Fabric: tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber como funciona a atualização manual.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualize uma aplicação utilizando o PowerShell
Pode utilizar cmdlets PowerShell para atualizar uma aplicação de Tecido de Serviço. Consulte [o tutorial de atualização de aplicações](service-fabric-application-upgrade-tutorial.md) de Tecido de Serviço e [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar uma política de verificação de saúde no ficheiro manifesto de aplicação
Cada serviço numa aplicação de Tecido de Serviço pode ter os seus próprios parâmetros de política de saúde que sobrepõem os valores padrão. Pode fornecer estes valores de parâmetro no ficheiro manifesto de aplicação.

O exemplo a seguir mostra como aplicar uma política única de verificação de saúde para cada serviço no manifesto de aplicação.

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
## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a atualização de uma aplicação, consulte [atualizar uma aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md).
