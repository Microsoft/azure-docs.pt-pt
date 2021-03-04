---
title: A bordo de um grupo de gestão para o Centro de Segurança Azure
description: Saiba como utilizar uma definição de Política Azure fornecida para permitir ao Azure Security Center todas as subscrições de um grupo de gestão.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 9db276425d510316c41126f8e942a1e2a362ea35
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101295"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Ativar o Centro de Segurança em todas as subscrições de um grupo de gestão

Pode utilizar a Azure Policy para ativar o Azure Security Center em todas as subscrições do Azure dentro do mesmo grupo de gestão (MG). Isto é mais conveniente do que aceder-lhes individualmente a partir do portal, e funciona mesmo que as subscrições pertençam a diferentes proprietários. 

Para bordo de um grupo de gestão e todas as suas subscrições:

1. Como utilizador com permissões **de Administração** de Segurança, abra a Política Azure e procure a definição **Enable Azure Security Center na sua subscrição**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="A definição de Política Azure Enable Azure Security Center na sua subscrição":::

1. Selecione **Atribua** e certifique-se de que define o âmbito para o nível MG.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Atribuir a definição Enable Azure Security Center na sua subscrição":::

    > [!TIP]
    > Além do âmbito, não existem parâmetros necessários.

1. **Selecione Criar uma tarefa de remediação** para garantir que todas as subscrições existentes que não tenham o Centro de Segurança ativado, serão a bordo.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Criar uma tarefa de remediação para a definição de Política Azure Enable Azure Security Center na sua subscrição":::

1. Quando a definição for atribuída, será:

    1. Detete todas as subscrições no MG que ainda não estão registadas no Centro de Segurança.
    1. Marque estas assinaturas como "incompatíveis".
    1. Marque como "conforme" todas as subscrições registadas (independentemente de terem o Azure Defender ligado ou desligado).

    A tarefa de remediação permitirá então ao Centro de Segurança, gratuitamente, as subscrições não conformes.

> [!IMPORTANT]
> A definição de política só permitirá ao Centro de Segurança as subscrições **existentes.** Para registar subscrições recém-criadas, abra o separador de conformidade, selecione as subscrições não conformes relevantes e crie uma tarefa de remediação. Repita este passo quando tiver uma ou mais novas subscrições que pretende monitorizar com o Centro de Segurança.

## <a name="optional-modifications"></a>Modificações opcionais

Existem várias formas de escolher modificar a definição da Política Azure: 

- **Definir a conformidade de forma diferente** - A política fornecida classifica todas as subscrições no MG que ainda não estão registadas no Centro de Segurança como "incompatíveis". Pode optar por defini-lo para todas as subscrições sem o Azure Defender.

    A definição fornecida define *qualquer uma* das definições de "preços" abaixo conforme. O que significa que uma subscrição definida como 'standard' ou 'free' está em conformidade.

    > [!TIP]
    > Quando um plano Azure Defender está ativado, é descrito numa definição de política como estando na definição 'Standard'. Quando está desativado, é "Grátis". Para conhecer as diferenças entre estes planos, consulte o [Security Center free vs Azure Defender ativado](security-center-pricing.md). 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Se o alterar para o seguinte, apenas as subscrições definidas como 'standard' serão classificadas como conformes:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Defina alguns planos do Azure Defender para aplicar ao permitir o Centro de Segurança** - A política fornecida permite ao Centro de Segurança sem qualquer um dos planos opcionais do Azure Defender. Pode escolher ativar um ou mais deles.

    A secção de definição fornecida `deployment` tem um `pricingTier` parâmetro. Por padrão, isto está definido para `free` , mas você pode modificá-lo. 


## <a name="next-steps"></a>Passos seguintes:

Agora que insaltou um grupo de gestão inteiro, permita as proteções avançadas do Azure Defender. 

> [!div class="nextstepaction"]
> [Ativar o Azure Defender](enable-azure-defender.md)
