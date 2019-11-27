---
title: Compreendendo a configuração de backup periódico no Azure Service Fabric | Microsoft Docs
description: Use o recurso de backup e restauração periódicos do Service Fabric para habilitar o backup de dados periódicos dos dados do seu aplicativo.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAA45B4A-0258-4CB3-A825-7E8F70F28401
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: e0c40c005c27130d422e0dacaae29461b65b7df7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232504"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Compreendendo a configuração de backup periódico no Azure Service Fabric

Configurar o backup periódico de seus serviços confiáveis com estado ou Reliable Actors consiste nas seguintes etapas:

1. **Criação de políticas de backup**: nesta etapa, uma ou mais políticas de backup são criadas dependendo dos requisitos.

2. **Habilitando o backup**: nesta etapa, você associa as políticas de backup criadas na **etapa 1** para as entidades, o _aplicativo_, o _serviço_ou uma _partição_necessária.

## <a name="create-backup-policy"></a>Criar política de backup

Uma política de backup consiste nas seguintes configurações:

* **Restauração automática de perda de dados**: especifica se a restauração deve ser iniciada automaticamente usando o backup mais recente disponível, caso a partição apresente um evento de perda de dados.

* **Máximo de backups incrementais**: define o número máximo de backups incrementais a serem feitos entre dois backups completos. Backups incrementais máximos especificam o limite superior. Um backup completo pode ser feito antes de o número especificado de backups incrementais ser concluído em uma das seguintes condições

    1. A réplica nunca fez um backup completo, pois se tornou primária.

    2. Alguns dos registros de log desde o último backup foram truncados.

    3. A réplica ultrapassou o limite de MaxAccumulatedBackupLogSizeInMB.

* **Agendamento de backup**: a hora ou a frequência na qual fazer backups periódicos. É possível agendar backups para serem recorrentes no intervalo especificado ou em um horário fixo diário/semanal.

    1. **Agendamento de backup baseado em frequência**: esse tipo de agendamento deve ser usado se a necessidade for fazer o backup de dados em intervalos fixos. O intervalo de tempo desejado entre dois backups consecutivos é definido usando o formato ISO8601. O agendamento de backup baseado em frequência dá suporte à resolução de intervalo para o minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Agendamento de backup baseado em tempo**: esse tipo de agendamento deve ser usado se a necessidade for fazer o backup de dados em horários específicos do dia ou da semana. O tipo de frequência de agendamento pode ser diário ou semanal.
        1. **Agendamento de backup com base em tempo _diário_** : esse tipo de agendamento deve ser usado se a ID necessária para fazer o backup de dados em horários específicos do dia. Para especificar isso, defina `ScheduleFrequencyType` como _Daily_; e definir `RunTimes` para a lista de tempo desejado durante o dia no formato ISO8601, a data especificada junto com o tempo será ignorada. Por exemplo, `0001-01-01T18:00:00` representa _6:00 PM_ diariamente, ignorando a parte da data _0001-01-01_. O exemplo abaixo ilustra a configuração para disparar o backup diário às _9:00 a.m._ e _6:00 PM_ diariamente.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Daily",
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

        2. **Agendamento de backup com base em tempo _semanal_** : esse tipo de agendamento deve ser usado se a ID necessária para fazer o backup de dados em horários específicos do dia. Para especificar isso, defina `ScheduleFrequencyType` como _semanal_; Defina `RunDays` como lista de dias em uma semana quando o backup precisar ser disparado e definido `RunTimes` para a lista de tempo desejado durante o dia no formato ISO8601, a data especificada junto com o tempo será ignorada. Lista de dias da semana em que o backup periódico será disparado. O exemplo abaixo ilustra a configuração para disparar o backup diário às _9:00 am_ e _6:00 PM_ durante segunda-feira a sexta-feira.

            ```json
            {
                "ScheduleKind": "TimeBased",
                "ScheduleFrequencyType": "Weekly",
                "RunDays": [
                   "Monday",
                   "Tuesday",
                   "Wednesday",
                   "Thursday",
                   "Friday"
                ],
                "RunTimes": [
                  "0001-01-01T09:00:00Z",
                  "0001-01-01T18:00:00Z"
                ]
            }
            ```

