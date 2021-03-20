---
title: Compreender a configuração da cópia de segurança periódica
description: Utilize a cópia de segurança periódica do Service Fabric e a funcionalidade de restauro para configurar a cópia de segurança periódica dos seus serviços estatais confiáveis ou de atores fidedignicos.
ms.topic: article
ms.date: 2/01/2019
ms.openlocfilehash: 2607502af44b178131820d78f23bcdf4e32454a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018890"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Compreensão da configuração de backup periódica no Tecido de Serviço Azure

Configurar a cópia de segurança periódica dos seus serviços estatais confiáveis ou atores fiáveis consiste nos seguintes passos:

1. **Criação de políticas de backup**: Neste passo, uma ou mais políticas de backup são criadas em função dos requisitos.

2. **Ativar o backup**: Neste passo, associa as políticas de backup criadas no **Passo 1** às entidades requeridas, _Aplicação,_ _Serviço_ ou _Partição_.

## <a name="create-backup-policy"></a>Criar Política de Backup

Uma política de backup consiste nas seguintes configurações:

* **Restauração automática na perda de dados**: Especifica se deve desencadear a restauração automaticamente utilizando a cópia de segurança mais recente disponível no caso de a partição experimentar um evento de perda de dados.
> [!NOTE]
> Recomenda-se não definir auto-restauração em clusters de produção
>

* **Cópias de segurança incrementais máximas**: Define o número máximo de cópias de segurança incrementais a serem tomadas entre duas cópias de segurança completas. Cópias de segurança incrementais max especificam o limite superior. Uma cópia de segurança completa pode ser tomada antes de o número especificado de cópias de segurança incrementais ser concluído numa das seguintes condições

    1. A réplica nunca teve uma cópia de segurança completa desde que se tornou primária.

    2. Alguns dos registos desde que o último backup foi truncado.

    3. Réplica passou o limite MaxAccumulatedBackupLogSizeInMB.

* **Horário de reserva**: A hora ou a frequência para então fazer cópias de segurança periódicas. Pode-se agendar cópias de segurança para serem recorrentes num intervalo especificado ou num horário fixo diariamente/semanalmente.

    1. **Horário de backup baseado em frequências**: Este tipo de programação deve ser utilizado se for necessário fazer cópia de segurança de dados em intervalos fixos. O intervalo de tempo desejado entre duas cópias de segurança consecutivas é definido utilizando o formato ISO8601. O horário de backup baseado em frequências suporta a resolução de intervalos ao minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Horário de backup baseado no tempo**: Este tipo de programação deve ser utilizado se for necessário fazer cópia de segurança de dados em horas específicas do dia ou da semana. O tipo de frequência de programação pode ser diário ou semanal.
        1. **_Agenda_ de backup baseada no tempo diário**: Este tipo de programação deve ser usado se for necessário obter cópia de segurança de dados em horas específicas do dia. Para especificar isto, definido `ScheduleFrequencyType` para _Daily;_ e definir `RunTimes` para listar a hora desejada durante o dia no formato ISO8601, a data especificada juntamente com o tempo será ignorada. Por exemplo, `0001-01-01T18:00:00` representa _18:00 todos_ os dias, ignorando a data _parte 0001-01-01_. Abaixo o exemplo ilustra a configuração para acionar a cópia de segurança diária às _9:00_ e _18:00 todos_ os dias.

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

        2. **_Horário semanal_ de backup baseado em tempo**: Este tipo de programação deve ser usado se for necessário obter cópia de segurança de dados em horas específicas do dia. Para especificar isto, definido `ScheduleFrequencyType` para _Weekly_; definir para lista de dias numa semana em que a cópia de `RunDays` segurança precisa ser ativada e definida para a `RunTimes` lista de tempo desejado durante o dia no formato ISO8601, a data especificada juntamente com o tempo será ignorada. Lista de dias de uma semana para desencadear a cópia de segurança periódica. Abaixo o exemplo ilustra a configuração para ativar o backup diário às _9:00_ e _18:00_ pm durante segunda a sexta-feira.

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

