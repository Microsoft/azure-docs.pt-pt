---
title: Configurar configurações de diagnóstico de abóbada em escala
description: Configurar definições de diagnóstico de log analytics para todos os cofres num determinado âmbito utilizando a Política Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92171531"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Configurar configurações de diagnóstico de abóbada em escala

A solução de reporte fornecida pela Azure Backup aproveita o Log Analytics (LA). Para que os dados de um dado cofre sejam enviados para LA, é necessário criar uma [definição](./backup-azure-diagnostic-events.md) de diagnóstico para o cofre.

Muitas vezes, adicionar uma definição de diagnóstico manualmente por abóbada pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado também precisa de ter configurações de diagnóstico ativadas para poder ver relatórios para este cofre.

Para simplificar a criação de configurações de diagnóstico em escala (com LA como destino), o Azure Backup fornece uma [Política Azure](../governance/policy/index.yml)incorporada. Esta política adiciona uma definição de diagnóstico de LA a todos os cofres de um determinado grupo de subscrição ou recursos. As seguintes secções fornecem instruções sobre como utilizar esta política.

## <a name="supported-scenarios"></a>Cenários Suportados

* A política pode ser aplicada de uma só vez a todos os cofres dos Serviços de Recuperação numa determinada subscrição (ou a um grupo de recursos dentro da subscrição). O utilizador que atribui a apólice precisa de ter o acesso **do Proprietário** à subscrição à qual a apólice é atribuída.

* O espaço de trabalho de LA, conforme especificado pelo utilizador (para o qual os dados de diagnóstico serão enviados) pode estar numa subscrição diferente dos cofres aos quais a política é atribuída. O utilizador precisa de **ter** o leitor, **o contribuinte,** ou o **proprietário** acesso à subscrição na qual existe o espaço de trabalho de LA especificado.

* O âmbito do Grupo de Gestão não é atualmente suportado.

* A política incorporada não está atualmente disponível nas nuvens nacionais.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Atribuindo a política incorporada a um âmbito

Para atribuir a política para cofres no âmbito exigido, siga os passos abaixo:

1. Inscreva-se no portal Azure e navegue para o **Painel de Política.**
2. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
3. Filtrar a lista para **categoria=backup**. Localizar a política denominada **[Pré-visualização]: Implementar definições de diagnóstico para cofre de serviços de recuperação para registar espaço de trabalho para categorias específicas de recursos**.

    ![Painel de definição de política](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Selecione o nome da apólice. Será redirecionado para a definição detalhada para esta apólice.

    ![Definição detalhada da política](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Selecione o botão **Atribuir** na parte superior do painel. Isto redireciona-o para o painel **de Política de Atribuição.**

6. Em **Basics**, selecione os três pontos junto ao campo **Scope.** Isto abre um painel de contexto certo onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, de modo a que a política seja aplicada apenas para cofres num determinado grupo de recursos.

    ![Básicos de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Nos parâmetros , introduza as **seguintes** informações:

    * **Nome do Perfil** - O nome que será atribuído às definições de diagnóstico criadas pela política.
    * **Log Analytics Workspace** - O espaço de trabalho do Log Analytics ao qual deve ser associada a definição de diagnóstico. Os dados de diagnóstico de todos os cofres no âmbito da atribuição de política serão empurrados para o espaço de trabalho especificado de LA.

    * **Nome da etiqueta de exclusão (opcional) e valor da etiqueta de exclusão (opcional)** - Pode optar por excluir os cofres que contenham um determinado nome e valor da atribuição de políticas. Por exemplo, se **não** quiser adicionar uma definição de diagnóstico aos cofres que tenham uma etiqueta 'isTest' definida para o valor 'sim', deve introduzir 'isTest' no campo **'Nome de Identificação de Exclusão'** e 'sim' no campo **'Valor de Etiqueta de Exclusão'.** Se algum (ou ambos) destes dois campos ficarem vazios, a política será aplicada a todos os cofres relevantes, independentemente das etiquetas que contenham.

    ![Parâmetros de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Criar uma tarefa de reparação** - Uma vez que a política é atribuída a um âmbito, quaisquer novos cofres criados nesse âmbito automaticamente configuram as definições de diagnóstico de LA (no prazo de 30 minutos a partir do momento da criação do cofre). Para adicionar uma definição de diagnóstico aos cofres existentes no âmbito, pode desencadear uma tarefa de reparação no tempo de atribuição de políticas. Para desencadear uma tarefa de remediação, selecione a caixa de **verificação Criar uma tarefa de remediação**.

    ![Remediação de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navegue no **separador 'Rever+Criar'** e selecione **Criar.**

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Em que condições a tarefa de reparação se aplicará a um cofre?

A tarefa de reparação é aplicada a cofres que não estão em conformidade de acordo com a definição da política. Um cofre não é conforme se satisfizer uma das seguintes condições:

* Não há definição de diagnóstico para o cofre.
* As definições de diagnóstico estão presentes para o cofre, mas nenhuma das definições tem **todos os** eventos específicos de recursos ativados com LA como destino, e recursos **específicos** selecionados no toggle.

Assim, mesmo que um utilizador tenha um cofre com o evento AzureBackupReport ativado no modo AzureDiagnostics (que é suportado por Relatórios de Cópia de Segurança), a tarefa de remediação continuará a aplicar-se a este cofre, uma vez que o modo específico de Recursos é a forma recomendada de criar definições de diagnóstico, [avançando.](./backup-azure-diagnostic-events.md#legacy-event)

Além disso, se um utilizador tiver um cofre com apenas um subconjunto dos seis eventos específicos de recursos ativados, a tarefa de remediação será aplicada a este cofre, uma vez que os Relatórios de Backup funcionarão como esperado apenas se todos os seis eventos específicos de recursos estiverem ativados.

> [!NOTE]
>
> Se um cofre tiver uma definição de diagnóstico existente com um **subconjunto de** categorias específicas de recursos habilitadas, configuradas para enviar dados para um determinado espaço de trabalho de LA, diga 'Workspace X', então a tarefa de remediação falhará (apenas para esse cofre) se o espaço de trabalho la de destino fornecido na atribuição de Política for o **mesmo** 'Workspace X'.
>
>Isto porque, se os eventos ativados por duas configurações de diagnóstico diferentes no mesmo recurso **se sobrepõem** de alguma forma, então as definições não podem ter o mesmo espaço de trabalho de LA que o destino. Você terá que resolver manualmente esta falha, navegando para o cofre relevante e configurando um ajuste de diagnóstico com um espaço de trabalho de LA diferente como o destino.
>
> Note que a tarefa de remediação **não** falhará se a definição de diagnóstico existente como apenas AzureBackupReport ativada com o Workspace X como destino, uma vez que neste caso, não haverá sobreposição entre os eventos ativados pela configuração existente e os eventos habilitados pela configuração criada pela tarefa de remediação.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como usar relatórios de backup](./configure-reports.md)
* [Saiba mais sobre a Política Azure](../governance/policy/index.yml)
* [Use a Política Azure para ativar automaticamente a cópia de segurança para todos os VMs num âmbito de oferta](./backup-azure-auto-enable-backup.md)
