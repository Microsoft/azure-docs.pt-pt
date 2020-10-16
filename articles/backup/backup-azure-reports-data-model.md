---
title: Modelo de dados para eventos de diagnóstico de backup Azure
description: Este modelo de dados faz referência ao Modo Específico de Recurso de envio de eventos de diagnóstico para Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: c2c5d37596be104c4b1dc7e865586a4728a27bae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569598"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre entidades de backup fundamentais, tais como cofres e itens de backup.

| **Campo**                         | **Tipo de Dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recursos para recolha de dados. Por exemplo, identificação de recurso do cofre dos Serviços de Recuperação. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual - BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Azure Monitor. Por exemplo, CoreAzureBackup. |
| Representação do Agente                      | Texto          | Número de versão do Agente Backup ou do Agente de Proteção (no caso do SC DPM e MABS) |
| AzureBackupAgentVer           | Texto          | Versão do Agente de Backup Azure no Servidor de Gestão de Cópias de Segurança |
| AzureDataCenter                   | Texto          | Centro de dados onde o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão de aplicação do item de backup                       |
| BackupItemFriendlyName            | Texto          | Nome amigável do item de backup                             |
| Nome backupItem                    | Texto          | Nome do item de reserva                                      |
| Estado de Proteção de BackupItem         | Texto          | Estado de proteção do item de reserva                          |
| BackupItemFrontEndSize            | Texto          | Tamanho frontal do item de backup                            |
| BackupItemType                    | Texto          | Tipo de artigo de reserva. Por exemplo: VM, FileFolder             |
| BackupItemUniqueId                | Texto          | Identificador único do item de backup                         |
| BackupManagementServerType        | Texto          | Tipo de Servidor de Gestão de Cópias de Segurança, como em MABS, SC DPM     |
| BackupManagementServerUniqueId    | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Cópias de Segurança      |
| BackupManagementType              | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupManagementServerName        | Texto          | Nome do Servidor de Gestão de Cópias de Segurança                         |
| BackupManagementServerOSVersion   | Texto          | Versão OS do Servidor de Gestão de Cópias de Segurança                   |
| BackupManagementServerVersion     | Texto          | Versão do Servidor de Gestão de Cópias de Segurança                      |
| ÚltimaRecoveryPointLocation       | Texto          | Localização do último ponto de recuperação para o item de backup    |
| ÚltimaRecoveryPointTime           | DateTime      | Data do último ponto de recuperação para o item de backup   |
| Mais antigoRecoveryPointLocation       | Texto          | Localização do ponto de recuperação mais antigo para o item de backup    |
| Mais antigoRecoveryPointTime           | DateTime      | Data do último ponto de recuperação para o item de backup   |
| PolicyUniqueId                    | Texto          | ID único para identificar a política                             |
| Nome ProtetorContainerFriendlyName    | Texto          | Nome amigável do servidor protegido                        |
| Proteção DaLocação        | Texto          | Se o Contentor Protegido está localizado no local ou em Azure |
| Nome protetor do Nome do Protetor            | Texto          | Nome do Recipiente Protegido                            |
| Protegiu oTipoType do Proteção          | Texto          | Tipo de SO do Recipiente Protegido                          |
| ProteçãoContainerOSVer       | Texto          | Versão SO do Recipiente Protegido                        |
| Estado de Proteção de Proteção de Proteção do Estado de Proteção do Protetor | Texto          | Estado de proteção do contentor protegido                  |
| Type protetora do Protetor            | Texto          | Se o Recipiente Protegido é um servidor ou um recipiente  |
| ProtectedContainerUniqueId        | Texto          | ID único usado para identificar o recipiente protegido para tudo, exceto VMs apoiados usando DPM, MABS |
| ProtectedContainerWorkloadType    | Texto          | Tipo de recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| NomeGrupoProteção               | Texto          | Nome do Grupo de Proteção O item de backup está protegido em, para SC DPM, e MABS, se aplicável |
| ResourceGroupName                 | Texto          | Grupo de recursos do recurso (por exemplo, cofre dos Serviços de Recuperação) para a recolha de dados |
| SchemaVersão                     | Texto          | Este campo denota a versão atual do esquema. É **V2.** |
| Estado secundário de proteção de Backup    | Texto          | Se a proteção secundária está ativada para o item de backup  |
| Estado                             | Texto          | Estado do objeto de item de reserva. Por exemplo, Ative, Eliminado |
| ArmazenamentoPilicaçãoType            | Texto          | Tipo de replicação de armazenamento para o cofre. Por exemplo, GeoRedundant |
| SubscriptionId                    | Texto          | Identificador de subscrição do recurso (por exemplo, cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| Nome do cofre                         | Texto          | Nome do cofre                                            |
| Saltos de abóbada                         | Texto          | Etiquetas associadas ao recurso de cofre                    |
| VaultUniqueId                     | Texto          | Identificador Único do cofre                             |
| SourceSystem                      | Texto          | Sistema de origem dos dados atuais - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Esta tabela fornece detalhes sobre campos relacionados com alerta.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador único para o recurso sobre os dados recolhidos. Por exemplo, um ID de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, Alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupAlerts |
| Código de Alerta                      | Texto          | Código para identificar de forma única um tipo de alerta                     |
| AlertaConsolidaçãoStatus       | Texto          | Identifique se o alerta é um alerta consolidado ou não         |
| AlertOccurrenceDateTime        | DateTime      | Data e hora em que o alerta foi criado                     |
| Alerta Elevado                  | Texto          | Tipo de entidade em que o alerta é levantado                        |
| Alertaseverity                  | Texto          | Gravidade do alerta. Por exemplo, Critical                 |
| AlertaStatus                    | Texto          | Estado do alerta. Por exemplo, Ative                     |
| AlertTimeToResolveInMinutes    | Número        | Tempo tomado para resolver um alerta. Em branco para alertas ativos.     |
| AlertaType                      | Texto          | Tipo de alerta. Por exemplo, Backup                           |
| AlertaUniqueId                  | Texto          | Identificador único do alerta gerado                    |
| BackupItemUniqueId             | Texto          | Identificador único do item de backup associado ao alerta |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| CondeOfAlertsConsolidado      | Número        | Número de alertas consolidados se for um alerta consolidado  |
| ProtectedContainerUniqueId     | Texto          | Identificador único do servidor protegido associado ao alerta |
| Recomendação              | Texto          | Ação recomendada para resolver o alerta                      |
| SchemaVersão                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Eliminado |
| StorageUniqueId                | Texto          | ID único usado para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID único usado para identificar o cofre relacionado com o alerta    |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela fornece campos básicos protegidos relacionados com casos.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador único para o recurso sobre os dados recolhidos. Por exemplo, um ID de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Nome da operação, por exemplo, ProtectInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | ID exclusivo do item de backup                                 |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| ProtectedContainerUniqueId     | Texto          | ID único para identificar o recipiente protegido em que o trabalho é executado |
| ProtectedInstanceCount         | Texto          | Contagem de instâncias protegidas para o item de backup associado ou recipiente protegido nessa data |
| SchemaVersão                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado do objeto de item de backup, por exemplo, Ative, Deleted |
| VaultUniqueId                  | Texto          | Identificador único do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre campos relacionados com o emprego.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Job    |
| Categoria                       | Texto          | Este campo representa categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Texto          | Campo para especificar se o trabalho é Ad Hoc ou Agendado           |
| BackupItemUniqueId             | Texto          | ID exclusivo usado para identificar o item de backup relacionado com a entidade de armazenamento |
| BackupManagementServerUniqueId | Texto          | ID exclusivo usado para identificar o servidor de gestão de backup relacionado com a entidade de armazenamento |
| BackupManagementType           | Texto          | Tipo de fornecedor para realizar backup, por exemplo, IaaSVM, FileFolder a que este trabalho pertence |
| DataTransferredInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| JobFailureCode                 | Texto          | Cadeia de código de falha por causa da falha de trabalho    |
| JobOperation                   | Texto          | Operação para que trabalho é executado por exemplo, Backup, Restaurar, Configurar Backup |
| JobOperationSubType            | Texto          | Sub tipo de operação de trabalho. Por exemplo, 'Log', no caso do Trabalho de Backup de Registo |
| Tempo de TrabalhoStartDate               | DateTime      | Data e hora quando o trabalho começou a correr                       |
| Estado da Tarefa                      | Texto          | Estado do trabalho acabado, por exemplo, Concluído, Falhado   |
| JobUniqueId                    | Texto          | ID único para identificar o trabalho                                |
| ProtectedContainerUniqueId     | Texto          | Identificador único do servidor protegido associado ao trabalho |
| RecuperaçãoJobDestination         | Texto          | Destino de um trabalho de recuperação, onde os dados são recuperados   |
| RecuperaçãoJobRPDateTime          | DateTime      | A data, hora em que o ponto de recuperação que está a ser recuperado foi criado |
| RecuperaçãoJobLocation            | Texto          | O local onde o ponto de recuperação que está a ser recuperado foi armazenado |
| RecoveryLocationType           | Texto          | Tipo de Localização de Recuperação                                |
| SchemaVersão                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de trabalho, por exemplo, Ativo, Eliminado |
| VaultUniqueId                  | Texto          | Identificador único do cofre protegido associado ao trabalho |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Esta tabela fornece detalhes sobre campos relacionados com políticas.

| **Campo**                       | **Tipo de Dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador único para o recurso sobre os dados recolhidos. Por exemplo, um ID de recurso de cofre de serviços de recuperação |
| OperationName                   | Texto           | Nome da operação, por exemplo, Política ou PolíticaAssociação |
| Categoria                        | Texto           | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Texto           | Dias da semana em que os backups foram agendados            |
| BackupFrequency                 | Texto           | Frequência com que os backups são executados. Por exemplo, diariamente, semanalmente |
| BackupManagementType            | Texto           | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Texto           | Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável |
| Horários de backup                     | Texto           | Data e hora quando estão agendadas cópias de segurança                     |
| DailyRetentionDuration          | Número Inteiro   | Duração total da retenção em dias para cópias de segurança configuradas      |
| DailyRetentionTimes             | Texto           | Data e hora em que a retenção diária foi configurada            |
| DiffBackupDaysOfTheWeek         | Texto           | Dias da semana para backups diferenciais para SQL em Azure VM Backup |
| DiffBackupFormat                | Texto           | Formato para backups diferenciais para SQL em backup VM Azure   |
| DiffBackupRetentionDuration     | Número Decimal | Duração de retenção para backups diferenciais para SQL em Backup VM Azure |
| DiffBackupTime                  | Hora           | Tempo para backups diferenciais para SQL em Backup VM Azure     |
| LogBackupFrequency              | Número Decimal | Frequência para backups de registo para SQL                            |
| LogBackupRetentionDuration      | Número Decimal | Duração de retenção para backups de registo para SQL em Backup VM Azure |
| Meses de Retenção MensalOfTheMonth  | Texto           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, Primeiro, Último |
| MonthlyRetentionDaysOfTheWeek   | Texto           | Dias da semana selecionados para retenção mensal              |
| Retenção MensalDuration        | Texto           | Duração total da retenção em meses para cópias de segurança configuradas    |
| MensalRetentionFormat          | Texto           | Tipo de configuração para retenção mensal. Por exemplo, diariamente para o dia, semanalmente para a semana |
| Horários mensais de retenção           | Texto           | Data e hora em que a retenção mensal é configurada           |
| Weekseses de Retenção MensalOfTheMonth | Texto           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, Primeiro, Último |
| PolicyName                      | Texto           | Nome da política definida                                   |
| PolicyUniqueId                  | Texto           | ID único para identificar a política                             |
| PolicyTimeZone                  | Texto           | Timezone em que os campos de tempo da política são especificados nos registos |
| RetençãoDuration               | Texto           | Duração de retenção para backups configurados                    |
| Tipo de Retenção                   | Texto           | Tipo de retenção                                            |
| SchemaVersão                   | Texto           | Este campo denota a versão atual do esquema, é **V2** |
| Estado                           | Texto           | Estado atual do objeto político. Por exemplo, Ative, Eliminado |
| SincronizaçãoFrequênciaPerDay  | Número Inteiro   | Número de vezes num dia uma cópia de segurança de ficheiros é sincronizada para SC DPM e MABS |
| VaultUniqueId                   | Texto           | ID único do cofre a que esta política pertence          |
| WeeklyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para retenção semanal               |
| SemanalRetentionDuration         | Número Decimal | Duração total da retenção semanal em semanas para backups configurados |
| Horários semanais de retenção            | Texto           | Data e hora em que a retenção semanal é configurada            |
| AnualmenteRetentionDaysOfTheMonth   | Texto           | Datas do mês selecionadas para retenção anual             |
| AnualmenteRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para retenção anual               |
| Retenção AnualDuration         | Número Decimal | Duração total da retenção em anos para cópias de segurança configuradas     |
| AnualRetentionFormat           | Texto           | Tipo de configuração para retenção anual, por exemplo, diária para base diurna, semanalmente para a semana |
| AnualretentionMonthsOfTheYear  | Texto           | Meses do ano selecionados para retenção anual             |
| Horários de retenção anual            | Texto           | Data e hora em que a retenção anual é configurada            |
| Semanas de Retenção AnualOfTheMonth  | Texto           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupstorage

Esta tabela fornece detalhes sobre campos relacionados com armazenamento.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Armazenamento ou ArmazenamentoAssociação |
| Categoria                       | Texto          | Este campo representa categoria de dados de diagnóstico empurrados para os registos do Monitor Azure - AddonAzureBackupStorage |
| BackupItemUniqueId             | Texto          | ID único usado para identificar o item de backup para VMs apoiado usando DPM, MABS |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| Trabalho Preferido SobreVolume      | Texto          | Carga de trabalho para a qual este volume é o armazenamento preferido      |
| ProtectedContainerUniqueId     | Texto          | Identificador único do recipiente protegido associado ao item de reserva |
| SchemaVersão                  | Texto          | Versão do esquema. Por exemplo, **V2**                   |
| Estado                          | Texto          | Estado do objeto de item de reserva. Por exemplo, Ative, Eliminado |
| ArmazenamentoAllocatedInMBs          | Número        | Tamanho do armazenamento atribuído pelo item de backup correspondente no armazenamento correspondente do tipo Disco |
| ArmazenamentoConsumedInMBs           | Número        | Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| Nome de armazenamento                    | Texto          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| StorageTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido por entidade de armazenamento     |
| Dispositivo de armazenamento                    | Texto          | Tipo de Armazenamento, por exemplo Cloud, Volume, Disco             |
| StorageUniqueId                | Texto          | ID único usado para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID único usado para identificar o cofre relacionado com a entidade de armazenamento |
| VolumeFriendlyName             | Texto          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="valid-operation-names-for-each-table"></a>Nomes de operação válidos para cada tabela

Cada registo nas tabelas acima tem um **Nome de Operação**associado . Um Nome de Operação descreve o tipo de registo (e também indica quais os campos na tabela que estão povoados para esse registo). Cada tabela (categoria) suporta um ou mais nomes de operação distintos. Abaixo está um resumo dos nomes de operação suportados para cada uma das tabelas acima.

| **Nome de mesa / Categoria**                   | **Nomes de operação apoiados** | **Descrição**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | Representa um registo que contém todos os detalhes de um determinado item de backup, tais como ID, nome, tipo, etc. |
| CoreAzureBackup | BackupItemAssociation | Representa um mapeamento entre um item de reserva e o seu recipiente protegido associado (se aplicável). |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | Representa um mapeamento entre um item de reserva e o seu tamanho frontal. |
| CoreAzureBackup | Protegido | Representa um registo que contém todos os detalhes de um determinado recipiente protegido, tais como ID, nome, tipo, etc. |
| CoreAzureBackup | Proteção DaAssociação DoContainer | Representa um mapeamento entre um recipiente protegido e o cofre utilizado para a sua cópia de segurança. |
| CoreAzureBackup | Cofre | Representa um registo que contém todos os detalhes de um dado cofre, por exemplo. ID, nome, etiquetas, localização, etc. |
| CoreAzureBackup | Ponto de Recuperação | Representa um registo que contém o ponto de recuperação mais antigo e mais recente para um determinado item de reserva. |
| AddonAzureBackupJobs | Tarefa |  Representa um registo que contém todos os detalhes de um dado trabalho. Por exemplo, operação de trabalho, hora de início, estado, etc. |
| AddonAzureBackupAlerts | Alerta | Representa um registo que contém todos os detalhes de um dado alerta. Por exemplo, alertar o tempo de criação, a gravidade, o estado, etc.  |
| AddonAzureBackupstorage | Armazenamento | Representa um registo que contém todos os detalhes de uma determinada entidade de armazenamento. Por exemplo, nome de armazenamento, tipo etc. |
| AddonAzureBackupstorage | ArmazenamentoAssociação | Representa um mapeamento entre um item de backup e o armazenamento total de nuvem consumido pelo item de backup. |
| AddonAzureBackupProtectedInstance | Proteção | Representa um registo que contém a contagem de exemplos protegida para cada recipiente ou elemento de reserva. Para a cópia de segurança Azure VM, a contagem de casos protegidos está disponível ao nível do item de reserva, para outras cargas de trabalho que está disponível ao nível do recipiente protegido. |
| AddonAzureBackupPolicy | Política |  Representa um registo que contém todos os detalhes de uma política de backup e retenção. Por exemplo, ID, nome, definições de retenção, etc. |
| AddonAzureBackupPolicy | PolíticaAssociação | Representa um mapeamento entre um item de backup e a política de backup aplicada ao mesmo. |   

Muitas vezes, terá de realizar juntas entre diferentes tabelas, bem como diferentes conjuntos de registos que fazem parte da mesma tabela (diferenciada pelo Nome da Operação) para obter todos os campos necessários para a sua análise. Consulte as [consultas de amostra](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries) para começar. 

## <a name="next-steps"></a>Passos seguintes

- [Saiba como enviar dados de diagnóstico para registar análises](./backup-azure-diagnostic-events.md)
- [Saiba como escrever consultas em tabelas específicas de recursos](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)