* **Armazenamento de cópia de segurança**: Especifica a localização para carregar cópias de segurança. O armazenamento pode ser ou a loja de blob Azure ou a partilha de ficheiros.
    1. **Loja blob Azure**: Este tipo de armazenamento deve ser selecionado quando a necessidade é armazenar cópias de segurança geradas em Azure. Tanto os agrupamentos _autónomos_ como os _baseados em Azure_ podem utilizar este tipo de armazenamento. A descrição deste tipo de armazenamento requer o fio de ligação e o nome do recipiente onde as cópias de segurança precisam de ser carregadas. Se o recipiente com o nome especificado não estiver disponível, então é criado durante o upload de uma cópia de segurança.

        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

        > [!NOTE]
        > Backup restaurar Serviço não funciona com armazenamento V1 Azure
        >

    2. **Partilha de ficheiros**: Este tipo de armazenamento deve ser selecionado para clusters _autónomos_ quando a necessidade é armazenar a cópia de segurança dos dados no local. A descrição deste tipo de armazenamento requer uma trajetória de partilha de ficheiros onde as cópias de segurança precisam de ser carregadas. O acesso à partilha de ficheiros pode ser configurado usando uma das seguintes opções
        1. _Autenticação integrada do Windows,_ onde o acesso à partilha de ficheiros é fornecido a todos os computadores pertencentes ao cluster De Tecido de Serviço. Neste caso, desconfie de campos seguintes para configurar o armazenamento de backup baseado em _partilha de ficheiros._

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Proteger a partilha de ficheiros utilizando o nome de utilizador e a palavra-passe,_ onde o acesso à partilha de ficheiros é fornecido a utilizadores específicos. A especificação de armazenamento de partilha de ficheiros também fornece a capacidade de especificar o nome de utilizador secundário e a palavra-passe secundária para fornecer credenciais de recuo no caso de a autenticação falhar com o nome de utilizador primário e a palavra-passe primária. Neste caso, desconfie de campos seguintes para configurar o armazenamento de backup baseado em _partilha de ficheiros._

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
> Certifique-se de que a fiabilidade do armazenamento satisfaz ou excede os requisitos de fiabilidade dos dados de backup.
>

* **Política de Retenção**: Especifica a política de retenção de cópias de segurança no armazenamento configurado. Apenas a Política Básica de Retenção é apoiada.
    1. **Política básica de retenção**: Esta política de retenção permite garantir uma utilização ótima do armazenamento removendo ficheiros de backup que já não são necessários. `RetentionDuration` pode ser especificado para definir o tempo para o qual as cópias de segurança são necessárias para serem mantidas no armazenamento. `MinimumNumberOfBackups` é um parâmetro opcional que pode ser especificado para garantir que o número especificado de cópias de segurança são sempre mantidos independentemente do `RetentionDuration` . Abaixo o exemplo ilustra a configuração para reter backups durante _10_ dias e não permite que o número de backups desça abaixo de _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Ativar a cópia de segurança periódica
Depois de definir a política de backup para cumprir os requisitos de backup de dados, a política de backup deve ser adequadamente associada a uma _aplicação_, ou _serviço,_ ou a uma _partição_.

> [!NOTE]
> Certifique-se de que não existem atualizações de aplicações em andamento antes de permitir o backup
>

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagação hierárquica da política de backup
No Tecido de Serviço, a relação entre aplicação, serviço e partições é hierárquica como explicado no [modelo de Aplicação.](./service-fabric-application-model.md) A política de backup pode ser associada a uma _aplicação,_ _serviço_ ou a uma _divisória_ na hierarquia. A política de backup propaga-se hierárquicamente para o próximo nível. Assumindo que existe apenas uma política de backup criada e associada a uma _aplicação_, todas as divisórias estatais pertencentes a todos os _serviços estatais confiáveis_ e _atores fiáveis_ da _aplicação_ serão apoiadas usando a política de backup. Ou se a política de backup estiver associada a um _serviço estatal fiável,_ todas as suas divisórias serão apoiadas utilizando a política de backup.

### <a name="overriding-backup-policy"></a>Política de backup dominante
Pode haver um cenário em que a cópia de segurança dos dados com o mesmo horário de backup é necessária para todos os serviços da aplicação, exceto para serviços específicos em que a necessidade é ter cópia de segurança de dados usando um horário de frequência mais elevado ou levar backup para uma conta de armazenamento diferente ou partilha de ficheiros. Para resolver estes cenários, o serviço de restauro de backup fornece facilidades para anular a política propagada no âmbito de serviço e partição. Quando a política de backup está associada ao _serviço_ ou _à partilha,_ substitui a política de backup propagada, se houver.

### <a name="example"></a>Exemplo

