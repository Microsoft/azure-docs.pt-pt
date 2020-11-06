---
title: Implementar amostras de plantas do NHS do Reino Unido & do Reino Unido
description: Implementar etapas para as amostras de plantas do NHS do Reino Unido e do Reino Unido, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: 14e832397c21f183c94720bc71f06530d9caaf14
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420330"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implementar as amostras de plantas do NHS oficial do Reino Unido e do Reino Unido

Para a colocação das amostras de projeto oficial do Reino Unido e do Nhs do Reino Unido, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta oficial do **Reino Unido** ou do NHS do Reino **Unido** em _outras amostras_ e selecione **Utilize esta amostra.**

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta** : Forneça um nome para a sua cópia da amostra de planta.
   - **Localização de definição** : Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ , na parte superior da página, ou **Seguinte: Artefactos** , na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do padrão.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema** , na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de mudança** como "Primeira versão publicada a partir da amostra de plantas do UK OFFICIAL ou UK NHS.". Em seguida, selecione **Publicar** , na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema** , na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições** : Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição** : O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização** : Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta** : Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir** , na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Iniciativa de blueprint para UK OFFICIAL ou UK NHS|Atribuição de políticas |Tipos de recursos para auditar registos de diagnóstico (Policy: Blueprint initiative for UK OFFICIAL ou UK NHS) |Lista de tipos de recursos para auditar se a definição de registo de diagnóstico estiver ativada.  Para obter valores aceitáveis, consulte [serviços, esquemas e categorias de Registos de Diagnóstico Azure](../../../../azure-monitor/platform/resource-logs-schema.md). |
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux |Atribuição de políticas |Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito (Política: \[ Pré-visualização \] : Implementar Agente de Análise de Registo para VMs Linux) |(Opcional) O valor predefinido não é _nenhum._ Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows |Atribuição de políticas |Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito (Política: \[ Pré-visualização \] : Implementar Agente de Análise de Registo para VMs do Windows) |(Opcional) O valor predefinido não é _nenhum._ Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar as amostras de plantas do NHS do Reino Unido e do Reino Unido, visite os seguintes artigos para saber mais sobre a visão geral e controlar o mapeamento:

> [!div class="nextstepaction"]
> [Plantas do NHS oficial do Reino Unido e do Reino Unido - Visão geral](./index.md) 
>  [Plantas do NHS oficial do Reino Unido e do Reino Unido - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
