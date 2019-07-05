---
title: Exemplo - oficial do Reino Unido e NHS do Reino Unido esquema - implementar passos
description: Implemente as etapas de exemplos de esquema do Reino Unido NHS e do Reino Unido oficial.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 43aae882f27031d3e51ac8a4f5a68d243a973d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453206"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implementar a oficial do Reino Unido e os exemplos de esquema NHS do Reino Unido

Para implementar a oficial do Reino Unido e os exemplos de esquema NHS do Reino Unido, é necessário os seguintes passos:

> [!div class="checklist"]
> - Criar um novo plano de gráfico do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Em primeiro lugar, implementa o exemplo de esquema ao criar uma novo esquema no seu ambiente com o exemplo de como um starter.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **oficial do Reino Unido** ou **NHS do Reino Unido** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de esquema.
   - **Localização da definição**: Utilize o botão de reticências e selecione o grupo de gestão para guardar a sua cópia do exemplo para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que constituem o exemplo de esquema. Muitos dos artefatos os parâmetros que vamos definir mais tarde. Selecione **Guardar rascunho** quando terminar a rever o exemplo de esquema.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada. A cópia do exemplo de esquema pode ser personalizada para seu ambiente e as necessidades, mas que a modificação pode movê-lo para fora do padrão.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. A nova página à direita, fornecem uma **versão** para obter uma cópia do exemplo de esquema. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicada do exemplo de plano gráfico oficial do Reino Unido ou NHS do Reino Unido." Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois da cópia do exemplo de plano gráfico de ter sido com êxito **publicado**, podem ser atribuído a uma subscrição dentro do grupo de gestão que foram salvos. Este passo é onde os parâmetros são fornecidos para que cada implementação da cópia do exemplo de plano gráfico exclusivo.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione um ou mais das subscrições que estão no grupo de gestão é guardado sua cópia do exemplo de plano gráfico para. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada um usando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome do plano gráfico.
       Altere conforme necessário, ou deixe como está.
     - **Localização**: Selecione uma região para a identidade gerida a ser criado em. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão da definição de esquema**: Escolher uma **publicado** versão da sua cópia do exemplo de esquema.

   - Atribuição de bloqueio

     Selecione o bloqueio de esquema definição para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ geridos a opção de identidade.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para ver uma lista completa ou parâmetros de artefacto e suas descrições, veja [tabela de parâmetros de artefacto](#artifact-parameters-table).

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página. A atribuição do esquema é criada e começa a implementação de artefactos. Implementação demora aproximadamente uma hora. Para verificar o estado da implementação, abra a atribuição do esquema.

> [!WARNING]
> O serviço de esquemas do Azure e os exemplos de esquema incorporados estão **gratuita**. Recursos do Azure estão [o preço por produto](https://azure.microsoft.com/pricing/). Utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para calcular o custo da execução de recursos implementados por este exemplo de esquema.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela seguinte fornece uma lista do plano gráfico de parâmetros de artefacto:

Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Esquema de iniciativa para oficial do Reino Unido ou NHS do Reino Unido|Atribuição de política |Tipos de recursos para os registos de diagnóstico de auditoria (política: Esquema iniciativa para oficial do Reino Unido ou NHS do Reino Unido) |Lista de tipos de recursos para auditar se a definição de registo de diagnóstico é nota ativada.  Para os valores aceitáveis, consulte [suportada serviços, os esquemas e categorias para os registos de diagnóstico do Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|[Preview]\: Implementar o agente de análise de registo para VMs do Linux |Atribuição de política |Opcional: Lista de imagens VM que tenha suporte SO Linux para adicionar ao âmbito (política: [Preview]: Implementar o agente de análise de registo para VMs do Linux) |(Opcional) Valor predefinido é _none_. Para obter mais informações, consulte [criar uma área de trabalho do Log Analytics no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|[Preview]\: Implementar o agente de análise de registo para VMs do Windows |Atribuição de política |Opcional: Lista de imagens VM que têm suporte SO do Windows para adicionar ao âmbito (política: [Preview]: Implementar o agente de análise de registo para VMs do Windows) |(Opcional) Valor predefinido é _none_. Para obter mais informações, consulte [criar uma área de trabalho do Log Analytics no portal do Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu os passos para implementar a oficial do Reino Unido e os exemplos de esquema do Reino Unido NHS, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Reino Unido oficiais e do Reino Unido NHS planos gráficos – descrição geral](./index.md)
> [oficial do Reino Unido e esquemas do Reino Unido NHS - controlar mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
