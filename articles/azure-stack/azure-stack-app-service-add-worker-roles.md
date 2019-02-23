---
title: Aumentar horizontalmente as funções de trabalho nos serviços de aplicação - Azure Stack | Documentos da Microsoft
description: Documentação de orientação detalhada para o dimensionamento dos serviços de aplicações do Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2010
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 839fa7fe8374f1f85b019178d4c3fe53f7137372
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729649"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Serviço de aplicações no Azure Stack: Adicionar mais funções de infraestrutura ou de trabalho

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*  

Este documento fornece instruções sobre como dimensionar o serviço de aplicações em funções de infraestrutura e de trabalho do Azure Stack. Ela contém passos para criar funções de trabalho adicional para suportar aplicações de qualquer tamanho.

> [!NOTE]
> Se o seu ambiente do Azure Stack não tem mais de 96 GB de RAM, poderá ter dificuldades a adição de capacidade adicional.

Serviço de aplicações no Azure Stack, por predefinição, suporta escalões de worker gratuito e partilhado. Para adicionar outros escalões de worker, terá de adicionar mais funções de trabalho.

Se não tiver a certeza de que foi implementado com o serviço de aplicações predefinido na instalação do Azure Stack, pode rever informações adicionais sobre o [serviço de aplicações no Descrição geral do Azure Stack](azure-stack-app-service-overview.md).

Serviço de aplicações do Azure no Azure Stack implementa todas as funções com conjuntos de dimensionamento de Máquina Virtual e como tal tira partido das capacidades de dimensionamento desta carga de trabalho. Por conseguinte, todos os dimensionamento dos escalões de worker é feita por meio de administrador de serviço de aplicações.

## <a name="add-additional-workers-with-powershell"></a>Adicionar trabalhos adicionais com o PowerShell

1. [Configurar o ambiente de administração do Azure Stack do PowerShell](azure-stack-powershell-configure-admin.md)

2. Utilize este exemplo para aumentar horizontalmente o conjunto de dimensionamento:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Este passo pode demorar várias horas a concluir consoante o tipo de função e o número de instâncias.
   >
   >

3. Monitorizar o estado de novas instâncias de função na administração de serviço de aplicações, para verificar o estado de uma instância de função individuais clique no tipo de função na lista.

## <a name="add-additional-workers-using-the-administration-portal"></a>Adicionar trabalhos adicionais com o portal de administração

1. Inicie sessão no portal de administração do Azure Stack como administrador de serviços.

2. Navegue até **dos serviços de aplicações**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Clique em **funções**. Aqui vê a divisão de todas as funções do serviço de aplicações implementada.

4. Clique com o botão direito do rato na linha do tipo que pretende dimensionar e, em seguida, clique em **conjunto de dimensionamento**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Clique em **Scaling**, selecione o número de instâncias que pretende dimensionar e, em seguida, clique em **guardar**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Serviço de aplicações no Azure Stack irá agora adicionar as VMs adicionais, configurá-los, instalar o software necessário e marcá-los como pronto quando esse processo estiver concluído. Este processo pode demorar cerca de 80 minutos.

7. Pode monitorizar o progresso de preparação das novas funções ao visualizar os operadores no **funções** painel.

## <a name="result"></a>Resultado

Depois de serem totalmente implementado e estiver pronto, os operadores de se tornar disponíveis para os utilizadores implementar a sua carga de trabalho neles. O código a seguir mostra um exemplo de vários escalões de preço disponíveis por predefinição. Se não houver nenhum trabalhadores disponíveis para um escalão de worker específico, a opção de escolher o escalão de preço correspondente não está disponível.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Para ampliar a gestão, funções de Front-End ou publicador adicionar que deve aumentar horizontalmente o tipo de função correspondente. 
>
>

Para ampliar a gestão de Front-End, funções ou publicador, siga os mesmos passos que selecionar o tipo de função adequados. Controladores não são implantados como conjuntos de dimensionamento e, portanto, dois devem ser implementada no momento da instalação para todas as implementações de produção.

### <a name="next-steps"></a>Passos Seguintes

[Configurar origens de implementação](azure-stack-app-service-configure-deployment-sources.md)