Este exemplo utiliza a configuração com duas aplicações, _MyApp_A_ e _MyApp_B_. A _aplicação MyApp_A_ contém dois serviços estatais confiáveis, _SvcA1_  &  _SvcA3,_ e um serviço de ator fiável, _ActorA2_. _O SvcA1_ contém três divisórias, enquanto _o ActorA2_ e _o SvcA3_ contêm duas divisórias cada.  A _aplicação MyApp_B_ contém três serviços estatais fiáveis, _SvcB1,_ _SvcB2_ e _SvcB3_. _SvcB1_ e _SvcB2_ contém duas divisórias cada, enquanto _o SvcB3_ contém três divisórias.

Assuma que os requisitos de backup de dados destas aplicações são os seguintes

1. MyApp_A
    1. Crie uma cópia de segurança diária dos dados para todas as divisórias de todos os _serviços fiáveis_ e _de atores fidedigtos_ pertencentes à aplicação. Faça upload de dados de backup para a localização _BackupStore1_.

    2. Um dos serviços, o _SvcA3,_ requer cópia de segurança de dados a cada hora.

    3. O tamanho dos dados na _partição SvcA1_P2_ é mais do que o esperado e os seus dados de backup devem ser armazenados em diferentes locais de armazenamento _BackupStore2_.

2. MyApp_B
    1. Crie cópia de segurança de dados todos os domingos às 8:00 para todas as divisórias do serviço _SvcB1._ Faça upload de dados de backup para a localização _BackupStore1_.

    2. Crie cópia de segurança de dados todos os dias às 8:00 da manhã para _SvcB2_P1_ de partição . Faça upload de dados de backup para a localização _BackupStore1_.

