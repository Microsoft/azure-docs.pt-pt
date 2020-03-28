---
title: Implementar amostra de planta da zona de aterragem da migração da CAF
description: Desloque as etapas para a amostra de planta da zona de aterragem da migração da CAF, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: da17083e8e1b27739288a9d9ac087d071ff4861c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74545454"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Implemente o Quadro de Adoção de Nuvem da Microsoft para a amostra de planta da zona de aterragem migratória azure

Para implantar a amostra de planta da zona de aterragem da migração azure Blueprints CAF, devem ser tomadas as seguintes medidas:

> [!div class="checklist1"]
> - Recomendado para implantar a amostra de plantas [da Fundação CAF](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra da área de aterragem da zona de **migração caf** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _Fundamentos_ da amostra de plantas:
   - **Nome da planta** Forneça um nome para a sua cópia da amostra de planta da zona de aterragem da zona de migração da CAF.
   - **Localização da definição** Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la da orientação da zona de aterragem migratória CAF.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. Na nova página à direita, forneça uma **Versão** para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça notas de **alteração** como "Primeira versão publicada a partir da amostra de planta da zona de aterragem da migração da CAF." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez que a cópia da amostra de plantas tenha sido **publicada**com sucesso, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de plantas única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - Noções básicas
     - **Assinaturas**: Selecione uma ou mais das subscrições que estão no grupo de gestão para a qual guardou a sua cópia da amostra de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada utilização dos parâmetros introduzidos.
     - **Nome de atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude conforme necessário ou saia como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar.
     - O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído.
       Para saber mais, consulte [identidades geridas para os recursos do Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - Versão de **definição**de planta : Escolha uma versão **publicada** da sua cópia da amostra de plantas.
    
   - Atribuição de bloqueio

     Selecione a definição de bloqueio da planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Escolha o _sistema_ predefinido atribuído à opção de identidade gerida ou a opção de identidade atribuída pelo _utilizador._

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição de planta para fornecer consistência.

       - **Organização**: Insira o nome da sua organização, como Contoso ou Fabrikam, deve ser único.
       - **AzureRegion**: Selecione uma região Azure para implantação.
       
   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento leva cerca de cinco minutos. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Implementar zona de aterragem vNET|Modelo do Resource Manager|IPAddress_Space|**Locked** - Forneça dois primeiros plátanos exemplo, 10.0|
|Implementar cofre de chaves|Modelo do Resource Manager|Política de Acesso KV|**Bloqueado** - Id de grupo ou objeto de utilizador para conceder permissões no Cofre chave|
|Implementar log analytics|Modelo do Resource Manager|LogAnalytics_DataRetention|**Bloqueado** - Número de dias os dados serão retidos no Log Analytics|
|Implementar log analytics|Modelo do Resource Manager|LogAnalytics_Location|**Bloqueado** - Região utilizada na criação do espaço de trabalho|
|Implementar migração azure|Modelo do Resource Manager|Azure_Migrate_Location|**Locked** - Selecione a Região para implantar o Azure Migrate|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de planta da zona de aterragem migratória caf, visite os seguintes artigos para saber sobre a arquitetura:

> [!div class="nextstepaction"]
> [Projeto da zona de aterragem da migração da CAF - visão geral](./index.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).