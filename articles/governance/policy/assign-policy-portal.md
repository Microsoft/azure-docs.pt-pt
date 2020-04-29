---
title: 'Quickstart: Nova atribuição de política com portal'
description: Neste arranque rápido, você usa o portal Azure para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80240012"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Quickstart: Criar uma atribuição de políticas para identificar recursos não conformes

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de políticas e atribui os VMs de auditoria que não utilizam a definição de política de _disquetes geridos._

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Pesquisa de Política em Todos os Serviços" border="false":::

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Selecione página de Atribuições a partir da página de visão geral da política" border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Atribuir uma definição de política a partir da página de Atribuições" border="false":::

1. Na página **Atribuir Política**, selecione **Âmbito** ao clicar no botão de reticências e ao selecionar um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Âmbito**.

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais. Por isso, deixe-as em branco por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. O Azure Policy já inclui as definições de política incorporadas que pode utilizar. Existem muitas disponíveis, como:

   - Impor etiqueta e o respetivo valor
   - Aplicar etiqueta e o respetivo valor
   - Herde uma etiqueta do grupo de recursos se faltar

   Para obter uma lista parcial das políticas disponíveis em butinsas, consulte [as amostras da Política Azure.](./samples/index.md)

1. Pesquise nas definições de política para encontrar a definição _Auditar VMs que não utilizam discos geridos_. Clique nessa política e clique em **Selecionar**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Encontrar a definição de política correta" border="false":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Neste exemplo, vamos deixar _Auditar VMs que não utilizam discos geridos_. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** será preenchido automaticamente com base em quem tem sessão iniciada. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _deve_ ser verificada quando a política ou iniciativa inclui uma política com o efeito [deployIfNotExists.](./concepts/effects.md#deployifnotexists) Como a política usada para este arranque rápido não, deixe-a em branco. Para obter mais informações, veja [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](./how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

Está pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Selecione **Compliance** no lado esquerdo da página. Em seguida, localize os **VMs** de auditoria que não utilizam a atribuição de políticas de discos geridos que criou.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Detalhes de conformidade na página de Conformidade política" border="false":::

Se existem recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Quando uma condição é avaliada em relação aos seus recursos existentes e resulta como verdadeira, esses recursos são então marcados como em não conformidade com a política. A tabela seguinte mostra como funcionam os diferentes efeitos de política com a avaliação de condição para o estado de conformidade resultante. Embora não veja a lógica de avaliação no portal Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser em conformidade ou em não conformidade.

| **Estado do Recurso** | **Efeito** | **Avaliação da Política** | **Estado de Compatibilidade** |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Clique direito nos **VMs de auditoria que não utilizam** a atribuição da política de discos geridos e selecione eliminar a **atribuição**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Eliminar uma atribuição da página compliance" border="false":::

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, atribuiu uma definição de política a um âmbito e avaliou o respetivo relatório de conformidade.
A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)