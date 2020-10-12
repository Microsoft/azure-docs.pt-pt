---
title: Mover tópicos do sistema de grelha de eventos Azure para outra região
description: Este artigo mostra-lhe como mover tópicos do sistema Azure Event Grid de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89086187"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Mover tópicos do sistema de grelha de eventos Azure para outra região
Talvez queira mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades. 

Aqui estão os passos de alto nível abrangidos por este artigo: 

- **Exporte o grupo de recursos** que contém a conta de Armazenamento Azure e o tópico do sistema associado para um modelo de Gestor de Recursos. Também pode exportar um modelo apenas para o tópico do sistema. Se for por este caminho, lembre-se de mover a fonte de eventos Azure (neste exemplo, uma conta de Armazenamento Azure) para a outra região antes de mover o tópico do sistema. Em seguida, no modelo exportado para o tópico do sistema, atualize o ID externo para a conta de armazenamento na região alvo. 
- **Modifique o modelo** para adicionar a `endpointUrl` propriedade para apontar para um webhook que subscreve o tópico do sistema. Quando o tópico do sistema é exportado, a sua subscrição (neste caso, é um webhook) também é exportada para o modelo, mas a `endpointUrl` propriedade não está incluída. Por isso, é necessário atualizá-lo para indicar o ponto final que subscreve o tema. Além disso, atualize o valor do `location` imóvel para a nova localização ou região. Para outros tipos de manipuladores de eventos, você precisa de atualizar apenas a localização. 
- **Utilize o modelo para implantar recursos** na região alvo. Você especificará nomes para a conta de armazenamento e o tópico do sistema a ser criado na região alvo. 
- **Verifique a implantação**. Verifique se o webhook é invocado quando fizer o upload de um ficheiro para o armazenamento de bolhas na região alvo. 
- Para **completar o movimento**, elimine os recursos (fonte de evento e tópico do sistema) da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Complete o [Quickstart: Route Blob eventos de armazenamento para o ponto final da web com o portal Azure](blob-event-quickstart-portal.md) na região de origem. Este passo é **opcional.** Faça-o para testar etapas neste artigo. Mantenha a conta de armazenamento num grupo de recursos separado do plano de Serviço de Aplicações e Serviço de Aplicações. 
- Certifique-se de que o serviço de Grade de Eventos está disponível na região alvo. Ver [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos para o grupo de recursos que contém a fonte de evento do sistema (conta Azure Storage) e o tópico do sistema associado. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **grupos de recursos** no menu esquerdo. Em seguida, selecione o grupo de recursos que contém a fonte de evento para a qual o tópico do sistema foi criado. No exemplo seguinte, é a conta de **Armazenamento Azure.** O grupo de recursos contém a conta de armazenamento e o tópico do sistema associado. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Página de grupo de recursos":::        
3. No menu esquerdo, selecione **o modelo de exportação** em **Definições**e, em seguida, selecione **Descarregue** na barra de ferramentas. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Página de grupo de recursos"
        }
        ```

        > [!NOTE]
        > Para outros tipos de manipuladores de eventos, todas as propriedades são exportadas para o modelo. Basta atualizar a `location` propriedade para a região alvo, como mostrado no passo seguinte. 
7. Atualização `location` do recurso de conta de **armazenamento** para a região ou localização alvo. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Repita o passo para atualizar `location` o recurso tópico do **sistema** no modelo. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implemente o modelo para criar uma conta de armazenamento e um tópico de sistema para a conta de armazenamento na região alvo. 

1. No portal Azure, selecione **Criar um recurso**.
2. Em **Search the Marketplace**, **digitar a implementação do modelo**e, em seguida, premir **ENTER**.
3. Selecione **a implementação do modelo**.
4. Selecione **Criar**.
5. Selecione **Construa o seu próprio modelo no editor.**
6. Selecione **carregar o ficheiro**e, em seguida, siga as instruções para carregar otemplate.js** no** ficheiro que descarregou na última secção.
7. **Selecione Guardar** para guardar o modelo. 
8. Na página **de implementação personalizada,** siga estes passos. 
    1. Selecione uma **subscrição Azure**. 
    1. Selecione um grupo de **recursos** existente na região alvo ou crie um. 
    1. Para **a Região**, selecione a região alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura.
    1. Para o nome do tópico do **sistema,** insira um nome para o tópico do sistema que será associado à conta de armazenamento.  
    1. Para o nome da **conta de armazenamento,** insira um nome para a conta de armazenamento a criar na região alvo. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Página de grupo de recursos":::
    5. Selecione **'Rever + criar'** na parte inferior da página. 
    1. No **'Rever + criar** página', rever as definições e selecionar **Criar**. 

## <a name="verify"></a>Verificação
1. Após a implementação ter sucesso, **selecione o grupo de recursos Goto**. 
1. Na página do **grupo Recursos,** verifique se a fonte do evento (neste exemplo, conta Azure Storage) e o tópico do sistema são criados. 
1. Faça o upload de um ficheiro para um contentor no armazenamento da Azure Blob e verifique se o webhook recebeu o evento. Para mais informações, consulte [Enviar um evento para o seu ponto final.](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Para completar o movimento, elimine o grupo de recursos que contém a conta de armazenamento e o tópico do sistema associado na região de origem.  

Se quiser recomeçar, elimine o grupo de recursos na região alvo e repita os passos nas secções [Preparar](#prepare) e [Recriar](#recreate) deste artigo.

Para eliminar um grupo de recursos (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **grupos de Recursos**e selecione **grupos** de Recursos a partir dos resultados da pesquisa. 
2. Selecione o grupo de recursos para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 

    :::image type="content" source="./media/move-system-topics-across-regions/delete-resource-group-button.png" alt-text="Página de grupo de recursos":::
3. Na página de confirmação, insira o nome do grupo de recursos e selecione **Delete**.  

## <a name="next-steps"></a>Passos seguintes
Aprendeu a mover uma fonte de eventos Azure e o seu tópico de sistema associado de uma região para outra região. Consulte os seguintes artigos para mover tópicos personalizados, domínios e espaços de nomes de parceiros em regiões.

- [Mover tópicos personalizados através das regiões.](move-custom-topics-across-regions.md) 
- [Mover domínios através das regiões.](move-domains-across-regions.md) 
- [Mover espaços de nomes de parceiros em regiões.](move-partner-namespaces-across-regions.md) 

Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte o seguinte artigo: [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).
