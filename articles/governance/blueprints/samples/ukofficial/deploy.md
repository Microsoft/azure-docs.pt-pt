---
title: Implante os exemplos do NHS Blueprint do Reino Unido & Reino Unido
description: Implante as etapas para os exemplos do Blueprint oficial do Reino Unido e do Reino Unido NHS, incluindo detalhes do parâmetro de artefato do Blueprint.
ms.date: 06/26/2019
ms.topic: sample
ms.openlocfilehash: 3d69f4477163618aa225150ac6352a16e901f826
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546403"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implantar os exemplos do Blueprint oficial do Reino Unido e do Reino Unido NHS

Para implantar os exemplos de plano gráfico do Reino Unido e do Reino Unido NHS, as seguintes etapas devem ser executadas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre o exemplo **oficial do Reino Unido** ou do **Reino Unido do NHS** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: forneça um nome para a sua cópia do exemplo Blueprint.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-la para fora do padrão.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada do exemplo do Reino Unido ou do RU NHS Blueprint". Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - Noções básicas

     - **Assinaturas**: selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada uma usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere conforme necessário ou deixe como está.
     - **Local**: selecione uma região na qual a identidade gerenciada deve ser criada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Atribuição de bloqueio

     Selecione a configuração de bloqueio Blueprint para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerenciada _atribuída pelo sistema_ padrão.

   - Parâmetros de artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Iniciativa Blueprint para o Reino Unido ou o NHS UK|Atribuição de política |Tipos de recursos para auditar logs de diagnóstico (política: iniciativa Blueprint para o Reino Unido ou NHS do Reino Unido) |Lista de tipos de recursos para auditoria se a configuração do log de diagnóstico está habilitada.  Para obter valores aceitáveis, consulte [serviços, esquemas e categorias com suporte para logs de diagnóstico do Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\]de visualização de \[: implantar o agente de Log Analytics para VMs Linux |Atribuição de política |Opcional: lista de imagens de VM com suporte para SO Linux para adicionar ao escopo (política: \[visualização\]: implantar o agente de Log Analytics para VMs Linux) |Adicional O valor padrão é _None_. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\]de visualização do \[: implantar o agente de Log Analytics para VMs do Windows |Atribuição de política |Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo (política: \[visualização\]: implantar o agente de Log Analytics para VMs do Windows) |Adicional O valor padrão é _None_. Para obter mais informações, consulte [criar um log Analytics espaço de trabalho no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Passos Seguintes

Agora que você analisou as etapas para implantar os exemplos do plano gráfico do Reino Unido e do Reino Unido NHS, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Blueprints do Reino Unido e do Reino Unido NHS-visão geral](./index.md)
> diagramas do [Reino Unido e do Reino Unido NHS – mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).