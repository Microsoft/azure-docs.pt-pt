---
title: Modelo de dados para eventos de diagnóstico de backup azure
description: Este modelo de dados está em referência ao modo específico de enviar eventos de diagnóstico para Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 0713db1cee9d6737ce69cb108f3cb8f81d1eb2ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183573"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre entidades de backup principais, tais como cofres e itens de backup.

| **Campo**                         | **Tipo de Dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre dos Serviços de Recuperação. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual - BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. Por exemplo, CoreAzureBackup. |
| AgenteVersão                      | Texto          | Número de versão do Agente Backup ou do Agente de Proteção (no caso de SC DPM e MABS) |
| Versão AzureBackupAgent           | Texto          | Versão do Agente de Backup Azure no Servidor de Gestão de Backup |
| AzureDataCenter                   | Texto          | Centro de dados onde o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão de aplicação do item de backup                       |
| Nome backupItemFriendly            | Texto          | Nome amigável do item de reserva                             |
| Nome de BackupItem                    | Texto          | Nome do item de reserva                                      |
| BackupItemProtectionState         | Texto          | Estado de Proteção do Item de Backup                          |
| BackupItemFrontEndSize            | Texto          | Tamanho frontal do item de reserva                            |
| BackupItemType                    | Texto          | Tipo de artigo de reserva. Por exemplo: VM, FileFolder             |
| BackupItemUniqueId                | Texto          | Identificador único do item de reserva                         |
| Tipo de servidor de gestão de backup        | Texto          | Tipo de Servidor de Gestão de Backup, como em MABS, SC DPM     |
| BackupManagementServerUniqueid    | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Backup      |
| BackupManagementType              | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| Nome do Servidor de Gestão de Backup        | Texto          | Nome do Servidor de Gestão de Backup                         |
| Versão do BackupManagementServerOS   | Texto          | Versão OS do Servidor de Gestão de Backup                   |
| Versão do Servidor de Gestão de Backup     | Texto          | Versão do Servidor de Gestão de Backup                      |
| ÚltimaS RecoveryPointLocation       | Texto          | Localização do último ponto de recuperação para o item de backup    |
| ÚltimaRecoveryPointTime           | DateTime      | Data do último ponto de recuperação para o item de backup   |
| Localização de Pontode Recuperação Mais Antiga       | Texto          | Localização do ponto de recuperação mais antigo para o item de reserva    |
| Tempo de Recuperação Mais Antigo           | DateTime      | Data do último ponto de recuperação para o item de backup   |
| PolíticaUniqueId                    | Texto          | Id único para identificar a política                             |
| Nome amigável de contentores protegidos    | Texto          | Nome amigável do servidor protegido                        |
| Localização de contentores protegidos        | Texto          | Se o contentor protegido está localizado no local ou em Azure |
| Nome de contentorprotegido            | Texto          | Nome do recipiente protegido                            |
| Recipientes ProtegidosOSType          | Texto          | Tipo OS do recipiente protegido                          |
| Versão ProtectedContainerOSOs       | Texto          | Versão OS do recipiente protegido                        |
| Estado de Proteção de Contentores Protegidos | Texto          | Estado de proteção do contentor protegido                  |
| Recipiente protegidoType            | Texto          | Se o recipiente protegido é um servidor, ou um recipiente  |
| Recipiente protegidoUniqueId        | Texto          | Id único usado para identificar o recipiente protegido para tudo, exceto VMs apoiados usando DPM, MABS |
| Carga de trabalho de contentores protegidos    | Texto          | Tipo de recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| NomeGrupoProteção               | Texto          | Nome do Grupo de Proteção o Item de Backup está protegido em, para SC DPM, e MABS, se aplicável |
| ResourceGroupName                 | Texto          | Grupo de recursos do recurso (por exemplo, cofre de Serviços de Recuperação) para dados que estão a ser recolhidos |
| SchemaVersion                     | Texto          | Este campo denota a versão atual do esquema, é **V2** |
| Estado de Proteção secundária de Backup    | Texto          | Se a proteção secundária está ativada para o item de reserva  |
| Estado                             | Texto          | Estado do objeto de objeto de reserva. Por exemplo, Ativo, Eliminado |
| Tipo de replicação de armazenamento            | Texto          | Tipo de replicação de armazenamento para o cofre. Por exemplo, GeoRedundant |
| SubscriptionId                    | Texto          | Identificador de assinatura do recurso (por exemplo, cofre de Serviços de Recuperação) para o qual os dados são recolhidos |
| Nome do cofre                         | Texto          | Nome do cofre                                            |
| VaultTags                         | Texto          | Etiquetas associadas ao recurso do cofre                    |
| AbóbadaUniqueid                     | Texto          | Identificador único do cofre                             |
| SourceSystem                      | Texto          | Sistema fonte dos dados atuais - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Esta tabela fornece detalhes sobre campos relacionados com alerta.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador único para o recurso sobre quais os dados recolhidos. Por exemplo, um ID de recurso de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, Alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupAlerts |
| Código de Alerta                      | Texto          | Código para identificar exclusivamente um tipo de alerta                     |
| Estado de Consolidação de Alerta       | Texto          | Identifique se o alerta é um alerta consolidado ou não         |
| Hora da data da ocorrência de alerta        | DateTime      | Data e hora quando o alerta foi criado                     |
| Alertaraisedon                  | Texto          | Tipo de entidade em que o alerta é levantado                        |
| AlertasGravidade                  | Texto          | Gravidade do alerta. Por exemplo, Crítico                 |
| Estado de alerta                    | Texto          | Estado do alerta. Por exemplo, Ativo                     |
| Minutos de alerta    | Número        | Tempo necessário para resolver um alerta. Em branco para alertas ativos.     |
| Tipo de alerta                      | Texto          | Tipo de alerta. Por exemplo, Backup                           |
| Alertuniqueid                  | Texto          | Identificador único do alerta gerado                    |
| BackupItemUniqueId             | Texto          | Identificador único do item de reserva associado ao alerta |
| BackupManagementServerUniqueid | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| CondeOfAlertsConsolidado      | Número        | Número de alertas consolidados se se trata de um alerta consolidado  |
| Recipiente protegidoUniqueId     | Texto          | Identificador único do servidor protegido associado ao alerta |
| Ação Recomendada              | Texto          | Ação recomendada para resolver o alerta                      |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Eliminado |
| ArmazenamentoUniqueId                | Texto          | Id único usado para identificar a entidade de armazenamento                |
| AbóbadaUniqueid                  | Texto          | Identificação única usada para identificar o cofre relacionado com o alerta    |
| SourceSystem                   | Texto          | Sistema fonte dos dados atuais - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela fornece campos básicos protegidos relacionados com instâncias.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador único para o recurso sobre quais os dados recolhidos. Por exemplo, um ID de recurso de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Nome da operação, por exemplo, ProtectedInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | Identificação única do item de reserva                                 |
| BackupManagementServerUniqueid | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| Recipiente protegidoUniqueId     | Texto          | Identificação única para identificar o recipiente protegido em que o trabalho é executado |
| Contagem de casos protegidos         | Texto          | Contagem de instâncias protegidas para o item de reserva associado ou recipiente protegido nessa data-data |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado do objeto de cópia de segurança, por exemplo, Ativo, Eliminado |
| AbóbadaUniqueid                  | Texto          | Identificador único do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema fonte dos dados atuais - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre campos relacionados com o trabalho.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Job    |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Texto          | Campo para especificar se o trabalho é Ad Hoc ou Agendado           |
| BackupItemUniqueId             | Texto          | Id único usado para identificar o item de backup relacionado com a entidade de armazenamento |
| BackupManagementServerUniqueid | Texto          | ID único usado para identificar o servidor de gestão de backup relacionado com a entidade de armazenamento |
| BackupManagementType           | Texto          | Tipo de fornecedor para executar backup, por exemplo, IaaSVM, FileFolder a que este alerta pertence |
| DataTransferInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| Código de Insucesso de Emprego                 | Texto          | Cadeia de código de falha por causa da falha de emprego aconteceu    |
| Operação de Emprego                   | Texto          | Operação para a qual o trabalho é executado, por exemplo, Backup, Restaurar, Configurar Backup |
| JobOperationSubType            | Texto          | Sub Tipo da Operação de Trabalho. Por exemplo, 'Log', no caso do Log Backup Job |
| Hora do início do trabalho               | DateTime      | Data e hora quando o trabalho começou a funcionar                       |
| Estado da Tarefa                      | Texto          | Estado do trabalho acabado, por exemplo, Concluído, Falhado   |
| JobUniqueid                    | Texto          | Identificação única para identificar o trabalho                                |
| Recipiente protegidoUniqueId     | Texto          | Identificador único do servidor protegido associado ao alerta |
| RecoveryJobDestination         | Texto          | Destino de um trabalho de recuperação, onde os dados são recuperados   |
| RecoveryJobRPDateTime          | DateTime      | A data, hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobLocation            | Texto          | O local onde o ponto de recuperação que está a ser recuperado foi armazenado |
| Tipo de Localização de Recuperação           | Texto          | Tipo de Local de Recuperação                                |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Eliminado |
| AbóbadaUniqueid                  | Texto          | Identificador único do cofre protegido associado ao alerta |
| SourceSystem                   | Texto          | Sistema fonte dos dados atuais - Azure                    |

