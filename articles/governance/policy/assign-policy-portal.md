---
title: 'Quickstart: New policy assignment with portal'
description: In this quickstart, you use Azure portal to create an Azure Policy assignment to identify non-compliant resources.
ms.date: 12/06/2018
ms.topic: quickstart
ms.openlocfilehash: 737faafa65afb87640f7f69b02093c058bd77424
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216820"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Quickstart: Create a policy assignment to identify non-compliant resources

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em *não conformidade* com a atribuição de política.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

In this quickstart, you create a policy assignment and assign the *Audit VMs that do not use managed disks* policy definition.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Search for Policy in All Services](./media/assign-policy-portal/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   ![Select Assignments page from Policy Overview page](./media/assign-policy-portal/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Assign a policy definition from Assignments page](./media/assign-policy-portal/select-assign-policy.png)

1. Na página **Atribuir Política**, selecione **Âmbito** ao clicar no botão de reticências e ao selecionar um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Âmbito**.

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais, por isso deixe este campo em branco, por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. O Azure Policy já inclui as definições de política incorporadas que pode utilizar. Existem muitas disponíveis, como:

   - Impor etiqueta e o respetivo valor
   - Aplicar etiqueta e o respetivo valor
   - Requer a Versão do SQL Server 12.0

   For a partial list of available built-in policies, see [Azure Policy samples](./samples/index.md).

1. Pesquise nas definições de política para encontrar a definição *Auditar VMs que não utilizam discos geridos*. Clique nessa política e clique em **Selecionar**.

   ![Encontrar a definição de política correta](./media/assign-policy-portal/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Neste exemplo, vamos deixar *Auditar VMs que não utilizam discos geridos*. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** será preenchido automaticamente com base em quem tem sessão iniciada. Este campo é opcional,por isso pode introduzir valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. This box _must_ be checked when the policy or initiative includes a policy with the [deployIfNotExists](./concepts/effects.md#deployifnotexists) effect. As the policy used for this quickstart doesn't, leave it blank. Para obter mais informações, consulte [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](./how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

You're now ready to identify non-compliant resources to understand the compliance state of your environment.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Select **Compliance** in the left side of the page. Then locate the **Audit VMs that do not use managed disks** policy assignment you created.

![Compliance details on the Policy Compliance page](./media/assign-policy-portal/policy-compliance.png)

If there are any existing resources that aren't compliant with this new assignment, they appear under **Non-compliant resources**.

Quando uma condição é avaliada em relação aos seus recursos existentes e resulta como verdadeira, esses recursos são então marcados como em não conformidade com a política. A tabela seguinte mostra como funcionam os diferentes efeitos de política com a avaliação de condição para o estado de conformidade resultante. Although you don't see the evaluation logic in the Azure portal, the compliance state results are shown. O resultado do estado de conformidade pode ser em conformidade ou em não conformidade.

| **Estado do Recurso** | **Efeito** | **Avaliação da Política** | **Estado de Compatibilidade** |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Novo | Audit, AuditIfNotExist\* | Falso | Em conformidade |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

## <a name="clean-up-resources"></a>Limpar recursos

To remove the assignment created, follow these steps:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Clique com o botão direito do rato na atribuição de política **Auditar VMs que não utilizam discos geridos** e selecione **Eliminar atribuição**

   ![Delete an assignment from the Compliance page](./media/assign-policy-portal/delete-assignment.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, atribuiu uma definição de política a um âmbito e avaliou o respetivo relatório de conformidade.
The policy definition validates that all the resources in the scope are compliant and identifies which ones aren't.

To learn more about assigning policies to validate that new resources are compliant, continue to the tutorial for:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)