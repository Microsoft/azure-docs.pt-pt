---
title: Mova tópicos personalizados da Grelha de Eventos Azure para outra região
description: Este artigo mostra-lhe como mover tópicos personalizados da Azure Event Grid de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145349"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Mova tópicos personalizados da Grelha de Eventos Azure para outra região
Talvez queira mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades. 

Aqui estão os passos de alto nível abrangidos por este artigo: 

- **Exporte o** recurso tópico personalizado para um modelo de Gestor de Recursos Azure. 

    > [!IMPORTANT]
    > Apenas o tópico personalizado é exportado para o modelo. Quaisquer subscrições para o tópico não são exportadas.
- **Utilize o modelo para implementar o tópico personalizado** para a região alvo. 
- **Crie subscrições manualmente** na região alvo. Quando você exportou o tópico personalizado para um modelo na região atual, apenas o tópico é exportado. As subscrições não estão incluídas no modelo, por isso crie-as manualmente após a criação do tópico personalizado na região alvo. 
- **Verifique a implantação**. Verifique se o tópico personalizado é criado na região alvo. 
- Para **completar o movimento,** elimine o tópico personalizado da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Complete o [Quickstart: Encaminhe os eventos personalizados para](custom-event-quickstart-portal.md) o ponto final da web na região de origem. Faça este passo para que possa testar etapas neste artigo. 
- Certifique-se de que o serviço de Grade de Eventos está disponível na região alvo. Ver [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos para o tópico personalizado. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa, escreva **tópicos de Grade de Eventos**e selecione **Tópicos de Grelha de Eventos** da lista de resultados. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Procurar e selecionar tópicos de Grelha de Eventos":::
3. Selecione o **tópico** que pretende exportar para um modelo de Gestor de Recursos. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Procurar e selecionar tópicos de Grelha de Eventos":::   
4. Na página **tópico da grelha de evento,** selecione o modelo de **exportação** em **definições** no menu esquerdo e, em seguida, selecione **Descarregue** na barra de ferramentas. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Procurar e selecionar tópicos de Grelha de Eventos"
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implemente o modelo para criar um tópico personalizado na região alvo. 

1. No portal Azure, selecione **Criar um recurso**.
2. Em **Search the Marketplace**, **digitar a implementação do modelo**e, em seguida, premir **ENTER**.
3. Selecione **a implementação do modelo**.
4. Selecione **Criar**.
5. Selecione **Construa o seu próprio modelo no editor.**
6. Selecione **carregar o ficheiro**e, em seguida, siga as instruções para carregar otemplate.js** no** ficheiro que descarregou na última secção.
7. **Selecione Guardar** para guardar o modelo. 
8. Na página **de implementação personalizada,** siga estes passos: 
    1. Selecione uma **subscrição Azure**. 
    1. Selecione um grupo de **recursos** existente na região alvo ou crie um. 
    1. Para **a Região**, selecione a região alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 
    1. Para o **nome do tópico,** insira um novo nome para o tema. 
    1. Selecione **'Rever + criar'** na parte inferior da página. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Procurar e selecionar tópicos de Grelha de Eventos":::
    1. No **'Rever + criar** página', rever as definições e selecionar **Criar**. 

## <a name="verify"></a>Verificação

1. Depois de a implementação ter sucesso, selecione **Ir para o recurso**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Procurar e selecionar tópicos de Grelha de Eventos":::
1. Confirme que vê a página **tópico da grelha de evento** para o tópico personalizado.   
1. Siga os passos na [Rota eventos personalizados para um ponto final web](custom-event-quickstart-portal.md#send-an-event-to-your-topic) para enviar eventos para o tópico. Verifique se o manipulador de eventos webhook é invocado. 

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Para completar o movimento, elimine o tópico personalizado na região de origem.  

Se quiser recomeçar, elimine o tópico na região alvo e repita os passos nas secções [Preparar](#prepare) e [Recriar](#recreate) deste artigo.

Para eliminar um tópico personalizado utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **Tópicos de Grelha de Eventos**e selecione Tópicos de Grelha de Eventos a partir dos **resultados** da pesquisa. 
2. Selecione o tópico para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. Na página de confirmação, insira o nome do grupo de recursos e selecione **Delete**.  

Para eliminar o grupo de recursos que contém o tópico personalizado utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **grupos de Recursos**e selecione **grupos** de Recursos a partir dos resultados da pesquisa. 
2. Selecione o grupo de recursos para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. Na página de confirmação, insira o nome do grupo de recursos e selecione **Delete**.  

## <a name="next-steps"></a>Passos seguintes
Aprendeu a mover um tópico personalizado da Grade de Eventos de uma região para outra região. Consulte os seguintes artigos para mover tópicos, domínios e espaços de nomes de parceiros em regiões.

- [Mover tópicos de sistema em regiões.](move-system-topics-across-regions.md) 
- [Mover domínios através das regiões.](move-domains-across-regions.md) 
- [Mover espaços de nomes de parceiros em regiões.](move-partner-namespaces-across-regions.md)

Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte o seguinte artigo: [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).