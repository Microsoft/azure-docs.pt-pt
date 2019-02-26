---
title: Gerir os dados da Automatização do Azure
description: Este artigo contém vários tópicos para gerir um ambiente de automatização do Azure.  Atualmente, inclui retenção de dados e realizar backup de recuperação de desastres de automatização do Azure na automatização do Azure.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3e217e0e3367c6e1200567f589749fec9e626da8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817461"
---
# <a name="managing-azure-automation-data"></a>Gerir os dados da Automatização do Azure
Este artigo contém vários tópicos para gerir um ambiente de automatização do Azure.

## <a name="data-retention"></a>Retenção de dados
Quando elimina um recurso na automatização do Azure, são retido durante 90 dias para fins de auditoria antes de serem removidos permanentemente.  Não é possível ver ou utilizar o recurso durante este período.  Esta política também se aplica aos recursos que pertencem a uma conta de automatização foi eliminada.

A automatização do Azure elimina automaticamente e remove permanentemente a trabalhos mais de 90 dias.

A tabela seguinte resume a política de retenção para os recursos diferentes.

| Dados | Política |
|:--- |:--- |
| Contas |Remover permanentemente os 90 dias após a conta for eliminada por um utilizador. |
| Elementos |Removido de forma permanente 90 dias após o elemento é eliminado por um utilizador ou 90 dias após a conta que contém que o elemento é eliminado por um utilizador. |
| Módulos |Permanentemente removido 90 dias depois de eliminar o módulo por um utilizador ou 90 dias após a conta que contém que o módulo foi eliminado por um utilizador. |
| Runbooks |Permanentemente removido 90 dias após o recurso é excluído por um utilizador ou 90 dias após a conta que contém que o recurso é excluído por um utilizador. |
| Tarefas |Eliminado e permanentemente removidos 90 dias após a última a ser modificado. Isto pode ser após a tarefa for concluída, está parada ou está suspensa. |
| Ficheiros MOF/configurações de nó |Configuração do nó antigo é permanentemente removida 90 dias após é gerada uma nova configuração de nó. |
| Nós de DSC |Permanentemente removido 90 dias após o nó é anulado o registo da conta de automatização com o portal do Azure ou o [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) cmdlet no Windows PowerShell. Nós também permanentemente são removidos 90 dias após a conta que contém que o nó é eliminado por um utilizador. |
| Relatórios de nó |Permanentemente removido 90 dias depois de um novo relatório é gerado para esse nó |

A política de retenção aplica-se a todos os utilizadores e atualmente não pode ser personalizada.

No entanto, se precisar de reter dados durante um período mais longo do tempo, pode reencaminhar os runbook registos da tarefa para registos do Azure Monitor.  Para obter mais informações, consulte [reencaminhar dados de tarefa da automatização do Azure para registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).   

## <a name="backing-up-azure-automation"></a>Fazer cópia de segurança da Automatização do Azure
Quando elimina uma conta de automatização no Microsoft Azure, todos os objetos na conta são eliminados incluindo runbooks, módulos, configurações, configurações, tarefas e ativos. Não não possível recuperar os objetos depois da conta for eliminada.  Pode utilizar as seguintes informações para o conteúdo da sua conta de automatização de cópia de segurança antes de o eliminar. 

### <a name="runbooks"></a>Runbooks
Pode exportar os runbooks para arquivos de script usando o portal do Azure ou o [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet no Windows PowerShell.  Esses arquivos de script podem ser importados para outra conta de automatização, conforme discutido [criar ou importar um Runbook](https://msdn.microsoft.com/library/dn643637.aspx).

### <a name="integration-modules"></a>Módulos de integração
Não é possível exportar os módulos de integração da automatização do Azure.  Tem de se certificar de que estão disponíveis fora a conta de automatização.

### <a name="assets"></a>Elementos
Não é possível exportar [ativos](https://msdn.microsoft.com/library/dn939988.aspx) da automatização do Azure.  Com o portal do Azure, deve observar os detalhes de variáveis, credenciais, certificados, ligações e agendas.  Em seguida, deve criar manualmente qualquer recurso que é utilizado por runbooks que importar para a automatização de outro.

Pode usar [cmdlets do Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) para obter os detalhes da ativos não encriptados e guardá-los para referência futura ou crie recursos equivalentes na outra conta de automatização.

Não é possível obter o valor para variáveis encriptadas ou o campo de palavra-passe de credenciais com os cmdlets.  Se não sabe estes valores, em seguida, pode obtê-las a partir de um runbook com o [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) atividades.

Não é possível exportar os certificados da automatização do Azure.  Tem de se certificar de que todos os certificados estão disponíveis fora do Azure.

### <a name="dsc-configurations"></a>Configurações de DSC
Pode exportar as configurações para arquivos de script usando o portal do Azure ou o [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) cmdlet no Windows PowerShell. Estas configurações podem ser importadas e utilizadas em outra conta de automatização.

## <a name="geo-replication-in-azure-automation"></a>Replicação geográfica na automatização do Azure
A georreplicação padrão nas contas de automatização do Azure, faz o backup de dados de conta para uma região geográfica diferente para redundância. Pode escolher a região primária quando configurar a sua conta e, em seguida, em que uma região secundária é atribuída automaticamente ao mesmo. Os dados secundários, copiados da região primária, é constantemente atualizados em caso de perda de dados.  

A tabela seguinte mostra o emparelhamento de regiões disponíveis de primário e secundário.

| Primária | Secundária |
| --- | --- |
| EUA Centro-Sul |EUA Centro-Norte |
| Este dos EUA 2 |EUA Central |
| Europa Ocidental |Europa do Norte |
| Sudeste Asiático |Ásia Oriental |
| Leste do Japão |Oeste do Japão |

Na improvável eventualidade que uma região primária os dados são perdidos, a Microsoft tenta recuperá-la. Se não não possível recuperar os dados primários, em seguida, é efetuada a ativação pós-falha geográfica e os clientes afetados serão notificados sobre isso através de sua assinatura.


