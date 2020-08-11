---
title: Gerir instantâneos utilizando ficheiros Azure NetApp Microsoft Docs
description: Descreve como criar e gerir instantâneos utilizando ficheiros Azure NetApp.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 85990aee5143c9ccc0362a00597a748763977204
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080220"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gerir instantâneos com o Azure NetApp Files

O Azure NetApp Files suporta a criação de instantâneos a pedido e a utilização de políticas instantâneas para agendar a criação automática de instantâneos.  Também pode restaurar uma imagem instantânea para um novo volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Crie um instantâneo a pedido para um volume

Pode criar instantâneos de volume a pedido. 

1.  Vá ao volume para o que pretende criar uma imagem. Clique **em Snapshots**.

    ![Navegue para instantâneos](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Clique **+ Adicione instantâneo** para criar uma imagem a pedido para um volume.

    ![Adicionar instantâneo](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Na janela New Snapshot, forneça um nome para o novo instantâneo que está a criar.   

    ![Novo instantâneo](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Clique em **OK**. 

## <a name="manage-snapshot-policies"></a>Gerir políticas de instantâneo

Pode agendar as imagens de volume a serem tomadas automaticamente utilizando políticas de instantâneo. Também pode modificar uma política de instantâneos conforme necessário, ou eliminar uma política de instantâneos de que já não precisa.  

### <a name="register-the-feature"></a>Registar a funcionalidade

A funcionalidade **de política instantânea** está atualmente em pré-visualização. Se estiver a utilizar esta funcionalidade pela primeira vez, tem de registar a funcionalidade primeiro. 

1. Registar a função: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Verifique o estado do registo da funcionalidade: 

    > [!NOTE]
    > O **Estado de Registo** pode estar no estado até `Registering` 60 minutos antes de mudar para `Registered` . Aguarde até que o estado seja **registado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

### <a name="create-a-snapshot-policy"></a>Criar uma política instantânea 

Uma política instantânea permite especificar a frequência de criação instantânea em ciclos de hora, dia, semanais ou mensais. Também é necessário especificar o número máximo de instantâneos para reter para o volume.  

1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

    ![Navegação de política instantânea](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Na janela Snapshot Policy, desapeiça o Estado de Política **para Ativar**. 

3.  Clique no separador **Hourly,** **Daily,** **Weekly**ou **Monthly** para criar políticas de instantâneos horárias, diárias, semanais ou mensais. Especificar o **número de instantâneos para manter**.  

    Consulte [os limites de recursos para ficheiros Azure NetApp](azure-netapp-files-resource-limits.md) sobre o número máximo de instantâneos permitidos para um volume. 

    O exemplo a seguir mostra a configuração da política de instantâneo de hora a hora. 

    ![Política instantânea de hora em hora](../media/azure-netapp-files/snapshot-policy-hourly.png)

    O exemplo a seguir mostra a configuração diária da política de instantâneos.

    ![Política instantânea diariamente](../media/azure-netapp-files/snapshot-policy-daily.png)

    O exemplo a seguir mostra a configuração semanal da política de instantâneos.

    ![Política instantânea semanal](../media/azure-netapp-files/snapshot-policy-weekly.png)

    O exemplo a seguir mostra a configuração da política de instantâneo mensal.

    ![Política de instantâneo mensal](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Clique em **Guardar**.  

Se precisar de criar políticas de instantâneo adicionais, repita o Passo 3.
As políticas que criou aparecem na página de política do Snapshot.

Se quiser um volume para utilizar a política de instantâneo, tem de [aplicar a política ao volume](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Aplicar uma política instantânea num volume

Se quiser que um volume utilize uma política de instantâneo que criou, tem de aplicar a política ao volume. 

1.  Aceda à página **Volumes,** clique com o botão direito no volume a que pretende aplicar uma política de instantâneo e selecione **Editar**.

    ![Volumes menu de clique direito](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Na janela Editar, sob **a política Snapshot,** selecione uma política para utilizar para o volume.  Clique **em OK** para aplicar a política.  

    ![Edição de política instantânea](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Modificar uma política instantânea 

Pode modificar uma política de instantâneo existente para alterar o estado de política, a frequência instantânea (hora, dia, dia, semanal ou mensal) ou o número de instantâneos a manter.  
 
1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

2.  Clique à direita na política de instantâneo que pretende modificar e, em seguida, **selecione Editar**.

    ![Menu de clique de política instantânea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Faça as alterações na janela 'Política instantânea' que aparece e, em seguida, clique em **Guardar**. 

### <a name="delete-a-snapshot-policy"></a>Excluir uma política instantânea 

Pode apagar uma política de instantâneo que já não pretende manter.   

1.  A partir da vista da Conta NetApp, clique na **política Snapshot**.

2.  Clique em direito na política de instantâneo que pretende modificar e, em seguida, **selecione Delete**.

    ![Menu de clique de política instantânea](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Clique **em Sim** para confirmar que pretende eliminar a política de instantâneo.   

    ![Política instantânea eliminar confirmação](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Restaurar um instantâneo para um novo volume

Atualmente, pode restaurar uma imagem apenas para um novo volume. 
1. Selecione **Snapshots** da lâmina volume para visualizar a lista de instantâneos. 
2. Clique com o botão direito no instantâneo para restaurar e **selecione Restaurar para novo volume** a partir da opção menu.  

    ![Restaurar o instantâneo para um novo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Na janela Criar um Volume, forneça informações sobre o novo volume:  
    * **Nome**   
        Especifique o nome do volume que está a criar.  
        
        O nome tem de ser exclusivo dentro de um grupo de recursos. Deve ter pelo menos três caracteres de comprimento.  Pode utilizar carateres alfanuméricos.

    * **Quota**  
        Especifique a quantidade de armazenamento lógico que pretende atribuir ao volume.  

    ![Restaurar para novo volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Clique **em Review+criar**.  Clique em **Criar**.   
    O novo volume utiliza o mesmo protocolo que o instantâneo utiliza.   
    O novo volume ao qual o instantâneo é restaurado aparece na lâmina Volumes.

## <a name="next-steps"></a>Passos seguintes

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Resource limits for Azure NetApp Files](azure-netapp-files-resource-limits.md) (Limites dos recursos do Azure NetApp Files)
