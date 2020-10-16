---
title: Mover os domínios da Grelha de Eventos Azure para outra região
description: Este artigo mostra-lhe como mover os domínios da Grelha de Eventos Azure de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89086217"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Mover os domínios da Grelha de Eventos Azure para outra região
Talvez queira mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades. 

Aqui estão os passos de alto nível abrangidos por este artigo: 

- **Exporte o** recurso de domínio para um modelo de Gestor de Recursos Azure. 

    > [!IMPORTANT]
    > O recurso de domínio e os tópicos no domínio são exportados para o modelo. As assinaturas para tópicos de domínio não são exportadas. 
- **Utilize o modelo para implantar o domínio** na região alvo. 
- **Crie subscrições para tópicos de domínio manualmente** na região alvo. Quando exportaste o domínio para um modelo na região atual, as subscrições para tópicos de domínio não são exportadas. Então, crie-os depois de criados tópicos de domínio e domínio na região alvo. 
- **Verifique a implantação**. Envie um evento para um tópico de domínio no domínio e verifique se o manipulador de eventos associado à subscrição é invocado. 
- Para **completar o movimento,** elimine o domínio da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Certifique-se de que o serviço de Grade de Eventos está disponível na região alvo. Ver [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)

## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos para o domínio. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa, digite **Os Domínios da Grelha de Eventos**e selecione **Os Domínios** da Grelha de Eventos na lista de resultados. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Procure e selecione domínios de Grade de Eventos":::
3. Selecione o **domínio** que pretende exportar para um modelo de Gestor de Recursos. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Procure e selecione domínios de Grade de Eventos":::   
4. Na página de **Domínio da grelha de evento,** selecione o modelo de **exportação** em **Definições** no menu esquerdo e, em seguida, selecione **Descarregue** na barra de ferramentas. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Procure e selecione domínios de Grade de Eventos" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Os tópicos de domínio e domínio são exportados. As assinaturas para tópicos de domínio não são exportadas. Por isso, é necessário criar subscrições para tópicos de domínio depois de mover tópicos de domínio. 
5. Localize o ficheiro **.zip** que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha. Este ficheiro zip contém ficheiros JSON de modelo e parâmetros. 
1. Abra a **template.jsnum** editor à sua escolha. 
8. Atualização `location` do recurso de **domínio** para a região ou localização alvo. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implemente o modelo para criar os tópicos de domínio e domínio na região alvo. 

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
    1. Para o nome de **domínio,** insira um novo nome para o domínio. 
    1. Selecione **Rever + criar**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Procure e selecione domínios de Grade de Eventos":::        
    1. Após a validação do modelo ter sido bem sucedida, **selecione Criar** na parte inferior da página para implementar o recurso. 
    1. Depois de a implementação ter sucesso, selecione **Ir para o grupo de recursos** para navegar para a página do grupo de recursos. Confirme que há um domínio no grupo de recursos. Selecione o domínio. Confirme que existem tópicos de domínio no domínio. 

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Para completar o movimento, elimine o domínio na região de origem.  

Se quiser recomeçar, elimine o domínio na região alvo e repita os passos nas secções [Preparar](#prepare) e [Recriar](#recreate) este artigo.

Para eliminar um domínio utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, digite **Domínios de grelha de eventos**e selecione Domínios de Grelha de Evento a partir dos **resultados** da pesquisa. 
2. Selecione o domínio para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. Na página de confirmação, insira o nome do grupo de recursos e selecione **Delete**.  

Para eliminar o grupo de recursos que contém o domínio utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, escreva **grupos de Recursos**e selecione **grupos** de Recursos a partir dos resultados da pesquisa. 
2. Selecione o grupo de recursos para eliminar e **selecione Eliminar** a partir da barra de ferramentas. 
3. Na página de confirmação, insira o nome do grupo de recursos e selecione **Delete**.  

## <a name="next-steps"></a>Passos seguintes
Aprendeu a mover um domínio de Grade de Eventos de uma região para outra região. Consulte os seguintes artigos para tópicos de sistema em movimento, tópicos personalizados e espaços de nomes de parceiros em regiões.

- [Mover tópicos de sistema em regiões.](move-system-topics-across-regions.md) 
- [Mover tópicos personalizados através das regiões.](move-custom-topics-across-regions.md) 
- [Mover espaços de nomes de parceiros em regiões.](move-partner-namespaces-across-regions.md)

Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte o seguinte artigo: [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).