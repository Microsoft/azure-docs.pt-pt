---
title: Mova um espaço de nome Azure Relay para outra região
description: Este artigo mostra-lhe como mover um espaço de nome Azure Relay da região atual para outra região.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463829"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Mova um espaço de nome Azure Relay para outra região
Este artigo mostra-lhe como mover um espaço de nome Azure Relay de uma região para outra região. Aqui estão os passos de alto nível:

1. **Exporte** o espaço de nome Relay para um modelo de Gestor de Recursos Azure.
1. **Atualizar localização (região)** para recursos no modelo. Além disso, elimine quaisquer relés **dinâmicos** do WCF do modelo. 

    Os relés WCF têm dois modos. No primeiro modo, o relé WCF é criado explicitamente usando o modelo do portal Azure ou do Gestor de Recursos Azure. Na página **wcf Relays** do portal Azure, você vê a propriedade **isDynamic** definida como **falsa** para um retransmissor neste modo. 

    No segundo modo, o relé WCF é gerado automaticamente quando um ouvinte (servidor) se conecta para um determinado endereço de ponto final. Enquanto o ouvinte estiver ligado ao relé, vê o retransmissor na lista de relés WCF no portal Azure. Para um retransmissor neste modo, a propriedade **isDynamic** é definida como **verdadeira** porque é gerada dinamicamente. O relé dinâmico do WCF desaparece quando o ouvinte se desliga. 
1. **Implemente** recursos utilizando o modelo para a região alvo.

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o serviço Azure Relay está disponível na região alvo. Ver [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all) 
 
## <a name="prepare"></a>Preparação
Para começar, exporte um modelo de Gestor de Recursos. Este modelo contém definições que descrevem o seu espaço de nome Azure Relay.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** e, em seguida, selecione o seu espaço de nomeS Azure Relay.
3. Selecione **o modelo de exportação** em **Definições** no menu esquerdo.
4. Escolha **Baixar** na página do **modelo de exportação.**

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Baixar modelo de Gestor de Recursos":::
5. Localize o ficheiro .zip que descarregou do portal e desaperte esse ficheiro para uma pasta à sua escolha. Este ficheiro zip contém os ficheiros JSON do modelo e parâmetros. 
1. Abra a **template.jsno** ficheiro da pasta extraída num editor à sua escolha.
1. Procure, `location` e substitua o valor do imóvel com o novo nome para a região. Para obter códigos de localização, consulte [as localizações do Azure.](https://azure.microsoft.com/global-infrastructure/locations/) O código para uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Remover definições de recursos dinâmicos de **retransmissão wcf** (tipo: `Microsoft.Relay/namespaces/WcfRelays` ). Os relés dinâmicos do WCF são os que têm propriedade **édinâmica** definida para **ser verdadeira** na página **de Relays.** No exemplo seguinte, o **ecoservice** é um relé WCF dinâmico e a sua definição deve ser removida do gabarito. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Relés dinâmicos":::

## <a name="move"></a>Mover
Implemente o modelo para criar um espaço de nome retransmissor na região alvo. 

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
1. Na página do **grupo Recursos,** selecione o espaço de nomes Azure Relay. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Selecione espaço de nomes Azure Relay":::    
1. Na página **Azure Relay namespace,** selecione **Hybrid Connections** ou **WCF Relays** no menu esquerdo para verificar se as ligações híbridas e os relés WCF são criados. Se se esqueceu de apagar definições para retransmissores dinâmicos do WCF antes de importar o modelo, elimine-os na página **de Retransmissores WCF.** Os relés dinâmicos do WCF são criados automaticamente quando os clientes se ligam ao espaço de nomes Relay. 

## <a name="discard-or-clean-up"></a>Eliminar ou limpar
Após a implementação, se quiser recomeçar, pode eliminar o **espaço de nomes target Azure Relay** e repetir os passos descritos nas secções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para cometer as alterações e completar o movimento de um espaço de nome, elimine o **espaço de nomeS Azure Relay** na região de origem. 

Para eliminar um espaço de nome Azure Relay (fonte ou alvo) utilizando o portal Azure:

1. Na janela de pesquisa no topo do portal Azure, **escreva Relés** e selecione **Retransmissores** de **Serviços** nos resultados da pesquisa. Você vê todos os espaços de nomes Azure Relay numa lista.
2. Selecione o espaço de nome alvo para apagar para abrir a página **'Relay'.** 
1. Na página **'Retransmissão',** **selecione Eliminar** a partir da barra de ferramentas. 

    ![Apagar espaço de nome - botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **Delete Namespace,** digite o nome do espaço de nomeS Azure Relay para confirmar a eliminação e, em seguida, selecione **Delete**. 

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, você mudou um espaço de nome Azure Relay de uma região para outra região. Para saber mais sobre a movimentação de recursos entre regiões e recuperação de desastres em Azure, consulte:

- [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
