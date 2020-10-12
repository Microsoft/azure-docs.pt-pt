---
title: Gerir um pool manual de capacidade QoS para ficheiros Azure NetApp Microsoft Docs
description: Descreve como gerir um pool de capacidade que utiliza o tipo QoS manual, incluindo a criação de um pool manual de capacidade QoS e a alteração de um pool de capacidade para usar qoS manual.
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: d2abb66684738e97ade2a2e67309a4e7b53c4734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442085"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Gerir um conjunto de capacidade QoS manual

Este artigo descreve como gerir uma piscina de capacidade que utiliza o tipo QoS manual.  

Consulte a hierarquia de armazenamento dos ficheiros Escandaço do [Azure NetApp](azure-netapp-files-understand-storage-hierarchy.md) e considerações de desempenho para a [Azure NetApp Files](azure-netapp-files-performance-considerations.md) para entender as considerações sobre os tipos de QoS.  

## <a name="register-the-feature"></a>Registar a funcionalidade
A função manual do tipo QoS encontra-se atualmente em pré-visualização. Se estiver a utilizar esta funcionalidade pela primeira vez, tem de registar a funcionalidade primeiro.
  
1.  Registar a função:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Verifique o estado do registo da funcionalidade: 

    > [!NOTE]
    > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja **registado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Também pode utilizar [comandos Azure CLI](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` e registar a funcionalidade e exibir o estado de `az feature show` registo. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Configurar um novo pool manual de capacidade QoS 

Para criar um novo pool de capacidade utilizando o tipo QoS manual:

1. Siga os passos na [Configuração de uma piscina de capacidade.](azure-netapp-files-set-up-capacity-pool.md)  

2. Na janela New Capacity Pool, selecione o tipo **Manual QoS.**  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Alterar uma piscina de capacidade para usar qoS manual

Pode alterar um pool de capacidade que atualmente utiliza o tipo auto QoS para utilizar o tipo QoS manual.  

> [!IMPORTANT]
> Definir o tipo de capacidade para QoS manual é uma alteração permanente. Não é possível converter uma ferramenta de capacidade manual do tipo QoS num pool automático de capacidade QoS. 

1. A partir da lâmina de gestão da sua conta NetApp, clique em **pools de capacidade** para exibir as piscinas de capacidade existentes.   
 
2.  Clique na piscina de capacidade que pretende alterar para utilizar o QoS manual.

3.  Clique **em Alterar O tipo QoS**. Em seguida, desa ajuste o **novo tipo QoS** para **manual**. Clique em **OK**. 

![Alterar tipo QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Monitorize a produção de um pool manual de capacidade QoS  

As métricas estão disponíveis para ajudá-lo a monitorizar a leitura e a escrever o resultado de um volume.  Ver [métricas para ficheiros Azure NetApp](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Modifique a produção atribuída de um volume QoS manual 

Se um volume estiver contido num pool manual de capacidade QoS, pode modificar a potência de volume atribuída conforme necessário.

1. Na página **Volumes,** selecione o volume cuja produção pretende modificar.   

2. Clique **em Alterar a produção**. Especifique a **produção (MiB/S)** que deseja. Clique em **OK**. 

    ![Alterar produção QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Passos seguintes  

* [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md)
* [Considerações de desempenho para o Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Problemas de capacidade de resolução de problemas](troubleshoot-capacity-pools.md)
* [Hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Modelo de custo para os Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
* [Criar um volume NFS](azure-netapp-files-create-volumes.md)
* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)


