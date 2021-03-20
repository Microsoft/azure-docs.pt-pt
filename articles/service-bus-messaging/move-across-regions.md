---
title: Mover um espaço de nomes de autocarro de serviço para outra região | Microsoft Docs
description: Este artigo mostra-lhe como mover um espaço de nomes Azure Service Bus da região atual para outra região.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88861066"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Mova um espaço de nome de ônibus de serviço Azure para outra região
Existem vários cenários em que você gostaria de mover o seu espaço de nome de Service Bus existente de uma região para outra. Por exemplo, pode querer criar um espaço com o mesmo nome para testes. Você também pode querer criar um espaço de nome secundário em outra região como parte do planeamento de recuperação de [desastres.](service-bus-geo-dr.md)

Aqui estão os passos de alto nível:

1. Exporte o espaço de nome do Service Bus na região atual para um modelo de Gestor de Recursos Azure. 
1. Atualizar a localização para os recursos no modelo. Além disso, remova o filtro de subscrição predefinido do modelo porque não pode criar uma regra predefinição, uma vez que é criada automaticamente para si. 
1. Utilize o modelo para implantar o espaço de nomes do Service Bus para a região alvo. 
1. Verifique a implementação para garantir que o espaço de nomes, filas, tópicos e subscrições de tópicos são todos criados na região alvo. 
1. Complete o movimento eliminando o espaço de nome da região de origem após o processamento de todas as mensagens. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o Azure Service Bus e as funcionalidades que a sua conta utiliza são suportados na região alvo.
 
## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos. Este modelo contém definições que descrevem o seu espaço de nomes de Service Bus.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione o seu espaço de nomes de Service Bus.
3. Selecione > **Definições**  >  **Modelo de exportação**.
4. Escolha **Baixar** na página do **modelo de exportação.**

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Baixar modelo de Gestor de Recursos":::
5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha. Este ficheiro zip contém os ficheiros JSON do modelo e parâmetros. 
1. Abra a template.jsno ficheiro na pasta extraída. 
1. Procure, `location` e substitua o valor do imóvel com o novo nome para a região ou localização. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Remover definições de recursos do tipo: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Não se esqueça de remover o carácter da vírgula `,` () que precede esta secção para manter o JSON válido.  

    > [!NOTE]
    > Não é possível criar uma regra padrão para uma subscrição utilizando um modelo de Gestor de Recursos. A regra padrão é criada automaticamente quando a subscrição é criada na região alvo. 

## <a name="move"></a>Mover
Implemente o modelo para criar um espaço de nome de ônibus de serviço na região alvo. 

1. No portal Azure, selecione **Criar um recurso**.
2. Em **Search the Marketplace**, **digite a implementação** do modelo para o texto de pesquisa, selecione **a implementação do modelo (implementar usando modelos personalizados)** e, em seguida, prima **ENTER**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nova implementação do modelo":::    
1. Na página de implementação do **modelo,** selecione **Criar**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Nova implementação do modelo - crie botão":::        
1. Na página **de implementação personalizada,** selecione **Construa o seu próprio modelo no editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Construa o seu próprio modelo no editor - link":::            
1. Na página do **modelo editar,** selecione Carregar o **ficheiro** na barra de ferramentas e, em seguida, seguir as instruções para carregar o **template.jsno** ficheiro que descarregou na última secção.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Selecione o modelo":::                
1. **Selecione Guardar** para guardar o modelo. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Modelo de poupança":::                    
1. Na página **de implementação personalizada,** siga estes passos: 
    1. Selecione uma **subscrição Azure**. 
    2. Selecione um grupo de **recursos** existente ou crie um. 
    3. Selecione a **localização** ou região do alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 
    4. Introduza um novo **nome para o espaço de nomes.**
    1. Selecione **Rever + criar**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Implementar o modelo do Gestor de Recursos":::
    1. Na página **'Rever + criar',** selecione **Criar** na parte inferior da página. 
    
## <a name="verify"></a>Verificação
1. Depois de a implementação ter sido bem sucedida, selecione **Ir para o grupo de recursos**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Ir para a ligação de grupo de recursos":::    
1. Na página do **grupo Recursos,** selecione o espaço de nomes do Service Bus. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Selecione o espaço de nomes do ônibus de serviço":::    
1. Na página de **espaço de nomes do Service Bus,** verifique se vê as filas, tópicos e subscrições da região de origem. 
    1. **Vê-se filas** no espaço de nomes na parte inferior do painel direito.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Filas no espaço de nomes":::
    2. Mude para o **separador Tópicos** para ver tópicos no espaço de nomes
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Tópicos no espaço de nomes":::
    3. Selecione o tópico para verificar se as subscrições são criadas. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Assinaturas de tópicos":::      
    
    

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o **espaço de nomes do Service Bus,** e repetir os passos descritos nas secções [Prepare](#prepare) e [Mover](#move) deste artigo.

Para comprometer as alterações e completar o movimento de um espaço de nomes de Service Bus, elimine o **espaço de nomes de origem Service Bus**. Certifique-se de que processa todas as mensagens antes de apagar o espaço de nomes. 

Para eliminar um espaço de nomes de Service Bus (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **Service Bus** e selecione **Service Bus** a partir dos resultados da pesquisa. Você vê os espaços de nome do Service Bus numa lista.
2. Selecione o espaço de nome alvo para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 

    ![Apagar espaço de nome - botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **Eliminar Recursos,** verifique os recursos selecionados e confirme a eliminação digitando **sim** e, em seguida, selecione **Delete**. 

    Outra opção é eliminar o grupo de recursos que tem o espaço de nomes do Service Bus. Na página do **grupo Recursos,** selecione Eliminar o **grupo de recursos** na barra de ferramentas e, em seguida, confirmar a eliminação. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você mudou um espaço de nome de Azure Service Bus de uma região para outra e limpou os recursos de origem.  Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:

- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
