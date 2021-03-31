---
title: Mova um cluster dedicado a Azure Event Hubs para outra região | Microsoft Docs
description: Este artigo mostra-lhe como mover um cluster dedicado a Azure Event Hubs da região atual para outra região.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89380836"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Mover um cluster dedicado a Azure Event Hubs para outra região
Este artigo mostra-lhe como exportar um modelo de Gestor de Recursos Azure para um cluster dedicado de Eventos existente e, em seguida, usar o modelo para criar um cluster com as mesmas configurações em outra região. 

Se você tiver outros recursos, como espaços de nome e centros de eventos no grupo de recursos Azure que contém o cluster Event Hubs, você pode querer exportar o modelo ao nível do grupo de recursos para que todos os recursos relacionados possam ser transferidos para a nova região em um passo. Os passos deste artigo mostram-lhe como exportar um **cluster de Centros de Eventos** para o modelo. Os passos para exportar um **grupo de recursos** para o modelo são semelhantes. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o cluster dedicado pode ser criado na região alvo. A maneira mais fácil de descobrir é usar o portal Azure para tentar criar um cluster dedicado ao [Event Hubs.](event-hubs-dedicated-cluster-create-portal.md) Vê-se a lista de regiões que são apoiadas nessa altura para a criação do cluster. 

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos. Este modelo contém configurações que descrevem o cluster dedicado do Event Hubs.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione o cluster dedicado do Event Hubs.
3. Selecione > **Definições**  >  **Modelo de exportação**.
4. Escolha **Baixar** na página do **modelo de exportação.**

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Baixar modelo de Gestor de Recursos" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha.

   Este ficheiro zip contém os ficheiros .json que incluem o modelo e scripts para implementar o modelo.


## <a name="move"></a>Mover

Implemente o modelo para criar um cluster dedicado ao Event Hubs na região alvo. 


1. No portal Azure, selecione **Criar um recurso**.
2. Em **Search the Marketplace**, design de design de **modelos** e selecione **a implementação do modelo (implementar usando modelos personalizados)**.
5. Selecione **Construa o seu próprio modelo no editor.**
6. Selecione **carregar o ficheiro** e, em seguida, siga as instruções para carregar otemplate.js **no** ficheiro que descarregou na última secção.
1. Atualizar o valor do `location` imóvel para apontar para a nova região. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. **Selecione Guardar** para guardar o modelo. 
1. Na página **de implementação personalizada,** siga estes passos: 
    1. Selecione uma **subscrição Azure**. 
    2. Selecione um grupo de **recursos** existente ou crie um. 
    3. Selecione a **localização** ou região do alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 
    4. Na secção **DEFINIÇÕES,** faça os seguintes passos:    
        1. Introduza o novo **nome de cluster**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Implementar o modelo do Gestor de Recursos":::
    5. Selecione **'Rever + criar'** na parte inferior da página. 
    1. Na página **'Rever + criar',** rever as definições e, em seguida, selecionar **Criar**.  

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o cluster dedicado do **Event Hubs** e repetir os passos descritos nas secções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para comprometer as alterações e completar o movimento de um cluster Event Hubs, elimine o **cluster Event Hubs** na região original. 

Para eliminar um cluster Desateia de Centros de Eventos (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **Clusters De Eventos** e selecione **Clusters De Eventos** a partir dos resultados da pesquisa. Você vê o cluster Event Hubs numa lista.
2. Selecione o cluster para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. Na página **Eliminar Cluster,** confirme a eliminação digitando o nome do **cluster** e, em seguida, selecione **Delete**. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a mover um cluster dedicado ao Event Hubs de uma região para outra. 

Consulte os [espaços de nomes move event hubs em todas as regiões](move-across-regions.md) para obter instruções sobre a deslocação de um espaço de nome de uma região para outra região. 

Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:

- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Mover VMs do Azure para outra região](../site-recovery/azure-to-azure-tutorial-migrate.md)