## <a name="addonazurebackuppolicy"></a>Política addonAzureBackup

Esta tabela fornece detalhes sobre os domínios relacionados com as políticas.

| **Campo**                       | **Tipo de Dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador único para o recurso sobre quais os dados recolhidos. Por exemplo, um ID de recurso de recurso de cofre de serviços de recuperação |
| OperationName                   | Texto           | Nome da operação, por exemplo, Policy or PolicyAssociation |
| Categoria                        | Texto           | Categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupPolicy |
| BackupDaysOftheWeek             | Texto           | Dias da semana em que os backups foram agendados            |
| BackupFrequência                 | Texto           | Frequência com que são executadas cópias de segurança. Por exemplo, diariamente, semanalmente |
| BackupManagementType            | Texto           | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupManagementServerUniqueid  | Texto           | Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável |
| BackupTimes                     | Texto           | Data e hora quando os backups estão agendados                     |
| Duração da Retenção Diária          | Número Inteiro   | Duração total da retenção em dias para cópias de segurança configuradas      |
| DailyRetentionTimes             | Texto           | Data e hora em que a retenção diária foi configurada            |
| DiffbackupDaysoftheWeek         | Texto           | Dias da semana para backups diferenciais para SQL em Backup VM Azure |
| Formato DiffBackup                | Texto           | Formato para backups diferenciais para SQL em backup VM Azure   |
| Duração da retenção de difusão     | Número Decimal | Duração da retenção para backups diferenciais para SQL em Backup VM Azure |
| DiffBackupTime                  | Hora           | Tempo para backups diferenciais para SQL em Backup VM Azure     |
| LogBackupFrequency              | Número Decimal | Frequência para backups de registo para SQL                            |
| Duração da retenção de logbackup      | Número Decimal | Duração da retenção para backups de registo para SQL em Backup VM Azure |
| MensaisReDaysOfTheMonth  | Texto           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, Primeiro, Último, etc. |
| MensaisReDaysOfTheWeek   | Texto           | Dias da semana selecionados para retenção mensal              |
| Duração mensal da retenção        | Texto           | Duração total da retenção em meses para cópias de segurança configuradas    |
| Formato Mensal de Retenção          | Texto           | Tipo de configuração para retenção mensal. Por exemplo, diariamente para o dia baseado, semanalmente para a semana |
| MensalRetimes           | Texto           | Data e hora em que a retenção mensal é configurada           |
| MensalResWeeksOfTheMonth | Texto           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, Primeiro, Último, etc. |
| PolicyName                      | Texto           | Nome da política definida                                   |
| PolíticaUniqueId                  | Texto           | Id único para identificar a política                             |
| PolicyTimeZone                  | Texto           | Fuso horário em que os campos de tempo de política são especificados nos registos |
| Duração da retenção               | Texto           | Duração da retenção para cópias de segurança configuradas                    |
| RetençãoTipo                   | Texto           | Tipo de retenção                                            |
| SchemaVersion                   | Texto           | Este campo denota versão atual do esquema, é **V2** |
| Estado                           | Texto           | Estado atual do objeto político. Por exemplo, Ativo, Eliminado |
| SincronizaçãoFrequênciaPerDay  | Número Inteiro   | Número de vezes num dia uma cópia de segurança de ficheiros é sincronizada para SC DPM e MABS |
| AbóbadaUniqueid                   | Texto           | Identificação única do cofre que esta política pertence a          |
| SemanalmenteResDays oftheWeek    | Texto           | Dias da semana selecionados para retenção semanal               |
| Duração semanal da retenção         | Número Decimal | Duração total da retenção semanal em semanas para cópias de segurança configuradas |
| SemanalRetimes            | Texto           | Data e hora em que a retenção semanal é configurada            |
| Dias de Retenção AnualDoTheMonth   | Texto           | Datas do mês selecionadas para retenção anual             |
| Dias de Retenção AnualDaSemana    | Texto           | Dias da semana selecionados para retenção anual               |
| Duração anual da retenção         | Número Decimal | Duração total da retenção em anos para cópias de segurança configuradas     |
| Formato Anual de Retenção           | Texto           | Tipo de configuração para retenção anual, por exemplo, diariamente para dia baseado, semanalmente para a semana |
| Retenções AnuaisMeseSDo Ano  | Texto           | Meses do ano selecionados para retenção anual             |
| Ano da RetençãoTimes            | Texto           | Data e hora em que a retenção anual é configurada            |
| Semanas de Retenção AnualDoTheMonth  | Texto           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema fonte dos dados atuais - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Esta tabela fornece detalhes sobre campos relacionados com armazenamento.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Storage or StorageAssociation |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico empurrados para registos do Monitor Azure - AddonAzureBackupStorage |
| BackupItemUniqueId             | Texto          | Id único usado para identificar o item de backup para VMs apoiado usando DPM, MABS |
| BackupManagementServerUniqueid | Texto          | Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável |
| BackupManagementType           | Texto          | Tipo de fornecedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| Volume de Carga seleção preferencial      | Texto          | Carga de trabalho para a qual este volume é o armazenamento preferido      |
| Recipiente protegidoUniqueId     | Texto          | Identificador único do servidor protegido associado ao alerta |
| SchemaVersion                  | Texto          | Versão do esquema. Por exemplo, **V2**                   |
| Estado                          | Texto          | Estado do objeto de objeto de reserva. Por exemplo, Ativo, Eliminado |
| ArmazenamentoAllocatedInMBs          | Número        | Tamanho do armazenamento atribuído pelo produto de reserva correspondente no armazenamento correspondente do tipo Disk |
| ArmazenamentoConsumidoInMBs           | Número        | Tamanho do armazenamento consumido pelo produto de reserva correspondente no armazenamento correspondente |
| Nome de armazenamento                    | Texto          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| ArmazenamentoTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido por entidade de armazenamento     |
| Tipo de armazenamento                    | Texto          | Tipo de Armazenamento, por exemplo Cloud, Volume, Disco             |
| ArmazenamentoUniqueId                | Texto          | Id único usado para identificar a entidade de armazenamento                |
| AbóbadaUniqueid                  | Texto          | Identificação única usada para identificar o cofre relacionado com a entidade de armazenamento |
| VolumeFriendlyName             | Texto          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema fonte dos dados atuais - Azure                    |

## <a name="next-steps"></a>Passos seguintes

- [Saiba como enviar dados de diagnóstico para Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Saiba escrever consultas em tabelas específicas do Recurso](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)
