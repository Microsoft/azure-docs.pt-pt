---
title: Configurar as definições de diagnóstico do cofre em escala
description: Configure configurar definições de diagnóstico de log analytics para todos os cofres num dado âmbito usando a Política Azure
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584511"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Configurar as definições de diagnóstico do cofre em escala

A solução de reporte fornecida pela Azure Backup alavanca o Log Analytics (LA). Para que os dados de qualquer cofre seja enviado para LA, é necessário criar um [diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events) para aquele cofre.

Muitas vezes, adicionar uma definição de diagnóstico manualmente por cofre pode ser uma tarefa complicada. Além disso, qualquer novo cofre criado também precisa de ter configurações de diagnóstico habilitadas para poder ver relatórios para este cofre. 

Para simplificar a criação de definições de diagnóstico à escala (com LA como destino), o Azure Backup fornece uma [Política Azure](https://docs.microsoft.com/azure/governance/policy/)incorporada. Esta política adiciona uma definição de diagnóstico de LA a todos os cofres de uma determinada subscrição ou grupo de recursos. As seguintes secções fornecem instruções sobre como utilizar esta política.

## <a name="supported-scenarios"></a>Cenários Suportados

* A política pode ser aplicada de uma só vez a todos os cofres dos Serviços de Recuperação numa determinada subscrição (ou a um grupo de recursos dentro da subscrição). O utilizador que atribui a apólice tem de ter acesso ao 'Proprietário' à subscrição a que a apólice é atribuída.

* O Espaço de Trabalho de LA, tal como especificado pelo utilizador (para o qual serão enviados dados de diagnóstico) pode estar numa subscrição diferente dos cofres a que a apólice é atribuída. O utilizador precisa de ter acesso 'Reader', 'Contributor' ou 'Owner' à subscrição em que existe o espaço de trabalho especificado em LA.

* O âmbito do Grupo de Gestão não é atualmente suportado.

* A política incorporada não está atualmente disponível nas nuvens nacionais.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Atribuir a política incorporada a um âmbito

Para atribuir a política de cofres no âmbito necessário, siga os passos abaixo:

1. Inscreva-se no portal Azure e navegue para o Painel **político.**
2. Selecione **Definições** no menu esquerdo para obter uma lista de todas as políticas incorporadas em todos os Recursos Azure.
3. Filtre a lista para **Categoria=Monitorização**. Localize a política denominada **[Pré-visualização]: Implemente as Definições de Diagnóstico para o Cofre de Serviços de Recuperação para registar o espaço de trabalho do Analytics para categorias específicas**de recursos .

![Lâmina de Definição de Política](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Clique no nome da apólice. Será redirecionado para a definição detalhada desta política.

![Definição política detalhada](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Clique no botão **Atribuir** na parte superior da lâmina. Isto redireciona-o para a lâmina política de **atribuição.**

6. Em **Basics,** clique nos três pontos junto ao campo **Scope.** Isto abre uma lâmina de contexto certa onde pode selecionar a subscrição para a política a aplicar. Também pode selecionar opcionalmente um grupo de recursos, para que a política seja aplicada apenas para cofres de um determinado grupo de recursos.

![Fundamentos de Atribuição de Políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Em **Parâmetros,** introduza as seguintes informações:

* **Nome** do Perfil - O nome que será atribuído às definições de diagnóstico criadas pela apólice.
* **Log Analytics Workspace** - O espaço de trabalho de log analytics ao qual a definição de diagnóstico deve ser associada. Os dados de diagnóstico de todos os cofres no âmbito da atribuição de políticas serão empurrados para o espaço de trabalho especificado de LA.

* **Exclusão Nome de etiqueta (opcional) e Valor da Etiqueta de Exclusão (opcional)** - Pode optar por excluir cofres que contenham um determinado nome e valor da etiqueta da atribuição de política. Por exemplo, se **não** quiser adicionar uma definição de diagnóstico aos cofres que tenham uma etiqueta 'isTest' definida no valor 'sim', deve introduzir 'isTest' no campo **Exclusão nome** e "sim" no campo **Exclusão de Etiquetas.** Se algum (ou ambos) destes dois campos ficar vazio, a política será aplicada a todos os cofres relevantes, independentemente das etiquetas que contenham.

![Parâmetros de atribuição de políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. Criar uma tarefa de **reparação** - Uma vez atribuída a política a um âmbito, quaisquer novos cofres criados nesse âmbito automaticamente obtêm configurações de diagnóstico de LA configuradas (dentro de 30 minutos a partir do momento da criação do cofre). Para adicionar uma definição de diagnóstico aos cofres existentes no âmbito, pode desencadear uma tarefa de reparação no momento da atribuição de políticas. Para desencadear uma tarefa de reparação, selecione a caixa de verificação Criar uma tarefa de **reparação**. 

![Reparação de Atribuição de Políticas](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navegue para o separador **Review+Criar** e clique em **Criar**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Em que condições a tarefa de reparação se aplicará a um cofre?

A tarefa de reparação é aplicada a cofres que não sejam conformes de acordo com a definição da política. Um cofre não é conforme se satisfaz qualquer uma das seguintes condições:

* Não há definição de diagnóstico para o cofre.
* As definições de diagnóstico estão presentes para o cofre, mas nenhuma das definições tem **todos os** eventos específicos do Recurso habilitados com LA como destino, e **Recurso específico** selecionado no alternância. 

Assim, mesmo que um utilizador tenha um cofre com o evento AzureBackupReport ativado no modo AzureDiagnostics (que é suportado por Relatórios de Backup), a tarefa de reparação continuará a aplicar-se a este cofre, uma vez que o modo específico de Recurso é a forma recomendada de criar definições de diagnóstico, [para a frente.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)

Além disso, se um utilizador tiver um cofre com apenas um subconjunto dos seis eventos específicos de Recursos ativados, a tarefa de reparação aplicar-se-á a este cofre, uma vez que os Relatórios de Backup funcionarão como esperado se todos os seis eventos específicos do Recurso estiverem ativados.

> [!NOTE]
>
> Se um cofre tiver uma definição de diagnóstico existente com um subconjunto de categorias **específicas de Recursos** habilitadas, configuradas para enviar dados para um determinado espaço de trabalho de LA, digamos "Workspace X", então a tarefa de reparação falhará (só para esse cofre) se o espaço de trabalho de LA fornecido na atribuição de Política for o **mesmo** 'Workspace X'. 
>
>Isto porque, se os eventos habilitados por duas configurações de diagnóstico diferentes no mesmo recurso **se sobrepõem** de alguma forma, então as configurações não podem ter o mesmo espaço de trabalho de LA que o destino. Terá de resolver manualmente esta falha, navegando até ao cofre relevante e configurando uma definição de diagnóstico com um espaço de trabalho diferente de LA Workspace como destino.
>
> Note que a tarefa de reparação **não** falhará se a definição de diagnósticoexistente como apenas o AzureBackupReport habilitado com o Workspace X como destino, uma vez que, neste caso, não haverá sobreposição entre os eventos ativados pela configuração existente e os eventos ativados pela definição criada pela tarefa de reparação.

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como usar relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports)
* [Saiba mais sobre a Política De Azure](https://docs.microsoft.com/azure/governance/policy/)
* [Utilize a Política Azure para ativar automaticamente a cópia de segurança para todos os VMs num âmbito de dar](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