Para responder a estes requisitos de backup de dados, as políticas de backup BP_1 para BP_5 são criadas e a cópia de segurança é ativada da seguinte forma.
1. MyApp_A
    1. Crie uma política de backup, _BP_1,_ com um horário de backup baseado em frequências onde a frequência é definida para 24 Hrs. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Ative esta política para _MyApp_A_ de aplicação utilizando [a API de backup de aplicações.](/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Esta ação permite a cópia de segurança de dados utilizando _BP_1_ de política de backup para todas as divisórias de _serviços estatais fiáveis_ e _atores fiáveis_ pertencentes à aplicação _MyApp_A_.

    2. Crie uma política de backup, _BP_2,_ com um horário de backup baseado em frequências onde a frequência é definida para 1 Hrs. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore1_. Ative esta política de serviço _SvcA3_ utilizando [Enable Service Backup](/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação substitui a política propagada _BP_1,_ permitindo explicitamente _BP_2_ de política de backup para todas as divisórias de serviço _SvcA3,_ levando a uma cópia de segurança de dados utilizando _BP_2_ de política de backup para estas divisórias.

    3. Crie uma política de backup, _BP_3,_ com um horário de backup baseado em frequências onde a frequência é definida para 24 Hrs. e armazenamento de backup configurado para usar o local de armazenamento _BackupStore2_. Ative esta política de _partição SvcA1_P2_ utilizando ativar a API [de backup de partição.](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Esta ação substitui a política propagada _BP_1,_ permitindo explicitamente _BP_3_ de política de backup para _SvcA1_P2 de_ partilha.

2. MyApp_B
    1. Crie uma política de backup, _BP_4,_ com horário de backup baseado no tempo, onde o tipo de frequência do horário está definido para semanalmente, os dias de execução estão definidos para domingo, e os tempos de execução estão definidos para as 8:00 am. Armazenamento de cópia de segurança configurado para utilizar o local de armazenamento _BackupStore1_. Ative esta política de serviço _SvcB1_ utilizando [Enable Service Backup](/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação permite a cópia de segurança dos dados utilizando _BP_4_ de política de backup para todas as divisórias de serviço _SvcB1_.

    2. Crie uma política de backup, _BP_5,_ com horário de backup baseado no tempo, onde o tipo de frequência do horário é definido para diariamente e os tempos de execução estão definidos para as 8:00 da manhã. Armazenamento de cópia de segurança configurado para utilizar o local de armazenamento _BackupStore1_. Ative esta política de _partição SvcB2_P1_ utilizando ativar a API [de backup de partição.](/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Esta ação permite a cópia de segurança dos dados utilizando _BP_5_ de política de backup para _SvcB2_P1_ de partição .

O diagrama seguinte retrata explicitamente políticas de backup ativadas e políticas de backup propagadas.

![Hierarquia de Aplicação de Tecido de Serviço][0]

## <a name="disable-backup"></a>Desativar a cópia de segurança
As políticas de backup podem ser desativadas quando não há necessidade de fornecer dados de backup. A política de backup ativada numa _aplicação_ só pode ser desativada na mesma _aplicação_ utilizando a API [de backup de aplicações para deficientes,](/rest/api/servicefabric/sfclient-api-disableapplicationbackup) a política de backup ativada num _serviço_ pode ser desativada no mesmo _serviço_ utilizando a API de Backup do Serviço [de Desativação](/rest/api/servicefabric/sfclient-api-disableservicebackup) e a política de backup ativada numa _partição_ pode ser desativada na mesma _divisão_ utilizando a API de Backup [de Desativação.](/rest/api/servicefabric/sfclient-api-disablepartitionbackup)

* A desativação da política de backup para uma _aplicação_ impede que todas as cópias de segurança periódicas de dados ocorram como resultado da propagação da política de backup para divisórias de serviços fiáveis ou divisórias de ator fiável.

* A desativação da política de backup de um _serviço_ impede que todas as cópias de segurança periódicas de dados ocorram em resultado da propagação desta política de backup às divisórias do _serviço_.

* A desativação da política de backup para uma _partição_ impede que todos os dados periódicos de backup ocorram devido à política de backup na partição.

* Ao mesmo tempo que desativa a cópia de segurança para uma entidade (aplicação/serviço/partição), `CleanBackup` pode ser definida como _verdadeira_ para eliminar todas as cópias de segurança em armazenamento configurado.
    ```json
    {
        "CleanBackup": true 
    }
    ```
> [!NOTE]
> Certifique-se de que não há atualizações de aplicações em andamento antes de desativar o backup
>

## <a name="suspend--resume-backup"></a>Suspender & retomar o backup
Determinada situação pode exigir a suspensão temporária da cópia de segurança periódica dos dados. Nesta situação, dependendo da exigência, a API de reserva pode ser utilizada numa _Aplicação,_ _Serviço_ ou _Partição_. A suspensão de backup periódica é transitiva sobre a subtree da hierarquia da aplicação a partir do ponto em que é aplicada. 

* Quando a suspensão é aplicada num _Pedido_ de [Suspensão de Backup](/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API, todos os serviços e divisórias ao abrigo desta aplicação são suspensos para cópia de segurança periódica de dados.

* Quando a suspensão é aplicada num _Serviço_ utilizando a API [de Backup de Serviço suspenso,](/rest/api/servicefabric/sfclient-api-suspendservicebackup) todas as divisórias sob este serviço são suspensas para cópia de segurança periódica dos dados.

* Quando a suspensão é aplicada numa _partição_ utilizando a [API de backup de partição suspensa,](/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) então suspende as divisórias sob este serviço são suspensas para cópia de segurança periódica de dados.

Uma vez terminada a necessidade de suspensão, a cópia de segurança periódica dos dados pode ser restaurada utilizando a API de backup recomeçada. A cópia de segurança periódica deve ser retomada na mesma _aplicação,_ _serviço_ ou _partição_ onde foi suspensa.

* Se a suspensão foi aplicada num _Pedido,_ então deve ser retomada usando [a API de Backup de Aplicação de Currículo.](/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) 

* Se a suspensão foi aplicada num _Serviço,_ então deve ser retomada usando [a API de Backup do Serviço de Currículo.](/rest/api/servicefabric/sfclient-api-resumeservicebackup)

* Se a suspensão foi aplicada numa _partição,_ então deve ser retomada usando [a API de Backup de Partição de Currículo.](/rest/api/servicefabric/sfclient-api-resumepartitionbackup)

### <a name="difference-between-suspend-and-disable-backups"></a>Diferença entre cópias de segurança suspensas e desativadas
A cópia de segurança deve ser utilizada quando as cópias de segurança deixarem de ser necessárias para uma determinada aplicação, serviço ou divisão. Pode-se invocar um pedido de cópia de segurança para desativar, juntamente com o parâmetro de cópias de segurança limpos, o que significaria que todas as cópias de segurança existentes também são eliminadas. No entanto, a suspensão deve ser usada em cenários em que se quer desligar as cópias de segurança temporariamente, como quando o disco local fica cheio ou o backup de upload está falhando devido a problemas de rede conhecidos, etc. 

Embora o desativação possa ser invocado apenas a um nível que tenha sido anteriormente habilitado para o backup explicitamente, no entanto a suspensão pode ser aplicada a qualquer nível que esteja atualmente habilitado para o backup, quer diretamente quer através da herança/hierarquia. Por exemplo, se o backup estiver ativado a nível de aplicação, pode-se invocar o desativação apenas ao nível da aplicação, no entanto, a suspensão pode ser invocada no pedido, em qualquer serviço ou partição ao abrigo dessa aplicação. 

## <a name="auto-restore-on-data-loss"></a>Restauração automática na perda de dados
A divisória de serviço pode perder dados devido a falhas inesperadas. Por exemplo, o disco de duas de três réplicas para uma partição (incluindo a réplica primária) é corrompido ou limpo.

Quando o Service Fabric deteta que a partição está em perda de dados, invoca `OnDataLossAsync` o método de interface na partição e espera que a partição tome as medidas necessárias para sair da perda de dados. Nesta situação, se a política de backup eficaz na partição tiver `AutoRestoreOnDataLoss` definido a bandeira para `true` então o restauro é desencadeado automaticamente usando o mais recente backup disponível para esta partição.

> [!NOTE]
> Recomenda-se não definir auto-restauração em clusters de produção
>

## <a name="get-backup-configuration"></a>Obtenha configuração de backup
ApIs separados são disponibilizados para obter informações de configuração de backup em uma _aplicação,_ _serviço_ e âmbito _de partição._ [Obtenha informações de configuração de backup de aplicações](/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [obtenha informações de configuração de backup de serviço](/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)e obtenha informações de [configuração de backup de partição](/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) são estas APIs respectivamente. Principalmente, estas APIs devolvem a política de backup aplicável, âmbito no qual a política de backup é aplicada e detalhes de suspensão de backup. Segue-se uma breve descrição sobre os resultados devolvidos destas APIs.

- Info de configuração de backup da aplicação: fornece os detalhes da política de backup aplicada na aplicação e todas as políticas sobre-cheias em serviços e divisórias pertencentes à aplicação. Inclui também a informação de suspensão para a aplicação e serviços de aplicação, e divisórias.

- Info de configuração de backup de serviço: fornece os detalhes de uma política de backup eficaz no serviço e o âmbito em que esta política foi aplicada e todas as políticas sobrecarregadas nas suas divisórias. Inclui também a informação de suspensão do serviço e as suas divisórias.

- Informação de configuração de backup de partição: fornece os detalhes de uma política eficaz de backup na partição e o âmbito em que esta política foi aplicada. Inclui também a informação de suspensão para as divisórias.

## <a name="list-available-backups"></a>Lista de backups disponíveis

As cópias de segurança disponíveis podem ser listadas através da API da Lista de Backup. O resultado da chamada API inclui itens de informações de backup relacionados com todos os backups disponíveis no armazenamento de backup, que está configurado na política de backup aplicável. Diferentes variantes desta API são fornecidas para listar cópias de segurança disponíveis pertencentes a uma aplicação, serviço ou partição. Estas APIs suportam obter a _mais recente_ cópia de segurança disponível de todas as divisórias aplicáveis, ou filtragem de backups com base na _data de início_ e data de _fim_.

Estas APIs também suportam a paginação dos resultados, quando o parâmetro _MaxResults_ é definido para número inteiro não-zero positivo, então a API devolve os itens de informações de backup _maxResults_ máximos. No caso de existirem mais itens de informações de backup disponíveis do que o valor _MaxResults,_ então um token de continuação é devolvido. O parâmetro de token de continuação válido pode ser usado para obter o próximo conjunto de resultados. Quando o valor simbólico de continuação válido é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Nenhum sinal de continuação é incluído na resposta quando todos os resultados disponíveis são devolvidos.

Seguem-se as breves informações sobre variantes suportadas.

- [Obtenha a Lista de Backup da Aplicação](/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Devolve uma lista de cópias de segurança disponíveis para cada partição pertencente à aplicação de Tecido de Serviço.

- [Obtenha a Lista de Cópias de Segurança do Serviço](/rest/api/servicefabric/sfclient-api-getservicebackuplist): Devolve uma lista de cópias de segurança disponíveis para cada partição pertencente ao serviço de Tecido de Serviço.
 
- [Obtenha a Lista de Backup de Partição](/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Devolve uma lista de cópias de segurança disponíveis para a partição especificada.

## <a name="next-steps"></a>Passos seguintes
- [Backup restaurar referência API REST](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
