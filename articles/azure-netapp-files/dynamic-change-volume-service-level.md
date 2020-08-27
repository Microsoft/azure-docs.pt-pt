---
title: Alterar dinamicamente o nível de serviço de um volume para ficheiros Azure NetApp Microsoft Docs
description: Descreve como alterar dinamicamente o nível de serviço de um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: fb83b30f0844b9bf0e362e6f1e3a3822ba0044d1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950005"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Alterar o nível de serviço de um volume de forma dinâmica

Pode alterar o nível de serviço de um volume existente movendo o volume para outro pool de capacidade que utiliza o [nível](azure-netapp-files-service-levels.md) de serviço que deseja para o volume. Esta alteração ao nível de serviço no local para o volume não requer que se emigre os dados. Também não afeta o acesso ao volume.  

Esta funcionalidade permite-lhe atender às suas necessidades de carga de trabalho a pedido.  Pode alterar um volume existente para utilizar um nível de serviço mais elevado para um melhor desempenho, ou para utilizar um nível de serviço mais baixo para a otimização de custos. Por exemplo, se o volume estiver atualmente num pool de capacidade que utiliza o nível de serviço *Standard* e quiser que o volume utilize o nível de serviço *Premium,* pode mover o volume dinamicamente para um pool de capacidade que utilize o nível de serviço *Premium.*  

A capacidade de movimentação do volume já deve existir. A piscina de capacidade pode conter outros volumes.  Se quiser mover o volume para um pool de capacidade novinho em folha, tem de [criar a capacidade](azure-netapp-files-set-up-capacity-pool.md) de piscina antes de mover o volume.  

## <a name="considerations"></a>Considerações

* Depois de o volume ser transferido para outra piscina de capacidade, deixará de ter acesso aos registos de atividade de volume anteriores e às métricas de volume. O volume começará com novos registos de atividade e métricas sob a nova capacidade.

* Se mover um volume para um pool de capacidade de um nível de serviço mais elevado (por exemplo, passar de *Standard* para *Premium* ou *Ultra),* deve esperar pelo menos sete dias antes de poder mover esse volume *novamente* para um pool de capacidade de um nível de serviço mais baixo (por exemplo, passar de *Ultra* para *Premium* ou *Standard).*  

## <a name="register-the-feature"></a>Registar a funcionalidade

A funcionalidade para mover um volume para outro pool de capacidade está atualmente em pré-visualização. Se estiver a utilizar esta funcionalidade pela primeira vez, tem de registar a funcionalidade primeiro.

1. Registar a função: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Verifique o estado do registo da funcionalidade: 

    > [!NOTE]
    > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja **registado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Também pode utilizar comandos Azure CLI [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) e registar a funcionalidade e exibir o estado de [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) registo. 

## <a name="move-a-volume-to-another-capacity-pool"></a>Mover um volume para outra piscina de capacidade

1.  Na página Volumes, clique à direita no volume cujo nível de serviço pretende alterar. Selecione **Change Pool**.

    ![Volume de clique direito](../media/azure-netapp-files/right-click-volume.png)

2. Na janela change pool, selecione o pool de capacidade para a quais deseja mover o volume. 

    ![Mudar piscina](../media/azure-netapp-files/change-pool.png)

3.  Clique em **OK**.


## <a name="next-steps"></a>Passos seguintes  

* [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
