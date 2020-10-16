---
title: Mover espaços de nomes parceiros da Azure Event Grid para outra região
description: Este artigo mostra-lhe como mover espaços de nome de parceiros Azure Event Grid de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89086210"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Mover espaços de nomes parceiros da Azure Event Grid para outra região
Talvez queira mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades. 

Aqui estão os passos de alto nível abrangidos por este artigo: 

- **Exporte o** recurso de espaço de nome do parceiro para um modelo de Gestor de Recursos Azure. Elimine as definições para os recursos de canais de eventos no modelo. Um canal de eventos pode ter uma referência ao ID do Gestor de Recursos Azure do tópico do parceiro, que é propriedade de um cliente. Então, não podem ser criados usando o modelo na região alvo.  
- **Utilize o modelo para implantar o espaço de nome do parceiro** na região alvo. Em seguida, crie canais de eventos no novo espaço de nome de parceiros na região alvo. 
- Para **completar o movimento,** elimine o espaço de nome do parceiro da região de origem. 

    > [!NOTE]
    > - Exportar **tópicos** de parceiro para um modelo de Gestor de Recursos Azure não é suportado porque os clientes não podem criar um tópico de parceiro diretamente. 
    > - **Os registos de parceiros** são recursos globais (não ligados a nenhuma região específica), pelo que a sua deslocação de uma região para outra não é aplicável. 

## <a name="prerequisites"></a>Pré-requisitos
- Certifique-se de que o serviço de Grade de Eventos está disponível na região alvo. Ver [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos para o espaço de nome do parceiro. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa no topo, digite **os espaços de nome do parceiro Event Grid**e selecione Os Espaços de Nome do Parceiro de Grelha de Eventos na lista de **resultados.** 
3. Selecione o **espaço de nome do parceiro** que pretende exportar para um modelo de Gestor de Recursos. 
4. Na página **'Espaço nome do parceiro' '' '' '' '' ''** **'' 'Designar' 'Exportação'** **no** menu esquerdo e, em seguida, selecione **Descarregue** na barra de ferramentas. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Modelo de exportação -> Download" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Localize o ficheiro **.zip** que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha. Este ficheiro zip contém ficheiros JSON de modelo e parâmetros. 
1. Abra a **template.jsnum** editor à sua escolha. 
8. Atualização `location` para o recurso **tópico** para a região ou localização alvo. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implemente o modelo para criar um espaço de nome de parceiro na região alvo. 

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
    1. Para **localização,** selecione a região alvo. Se selecionar um grupo de recursos existente, esta definição é apenas de leitura. 
    1. Para o **nome do espaço parceiro,** insira um nome para o novo espaço de nome do parceiro. 
    1. Para o ID externo do registo do parceiro, insira o ID de recurso do registo do parceiro no seguinte formato: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Selecione o **eu concordo com os termos e condições indicados acima** da caixa de verificação.     
    1. Selecione **Review + criar** para iniciar o processo de implementação. 
    1. No **'Rever + criar** página', rever as definições e selecionar **Criar**. 

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Para completar o movimento, elimine o espaço de nome do parceiro na região de origem.  

Se quiser recomeçar, elimine o espaço de nome do parceiro na região alvo e repita os passos nas secções [Preparar](#prepare) e [Recriar](#recreate) deste artigo.

Para eliminar um espaço de nome de parceiro utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, **digite Os espaços de nome do parceiro de grelha**de eventos e selecione os espaços de **nomes de parceiros de grelha** de evento a partir dos resultados da pesquisa. 
2. Selecione o espaço de nome do parceiro para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. **Confirme** a eliminação para eliminar o espaço de nome do parceiro. 

## <a name="next-steps"></a>Passos seguintes
Aprendeu a mover um espaço de nome de parceiros de Event Grid de uma região para outra região. Consulte os seguintes artigos para tópicos de sistema em movimento, tópicos personalizados e domínios em regiões.

- [Mover tópicos de sistema em regiões.](move-system-topics-across-regions.md) 
- [Mover tópicos personalizados através das regiões.](move-custom-topics-across-regions.md) 
- [Mover domínios através das regiões.](move-domains-across-regions.md)

Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte o seguinte artigo: [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).