* **Armazenamento de backup**: especifica o local para carregar os backups. O armazenamento pode ser o repositório de blob do Azure ou o compartilhamento de arquivos.
    1. **Repositório de blob do Azure**: esse tipo de armazenamento deve ser selecionado quando a necessidade é armazenar backups gerados no Azure. Os clusters _autônomos_ e _baseados no Azure_ podem usar esse tipo de armazenamento. A descrição para esse tipo de armazenamento requer uma cadeia de conexão e o nome do contêiner em que os backups precisam ser carregados. Se o contêiner com o nome especificado não estiver disponível, ele será criado durante o carregamento de um backup.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Compartilhamento de arquivos**: esse tipo de armazenamento deve ser selecionado para clusters _autônomos_ quando a necessidade é armazenar o backup de dados no local. A descrição para esse tipo de armazenamento requer o caminho de compartilhamento de arquivos em que os backups precisam ser carregados. O acesso ao compartilhamento de arquivos pode ser configurado usando uma das seguintes opções
        1. _Autenticação integrada do Windows_, em que o acesso ao compartilhamento de arquivos é fornecido a todos os computadores pertencentes ao Cluster Service Fabric. Nesse caso, defina os campos a seguir para configurar o armazenamento de backup baseado em _compartilhamento de arquivos_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Proteger o compartilhamento de arquivos usando o nome de usuário e a senha_, onde o acesso ao compartilhamento de arquivos é fornecido a usuários específicos. A especificação de armazenamento de compartilhamento de arquivos também fornece a capacidade de especificar o nome de usuário secundário e a senha secundária para fornecer credenciais de fallback no caso de a autenticação falhar com o nome de usuário primário e a senha primária. Nesse caso, defina os campos a seguir para configurar o armazenamento de backup baseado em _compartilhamento de arquivos_ .

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore",
                "PrimaryUserName": "backupaccount",
                "PrimaryPassword": "<Password for backupaccount>",
                "SecondaryUserName": "backupaccount2",
                "SecondaryPassword": "<Password for backupaccount2>"
            }
            ```

> [!NOTE]
> Verifique se a confiabilidade do armazenamento atende ou excede os requisitos de confiabilidade dos dados de backup.
>

* **Política de retenção**: especifica a política para reter backups no armazenamento configurado. Há suporte apenas para a política de retenção básica.
    1. **Política de retenção básica**: essa política de retenção permite garantir a utilização de armazenamento ideal removendo arquivos de backup que não são mais necessários. `RetentionDuration` pode ser especificado para definir o período de tempo para o qual os backups devem ser retidos no armazenamento. `MinimumNumberOfBackups` é um parâmetro opcional que pode ser especificado para garantir que o número especificado de backups seja sempre mantido, independentemente do `RetentionDuration`. O exemplo abaixo ilustra a configuração para reter backups por _10_ dias e não permite que o número de backups fique abaixo de _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Habilitar backup periódico
Depois de definir a política de backup para atender aos requisitos de backup de dados, a política de backup deve ser apropriadamente associada a um _aplicativo_ou _serviço_ou uma _partição_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagação hierárquica da política de backup
No Service Fabric, a relação entre o aplicativo, o serviço e as partições é hierárquica, conforme explicado no [modelo de aplicativo](./service-fabric-application-model.md). A política de backup pode ser associada a um _aplicativo_, _serviço_ou uma _partição_ na hierarquia. A política de backup propaga hierarquicamente para o próximo nível. Supondo que haja apenas uma política de backup criada e associada a um _aplicativo_, todas as partições com estado que pertencem a todos os _serviços confiáveis com estado_ e _Reliable Actors_ do _aplicativo_ serão submetidas a backup usando o política de backup. Ou, se a política de backup estiver associada a um _serviço com estado confiável_, todas as suas partições serão submetidas a backup usando a política de backup.

### <a name="overriding-backup-policy"></a>Substituindo política de backup
Pode haver um cenário em que o backup de dados com o mesmo agendamento de backup é necessário para todos os serviços do aplicativo, exceto para serviços específicos em que a necessidade é fazer o backup de dados usando uma agenda de frequência mais alta ou fazer backup em uma conta de armazenamento diferente ou FileShare. Para resolver esses cenários, o serviço de restauração de backup fornece recursos para substituir a política propagada no escopo do serviço e da partição. Quando a política de backup está associada ao _serviço_ ou à _partição_, ela substitui a política de backup propagada, se houver.

### <a name="example"></a>Exemplo

Este exemplo usa a instalação com dois aplicativos, _MyApp_A_ e _MyApp_B_. O aplicativo _MyApp_A_ contém dois serviços confiáveis com estado, _SvcA1_ & _SvcA3_e um serviço de ator confiável, _ActorA2_. _SvcA1_ contém três partições, enquanto _ActorA2_ e _SvcA3_ contêm duas partições cada.  O aplicativo _MyApp_B_ contém três serviços confiáveis com estado, _SvcB1_, _SvcB2_e _SvcB3_. _SvcB1_ e _SvcB2_ contêm duas partições, cada uma enquanto _SvcB3_ contém três partições.

Suponha que os requisitos de backup de dados dos aplicativos sejam os seguintes

1. MyApp_A
    1. Crie backup diário de dados para todas as partições de todos os _serviços confiáveis com estado_ e _Reliable Actors_ pertencentes ao aplicativo. Carregar dados de backup para o local _BackupStore1_.

    2. Um dos serviços, _SvcA3_, requer o backup de dados a cada hora.

    3. O tamanho dos dados na partição _SvcA1_P2_ é mais do que o esperado e seus dados de backup devem ser armazenados em um local de armazenamento diferente _BackupStore2_.

2. MyApp_B
    1. Crie backup de dados todos os domingos às 8:00 para todas as partições do serviço _SvcB1_ . Carregar dados de backup para o local _BackupStore1_.

    2. Crie backup de dados todos os dias às 8:00 para a partição _SvcB2_P1_. Carregar dados de backup para o local _BackupStore1_.

Para atender a esses requisitos de backup de dados, as políticas de backup BP_1 para BP_5 são criadas e o backup é habilitado da seguinte maneira.
1. MyApp_A
    1. Crie a política de backup, _BP_1_, com o agendamento de backup baseado em frequência, em que Frequency é definido como 24 horas. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Habilite esta política para _MyApp_A_ de aplicativos usando a API de [backup de aplicativo](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) . Essa ação permite o backup de dados usando a política de backup _BP_1_ para todas as partições de _serviços confiáveis com estado_ e _Reliable Actors_ pertencentes ao _MyApp_A_de aplicativos.

    2. Crie a política de backup, _BP_2_, com o agendamento de backup baseado em frequência, em que Frequency está definida como 1 h. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Habilite esta política para o serviço _SvcA3_ usando a API de [backup do serviço de habilitação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) . Esta ação substitui a política propagada _BP_1_ pela política de backup habilitada explicitamente _BP_2_ para todas as partições de serviço _SvcA3_ que levam ao backup de dados usando a política de backup _BP_2_ para essas partições.

    3. Crie a política de backup, _BP_3_, com o agendamento de backup baseado em frequência, em que Frequency é definido como 24 horas. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore2_. Habilite esta política para _SvcA1_P2_ de partição usando habilitar API de [backup de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Esta ação substitui a política propagada _BP_1_ pela política de backup habilitada explicitamente _BP_3_ para _SvcA1_P2_de partição.

2. MyApp_B
    1. Crie a política de backup, _BP_4_, com o agendamento de backup baseado em tempo em que o tipo de frequência de agendamento está definido como semanal, a execução de dias é definida como domingo e os tempos de execução são definidos como 8:00 am. Armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Habilite esta política para o serviço _SvcB1_ usando a API de [backup do serviço de habilitação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) . Essa ação permite o backup de dados usando a política de backup _BP_4_ para todas as partições do serviço _SvcB1_.

    2. Crie a política de backup, _BP_5_, com o agendamento de backup baseado em tempo em que o tipo de frequência de agendamento é definido como diário e tempos de execução são definidos como 8:00 am. Armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Habilite esta política para _SvcB2_P1_ de partição usando habilitar API de [backup de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) . Essa ação permite o backup de dados usando a política de backup _BP_5_ para _SvcB2_P1_de partição.

O diagrama a seguir ilustra as políticas de backup habilitadas explicitamente e as políticas de backup propagadas.

![Hierarquia de aplicativo Service Fabric][0]

## <a name="disable-backup"></a>Desabilitar backup
As políticas de backup podem ser desabilitadas quando não há necessidade de fazer backup de dados. A política de backup habilitada em um _aplicativo_ só pode ser desabilitada no mesmo _aplicativo_ usando a API de [backup do aplicativo](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) de desabilitação, a política de backup habilitada em um _serviço_ pode ser desabilitada no mesmo _serviço_ usando [desabilitar ](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup)A API de backup do serviço e a política de backup habilitada em uma _partição_ podem ser desabilitadas na mesma _partição_ usando desabilitar API de [backup de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) .

* A desabilitação da política de backup para um _aplicativo_ interrompe todos os backups de dados periódicos que ocorrem como resultado da propagação da política de backup para partições de serviço confiáveis ou partições de ator confiáveis.

* Desabilitar a política de backup para um _serviço_ interrompe todos os backups de dados periódicos que ocorrem como resultado da propagação dessa política de backup para as partições do _serviço_.

* A desabilitação da política de backup para uma _partição_ interrompe todo o backup de dados periódicos ocorridos devido à política de backup na partição.

* Ao desabilitar o backup de uma entidade (aplicativo/serviço/partição), `CleanBackup` pode ser definido como _true_ para excluir todos os backups no armazenamento configurado.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Suspender & retomar backup
Determinada situação pode exigir uma suspensão temporária de dados periódicos de backup. Nessa situação, dependendo do requisito, a API de suspensão de backup pode ser usada em um _aplicativo_, _serviço_ou _partição_. A suspensão de backup periódico é transitiva sobre a subárvore da hierarquia do aplicativo do ponto em que é aplicada. 

* Quando a suspensão é aplicada a um _aplicativo_ usando a API de [backup do aplicativo de suspensão](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) , todos os serviços e partições desse aplicativo são suspensos para backup periódico de dados.

* Quando a suspensão é aplicada a um _serviço_ usando a API de [backup do serviço de suspensão](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) , todas as partições nesse serviço são suspensas para backup periódico de dados.

* Quando a suspensão é aplicada a uma _partição_ usando a API de [backup de partição de suspensão](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) , ela suspende as partições nesse serviço são suspensas para backup periódico de dados.

Quando a necessidade de suspensão terminar, o backup de dados periódicos poderá ser restaurado usando a API de backup de retomada. O backup periódico deve ser retomado no mesmo _aplicativo_, _serviço_ou _partição_ em que foi suspenso.

* Se a suspensão tiver sido aplicada a um _aplicativo_, ela deverá ser retomada usando a API de [backup do aplicativo de retomada](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) . 

* Se a suspensão tiver sido aplicada a um _serviço_, ela deverá ser retomada usando a API de [backup do serviço de retomada](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) .

* Se a suspensão tiver sido aplicada a uma _partição_, ela deverá ser retomada usando a API de [backup da partição de retomada](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) .

### <a name="difference-between-suspend-and-disable-backups"></a>Diferença entre suspender e desabilitar backups
Desabilitar o backup deve ser usado quando os backups não forem mais necessários para um determinado aplicativo, serviço ou partição. É possível invocar desabilitar solicitação de backup junto com o parâmetro limpar backups para ser verdadeiro, o que significa que todos os backups existentes também são excluídos. No entanto, a suspensão deve ser usada em cenários em que um deseja desativar os backups temporariamente, como quando o disco local fica cheio ou o carregamento do backup está falhando devido a um problema de rede conhecido, etc. 

Embora Disable possa ser invocado apenas em um nível que anteriormente foi habilitado para backup explicitamente, no entanto, a suspensão pode ser aplicada em qualquer nível que esteja atualmente habilitado para backup diretamente ou por meio de herança/hierarquia. Por exemplo, se o backup estiver habilitado em um nível de aplicativo, só poderá invocar Disable no nível do aplicativo, mas Suspend pode ser invocado no aplicativo, em qualquer serviço ou partição sob esse aplicativo. 

## <a name="auto-restore-on-data-loss"></a>Restauração automática de perda de dados
A partição de serviço pode perder dados devido a falhas inesperadas. Por exemplo, o disco de duas de três réplicas para uma partição (incluindo a réplica primária) é corrompido ou apagado.

Quando Service Fabric detecta que a partição está em perda de dados, ela invoca `OnDataLossAsync` método de interface na partição e espera que a partição execute a ação necessária para sair da perda de dados. Nessa situação, se a política de backup efetivo na partição tiver `AutoRestoreOnDataLoss` sinalizador definido como `true`, a restauração será disparada automaticamente usando o backup mais recente disponível para esta partição.

## <a name="get-backup-configuration"></a>Obter configuração de backup
APIs separadas são disponibilizadas para obter informações de configuração de backup em um _aplicativo_, _serviço_e escopo de _partição_ . [Obter](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)informações de configuração de backup do aplicativo, [obter informações de configuração de backup do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)e [obter informações de configuração de backup de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) são essas APIs, respectivamente. Principalmente, essas APIs retornam a política de backup aplicável, o escopo no qual a política de backup é aplicada e os detalhes de suspensão de backup. Veja a seguir uma breve descrição sobre os resultados retornados dessas APIs.

- Informações de configuração de backup do aplicativo: fornece os detalhes da política de backup aplicada no aplicativo e todas as políticas substituída em serviços e partições que pertencem ao aplicativo. Ele também inclui as informações de suspensão para o aplicativo e serviços de ti e partições.

- Informações de configuração de backup de serviço: fornece os detalhes da política de backup em vigor no serviço e o escopo no qual essa política foi aplicada e todas as políticas substituída em suas partições. Ele também inclui as informações de suspensão para o serviço e suas partições.

- Informações de configuração de backup de partição: fornece os detalhes da política de backup efetivo na partição e o escopo no qual essa política foi aplicada. Ele também inclui as informações de suspensão para as partições.

## <a name="list-available-backups"></a>Listar backups disponíveis

Os backups disponíveis podem ser listados usando a API obter lista de backup. O resultado da chamada à API inclui itens de informações de backup relacionados a todos os backups disponíveis no armazenamento de backup, que é configurado na política de backup aplicável. Variantes diferentes dessa API são fornecidas para listar os backups disponíveis que pertencem a um aplicativo, serviço ou partição. Essas APIs dão suporte à obtenção do backup _mais recente_ disponível de todas as partições aplicáveis ou à filtragem de backups com base na _data de início_ e na _data de término_.

Essas APIs também dão suporte à paginação dos resultados, quando o parâmetro _MaxResults_ é definido como um inteiro positivo diferente de zero, a API retorna o máximo de itens de informações de backup do _MaxResults_ . No caso, há mais itens de informações de backup disponíveis do que o valor _MaxResults_ , então um token de continuação é retornado. O parâmetro de token de continuação válido pode ser usado para obter o próximo conjunto de resultados. Quando um valor de token de continuação válido é passado para a próxima chamada da API, a API retorna o próximo conjunto de resultados. Nenhum token de continuação é incluído na resposta quando todos os resultados disponíveis são retornados.

Veja a seguir as informações breves sobre as variantes com suporte.

- [Obter lista de backup do aplicativo](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): retorna uma lista de backups disponíveis para cada partição que pertence a um determinado aplicativo Service Fabric.

- [Obter lista de backup de serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): retorna uma lista de backups disponíveis para cada partição que pertence a determinado serviço de Service Fabric.
 
- [Obter lista de backup de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): retorna uma lista de backups disponíveis para a partição especificada.

## <a name="next-steps"></a>Passos seguintes
- [Referência da API REST de restauração de backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
