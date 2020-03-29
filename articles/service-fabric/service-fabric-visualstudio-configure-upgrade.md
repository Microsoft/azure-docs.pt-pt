---
title: Configure a atualização de uma aplicação De Tecido de Serviço
description: Saiba como configurar as definições para atualizar uma aplicação Service Fabric utilizando o Microsoft Visual Studio.
author: mikkelhegn
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mikhegn
ms.openlocfilehash: ef88a528dbb6d326e5cc742d14c27218eb7502f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464076"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configure a atualização de uma aplicação de Tecido de Serviço no Estúdio Visual
As ferramentas do Estúdio Visual para o Tecido de Serviço Azure fornecem suporte de upgrade para publicação em clusters locais ou remotos. Existem três cenários em que pretende atualizar a sua aplicação para uma versão mais recente em vez de substituir a aplicação durante os testes e depuração:

* Os dados da aplicação não serão perdidos durante a atualização.
* A disponibilidade continua elevada para que não haja qualquer interrupção de serviço durante a atualização, se houver instâncias de serviço suficientes espalhadas por domínios de upgrade.
* Os testes podem ser feitos contra uma aplicação enquanto está a ser atualizado.

## <a name="parameters-needed-to-upgrade"></a>Parâmetros necessários para atualizar
Pode escolher entre dois tipos de implementação: regular ou upgrade. Uma implementação regular apaga quaisquer informações e dados de implementação anteriores no cluster, enquanto uma implementação de upgrade o preserva. Ao atualizar uma aplicação service Fabric no Estúdio Visual, precisa de fornecer parâmetros de atualização de aplicações e políticas de verificação de saúde. Os parâmetros de atualização da aplicação ajudam a controlar a atualização, enquanto as políticas de verificação de saúde determinam se a atualização foi bem sucedida. Ver [upgrade de aplicação Service Fabric: atualizar os parâmetros](service-fabric-application-upgrade-parameters.md) para mais detalhes.

Existem três modos de atualização: *Monitorizado,* *UnmonitoredAuto*e *UnmonitoredManual*.

* Uma atualização monitorizada automatiza o upgrade e verificação de saúde da aplicação.
* Uma atualização Não monitorizada Auto automatiza a atualização, mas ignora o exame de saúde da aplicação.
* Quando faz uma atualização Manual Não monitorizada, é necessário atualizar manualmente cada domínio de atualização.

Cada modo de atualização requer diferentes conjuntos de parâmetros. Consulte os parâmetros de [upgrade da Aplicação](service-fabric-application-upgrade-parameters.md) para saber mais sobre as opções de upgrade disponíveis.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Atualizar uma aplicação de Tecido de Serviço no Estúdio Visual
Se estiver a utilizar as ferramentas Visual Studio Service Fabric para atualizar uma aplicação Service Fabric, pode especificar um processo de publicação para ser uma atualização em vez de uma implementação regular, verificando a caixa de **verificação de aplicações.**

### <a name="to-configure-the-upgrade-parameters"></a>Para configurar os parâmetros de atualização
1. Clique no botão **Definições** ao lado da caixa de verificação. Aparece a caixa de diálogo de parâmetros de atualização de **edição.** A caixa de dialog de parâmetros de atualização de **edição** suporta os modos de atualização Manual monitorizados, Não Monitorizados e Não Monitorizados.
2. Selecione o modo de atualização que pretende utilizar e, em seguida, preencha a grelha do parâmetro.

    Cada parâmetro tem valores padrão. O parâmetro opcional *DefaultServiceTypeHealthPolicy* tem uma entrada de mesa de hash. Aqui está um exemplo do formato de entrada da tabela hash para *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* é outro parâmetro opcional que toma uma entrada de mesa de hash no seguinte formato:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Aqui está um exemplo da vida real:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Se selecionar o modo de atualização UnmonitorManual, tem de iniciar manualmente uma consola PowerShell para continuar e terminar o processo de atualização. Consulte a atualização da [aplicação Service Fabric: tópicos avançados](service-fabric-application-upgrade-advanced.md) para saber como funciona a atualização manual.

## <a name="upgrade-an-application-by-using-powershell"></a>Atualize uma aplicação utilizando o PowerShell
Pode utilizar cmdlets PowerShell para atualizar uma aplicação Service Fabric. Consulte o tutorial de atualização de [aplicações do Service Fabric](service-fabric-application-upgrade-tutorial.md) e o [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) para obter informações detalhadas.

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Especificar uma política de verificação de saúde no ficheiro manifesto de aplicação
Cada serviço numa aplicação de Tecido de Serviço pode ter os seus próprios parâmetros de política de saúde que sobrepõem os valores predefinidos. Pode fornecer estes valores de parâmetro no ficheiro manifesto de aplicação.

O exemplo que se segue mostra como aplicar uma política única de verificação de saúde para cada serviço no manifesto de candidatura.

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
Para obter mais informações sobre a atualização de uma aplicação, consulte [Atualização de uma aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md).
