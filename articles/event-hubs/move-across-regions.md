---
title: Mova um espaço de nome azure Event Hubs para outra região Microsoft Docs
description: Este artigo mostra-lhe como mover um espaço de nome azure Event Hubs da região atual para outra região.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 5b96bf1c538b3c5589a1993a0353292fadd0936d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690493"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Mova um espaço de nome azure Event Hubs para outra região
Existem vários cenários em que você gostaria de mover o seu espaço de nome de Event Hubs existente de uma região para outra. Por exemplo, pode querer criar um espaço de nome com a mesma configuração para testes. Você também pode querer criar um espaço de nome secundário em outra região como parte do planeamento de recuperação de [desastres.](event-hubs-geo-dr.md#setup-and-failover-flow)

> [!NOTE]
> Este artigo mostra-lhe como exportar um modelo de Gestor de Recursos Azure para um espaço de nome existente do Event Hubs e, em seguida, usar o modelo para criar um espaço de nome com as mesmas configurações de configuração em outra região. No entanto, este processo não move eventos que ainda não foram processados. Precisa processar os eventos a partir do espaço de nome original antes de o apagar.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os serviços e funcionalidades que a sua conta utiliza são suportados na região alvo.
- Para funcionalidades de pré-visualização, certifique-se de que a sua subscrição está listada para a região alvo.
- Se tiver uma **funcionalidade** de captura ativada para centros de eventos no espaço de nome, mova o [Azure Storage ou o Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) ou o [Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-migration-cross-region.md) antes de mover o espaço de nome sinuoso do Event Hubs. Também pode mover o grupo de recursos que contém espaços de nome de Armazenamento e Event Hubs para a outra região seguindo passos semelhantes aos descritos neste artigo. 
- Se o espaço de nome sem nome do Event Hubs estiver num cluster de Centros de **Eventos,** [crie um cluster dedicado](event-hubs-dedicated-cluster-create-portal.md) na **região alvo** antes de passar por etapas neste artigo. Também pode usar o [modelo de arranque rápido no GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) para criar um cluster de Hubs de Eventos. No modelo, remova a porção do espaço de nome do JSON para criar apenas o cluster. 

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos. Este modelo contém configurações que descrevem o espaço de nome do seu Event Hubs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. **Selecione Todos os recursos** e, em seguida, selecione o espaço de nome do Seu Event Hubs.

3. Selecione > Modelo**de exportação**de **definições** > .

4. Escolha **o Download** na página do modelo de **exportação.**

    ![Modelo de Gestor de Recursos de descarregamento](./media/move-across-regions/download-template.png)

5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

   Este ficheiro zip contém os ficheiros .json que incluem o modelo e scripts para implementar o modelo.


## <a name="move"></a>Mover

Desloque o modelo para criar um espaço de nome de Event Hubs na região alvo. 


1. No portal do Azure, selecione **Criar um recurso**.

2. Em **Pesquisar no Marketplace**, escreva **implementação de modelo** e prima **ENTER**.

3. Selecione **Implementação de modelo**.

4. Selecione **Criar**.

5. Selecione **Crie o seu próprio modelo no editor**.

6. Selecione **ficheiro Load**, e siga as instruções para carregar o ficheiro **template.json** que descarregou na última secção.

7. Selecione **Guardar** para salvar o modelo. 

8. Na página de **implementação personalizada,** siga estes passos: 

    1. Selecione uma **subscrição**Azure . 

    2. Selecione um grupo de **recursos** existente ou crie um. Se o espaço de nome de origem estiver num cluster de Centros de Eventos, selecione o grupo de recursos que contém cluster na região alvo. 

    3. Selecione a **localização** ou região alvo. Se selecionou um grupo de recursos existente, esta definição é apenas de leitura. 

    4. Na secção **DEFINIÇÕES,** faça os seguintes passos:
    
        1. introduza o novo **nome do espaço de nome.** 

            ![Modelo de Gestor de Recursos de implementação](./media/move-across-regions/deploy-template.png)

        2. Se o seu espaço de nome de origem estiver num cluster de Hubs de **Eventos,** insira nomes de grupo de **recursos** e cluster de Centros de **Eventos** como parte de **ID externo**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se o centro de eventos no seu espaço de nome utilizar uma conta `StorageAccounts_<original storage account name>_external` de Armazenamento para capturar eventos, especifique o nome do grupo de recursos e a conta de armazenamento para o campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selecione o **I concordo com os termos e condições indicados acima da** caixa de verificação. 
    
    6. Agora, selecione **Select Purchase** para iniciar o processo de implementação. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o espaço de nome do **Target Event Hubs**e repetir os passos descritos nas secções [preparem](#prepare) e [mover](#move) deste artigo.

Para comprometer as alterações e completar o movimento de um espaço de nome de Event Hubs, elimine o espaço de nome sinuoso do **Event Hubs**. Certifique-se de que processou todos os eventos no espaço de nome antes de apagar o espaço de nome. 

Para eliminar um espaço de nome de Event Hubs (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, digite Hubs de **Eventos,** e selecione Centros de Eventos a partir de **resultados** de pesquisa. Você vê os espaços de nome do Event Hubs numa lista.

2. Selecione o espaço de nome do alvo para eliminar e selecione **Apagar** a partir da barra de ferramentas. 

    ![Eliminar espaço de nome - botão](./media/move-across-regions/delete-namespace-button.png)

3. Na página **Eliminar Recursos*** verifique os recursos selecionados e confirme a eliminação escrevendo **sim**, e, em seguida, selecione **Eliminar**. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um espaço de nome azure Event Hubs de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e a recuperação de desastres em Azure, consulte:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) (Mover recursos para um grupo de recursos ou uma subscrição nova)
- [Mover VMs do Azure para outra região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
