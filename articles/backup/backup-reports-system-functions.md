---
title: Funções do sistema em Registos monitores Azure
description: Escreva consultas personalizadas em Registos do Monitor Azure usando funções do sistema
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 1d26adfd2bd1a3fc1506a334b4b661b66172192d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510554"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Funções do sistema em Registos monitores Azure

O Azure Backup fornece um conjunto de funções, chamadas funções do sistema ou funções de solução, que estão disponíveis por padrão nos seus espaços de trabalho Log Analytics (LA).
 
Estas funções funcionam com dados nas [tabelas de backup Azure em](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model) LA e devolvem dados formatados que o ajudam a recuperar facilmente informações de todas as suas entidades relacionadas com backup, utilizando consultas simples. Os utilizadores podem passar parâmetros para estas funções para filtrar os dados que são devolvidos por estas funções. 

Recomenda-se a utilização de funções do sistema para consultar os seus dados de backup em espaços de trabalho de LA para criar relatórios personalizados, uma vez que proporcionam uma série de benefícios, conforme detalhado na secção abaixo.

## <a name="benefits-of-using-system-functions"></a>Benefícios da utilização das funções do sistema

* **Consultas mais simples:** A utilização de funções ajuda a reduzir o número de juntas necessárias nas suas consultas. Por predefinição, as funções devolvem esquemas 'achatados', que incorporam toda a informação relativa à entidade (instância de backup, trabalho, cofre, e assim por diante) a ser questionada. Por exemplo, se precisar de obter uma lista de trabalhos de backup bem sucedidos através do nome do item de backup e do seu recipiente associado, uma simples chamada para a função **_AzureBackup_getJobs()** irá dar-lhe toda esta informação para cada trabalho. Por outro lado, consultar as tabelas cruas diretamente exigiria que executasse várias junções entre as mesas [AddonAzureBackupJobs](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#addonazurebackupjobs) e [CoreAzureBackup.](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model#coreazurebackup)

* **Transição mais suave do evento de diagnóstico do legado**: A utilização de funções do sistema ajuda-o a transitar suavemente do evento de diagnóstico [legado](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event) (AzureBackupReport em modo AzureDiagnostics) para os [eventos específicos do recurso](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users). Todas as funções do sistema fornecidas pelo Azure Backup permitem especificar um parâmetro que lhe permite escolher se a função deve consultar os dados apenas a partir das tabelas específicas do recurso, ou consultar dados da tabela do legado e das tabelas específicas de recursos (com desduplicação de registos).
    * Se tiver migrado com sucesso para as tabelas específicas do recurso, pode optar por excluir a tabela do legado de ser questionada pela função.
    * Se estiver neste momento em processo de migração e tiver alguns dados nas tabelas antigas que necessita para análise, pode optar por incluir a tabela do legado. Quando a transição estiver concluída e já não precisar de dados da tabela do legado, pode simplesmente atualizar o valor do parâmetro passado para a função nas suas consultas, para excluir a tabela do legado.
    * Se ainda estiver a utilizar apenas a tabela do legado, as funções continuarão a funcionar se optar por incluir a tabela do legado através do mesmo parâmetro. No entanto, [recomenda-se mudar para as tabelas específicas do recurso](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace) o mais cedo possível.

* **Reduz a possibilidade de quebra de consultas personalizadas**: Se o Azure Backup introduzir melhorias no esquema das tabelas de LA subjacentes para acomodar futuros cenários de reporte, a definição das funções também será atualizada para ter em conta as alterações do esquema. Assim, se utilizar funções do sistema para criar consultas personalizadas, as suas consultas não quebrarão, mesmo que existam alterações no esquema subjacente às tabelas.

> [!NOTE]
> As funções do sistema são mantidas pela Microsoft e as suas definições não podem ser editadas pelos utilizadores. Se necessitar de funções editáveis, pode criar [funções guardadas](https://docs.microsoft.com/azure/azure-monitor/logs/functions) em LA.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Tipos de funções do sistema oferecidas pelo Azure Backup

* **Funções principais**: Estas são funções que o ajudam a consultar qualquer uma das principais entidades de Backup Azure, tais como Instâncias de Backup, Abóbadas, Políticas, Empregos e Entidades de Faturação. Por exemplo, a função **_AzureBackup_getBackupInstances** devolve uma lista de todas as instâncias de backup que existem no seu ambiente a partir do último dia concluído (na UTC). Os parâmetros e esquema devolvido para cada uma destas funções principais são resumidos abaixo neste artigo.

* **Funções de tendência**: Estas são funções que devolvem registos históricos para as suas entidades relacionadas com backup (por exemplo, instâncias de backup, grupos de faturação) e permitem obter informações diárias, semanais e mensais sobre métricas-chave (por exemplo, Conde, Armazenamento consumido) relativas a essas entidades. Os parâmetros e esquema devolvido para cada uma destas funções de tendência são resumidos abaixo neste artigo.

> [!NOTE]
> Atualmente, as funções do sistema devolvem dados até ao último dia concluído (na UTC). Os dados do dia parcial atual não são devolvidos. Por isso, se procuras recuperar registos para o dia de hoje, terás de usar as mesas cruas de LA.


## <a name="list-of-system-functions"></a>Lista de funções do sistema

### <a name="core-functions"></a>Funções do núcleo

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults()

Esta função devolve a lista de todos os cofres dos Serviços de Recuperação no seu ambiente Azure que estão associados ao espaço de trabalho de LA.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd apenas se precisar de obter todos os registos relacionados com o cofre no período de tempo de RangeStart a RangeEnd. Por predefinição, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada cofre. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart apenas se precisar de obter todos os registos relacionados com o cofre no período de tempo de RangeStart a RangeEnd. Por predefinição, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada cofre. | N |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas os cofres que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas os cofres que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos relativos apenas aos cofres especificados. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único do cofre |
| Id | ID do cofre do Azure Resource Manager (ARM) |
| Name | Nome do cofre |
| SubscriptionId | ID da subscrição em que o cofre existe |
| Localização | Localização em que o cofre existe |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento associado ao cofre |
| Etiquetas | Etiquetas do cofre |
| TimeGenerated | Timetamp do recorde |
| Tipo |  Tipo de cofre, que é "Microsoft.RecoveryServices/vaults"|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies()

Esta função devolve a lista de políticas de backup que estão a ser utilizadas no seu ambiente Azure juntamente com informações detalhadas sobre cada política, tais como o tipo de fonte de dados, tipo de replicação de armazenamento, e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeStart apenas se precisar de obter todos os registos relacionados com a política no período de tempo de RangeStart a RangeEnd. Por padrão, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada política. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart apenas se precisar de obter todos os registos relacionados com a política no período de tempo de RangeStart a RangeEnd. Por padrão, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada política. | N |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas as políticas que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista de regiões separadas por vírgula como parâmetro para esta função ajuda-o a recuperar apenas as políticas que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos de políticas relativas apenas aos cofres especificados. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos de políticas em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único da política |
| Id | ID do Gestor de Recursos Azure (ARM) da política |
| Name | Nome da apólice |
| Solução de backup | Solução de reserva a que a política está associada. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| TimeGenerated | Timetamp do recorde |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à política |
| VaultResourceId | ID do cofre associado à apólice |
| Nome do cofre | Nome do cofre associado à apólice |
| Saltos de abóbada | Etiquetas do cofre associadas à política |
| Colocação de Abóbada | Localização do cofre associado à apólice |
| VaultSubscriptionId | ID de assinatura do cofre associado à política |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à política |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| Extensões | Propriedades adicionais da apólice |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs()

Esta função devolve uma lista de todos os trabalhos de backup e restauro que foram desencadeados num intervalo de tempo especificado, juntamente com informações detalhadas sobre cada trabalho, tais como estado de trabalho, duração do trabalho, dados transferidos, e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd para recuperar a lista de todos os trabalhos iniciados no período de tempo de RangeStart a RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart para recuperar a lista de todos os trabalhos que começaram no período de tempo de RangeStart a RangeEnd. | Y |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas os trabalhos que estão associados a cofres nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas os empregos associados a cofres nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar empregos apenas relativos aos cofres especificados. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure empregos em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| JobOperationList | Utilize este parâmetro para filtrar a saída da função para um tipo específico de trabalho. Por exemplo, Backup ou Restore. Por predefinição, o valor deste parâmetro é "*", o que faz com que a função procure trabalhos de Backup e Restauro. | N | "Backup" | 
| JobStatusList | Utilize este parâmetro para filtrar a saída da função para um estado de trabalho específico. Por exemplo, Concluído, Falhado, e assim por diante. Por predefinição, o valor deste parâmetro é "*", o que faz com que a função procure todos os postos de trabalho independentemente do estado. | N | "Falhado, concluído com os avisos" |
| JobFailureCodeList | Utilize este parâmetro para filtrar a saída da função para um código de avaria específico. Por predefinição, o valor deste parâmetro é "*", o que faz com que a função procure todos os trabalhos independentemente do código de falha. | N | "Sucesso"
| DatasourceSetName | Utilize este parâmetro para filtrar a saída da função para um determinado recurso-mãe. Por exemplo, para devolver o SQL em instâncias de backup Azure VM pertencentes à máquina virtual "testvm", especifique o _testvm_ como o valor deste parâmetro. Por predefinição, o valor é "*", o que faz com que a função procure registos em todas as instâncias de backup. | N | "testvm" |
| Nome do BackupInstance | Utilize este parâmetro para procurar empregos numa determinada instância de backup pelo nome. Por predefinição, o valor é "*", o que faz com que a função procure registos em todas as instâncias de backup. | N | "testvm" |
| ExcluirLog | Utilize este parâmetro para excluir que os trabalhos de registo sejam devolvidos pela função (ajuda no desempenho da consulta). Por padrão, o valor deste parâmetro é verdadeiro, o que faz com que a função exclua os trabalhos de registo. | N | true


**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único do trabalho |
| OperaçãoCategoria | Categoria da operação a ser realizada. Por exemplo, Backup, Restaurar |
| Operação | Detalhes da operação que está a ser realizada. Por exemplo, Log (para cópia de segurança de registo)|
| Estado | Estado do trabalho. Por exemplo, Concluído, Falhado, Concluído Com Avisos |
| ErrorTle | Código de falha do trabalho |
| StartTime | Data e hora em que o trabalho começou |
| DuraçãoInSecs | Duração do trabalho em segundos |
| DataTransferredInMBs | Dados transferidos pelo trabalho em MBs |
| Restaurar o Tempo de Restauro | A data e a hora em que o ponto de recuperação que está a ser recuperado foi criado |
| Restaurar ALocação de Polícia | O local onde o ponto de recuperação que está a ser recuperado foi armazenado |
| BackupInstanceUniqueId | Chave estrangeira que se refere à instância de backup associada ao trabalho |
| BackupInstanceId | ID do Gestor de Recursos Azure (ARM) da instância de backup associada ao trabalho |
| BackupInstanceFriendlyName | Nome da instância de backup associada ao trabalho |
| DatasourceResourceId | Identificação do gestor de recursos Azure (ARM) da fonte de dados subjacente associada ao trabalho. Por exemplo, ID do Gestor de Recursos Azure (ARM) do VM |
| DatasourceDê-se | Nome amigável da fonte de dados subjacente associada ao trabalho |
| DatasourceType | Tipo de fonte de dados associada ao trabalho. Por exemplo, "Microsoft.Compute/virtualMachines" |
| BackupSSolution | Solução de reserva a que o trabalho está associado. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| DatasourceSetResourceId | ID do recurso-mãe (ARM) do recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SQL em fonte de dados Azure VM, este campo conterá o ID do Gestor de Recursos Azure (ARM) do VM no qual existe a Base de Dados SQL |
| DatasourceSetType | Tipo de recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SAP HANA em fonte de dados Azure VM, este campo será Microsoft.Compute/virtualMachines uma vez que o recurso-mãe é um VM Azure |
| VaultResourceId | ID do cofre associado ao trabalho do Azure Resource Manager (ARM) |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao trabalho |
| Nome do cofre | Nome do cofre associado ao trabalho |
| Saltos de abóbada | Etiquetas do cofre associadas ao trabalho |
| VaultSubscriptionId | ID de assinatura do cofre associado ao trabalho |
| Colocação de Abóbada | Localização do cofre associado ao trabalho |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao trabalho |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| TimeGenerated | Timetamp do recorde |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances()

Esta função devolve a lista de casos de backup que estão associados aos seus cofres dos Serviços de Recuperação, juntamente com informações detalhadas sobre cada instância de backup, tais como o consumo de armazenamento em nuvem, política associada, e assim por diante.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd apenas se precisar de obter todos os registos relacionados com a instância de backup no período de tempo de RangeStart a RangeEnd. Por predefinição, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada instância de backup. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart apenas se precisar de obter todos os registos relacionados com instâncias de backup no período de tempo de RangeStart a RangeEnd. Por predefinição, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada instância de backup. | N |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas as instâncias de backup que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas as instâncias de backup que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos de casos de backup relativos apenas aos cofres especificados. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos de casos de backup em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | Utilize este parâmetro para escolher se inclui apenas as instâncias de backup que estão ativamente protegidas, ou incluir também os casos para os quais a proteção foi interrompida e os casos para os quais está pendente a cópia de segurança inicial. Os valores suportados são "Protected", "ProtectionStopped", "InitialBackupPending" ou uma combinação separada por vírgula de qualquer um destes valores. Por predefinição, o valor é "*", o que faz com que a função procure todas as instâncias de backup independentemente dos detalhes de proteção. | N | "Protegido" |
| DatasourceSetName | Utilize este parâmetro para filtrar a saída da função para um determinado recurso-mãe. Por exemplo, para devolver o SQL em instâncias de backup Azure VM pertencentes à máquina virtual "testvm", especifique o _testvm_ como o valor deste parâmetro. Por predefinição, o valor é "*", o que faz com que a função procure registos em todas as instâncias de backup. | N | "testvm" |
| Nome do BackupInstance | Utilize este parâmetro para procurar um determinado exemplo de backup pelo nome. Por predefinição, o valor é "*", o que faz com que a função procure todas as instâncias de backup. | N | "testvm" |
| DisplayAllFields | Utilize este parâmetro para escolher se deve recuperar apenas um subconjunto dos campos devolvidos pela função. Se o valor deste parâmetro for falso, a função elimina informações relacionadas com o ponto de armazenamento e retenção da saída da função. Isto é útil se estiver a usar esta função como um passo intermédio numa consulta maior e precisar de otimizar o desempenho da consulta eliminando colunas que não necessita para análise. Por predefinição, o valor deste parâmetro é verdadeiro, o que faz com que a função devolva todos os campos relativos à instância de backup. | N | true |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único da instância de backup |
| Id | ID do Gestor de Recursos Azure (ARM) da instância de backup |
| FriendlyName | Nome amigável da instância de backup |
| ProteçãoInfo | Informações sobre as definições de proteção da instância de backup. Por exemplo, proteção configurada, proteção parada, cópia de segurança inicial pendente |
| ÚltimaRecoveryPoint | Data e hora do último ponto de recuperação associado à instância de backup |
| Mais antigoSRecoveryPoint | Data e hora do ponto de recuperação mais antigo associado à instância de backup |
| SourceSizeInMBs | Tamanho frontal da instância de backup em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento total em nuvem consumido pela instância de backup no nível padrão do cofre |
| DataSourceFriendlyName | Nome amigável da fonte de dados correspondente à instância de backup |
| BackupSSolution | Solução de backup a que a instância de backup está associada. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| DatasourceType | Tipo de fonte de dados correspondente à instância de backup. Por exemplo, "Microsoft.Compute/virtualMachines" |
| DatasourceResourceId | Identificação do Gestor de Recursos Azure (ARM) da fonte de dados subjacente correspondente à instância de backup. Por exemplo, ID do Gestor de Recursos Azure (ARM) do VM |
| DatasourceSetFriendlyName | Nome amigável do recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SQL em fonte de dados Azure VM, este campo conterá o nome do VM no qual existe a Base de Dados SQL |
| DatasourceSetResourceId | ID do recurso-mãe (ARM) do recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SQL em fonte de dados Azure VM, este campo conterá o ID do Gestor de Recursos Azure (ARM) do VM no qual existe a Base de Dados SQL |
| DatasourceSetType | Tipo de recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SAP HANA em fonte de dados Azure VM, este campo será Microsoft.Compute/virtualMachines uma vez que o recurso-mãe é um VM Azure |
| PolicyName | Nome da política associada à instância de backup |
| PolicyUniqueId | Chave externa que se refere à política associada à instância de backup  |
| PolicyId | ID do Gestor de Recursos Azure (ARM) da política associada à instância de backup |
| VaultResourceId | ID do Azure Resource Manager (ARM) do cofre associado à instância de backup |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à instância de backup |
| Nome do cofre | Nome do cofre associado à instância de backup |
| Saltos de abóbada | Etiquetas do cofre associadas à instância de backup |
| VaultSubscriptionId | ID de assinatura do cofre associado à instância de backup |
| Colocação de Abóbada | Localização do cofre associada à instância de backup |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à instância de backup |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| TimeGenerated | Timetamp do recorde |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups()

Esta função devolve uma lista de todas as entidades de faturação relacionadas com backup (grupos de faturação) juntamente com informações sobre componentes de faturação chave, tais como o tamanho do frontend e armazenamento total na nuvem.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd apenas se precisar de obter todos os registos relacionados com o grupo de faturação no período de tempo de RangeStart a RangeEnd. Por padrão, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada grupo de faturação. | N | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart apenas se precisar de obter todos os registos relacionados com o grupo de faturação no período de tempo de RangeStart a RangeEnd. Por padrão, o valor de RangeStart e RangeEnd são nulos, o que fará com que a função recupere apenas o registo mais recente de cada grupo de faturação. | N |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas os grupos de faturação que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas os grupos de faturação que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos de casos de backup relativos apenas aos cofres especificados. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos de grupos de faturação em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| Nome do BillingGroup | Utilize este parâmetro para procurar um determinado grupo de faturação pelo nome. Por predefinição, o valor é "*", o que faz com que a função procure todos os grupos de faturação. | N | "testvm" |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único do grupo de faturação |
| FriendlyName | Nome amigável do grupo de faturação |
| Name | Nome do grupo de faturação |
| Tipo | Tipo de grupo de faturação. Por exemplo, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Tamanho frontal do grupo de faturação em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento total de nuvem consumido pelo grupo de faturação no nível padrão do cofre |
| BackupSSolution | Solução de backup a que o grupo de faturação está associado. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| VaultResourceId | ID do cofre associado ao grupo de faturação Azure Resource Manager (ARM) |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao grupo de faturação |
| Nome do cofre | Nome do cofre associado ao grupo de faturação |
| Saltos de abóbada | Etiquetas do cofre associadas ao grupo de faturação |
| VaultSubscriptionId | ID de assinatura do cofre associado ao grupo de faturação |
| Colocação de Abóbada | Localização do cofre associado ao grupo de faturação |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao grupo de faturação |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| TimeGenerated | Timetamp do recorde |
| Extensões | Propriedades adicionais do grupo de faturação |

### <a name="trend-functions"></a>Funções de Tendência

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends()

Esta função devolve registos históricos para cada instância de backup, permitindo-lhe visualizar as principais tendências diárias, semanais e mensais relacionadas com a contagem de casos de backup e consumo de armazenamento, a vários níveis de granularidade.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd para recuperar todos os registos relacionados com a instância de backup no período de tempo de RangeStart a RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart para recuperar todos os registos relacionados com a instância de backup no período de tempo de RangeStart a RangeEnd. | Y |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres   | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas as instâncias de backup que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas as instâncias de backup que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos de casos de backup relativos apenas aos cofres especificados. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos de casos de backup em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| ProtectionInfoList | Utilize este parâmetro para escolher se inclui apenas as instâncias de backup que estão ativamente protegidas, ou incluir também os casos para os quais a proteção foi interrompida e os casos para os quais está pendente a cópia de segurança inicial. Os valores suportados são "Protected", "ProtectionStopped", "InitialBackupPending" ou uma combinação separada por vírgula de qualquer um destes valores. Por predefinição, o valor é "*", o que faz com que a função procure todas as instâncias de backup independentemente dos detalhes de proteção. | N | "Protegido" |
| DatasourceSetName | Utilize este parâmetro para filtrar a saída da função para um determinado recurso-mãe. Por exemplo, para devolver o SQL em instâncias de backup Azure VM pertencentes à máquina virtual "testvm", especifique o _testvm_ como o valor deste parâmetro. Por predefinição, o valor é "*", o que faz com que a função procure registos em todas as instâncias de backup. | N | "testvm" |
| Nome do BackupInstance | Utilize este parâmetro para procurar um determinado exemplo de backup pelo nome. Por predefinição, o valor é "*", o que faz com que a função procure todas as instâncias de backup. | N | "testvm" |
| DisplayAllFields | Utilize este parâmetro para escolher se deve recuperar apenas um subconjunto dos campos devolvidos pela função. Se o valor deste parâmetro for falso, a função elimina informações relacionadas com o ponto de armazenamento e retenção da saída da função. Isto é útil se estiver a usar esta função como um passo intermédio numa consulta maior e precisar de otimizar o desempenho da consulta eliminando colunas que não necessita para análise. Por predefinição, o valor deste parâmetro é verdadeiro, o que faz com que a função devolva todos os campos relativos à instância de backup. | N | true |
| AgregaçãoType | Utilize este parâmetro para especificar a granularidade temporal em que os dados devem ser recuperados. Se o valor deste parâmetro for "Diário", a função devolve um registo por instância de backup por dia, permitindo-lhe analisar as tendências diárias de consumo de armazenamento e contagem de casos de backup. Se o valor deste parâmetro for "Semanal", a função devolve um recorde por instância de backup por semana, permitindo-lhe analisar tendências semanais. Da mesma forma, pode especificar "Mensalmente" para analisar tendências mensais. O valor predefinido é "Diário". Se estiver a visualizar dados em intervalos de tempo maiores, recomenda-se a utilização de "Weekly" ou "Monthly" para um melhor desempenho de consulta e facilidade de análise de tendências. | N | "Semanal" |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único da instância de backup |
| Id | ID do Gestor de Recursos Azure (ARM) da instância de backup |
| FriendlyName | Nome amigável da instância de backup |
| ProteçãoInfo | Informações sobre as definições de proteção da instância de backup. Por exemplo, proteção configurada, proteção parada, cópia de segurança inicial pendente |
| ÚltimaRecoveryPoint | Data e hora do último ponto de recuperação associado à instância de backup |
| Mais antigoSRecoveryPoint | Data e hora do ponto de recuperação mais antigo associado à instância de backup |
| SourceSizeInMBs | Tamanho frontal da instância de backup em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento total em nuvem consumido pela instância de backup no nível padrão do cofre |
| DataSourceFriendlyName | Nome amigável da fonte de dados correspondente à instância de backup |
| BackupSSolution | Solução de backup a que a instância de backup está associada. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| DatasourceType | Tipo de fonte de dados correspondente à instância de backup. Por exemplo, "Microsoft.Compute/virtualMachines" |
| DatasourceResourceId | Identificação do Gestor de Recursos Azure (ARM) da fonte de dados subjacente correspondente à instância de backup. Por exemplo, ID do Gestor de Recursos Azure (ARM) do VM |
| DatasourceSetFriendlyName | Nome amigável do recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SQL em fonte de dados Azure VM, este campo conterá o nome do VM no qual existe a Base de Dados SQL |
| DatasourceSetResourceId | ID do recurso-mãe (ARM) do recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SQL em fonte de dados Azure VM, este campo conterá o ID do Gestor de Recursos Azure (ARM) do VM no qual existe a Base de Dados SQL |
| DatasourceSetType | Tipo de recurso-mãe da fonte de dados (sempre que aplicável). Por exemplo, para um SAP HANA em fonte de dados Azure VM, este campo será Microsoft.Compute/virtualMachines uma vez que o recurso-mãe é um VM Azure |
| PolicyName | Nome da política associada à instância de backup |
| PolicyUniqueId | Chave externa que se refere à política associada à instância de backup  |
| PolicyId | ID do Gestor de Recursos Azure (ARM) da política associada à instância de backup |
| VaultResourceId | ID do Azure Resource Manager (ARM) do cofre associado à instância de backup |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado à instância de backup |
| Nome do cofre | Nome do cofre associado à instância de backup |
| Saltos de abóbada | Etiquetas do cofre associadas à instância de backup |
| VaultSubscriptionId | ID de assinatura do cofre associado à instância de backup |
| Colocação de Abóbada | Localização do cofre associada à instância de backup |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado à instância de backup |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| TimeGenerated | Timetamp do recorde |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends()

Esta função devolve registos históricos para cada entidade de faturação, permitindo-lhe visualizar as principais tendências diárias, semanais e mensais relacionadas com o tamanho frontal e o consumo de armazenamento, a vários níveis de granularidade.

**Parâmetros**

| **Nome do parâmetro** | **Descrição** | **Necessário?** | **Valor de exemplo** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilize este parâmetro juntamente com o parâmetro RangeEnd para recuperar todos os registos relacionados com o grupo de faturação no período de tempo de RangeStart a RangeEnd. | Y | "2021-03-03 00:00:00" |
| RangeEnd     | Utilize este parâmetro juntamente com o parâmetro RangeStart para recuperar todos os registos relacionados com o grupo de faturação no período de tempo de RangeStart a RangeEnd. | Y |"2021-03-10 00:00:00"|
| Lista de Subscrição de Cofres  | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de subscrições onde existem dados de backup. Especificar uma lista separada de vírgula de IDs de subscrição como parâmetro para esta função ajuda-o a recuperar apenas os grupos de faturação que estão nas subscrições especificadas. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as subscrições. | N | "00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111"|
| Lista de VaultLocation     | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de regiões onde existem dados de backup. Especificar uma lista separada de vírgulas de regiões como parâmetro para esta função ajuda-o a recuperar apenas os grupos de faturação que se encontram nas regiões especificadas. Por predefinição, o valor deste parâmetro é '*', o que faz com que a função procure registos em todas as regiões. | N | "Eastus, Westus"|
| VaultList    |Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de abóbadas. Especificar uma lista separada de vírgulas de nomes de cofres como parâmetro para esta função ajuda-o a recuperar registos de casos de backup relativos apenas aos cofres especificados. Por padrão, o valor deste parâmetro é '*', o que faz com que a função procure registos de grupos de faturação em todos os cofres. | N |"cofre1,abóbada2, cofre3"|
| VaultTypeList     | Utilize este parâmetro para filtrar a saída da função para registos relativos a um determinado tipo de abóbada. Atualmente, o único tipo de cofre suportado é "Microsoft.RecoveryServices/vaults", que é o valor padrão deste parâmetro. | N | "Microsoft.RecoveryServices/vaults"|
| ExcluirLegacyEvent  | Utilize este parâmetro para escolher se deve consultar dados na tabela AzureDiagnostics legacy AzureDiagnostics ou não. Se o valor deste parâmetro for falso, a função consulta dados tanto da tabela AzureDiagnostics como das tabelas específicas do Recurso. Se o valor deste parâmetro for verdadeiro, a função consulta dados apenas a partir das tabelas específicas do Recurso. O valor predefinido é verdadeiro. | N | true |
| BackupSolutionList | Utilize este parâmetro para filtrar a saída da função para um determinado conjunto de soluções de backup utilizadas no seu ambiente Azure. Por exemplo, se especificar "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM" como o valor deste parâmetro, a função apenas devolve registos relacionados com itens com cópia de segurança da Máquina Virtual Azure, SQL em backup VM Azure ou cópia de segurança DPM para Azure. Por padrão, o valor deste parâmetro é '*', que faz com que os registos de retorno da função digam respeito a todas as soluções de backup suportadas por Relatórios de Backup (valores suportados são "Azure Virtual Machine Backup", "SQL in Azure VM Backup", "SAP HANA in Azure VM Backup", "Azure Storage (Azure Files) Backup", "Azure Backup Agent", "DPM", "Azure Backup Server" ou uma combinação separada de vmas de qualquer um destes valores. | N | "Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent" |
| Nome do BillingGroup | Utilize este parâmetro para procurar um determinado grupo de faturação pelo nome. Por predefinição, o valor é "*", o que faz com que a função procure todos os grupos de faturação. | N | "testvm" |
| AgregaçãoType | Utilize este parâmetro para especificar a granularidade temporal em que os dados devem ser recuperados. Se o valor deste parâmetro for "Diário", a função devolve um recorde por grupo de faturação por dia, permitindo-lhe analisar as tendências diárias de consumo de armazenamento e tamanho frontal. Se o valor deste parâmetro for "Semanal", a função devolve um recorde por instância de backup por semana, permitindo-lhe analisar tendências semanais. Da mesma forma, pode especificar "Mensalmente" para analisar tendências mensais. O valor predefinido é "Diário". Se estiver a visualizar dados em intervalos de tempo maiores, recomenda-se a utilização de "Weekly" ou "Monthly" para um melhor desempenho de consulta e facilidade de análise de tendências. | N | "Semanal" |

**Campos Devolvidos**

| **Nome do campo** | **Descrição** |
| -------------- | --------------- |
| UniqueId | Chave primária denotando iD único do grupo de faturação |
| FriendlyName | Nome amigável do grupo de faturação |
| Name | Nome do grupo de faturação |
| Tipo | Tipo de grupo de faturação. Por exemplo, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Tamanho frontal do grupo de faturação em MBs |
| VaultStore_StorageConsumptionInMBs | Armazenamento total de nuvem consumido pelo grupo de faturação no nível padrão do cofre |
| BackupSSolution | Solução de backup a que o grupo de faturação está associado. Por exemplo, Backup Azure VM, SQL em Backup Azure VM, e assim por diante. |
| VaultResourceId | ID do cofre associado ao grupo de faturação Azure Resource Manager (ARM) |
| VaultUniqueId | Chave estrangeira que se refere ao cofre associado ao grupo de faturação |
| Nome do cofre | Nome do cofre associado ao grupo de faturação |
| Saltos de abóbada | Etiquetas do cofre associadas ao grupo de faturação |
| VaultSubscriptionId | ID de assinatura do cofre associado ao grupo de faturação |
| Colocação de Abóbada | Localização do cofre associado ao grupo de faturação |
| VaultStore_StorageReplicationType | Tipo de replicação de armazenamento do cofre associado ao grupo de faturação |
| Abóbadas | Tipo de cofre, que é "Microsoft.RecoveryServices/vaults" |
| TimeGenerated | Timetamp do recorde |
| Extensões | Propriedades adicionais do grupo de faturação |

## <a name="sample-queries"></a>Consultas de amostra

Abaixo estão algumas consultas de amostra para ajudá-lo a começar a usar funções do sistema.

- Todos os trabalhos de backup da Azure VM falhados num determinado intervalo de tempo

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Todos os trabalhos de backup de registo SQL em um determinado intervalo de tempo

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tendência semanal de armazenamento de backup consumida para VM "testvm"

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre Relatórios de Backup](https://docs.microsoft.com/azure/backup/configure-reports)
