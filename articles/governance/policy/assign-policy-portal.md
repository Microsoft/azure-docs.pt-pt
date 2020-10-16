---
title: 'Quickstart: Nova atribuição de políticas com portal'
description: Neste quickstart, você usa o portal Azure para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 10/05/2020
ms.topic: quickstart
ms.openlocfilehash: 51ca2f9e5d3f3df9304804ba3da2c5c5ceb0c19b
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875313"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de política e atribui os _VMs de auditoria que não utilizam_ a definição de política de discos geridos.

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

1. Na página **'Política de Atribuição',** desapedace o **Scope** selecionando a elipse e selecionando um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de política será imposta. Em seguida, utilize o botão **Selecione** na parte inferior da página **'Âmbito'.**

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais. Por isso, deixe-as em branco por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. O Azure Policy já inclui as definições de política incorporadas que pode utilizar. Existem muitas disponíveis, como:

   - Impor etiqueta e o respetivo valor
   - Aplicar etiqueta e o respetivo valor
   - Herdar uma etiqueta do grupo de recursos se faltar

   Para obter uma lista parcial das políticas incorporadas disponíveis, consulte [as amostras da Política Azure](./samples/index.md).

1. Pesquise nas definições de política para encontrar a definição _Auditar VMs que não utilizam discos geridos_. Selecione essa política e, em seguida, utilize o botão **Select.**

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Neste exemplo, vamos deixar _Auditar VMs que não utilizam discos geridos_. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** será preenchido automaticamente com base em quem tem sessão iniciada. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _deve_ ser verificada quando a política ou iniciativa inclui uma política com o [deployIfNotExists](./concepts/effects.md#deployifnotexists) ou [modificar](./concepts/effects.md#modify) o efeito. Como a política usada para este arranque rápido não, deixe-a em branco. Para obter mais informações, veja [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](./how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione **Atribuir**.

Está agora pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize os _VMs de auditoria que não utilizem_ a atribuição de política de discos geridos que criou.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

Se houver recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Quando uma condição é avaliada em relação aos seus recursos existentes e resulta como verdadeira, esses recursos são então marcados como em não conformidade com a política. A tabela seguinte mostra como funcionam os diferentes efeitos de política com a avaliação de condição para o estado de conformidade resultante. Embora não veja a lógica de avaliação no portal Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser em conformidade ou em não conformidade.

| Estado do Recurso | Efeito | Avaliação da Política | Estado de Compatibilidade |
| --- | --- | --- | --- |
| Nova ou Atualizada | Auditoria, Modificação, AuditoriaIfNotExist | Verdadeiro | Em Não Conformidade |
| Nova ou Atualizada | Auditoria, Modificação, AuditoriaIfNotExist | Falso | Compatível |
| Existe | Negar, Auditar, Append, Modificar, ImplementarIfNotExist, AuditIfNotExist | Verdadeiro | Em Não Conformidade |
| Existe | Negar, Auditar, Append, Modificar, ImplementarIfNotExist, AuditIfNotExist | Falso | Compatível |

> [!NOTE]
> Os efeitos DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja VERDADEIRA e a condição de existência seja FALSA para não ser conforme. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política _Auditar VMs que não utilizam discos geridos_ que criou.

1. Clique com o direito nos _VMs de auditoria que não utilizam_ a atribuição de política de discos geridos e selecione a atribuição de **Eliminar**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Screenshot de pesquisa de Política em Todos os Serviços." border="false":::

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, atribuiu uma definição de política a um âmbito e avaliou o respetivo relatório de conformidade.
A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)