---
title: Gerir os dados da Automatização do Azure
description: Este artigo contém múltiplos tópicos para gerir um ambiente de Automação Azure.  Atualmente inclui a Retenção de Dados e O Apoio à Recuperação de Desastres de Automação Azure na Automação Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132886"
---
# <a name="managing-azure-automation-data"></a>Gerir os dados da Automatização do Azure

Este artigo contém múltiplos tópicos para gerir um ambiente de Automação Azure.

## <a name="data-retention"></a>Retenção de dados

Quando elimina um recurso na Automação Azure, este é retido por 30 dias para efeitos de auditoria antes de ser removido permanentemente. Não se pode ver ou usar o recurso durante este tempo. Esta política aplica-se também aos recursos que pertencem a uma conta de automação que é eliminada.

A Azure Automation elimina e remove permanentemente os postos de trabalho com mais de 30 dias.

O quadro seguinte resume a política de retenção de diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Removido permanentemente 30 dias após a eliminação da conta por um utilizador. |
| Elementos |Removido permanentemente 30 dias após a eliminação do ativo por um utilizador, ou 30 dias após a conta que detém o ativo ser eliminado por um utilizador. |
| Módulos |Removido permanentemente 30 dias após o módulo ser eliminado por um utilizador, ou 30 dias após a conta que detém o módulo ser eliminada por um utilizador. |
| Runbooks |Removido permanentemente 30 dias após a eliminação do recurso por um utilizador, ou 30 dias após a conta que detém o recurso ser eliminado por um utilizador. |
| Tarefas |Eliminado e removido permanentemente 30 dias após a última modificação. Isto pode ser depois do trabalho estar concluído, ou suspenso. |
| Configurações de nó/ficheiros MOF |A configuração do nó antigo é permanentemente removida 30 dias após a geração de uma nova configuração do nó. |
| Nódosos DSC |Removido permanentemente 30 dias após o nó não estar registado na Conta de Automação utilizando o portal Azure ou o [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet no Windows PowerShell. Os nódosos também são removidos permanentemente 30 dias após a conta que detém o nó ser eliminada por um utilizador. |
| Relatórios do Nó |Removido permanentemente 90 dias após a geração de um novo relatório para aquele nó |

A política de retenção aplica-se a todos os utilizadores e atualmente não pode ser personalizada.

No entanto, se precisar de reter dados por um período mais longo de tempo, pode reencaminhar os registos de trabalho do livro de requalificação para os registos do Monitor Do Azure. Para mais informações, reveja os dados de trabalho da [Azure Automation para os registos do Monitor Do Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="backing-up-azure-automation"></a>Apoiando a Automação Azure

Ao eliminar uma conta de automação no Microsoft Azure, todos os objetos da conta são eliminados, incluindo livros de execução, módulos, configurações, configurações, empregos e ativos. Os objetos não podem ser recuperados após a eliminação da conta.  Pode utilizar as seguintes informações para fazer backup do conteúdo da sua conta de automação antes de a apagar.

### <a name="runbooks"></a>Runbooks

Pode exportar os seus livros de execução para ficheiros de script utilizando o portal Azure ou o [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet no Windows PowerShell. Estes ficheiros de script podem ser importados para outra conta de automação, conforme discutido na [Criação ou Importação de um Livro](/previous-versions/azure/dn643637(v=azure.100))de Execução .

### <a name="integration-modules"></a>Módulos de integração

Não é possível exportar módulos de integração da Azure Automation. Deve certificar-se de que estão disponíveis fora da conta de automação.

### <a name="assets"></a>Elementos

Não é possível exportar [ativos](/previous-versions/azure/dn939988(v=azure.100)) da Azure Automation.  Utilizando o portal Azure, deve ter em conta os detalhes de variáveis, credenciais, certificados, ligações e horários.  Em seguida, deve criar manualmente quaisquer ativos que sejam utilizados por livros de execução que importem para outra automatização.

Pode utilizar [cmdlets Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) para recuperar detalhes de ativos não encriptados e guardá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Não é possível obter o valor para variáveis encriptadas ou para o campo de palavra-passe das credenciais utilizando cmdlets. Se não conhece estes valores, então pode recuperá-los de um livro de execução utilizando as atividades [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) e [Get-AutomationPSCredential.](/previous-versions/azure/dn940015(v=azure.100))

Não é possível exportar certificados da Azure Automation. Deve certificar-se de que existem certificados fora do Azure.

### <a name="dsc-configurations"></a>Configurações DSC

Pode exportar as suas configurações para ficheiros de script utilizando o portal Azure ou o [Cmdlet DeConfiguração Export-AzureRmAutomationDscConfiguração](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) no Windows PowerShell. Estas configurações podem ser importadas e usadas noutra conta de automação.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicação na Automação Azure

A geo-replicação, standard nas contas da Automação Azure, apoia os dados das contas para uma região geográfica diferente para despedimento. Pode escolher uma região primária ao configurar a sua conta e, em seguida, uma região secundária é-lhe atribuída automaticamente. Os dados secundários, copiados da região primária, são continuamente atualizados em caso de perda de dados.  

A tabela que se segue mostra os pares disponíveis nas regiões primárias e secundárias.

| Primária | Secundária |
| --- | --- |
| E.U.A. Centro-Sul |E.U.A. Centro-Norte |
| E.U.A. Leste 2 |E.U.A. Central |
| Europa ocidental |Europa do Norte |
| Ásia Sudeste |Ásia Leste |
| Leste do Japão |Oeste do Japão |

No caso improvável de perder dados da região primária, a Microsoft tenta recuperá-la. Se os dados primários não puderem ser recuperados, então o geo-failover é realizado e os clientes afetados serão notificados sobre isso através da sua subscrição.
