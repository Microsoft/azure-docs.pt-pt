---
title: Mover um espaço de nomes Azure Event Hubs para outra região Microsoft Docs
description: Este artigo mostra-lhe como mover um espaço de nome Azure Event Hubs da região atual para outra região.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 51b02c34b0c28420a7e27da56b107ed3925a761b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537076"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mover um espaço de nomes Azure Event Hubs para outra região
Existem vários cenários em que você gostaria de mover o seu espaço de nomes de Eventos existente de uma região para outra. Por exemplo, pode querer criar um espaço com o mesmo nome para testes. Você também pode querer criar um espaço de nome secundário em outra região como parte do planeamento de recuperação de [desastres.](event-hubs-geo-dr.md#setup-and-failover-flow)

> [!NOTE]
> Este artigo mostra-lhe como exportar um modelo de Gestor de Recursos Azure para um espaço de nomes de Centros de Eventos existente e, em seguida, usar o modelo para criar um espaço de nome com as mesmas configurações em outra região. No entanto, este processo não move eventos que ainda não foram processados. Você precisa processar os eventos a partir do espaço de nome original antes de apagá-lo.

## <a name="prerequisites"></a>Pré-requisitos

- Garanta que os serviços e as funcionalidades que a conta utiliza são suportados na região de destino.
- Para as funcionalidades de pré-visualização, garanta que a subscrição está na lista de permissões da região de destino.
- Se tiver **uma funcionalidade de captura** ativada para centros de eventos no espaço de nomes, mova o [Azure Storage ou a Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) ou [a Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o espaço de nomes do Event Hubs. Também pode mover o grupo de recursos que contém espaços de nome de Storage e Event Hubs para a outra região, seguindo passos semelhantes aos descritos neste artigo. 
- Se o espaço de nomes do Event Hubs estiver num **cluster de Clusters de Eventos,** [crie um cluster dedicado](event-hubs-dedicated-cluster-create-portal.md) na **região alvo** antes de passar por etapas neste artigo. Também pode usar o [modelo de arranque rápido no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) para criar um cluster Desaquitro de Eventos. No modelo, remova a parte do espaço de nome do JSON para criar apenas o cluster. 

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos. Este modelo contém definições que descrevem o espaço de nomes do Event Hubs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione o seu espaço de nomes Desempacoto de Centros de Eventos.

3. Selecione > **Definições**  >  **Modelo de exportação**.

4. Escolha **Baixar** na página do **modelo de exportação.**

    ![Baixar modelo de Gestor de Recursos](./media/move-across-regions/download-template.png)

5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

   Este ficheiro zip contém os ficheiros .json que incluem o modelo e scripts para implementar o modelo.


## <a name="move"></a>Mover

Implemente o modelo para criar um espaço de nomes de Centros de Eventos na região alvo. 


1. No portal Azure, selecione **Criar um recurso**.

2. Em **Search the Marketplace**, **digitar a implementação do modelo**e, em seguida, premir **ENTER**.

3. Selecione **a implementação do modelo**.

4. Selecione **Criar**.

5. Selecione **Construa o seu próprio modelo no editor.**

6. Selecione **carregar o ficheiro**e, em seguida, siga as instruções para carregar otemplate.js**no** ficheiro que descarregou na última secção.

7. **Selecione Guardar** para guardar o modelo. 

8. Na página **de implementação personalizada,** siga estes passos: 

    1. Selecione uma **subscrição Azure**. 

    2. Selecione um grupo de **recursos** existente ou crie um. Se o espaço de nome de origem estava num cluster De Event Hubs, selecione o grupo de recursos que contém cluster na região alvo. 

    3. Selecione a **localização** ou região do alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 

    4. Na secção **DEFINIÇÕES,** faça os seguintes passos:
    
        1. insira o novo **nome de espaço de nome**. 

            ![Implementar o modelo do Gestor de Recursos](./media/move-across-regions/deploy-template.png)

        2. Se o seu espaço de nome de origem estava num **cluster de Clusters de Eventos, insira**nomes do grupo de **recursos** e do cluster **Event Hubs** como parte do **ID externo.** 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o centro de eventos no seu espaço de nome utilizar uma conta de Armazenamento para capturar eventos, especifique o nome do grupo de recursos e a conta de armazenamento para `StorageAccounts_<original storage account name>_external` o campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione o **eu concordo com os termos e condições indicados acima** da caixa de verificação. 
    
    6. Agora, **selecione Select Purchase** para iniciar o processo de implementação. 

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o **espaço de nomes target Event Hubs**e repetir os passos descritos nas secções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para comprometer as alterações e completar o movimento de um espaço de nomes de Event Hubs, elimine o **espaço de nomes de Centros de Eventos de origem**. Certifique-se de que processou todos os eventos no espaço de nomes antes de apagar o espaço de nomes. 

Para eliminar um espaço de nomes de Event Hubs (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **'Event Hubs'** e selecione Os Centros de Eventos a partir dos **resultados** da pesquisa. Você vê os espaços de nomes do Event Hubs numa lista.

2. Selecione o espaço de nome alvo para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 

    ![Apagar espaço de nome - botão](./media/move-across-regions/delete-namespace-button.png)

3. Na página **Eliminar Recursos*** verifique os recursos selecionados e confirme a eliminação digitando **sim**, e, em seguida, selecione **Delete**. 

## <a name="next-steps"></a>Próximos passos

Neste tutorial, você mudou um espaço de nome Azure Event Hubs de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
