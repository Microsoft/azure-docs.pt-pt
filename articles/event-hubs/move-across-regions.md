---
title: Mover um espaço de nomes Azure Event Hubs para outra região Microsoft Docs
description: Este artigo mostra-lhe como mover um espaço de nome Azure Event Hubs da região atual para outra região.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375198"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mover um espaço de nomes Azure Event Hubs para outra região
Este artigo mostra-lhe como exportar um modelo de Gestor de Recursos Azure para um espaço de nomes de Centros de Eventos existente e, em seguida, usar o modelo para criar um espaço de nome com as mesmas configurações em outra região. No entanto, este processo não move eventos que ainda não foram processados. Você precisa processar os eventos a partir do espaço de nome original antes de apagá-lo.
 
Se você tiver outros recursos no grupo de recursos Azure que contém o espaço de nomes Event Hubs, você pode querer exportar o modelo ao nível do grupo de recursos para que todos os recursos relacionados possam ser transferidos para a nova região em um passo. Os passos deste artigo mostram-lhe como exportar um **espaço de nome** para o modelo. Os passos para exportar um **grupo de recursos** para o modelo são semelhantes. 

## <a name="prerequisites"></a>Pré-requisitos

- Garanta que os serviços e as funcionalidades que a conta utiliza são suportados na região de destino.
- Se tiver **uma funcionalidade de captura** ativada para centros de eventos no espaço de nomes, mova o [Azure Storage ou a Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) ou [a Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o espaço de nomes do Event Hubs. Também pode mover o grupo de recursos que contém espaços de nome de Storage e Event Hubs para a outra região, seguindo passos semelhantes aos descritos neste artigo. 
- Se o espaço de nomes do Event Hubs estiver num **cluster de Clusters de Eventos,** [mova o cluster dedicado](move-cluster-across-regions.md) para a **região alvo** antes de passar por etapas neste artigo. Também pode usar o [modelo de arranque rápido no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) para criar um cluster Desaquitro de Eventos. No modelo, remova a parte do espaço de nome do JSON para criar apenas o cluster. 

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
2. Em **Search the Marketplace**, design de design de **modelos**e selecione **a implementação do modelo (implementar usando modelos personalizados)**.
5. Selecione **Construa o seu próprio modelo no editor.**
6. Selecione **carregar o ficheiro**e, em seguida, siga as instruções para carregar otemplate.js** no** ficheiro que descarregou na última secção.
1. Atualizar o valor do `location` imóvel para apontar para a nova região. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. **Selecione Guardar** para guardar o modelo. 
1. Na página **de implementação personalizada,** siga estes passos: 
    1. Selecione uma **subscrição Azure**. 
    2. Selecione um grupo de **recursos** existente ou crie um. Se o espaço de nome de origem estava num cluster De Event Hubs, selecione o grupo de recursos que contém cluster na região alvo. 
    3. Selecione a **localização** ou região do alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 
    4. Na secção **DEFINIÇÕES,** faça os seguintes passos:    
        1. Introduza o novo **nome de espaço de nome**. 

            ![Implementar o modelo do Gestor de Recursos](./media/move-across-regions/deploy-template.png)
        2. Se o seu espaço de nome de origem estava num **cluster de Clusters de Eventos, insira**nomes do grupo de **recursos** e do cluster **Event Hubs** como parte do **ID externo.** 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o centro de eventos no seu espaço de nome utilizar uma conta de Armazenamento para capturar eventos, especifique o nome do grupo de recursos e a conta de armazenamento para `StorageAccounts_<original storage account name>_external` o campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione **'Rever + criar'** na parte inferior da página. 
    1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar**.   

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o **espaço de nomes target Event Hubs**e repetir os passos descritos nas secções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para comprometer as alterações e completar o movimento de um espaço de nomes de Event Hubs, elimine o **espaço de nomes do Event Hubs** na região original. Certifique-se de que processou todos os eventos no espaço de nomes antes de apagar o espaço de nomes. 

Para eliminar um espaço de nomes de Event Hubs (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **'Event Hubs'** e selecione Os Centros de Eventos a partir dos **resultados** da pesquisa. Você vê os espaços de nomes do Event Hubs numa lista.
2. Selecione o espaço de nome alvo para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 

    ![Apagar espaço de nome - botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **Delete Namespace,** confirme a eliminação digitando o **nome do espaço de nome**e, em seguida, selecione **Delete**. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você mudou um espaço de nome Azure Event Hubs de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:


- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
