---
title: Implementar amostra de planta da zona de aterragem da migração CAF
description: Implementar etapas para a amostra de planta da zona de aterragem da CAF Migration, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 05/06/2020
ms.topic: sample
ms.openlocfilehash: 109c9a2c4c5670d6f5a676498ae1010a4ab0e418
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "82871175"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Implementar o Quadro de Adoção da Microsoft Cloud para o Azure migrar amostra de planta da zona de aterragem

Para implantar a amostra de planta da zona de desembarque da Azure Blueprints CAF, devem ser tomadas as seguintes medidas:

> [!div class="checklist1"]
> - Recomendado para implantar a amostra de planta [da Fundação CAF](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra da **zona de aterragem da CAF Migration** em _outras amostras_ e selecione **Utilize esta amostra.**

1. Introduza os _fundamentos_ da amostra da planta:
   - **Nome da planta** Forneça um nome para a sua cópia da amostra de planta da zona de desembarque de migração caf.
   - **Localização de definição** Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o _separador Artefactos_ na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra de planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. **Selecione Save Draft** quando terminar de rever a amostra de planta.

## <a name="publish-the-sample-copy"></a>Publique a cópia da amostra

A sua cópia da amostra foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser **publicado** antes de poder ser atribuído e implementado. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe da orientação da zona de aterragem migratória caf.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. **Selecione Publicar** a planta no topo da página. Na nova página à direita, forneça uma **versão** para a sua cópia da amostra de planta. Esta propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta da zona de desembarque de migração caf." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez publicada com **sucesso**a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de planta única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. Selecione Atribuir a **planta** no topo da página de definição de planta.

1. Fornecer os valores dos parâmetros para a atribuição do projeto:

   - Noções básicas
     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, será criada uma atribuição para cada um utilizando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude o que for necessário ou saia como for.
     - **Localização**: Selecione uma região para a identidade gerida a criar.
     - O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído.
       Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.
    
   - Atribuição de bloqueio

     Selecione a definição de bloqueio de planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Escolha a opção de identidade gerida por defeito _ou_ a opção de identidade _atribuída ao utilizador._

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são usados por muitos dos artefactos na definição de planta para fornecer consistência.

       - **Organização**: Insira o nome da sua organização como Contoso ou Fabrikam, deve ser único.
       - **AzureRegion**: Selecione uma região de Azure para implantação.
       
   - Parâmetros de artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. A implantação leva cerca de cinco minutos. Para verificar o estado de implantação, abra a atribuição do projeto.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **livres de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de funcionamento dos recursos utilizados por esta amostra de planta.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela a seguir fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Implementar zona de aterragem vNET|Modelo do Resource Manager|IPAddress_Space|**Bloqueado** - Forneça os dois primeiros octetos exemplo, 10.0|
|Implementar cofre de chaves|Modelo do Resource Manager|KV-AccessPolicy|**Bloqueado** - ID de objeto de grupo ou de utilizador para conceder permissões no Cofre de Chaves|
|Implementar Analítica de Registo|Modelo do Resource Manager|LogAnalytics_DataRetention|**Bloqueado** - Número de dias os dados serão retidos no Log Analytics|
|Implementar Analítica de Registo|Modelo do Resource Manager|LogAnalytics_Location|**Bloqueado** - Região utilizada na criação do espaço de trabalho|
|Implementar Azure Migrar|Modelo do Resource Manager|Azure_Migrate_Location|**Bloqueado** - Selecione a Região para implantar Azure Migrate|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implantar a amostra de planta da zona de aterragem migratória da CAF, visite os seguintes artigos para saber sobre a arquitetura:

> [!div class="nextstepaction"]
> [Projeto de zona de desembarque da migração CAF - Visão geral](./index.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
