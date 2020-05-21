---
title: Gestão dos dados da Automatização do Azure
description: Este artigo fornece conceitos de gestão de dados na Automatização Azure, incluindo retenção de dados e backup.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 44fe626abd71c13b7f16aa07c4ddf261ff3cacea
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715431"
---
# <a name="management-of-azure-automation-data"></a>Gestão dos dados da Automatização do Azure

Este artigo contém vários tópicos para a gestão de dados num ambiente de Automação Azure.

## <a name="data-retention"></a>Retenção de dados

Ao eliminar um recurso na Automação Azure, é retido por vários dias para efeitos de auditoria antes da remoção permanente. Não se pode ver ou usar o recurso durante este tempo. Esta política aplica-se também aos recursos que pertencem a uma conta de Automação eliminada.

O quadro seguinte resume a política de retenção de diferentes recursos.

| Dados | Política |
|:--- |:--- |
| Contas |Uma conta é removida permanentemente 30 dias após o utilizador aapagar. |
| Elementos |Um ativo é permanentemente removido 30 dias após o utilizador o apagar, ou 30 dias após um utilizador apagar uma conta que detém o ativo. |
| Nódosos DSC |Um nó DSC é removido permanentemente 30 dias depois de não ter sido registado a partir de uma conta De automação utilizando o portal Azure ou o [cmdlet Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-3.7.0) no Windows PowerShell. Um nó também é removido permanentemente 30 dias após um utilizador apagar a conta que detém o nó. |
| Tarefas |Um trabalho é eliminado e removido permanentemente 30 dias após a modificação, por exemplo, após o trabalho concluído, é interrompido ou suspenso. |
| Módulos |Um módulo é permanentemente removido 30 dias após o utilizador o apagar, ou 30 dias após um utilizador apagar a conta que detém o módulo. |
| Configurações de nó/ficheiros MOF |Uma configuração antiga do nó é permanentemente removida 30 dias após a geração de uma nova configuração do nó. |
| Relatórios do Nó |Um relatório do nó é removido permanentemente 90 dias após a geração de um novo relatório para esse nó. |
| Runbooks |Um livro de execução é removido permanentemente 30 dias após o utilizador apagar o recurso, ou 30 dias após um utilizador apagar a conta que detém o recurso. |

A política de retenção aplica-se a todos os utilizadores e atualmente não pode ser personalizada. No entanto, se precisar de guardar dados por um período mais longo, pode reencaminhar os dados de trabalho da [Azure Automation para os registos do Monitor Do Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="data-backup"></a>Backup de dados

Quando elimina uma conta de Automação em Azure, todos os objetos da conta são eliminados. Os objetos incluem livros de execução, módulos, configurações, configurações, empregos e ativos. Não podem ser recuperados depois da conta ser apagada. Pode utilizar as seguintes informações para fazer cópia sinuosa mente o conteúdo da sua conta Automation antes de a apagar.

### <a name="runbooks"></a>Runbooks

Pode exportar os seus livros de execução para ficheiros de script utilizando o portal Azure ou o [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) cmdlet no Windows PowerShell. Pode importar estes ficheiros de script para outra conta Automation, como discutido em Gerir livros de [execução em Automação Azure](manage-runbooks.md).

### <a name="integration-modules"></a>Módulos de integração

Não é possível exportar módulos de integração da Azure Automation. Deve disponibilizá-los fora da conta Automation.

### <a name="assets"></a>Elementos

Não é possível exportar ativos da Azure Automation: certificados, ligações, credenciais, horários e variáveis. Em vez disso, pode utilizar o portal Azure e os cmdlets Azure para observar os detalhes destes ativos. Em seguida, use estes detalhes para criar quaisquer ativos que sejam usados por cadernetas que você importa para outra conta De automatização.

Não é possível recuperar os valores de variáveis encriptadas ou os campos de senha de credenciais utilizando cmdlets. Se não conhece estes valores, pode recuperá-los num livro de corridas. Para recuperar valores variáveis, consulte [ativos variáveis na Automação Azure.](shared-resources/variables.md) Para saber mais sobre a recuperação de valores credenciais, consulte [os ativos credenciais na Automação Azure.](shared-resources/credentials.md)

 ### <a name="dsc-configurations"></a>Configurações DSC

Pode exportar as suas configurações DSC para ficheiros de script utilizando o portal Azure ou o [cmdlet de configuração Export-AzAutomationDscConfiguração](https://docs.microsoft.com/powershell/module/az.automation/export-azautomationdscconfiguration?view=azps-3.7.0
) no Windows PowerShell. Pode importar e utilizar estas configurações noutra conta da Automação.

## <a name="geo-replication-in-azure-automation"></a>Geo-replicação na Automação Azure

A geo-replicação é padrão nas contas da Automação Azure. Escolhe uma região primária ao configurar a sua conta. O serviço interno de georeplicação da Automação atribui automaticamente uma região secundária à conta. O serviço então continuamente a apoiar os dados da conta da região primária para a região secundária. A lista completa das regiões primárias e secundárias pode ser encontrada na continuidade do Negócio e recuperação de [desastres (BCDR): Regiões Emparelhadas de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

A cópia de segurança criada pelo serviço de geo-replicação automation é uma cópia completa de ativos de Automação, configurações e similares. Esta cópia de segurança pode ser usada se a região primária descer e perder dados. No caso improvável de se perderem dados para uma região primária, a Microsoft tenta recuperá-la. Se a empresa não conseguir recuperar os dados primários, utiliza falhas automáticas e informa-o da situação através da sua subscrição Do Azure. 

O serviço de georeplicação automation não é acessível diretamente a clientes externos se houver uma falha regional. Se pretender manter a configuração da Automação e os livros de execução durante falhas regionais:

1. Selecione uma região secundária para emparelhar com a região geográfica da sua conta de Automação Primária.

2. Criar uma conta de Automação na região secundária.

3. Na conta principal, exporte os seus livros como ficheiros de script.

4. Importe os livros de execução para a sua conta de Automação na região secundária.

## <a name="next-steps"></a>Próximos passos

* Para saber mais sobre ativos seguros na Automatização Azure, consulte [encriptar ativos seguros na Automação Azure.](automation-secure-asset-encryption.md)

* Para saber mais sobre a geo-replicação, consulte [Criar e utilizar geo-replicação ativa.](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication)