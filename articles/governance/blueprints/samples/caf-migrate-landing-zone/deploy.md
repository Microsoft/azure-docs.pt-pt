---
title: Implementar amostra de planta da zona de aterragem da migração CAF
description: Implementar etapas para a amostra de planta da zona de aterragem da CAF Migration, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: d5aed1d492fea102598a22921d04ed8681c727cb
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470120"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Implementar o Quadro de Adoção da Microsoft Cloud para o Azure migrar amostra de planta da zona de aterragem

Para implantar a amostra de planta da zona de desembarque da Azure Blueprints CAF, devem ser tomadas as seguintes medidas:

> [!div class="checklist1"]
> - Recomendado para implantar a amostra de planta [da Fundação CAF](../caf-foundation/index.md)

> [!div class="checklist2"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Localize o esquema de exemplo **Zona de destino de Migração do CAF** em _Outros Exemplos_ e selecione **Utilizar este exemplo**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:
   - **Nome da planta** Forneça um nome para a sua cópia da amostra de planta da zona de desembarque de migração caf.
   - **Localização de definição** Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia do esquema de exemplo pode ser personalizada de acordo com o seu ambiente e as suas necessidades, mas essa modificação pode afastá-la da orientação da zona de destino de Migração do CAF.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta da zona de desembarque de migração caf." Em seguida, selecione **Publicar**, na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas
     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar.
     - O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído.
       Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.
    
   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Escolha a opção de identidade gerida _atribuída pelo sistema_ predefinida ou a opção de identidade _atribuída pelo utilizador_.

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição do esquema, para proporcionar consistência.

       - **Organização**: Insira o nome da sua organização como Contoso ou Fabrikam, deve ser único.
       - **AzureRegion**: Selecione uma região de Azure para implantação.
       
   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implementação demora cerca de cinco minutos. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Implementar zona de aterragem vNET|Modelo do Resource Manager|IPAddress_Space|**Bloqueado** - indique os dois primeiros octetos; exemplo: 10,0|
|Implementar o Key Vault|Modelo do Resource Manager|KV-AccessPolicy|**Bloqueado** - ID de objeto de grupo ou de utilizador para conceder permissões no Cofre de Chaves|
|Implementar o Log Analytics|Modelo do Resource Manager|LogAnalytics_DataRetention|**Bloqueado** - Número de dias os dados serão retidos no Log Analytics|
|Implementar o Log Analytics|Modelo do Resource Manager|LogAnalytics_Location|**Bloqueado** - região utilizada ao estabelecer a área de trabalho|
|Implementar o Azure Migrate|Modelo do Resource Manager|Azure_Migrate_Location|**Bloqueado** - Selecione a Região para implantar Azure Migrate|

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
