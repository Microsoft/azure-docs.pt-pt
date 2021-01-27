---
title: Amostra de projeto benchmark da CIS Microsoft Azure Foundations
description: Descrição geral do exemplo de esquema CIS Microsoft Azure Foundations Benchmark. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: fc9e2443cd1e413588487646d6400ae1f2185208
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918421"
---
# <a name="cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Amostra de projeto benchmark da CIS Microsoft Azure Foundations

O exemplo de esquema CIS Microsoft Azure Foundations Benchmark oferece proteções de governação através do [Azure Policy](../../policy/overview.md) para o ajudar a avaliar recomendações específicas do CIS Microsoft Azure Foundations Benchmark. Este esquema ajuda os clientes a implementar um conjunto essencial de políticas para qualquer arquitetura implementada no Azure que tenha de implementar recomendações do CIS Microsoft Azure Foundations Benchmark.

## <a name="recommendation-mapping"></a>Mapeamento das recomendações

O [mapeamento da recomendação da Política Azure](../../policy/samples/cis-azure-1-1-0.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **domínios** de conformidade e **controlos** no CIS Microsoft Azure Foundations Benchmark v1.1.0. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implementar a amostra de projeto benchmark do Azure Blueprints CIS Microsoft Azure Foundations, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra **de benchmark v1.1.0 das Fundações CIS Microsoft Azure** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto benchmark da CIS Microsoft Azure Foundations.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos incluídos na amostra de planta. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com as recomendações de Benchmark da CIS Microsoft Azure Foundations Benchmark.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de projeto benchmark da CIS Microsoft Azure Foundations." Em seguida, selecione **Publicar**, na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Auditação CIS Microsoft Azure Foundations Benchmark 1.1.0 recomendações e implementar extensões VM de suporte específicas|Atribuição de políticas|Lista de regiões onde o Observador de Rede deve ser ativado|Uma lista de regiões separadas por pontos de separá-se. Para ver uma lista completa de regiões use Get-AzLocation. Ex: Eastus; eastus2|
|Auditação CIS Microsoft Azure Foundations Benchmark 1.1.0 recomendações e implementar extensões VM de suporte específicas|Atribuição de políticas|Lista de extensões de máquinas virtuais que são aprovadas para utilização|Uma lista de extensões separadas por pontos de separá-se. Para ver uma lista completa de extensões de máquinas virtuais, utilize a Get-AzVMExtensionImage. Ex: AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Próximos passos

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).