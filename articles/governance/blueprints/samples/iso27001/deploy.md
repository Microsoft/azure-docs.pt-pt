---
title: Exemplo-especificação técnica ISO 27001 – implantar etapas
description: Implante as etapas do exemplo de plano gráfico ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 661f512fee6f55e53d3f8d5df1150c0c1426687a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802593"
---
# <a name="deploy-the-iso-27001-blueprint-sample"></a>Implantar a amostra do plano gráfico ISO 27001

Para implantar o exemplo de plano gráfico ISO 27001 do Azure BluePrints, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre o exemplo de plano gráfico **ISO 27001** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: Forneça um nome para sua cópia do exemplo de plano gráfico ISO 27001.
   - **Local da definição**: Use as reticências e selecione o grupo de gerenciamento para salvar sua cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **em Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo de plano gráfico pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-lo para fora do padrão ISO 27001.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada do exemplo de plano gráfico ISO 27001". Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - Noções Básicas

     - **Subscrições**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada uma usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere conforme necessário ou deixe como está.
     - **Local**: Selecione uma região na qual a identidade gerenciada deve ser criada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: Escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Atribuição de Bloqueio

     Selecione a configuração de bloqueio Blueprint para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerenciada _atribuída pelo sistema_ padrão.

   - Parâmetros de esquema

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição do Blueprint para fornecer consistência.

     - **Local permitido para recursos e grupos de recursos**: Valor que indica os locais permitidos para grupos de recursos e recursos.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de dimensionamento de VM do Linux (VMSS)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens de VM com suporte do SO Linux para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional:\[\]|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux|Atribuição de política|Espaço de trabalho Log Analytics para VMs Linux|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux|Atribuição de política|Opcional: Lista de imagens de VM com suporte do SO Linux para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional:\[\]|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows|Atribuição de política|Log Analytics espaço de trabalho para VMSS (conjuntos de dimensionamento de VMs do Windows)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows|Atribuição de política|Opcional: Lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional:\[\]|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows|Atribuição de política|Espaço de trabalho Log Analytics para VMs do Windows|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows|Atribuição de política|Opcional: Lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional:\[\]|
|SKUs de contas de armazenamento permitidas|Atribuição de política|Lista de SKUs de armazenamento permitidas|A lista de SKUs que podem ser especificadas para contas de armazenamento.|
|SKUs de máquinas virtuais permitidas|Atribuição de política|Lista de SKUs de máquina virtual permitidas|A lista de SKUs que podem ser especificados para máquinas virtuais.|
|Iniciativa do esquema para ISO 27001|Atribuição de política|Lista de tipos de recursos que devem ter logs de diagnóstico habilitados|Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Azure monitor esquemas de logs de diagnóstico](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|

## <a name="next-steps"></a>Passos Seguintes

Agora que você analisou as etapas para implantar o exemplo de plano gráfico ISO 27001, visite os seguintes artigos para saber mais sobre o mapeamento de arquitetura e controle:

> [!div class="nextstepaction"]
> [Especificação técnica ISO 27001-visão geral](./index.md)
> [ISO 27001 Blueprint-mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
