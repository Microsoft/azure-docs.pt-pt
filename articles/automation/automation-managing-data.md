---
title: Gerir os dados da Automatização do Azure
description: Este artigo contém vários tópicos para gerenciar um ambiente de automação do Azure.  Atualmente inclui a retenção de dados e o backup da recuperação de desastre da automação do Azure na automação do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: daa5bab7c8d4cbe98ffe9a8a8a4b66da029fef5c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421895"
---
# <a name="managing-azure-automation-data"></a>Gerir os dados da Automatização do Azure
Este artigo contém vários tópicos para gerenciar um ambiente de automação do Azure.

## <a name="data-retention"></a>Retenção de dados
Quando você exclui um recurso na automação do Azure, ele é retido por 90 dias para fins de auditoria antes de ser removido permanentemente.  Você não pode ver ou usar o recurso durante esse tempo.  Essa política também se aplica a recursos que pertencem a uma conta de automação que é excluída.

A automação do Azure exclui automaticamente e remove permanentemente os trabalhos com mais de 90 dias.

A tabela a seguir resume a política de retenção para diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Removidos permanentemente 90 dias após a conta ser excluída por um usuário. |
| Elementos |Removidos permanentemente 90 dias após o ativo ser excluído por um usuário ou 90 dias após a conta que contém o ativo é excluída por um usuário. |
| Módulos |Removidos permanentemente 90 dias após o módulo ser excluído por um usuário ou 90 dias após a conta que contém o módulo ser excluída por um usuário. |
| Runbooks |Removidos permanentemente 90 dias após o recurso ser excluído por um usuário ou 90 dias após a conta que contém o recurso ser excluída por um usuário. |
| Tarefas |Excluídos e removidos permanentemente 90 dias após a última modificação. Isso pode ser após o trabalho ser concluído, ser interrompido ou suspenso. |
| Configurações de nó/Arquivos MOF |A configuração antiga do nó é removida permanentemente 90 dias após a geração de uma nova configuração de nó. |
| Nós DSC |Removidos permanentemente 90 dias após o cancelamento do registro do nó da conta de automação usando portal do Azure ou o cmdlet [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) no Windows PowerShell. Os nós também são removidos permanentemente 90 dias depois que a conta que contém o nó é excluída por um usuário. |
| Relatórios de nó |Removido permanentemente 90 dias após a geração de um novo relatório para esse nó |

A política de retenção se aplica a todos os usuários e, no momento, não pode ser personalizada.

No entanto, se você precisar reter dados por um período de tempo maior, poderá encaminhar logs de trabalho de runbook para logs de Azure Monitor.  Para obter mais informações, examine [encaminhar dados de trabalho de automação do Azure para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Fazer cópia de segurança da Automatização do Azure
Quando você exclui uma conta de automação no Microsoft Azure, todos os objetos na conta são excluídos, incluindo runbooks, módulos, configurações, configurações, trabalhos e ativos. Os objetos não podem ser recuperados depois que a conta é excluída.  Você pode usar as informações a seguir para fazer backup do conteúdo da sua conta de automação antes de excluí-la. 

### <a name="runbooks"></a>Runbooks
Você pode exportar seus runbooks para arquivos de script usando o portal do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) no Windows PowerShell.  Esses arquivos de script podem ser importados para outra conta de automação, conforme discutido na [criação ou importação de um runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Módulos de integração
Não é possível exportar módulos de integração da automação do Azure.  Você deve garantir que eles estejam disponíveis fora da conta de automação.

### <a name="assets"></a>Elementos
Você não pode exportar [ativos](/previous-versions/azure/dn939988(v=azure.100)) da automação do Azure.  Usando o portal do Azure, você deve observar os detalhes de variáveis, credenciais, certificados, conexões e agendas.  Você deve criar manualmente todos os ativos que são usados por runbooks importados em outra automação.

Você pode usar os [cmdlets do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) para recuperar detalhes de ativos não criptografados e salvá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Você não pode recuperar o valor de variáveis criptografadas ou o campo de senha de credenciais usando cmdlets.  Se você não souber esses valores, poderá recuperá-los de um runbook usando as atividades [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) e [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)) .

Você não pode exportar certificados da automação do Azure.  Você deve garantir que todos os certificados estejam disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações DSC
Você pode exportar suas configurações para arquivos de script usando o portal do Azure ou o cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) no Windows PowerShell. Essas configurações podem ser importadas e usadas em outra conta de automação.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na automação do Azure
A replicação geográfica, Standard nas contas de automação do Azure, faz backup de dados de conta para uma região geográfica diferente para redundância. Você pode escolher uma região primária ao configurar sua conta e, em seguida, uma região secundária é atribuída a ela automaticamente. Os dados secundários, copiados da região primária, são atualizados continuamente em caso de perda de dados.  

A tabela a seguir mostra os emparelhamentos de região primária e secundária disponíveis.

| Primária | Secundária |
| --- | --- |
| E.U.A. Centro-Sul |E.U.A. Centro-Norte |
| Este dos EUA 2 |Centro dos E.U.A. |
| Europa Ocidental |Europa do Norte |
| Sudeste Asiático |Este Asiático |
| Este do Japão |Oeste do Japão |

No caso improvável de os dados da região primária serem perdidos, a Microsoft tentará recuperá-lo. Se os dados primários não puderem ser recuperados, o failover geográfico será executado e os clientes afetados serão notificados sobre isso por meio de sua assinatura.


