---
title: 'Início rápido: nova atribuição de política com o portal'
description: Neste guia de início rápido, você usa portal do Azure para criar uma atribuição de Azure Policy para identificar recursos sem conformidade.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: ac876b546ea3bda7541db61e0ab68842a3845541
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482319"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Início rápido: criar uma atribuição de política para identificar recursos sem conformidade

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui a definição de política de _VMs de auditoria que não usam discos gerenciados_ .

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Pesquisar política em todos os serviços](./media/assign-policy-portal/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   ![Página Selecionar atribuições da página Visão geral da política](./media/assign-policy-portal/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Atribuir uma definição de política da página atribuições](./media/assign-policy-portal/select-assign-policy.png)

1. Na página **Atribuir Política**, selecione **Âmbito** ao clicar no botão de reticências e ao selecionar um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Âmbito**.

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais, por isso deixe este campo em branco, por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. O Azure Policy já inclui as definições de política incorporadas que pode utilizar. Existem muitas disponíveis, como:

   - Impor etiqueta e o respetivo valor
   - Aplicar etiqueta e o respetivo valor
   - Requer a Versão do SQL Server 12.0

   Para obter uma lista parcial de políticas internas disponíveis, consulte [exemplos de Azure Policy](./samples/index.md).

1. Pesquise nas definições de política para encontrar a definição _Auditar VMs que não utilizam discos geridos_. Clique nessa política e clique em **Selecionar**.

   ![Encontrar a definição de política correta](./media/assign-policy-portal/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Neste exemplo, vamos deixar _Auditar VMs que não utilizam discos geridos_. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** será preenchido automaticamente com base em quem tem sessão iniciada. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Essa caixa _deve_ ser verificada quando a política ou iniciativa inclui uma política com o efeito [deployIfNotExists](./concepts/effects.md#deployifnotexists) . Como não a política utilizada neste início rápido, deixe em branco. Para obter mais informações, veja [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](./how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

Agora você está pronto para identificar recursos sem conformidade para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Selecione **conformidade** no lado esquerdo da página. Em seguida, localize as **VMs de auditoria que não usam a atribuição de política de discos gerenciados** que você criou.

![Detalhes de conformidade na página conformidade da política](./media/assign-policy-portal/policy-compliance.png)

Se houver algum recurso existente que não esteja em conformidade com essa nova atribuição, eles aparecerão em **recursos sem conformidade**.

Quando uma condição é avaliada em relação aos seus recursos existentes e resulta como verdadeira, esses recursos são então marcados como em não conformidade com a política. A tabela seguinte mostra como funcionam os diferentes efeitos de política com a avaliação de condição para o estado de conformidade resultante. Embora você não veja a lógica de avaliação no portal do Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser em conformidade ou em não conformidade.

| **Estado do Recurso** | **Efeito** | **Avaliação da Política** | **Estado de Compatibilidade** |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição de criado, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Clique com o botão direito do mouse na atribuição **auditar VMs que não usam a política de discos gerenciados** e selecione **excluir atribuição**.

   ![Excluir uma atribuição da página conformidade](./media/assign-policy-portal/delete-assignment.png)

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, atribuiu uma definição de política a um âmbito e avaliou o respetivo relatório de conformidade.
A definição de política valida que todos os recursos no âmbito estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)