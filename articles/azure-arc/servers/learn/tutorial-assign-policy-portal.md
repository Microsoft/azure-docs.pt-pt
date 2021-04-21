---
title: Tutorial - Nova atribuição de políticas com portal Azure
description: Neste tutorial, você usa o portal Azure para criar uma atribuição de Política Azure para identificar recursos não conformes.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831659"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Tutorial: Criar uma atribuição política para identificar recursos não conformes

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos. A Azure Policy suporta a auditoria do estado do seu servidor ativado pelo Arco com as políticas de Configuração de Hóspedes. As políticas de configuração do hóspede não aplicam configurações, apenas auditam as definições dentro da máquina. Este tutorial passa pelo processo de criação e atribuição de uma política, identificando quais dos seus servidores ativados pelo Arco não têm o agente Log Analytics instalado.

No final deste processo, irá identificar com sucesso máquinas que não tenham o agente Log Analytics para Windows ou Linux instalados. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste tutorial, cria uma atribuição de política e atribui a _\[ Pré-visualização]: O agente Log Analytics deve ser instalado na definição de política das máquinas Linux Azure Arc._

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Pesquisa de Política em Todos os Serviços" border="false":::

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Selecione página de atribuições a partir da página de visão geral da política" border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Atribuir uma definição de política a partir da página atribuições" border="false":::

1. Na página **Atribuir Política**, selecione **Âmbito** ao clicar no botão de reticências e ao selecionar um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de política será imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Âmbito**.

   Este exemplo utiliza a assinatura **Parnell Aerospace.** A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais. Por isso, deixe-as em branco por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. O Azure Policy já inclui as definições de política incorporadas que pode utilizar. Existem muitas disponíveis, como:

   - Impor etiqueta e o respetivo valor
   - Aplicar etiqueta e o respetivo valor
   - Herdar uma etiqueta do grupo de recursos se faltar

   Para obter uma lista parcial das políticas incorporadas disponíveis, consulte [as amostras da Política Azure](../../../governance/policy/samples/index.md).

1. Procure na lista de definições de política para encontrar a _\[ pré-visualização:: O agente Log Analytics deve ser instalado na definição de máquinas Windows Azure Arc_ se tiver ativado o agente de servidores ativado pelo Arco numa máquina baseada no Windows. Para uma máquina baseada em Linux, encontre a _\[ pré-visualização correspondente]: O agente Log Analytics deve ser instalado na definição de política das máquinas Linux Azure Arc._ Clique nessa política e clique em **Selecionar**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Encontrar a definição de política correta" border="false":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Para este exemplo, deixe _\[ a pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Windows Azure Arc_ ou _\[ Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Linux Azure Arc,_ dependendo da qual selecionou. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** será preenchido automaticamente com base em quem tem sessão iniciada. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _deve_ ser verificada quando a política ou iniciativa inclui uma política com o efeito [implantadoIfNotExists.](../../../governance/policy/concepts/effects.md#deployifnotexists) Como a política usada para este arranque rápido não, deixe-a em branco. Para obter mais informações, veja [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

Está agora pronto para identificar recursos não conformes para entender o estado de conformidade do seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize a **\[ pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Windows Azure Arc** ou **\[ Pré-visualização]: O agente Log Analytics deve ser instalado na sua** atribuição de política de máquinas Linux Azure Arc que criou.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Detalhes de conformidade na página de Conformidade de Política" border="false":::

Se houver recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Quando uma condição é avaliada em relação aos seus recursos existentes e resulta como verdadeira, esses recursos são então marcados como em não conformidade com a política. A tabela seguinte mostra como funcionam os diferentes efeitos de política com a avaliação de condição para o estado de conformidade resultante. Embora não veja a lógica de avaliação no portal Azure, os resultados do estado de conformidade são mostrados. O resultado do estado de conformidade pode ser em conformidade ou em não conformidade.

| **Estado do Recurso** | **Efeito** | **Avaliação da Política** | **Estado de conformidade** |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Em Não Conformidade |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Compliance** (ou **Atribuições)** no lado esquerdo da página Azure Policy e localize a **\[ Pré-visualização]: O agente Log Analytics deve ser instalado nas suas máquinas Windows Azure Arc** ou **\[ Pré-visualização:: O agente Log Analytics deve ser instalado na sua** atribuição de política de máquinas Linux Azure Arc que criou.

1. Clique com o direito na atribuição de política e **selecione Eliminar a atribuição**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Excluir uma atribuição da página Compliance" border="false":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atribuiu uma definição de política a um âmbito e avaliou o seu relatório de conformidade. A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são. Agora está pronto para monitorizar a máquina de servidores ativado pelo Azure Arc, permitindo [insights em VM](../../../azure-monitor/vm/vminsights-overview.md).

Para aprender a monitorizar e ver o desempenho, o processo de execução e as suas dependências da sua máquina, continue ao tutorial:

> [!div class="nextstepaction"]
> [Ativar insights VM](tutorial-enable-vm-insights.md)