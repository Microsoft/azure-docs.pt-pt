---
title: Compreender a configuração da cópia de segurança periódica
description: Utilize a função de cópia de segurança periódica do Service Fabric para permitir a cópia de segurança periódica dos dados dos seus dados de aplicação.
author: hrushib
ms.topic: article
ms.date: 2/01/2019
ms.author: hrushib
ms.openlocfilehash: 34c6495e094a1160f6ac75b9f098934d5cbce967
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610153"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Compreender a configuração periódica de backup no Tecido de Serviço Azure

Configurar cópias de segurança periódicas dos seus serviços fiáveis ou atores fiáveis consiste nos seguintes passos:

1. **Criação de políticas de backup**: Neste passo, uma ou mais políticas de backup são criadas dependendo dos requisitos.

2. **Habilitar o backup**: Neste passo, associa as políticas de backup criadas no **Passo 1** às entidades, _Aplicação,_ _Serviço_ou Uma _Partição._

## <a name="create-backup-policy"></a>Criar política de backup

Uma política de backup consiste nas seguintes configurações:

* **Restabelecimento automático na perda**de dados : Especifica se deve desencadear a restauração automaticamente utilizando a mais recente cópia de segurança disponível no caso de a partição experimentar um evento de perda de dados.

* **Backups incrementais max**: Define o número máximo de backups incrementais a serem tomados entre duas cópias de segurança completas. As cópias de segurança incrementais máximas especificam o limite superior. Uma cópia de segurança completa pode ser tomada antes que o número especificado de cópias de segurança incrementais seja concluído numa das seguintes condições

    1. A réplica nunca recebeu um reforço completo desde que se tornou primária.

    2. Alguns dos registos desde a última cópia de segurança foram truncados.

    3. A réplica passou o limite MaxAcumuladodBackupLogSizeInMB.

* **Horário de reserva**: A hora ou frequência para obter cópias de segurança periódicas. Pode-se agendar backups para serem recorrentes a intervalos determinados ou a um horário fixo diariamente/semanalmente.

    1. **Programa ção de backup baseada em frequências**: Este tipo de horário deve ser utilizado se a necessidade for de obter a cópia de segurança dos dados em intervalos fixos. O intervalo de tempo desejado entre duas cópias de segurança consecutivas é definido utilizando o formato ISO8601. O calendário de backup baseado em frequência suporta a resolução de intervalos ao minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Calendário de backup baseado no tempo**: Este tipo de horário deve ser utilizado se a necessidade for de obter cópiade segurança de dados em horas específicas do dia ou da semana. O tipo de frequência de horário pode ser diário ou semanal.
        1. ** _Horário_ de backup baseado no tempo diário**: Este tipo de horário deve ser utilizado se o id necessário para obter cópias de segurança de dados em horas específicas do dia. Para especificar isto, definido `ScheduleFrequencyType` para o _Daily;_ e `RunTimes` definido para listar o tempo desejado durante o dia no formato ISO8601, a data especificada juntamente com o tempo serão ignoradas. Por exemplo, `0001-01-01T18:00:00` representa _18:00 todos_ os dias, ignorando a data _0001-01-01_. Abaixo o exemplo ilustra a configuração para desencadear a cópia de segurança diária às _9:00_ e _18:00_ todos os dias.

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

        2. ** _Horário_ de backup baseado no tempo semanal**: Este tipo de horário deve ser utilizado se o id necessário para obter cópiade segurança de dados em horas específicas do dia. Para especificar isto, definido `ScheduleFrequencyType` para _Weekly;_ definido `RunDays` para lista de dias numa semana em que `RunTimes` o backup precisa ser desencadeado e definido para listar o tempo desejado durante o dia no formato ISO8601, a data especificada juntamente com o tempo serão ignoradas. Lista de dias de uma semana para ativar a cópia de segurança periódica. Abaixo o exemplo ilustra a configuração para desencadear cópias de segurança diárias às _9:00_ e _18:00_ durante de segunda a sexta-feira.

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

* **Armazenamento de cópia de segurança:** Especifica a localização para fazer o upload de cópias de segurança. O armazenamento pode ser loja de blob Azure ou partilha de ficheiros.
    1. **Loja de blob Azure**: Este tipo de armazenamento deve ser selecionado quando a necessidade é armazenar cópias de segurança geradas em Azure. Tanto os clusters _autónomos_ como os _azure_ podem utilizar este tipo de armazenamento. A descrição deste tipo de armazenamento requer cadeia de ligação e nome do recipiente onde as cópias de segurança precisam de ser carregadas. Se o recipiente com o nome especificado não estiver disponível, então será criado durante o upload de uma cópia de segurança.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Partilha de ficheiros**: Este tipo de armazenamento deve ser selecionado para clusters _autónomos_ quando a necessidade é armazenar cópias de segurança de dados no local. A descrição deste tipo de armazenamento requer o caminho da partilha de ficheiros onde as cópias de segurança precisam de ser carregadas. O acesso à partilha de ficheiros pode ser configurado utilizando uma das seguintes opções
        1. _Autenticação Integrada do Windows,_ onde o acesso à partilha de ficheiros é fornecido a todos os computadores pertencentes ao cluster Service Fabric. Neste caso, delineie os seguintes campos para configurar o armazenamento de cópia de segurança baseado em _partilha de ficheiros._

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Proteger a partilha de ficheiros utilizando o nome do utilizador e a palavra-passe,_ onde a partilha de ficheiros é fornecida a utilizadores específicos. A especificação de armazenamento de partilha de ficheiros também fornece capacidade para especificar o nome de utilizador secundário e a palavra-passe secundária para fornecer credenciais de recuo no caso de a autenticação falhar com o nome principal do utilizador e a palavra-passe primária. Neste caso, delineie os seguintes campos para configurar o armazenamento de cópia de segurança baseado em _partilha de ficheiros._

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

* **Política de Retenção**: Especifica a política de retenção de cópias de segurança no armazenamento configurado. Apenas a Política básica de retenção é apoiada.
    1. **Política de Retenção Básica**: Esta política de retenção permite garantir uma utilização ótima do armazenamento removendo ficheiros de backup que já não são necessários. `RetentionDuration`pode ser especificado para definir o tempo de tempo para o qual as cópias de segurança são necessárias para serem retidas no armazenamento. `MinimumNumberOfBackups`é um parâmetro opcional que pode ser especificado para garantir que o número especificado de cópias de segurança são sempre retidos independentemente do `RetentionDuration`. Abaixo o exemplo ilustra a configuração para reter backups durante _10_ dias e não permite que o número de backups seja inferior a _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

## <a name="enable-periodic-backup"></a>Ativar cópia de segurança periódica
Após a definição da política de backup para satisfazer os requisitos de backup de dados, a política de backup deve ser adequadamente associada a uma _aplicação,_ ou _serviço,_ ou a uma _partilha_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagação hierárquica da política de backup
No Tecido de Serviço, a relação entre aplicação, serviço e partições é hierárquica, como explicado no [modelo de Aplicação.](./service-fabric-application-model.md) A política de backup pode ser associada quer a uma _aplicação,_ _serviço,_ quer a uma _divisão_ na hierarquia. A política de backup propaga-se hierárquicamente ao próximo nível. Assumindo que existe apenas uma política de backup criada e associada a uma _aplicação,_ todas as divisórias imponentes pertencentes a todos os _serviços de estado confiáveis_ e _atores fiáveis_ da _aplicação_ serão apoiadas usando a política de backup. Ou se a política de backup estiver associada a um serviço fiável e _audato,_ todas as suas divisórias serão apoiadas usando a política de backup.

### <a name="overriding-backup-policy"></a>Política de backup primordial
Pode haver um cenário em que a cópia de segurança de dados com o mesmo calendário de backup é necessária para todos os serviços da aplicação, exceto para serviços específicos onde a necessidade é ter backup de dados usando um horário de frequência mais elevado ou levando backup para uma conta de armazenamento diferente ou fileshare. Para abordar tais cenários, o serviço de restauro de backup fornece facilidade para anular a política propagada no âmbito de serviço e partilha. Quando a política de backup está associada ao _serviço_ ou _à partilha,_ substitui a política de backup propagada, se houver.

### <a name="example"></a>Exemplo

Este exemplo utiliza a configuração com duas aplicações, _MyApp_A_ e _MyApp_B._ Aplicação _MyApp_A_ contém dois serviços de stateude fiável, & _SvcA1 SvcA3,_ e um serviço de Ator Fiável, _ActorA2_. _SvcA1_ _O SvcA1_ contém três divisórias, enquanto o _ActorA2_ e o _SvcA3_ contêm duas divisórias cada.  A _aplicação MyApp_B_ contém três serviços fiáveis e estatais, _SvcB1,_ _SvcB2_e _SvcB3_. _SvcB1_ e _SvcB2_ contêm duas divisórias cada, enquanto _o SvcB3_ contém três divisórias.

Assuma que os requisitos de backup de dados destas aplicações são os seguintes

1. MyApp_A
    1. Crie cópias de segurança diárias de dados para todas as divisórias de todos os _serviços de Stateful Confiáveis_ e _Atores Fiáveis_ pertencentes à aplicação. Faça upload dos dados de backup para localizar _BackupStore1_.

    2. Um dos serviços, o _SvcA3,_ requer cópia de segurança de dados a cada hora.

    3. O tamanho dos dados na partilha _SvcA1_P2_ é mais do que o esperado e os seus dados de backup devem ser armazenados em diferentes locais de armazenamento _BackupStore2_.

2. MyApp_B
    1. Crie cópiade segurança de dados todos os domingos às 8:00 am para todas as divisórias do serviço _SvcB1._ Faça upload dos dados de backup para localizar _BackupStore1_.

    2. Crie cópia de segurança de dados todos os dias às 8:00 am para _SvcB2_P1_de partição . Faça upload dos dados de backup para localizar _BackupStore1_.

Para responder a estes requisitos de backup de dados, as políticas de backup BP_1 a BP_5 são criadas e a cópia de segurança está ativada da seguinte forma.
1. MyApp_A
    1. Crie uma política de backup, _BP_1,_ com um calendário de backup baseado em frequência, onde a frequência é definida para 24 Hrs. e armazenamento de reserva configurado para usar a localização de armazenamento _BackupStore1_. Ative esta política de _aplicação MyApp_A_ utilizando a API de backup de [aplicação ativa.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) Esta ação permite a cópia de segurança de dados utilizando _BP_1_ de política de backup para todas as divisórias de _serviços fiáveis e_ agentes _fiáveis_ pertencentes a _aplicações MyApp_A_.

    2. Crie uma política de backup, _BP_2,_ com um calendário de backup baseado em frequência, onde a frequência é definida para 1 Hrs. e armazenamento de reserva configurado para usar a localização de armazenamento _BackupStore1_. Ative esta política de serviço _SvcA3_ utilizando a [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação substitui a política propagada _BP_1_ por _uma_ política de backup ativada explicitamente BP_2 para todas as divisórias de serviço _SvcA3,_ levando à cópia de segurança dos dados utilizando _BP_2_ de política de backup para estas divisórias.

    3. Crie uma política de backup, _BP_3,_ com um calendário de backup baseado em frequência, onde a frequência é definida para 24 Hrs. e armazenamento de reserva configurado para usar a localização de armazenamento _BackupStore2_. Ative esta política de divisória _SvcA1_P2_ utilizando a API de backup de [partição ativa.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Esta ação substitui a _BP_1_ da política propagada, através de uma política de backup ativada explicitamente _BP_3_ para _SvcA1_P2_de partilha .

2. MyApp_B
    1. Crie uma política de backup, _BP_4,_ com horário de backup baseado no tempo, onde o tipo de frequência de horário é definido para semanal, os dias de execução estão definidos para domingo, e os tempos de execução estão definidos para as 8:00 da manhã. Armazenamento de reserva configurado para usar a localização de armazenamento _BackupStore1_. Ative esta política de serviço _SvcB1_ utilizando a [Enable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação permite a cópia de segurança de dados utilizando a política de backup _BP_4_ para todas as divisórias do serviço _SvcB1_.

    2. Crie uma política de backup, _BP_5,_ com horário de backup baseado no tempo, onde o tipo de frequência de horário é definido para o dia-a-dia e os tempos de execução estão definidos para as 8:00 da manhã. Armazenamento de reserva configurado para usar a localização de armazenamento _BackupStore1_. Ative esta política de partilha _SvcB2_P1_ utilizando a API de backup de [partição Ativa.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) Esta ação permite a cópia de segurança dos dados utilizando _BP_5 de_ política de backup para _SvcB2_P1_de partilha .

O diagrama seguinte retrata políticas de backup ativadas explicitamente e políticas de backup propagadas.

![Hierarquia de Aplicação de Tecido de Serviço][0]

## <a name="disable-backup"></a>Desativar a cópia de segurança
As políticas de backup podem ser desativadas quando não há necessidade de fazer backup de dados. A política de backup ativada numa _aplicação_ só pode ser desativada na mesma _aplicação_ utilizando a API de backup de [aplicação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) para deficientes, a política de backup ativada num _serviço_ pode ser desativada no mesmo _serviço_ utilizando a API de backup de serviço de desativação, e a política de backup ativada numa _divisória_ pode ser desativada na mesma _divisória_ utilizando a API de backup de [desativação.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) [Disable Service Backup](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup)

* Desativar a política de backup de uma _aplicação_ impede que todas as cópias de segurança periódicas de dados ocorram como resultado da propagação da política de backup a divisórias de serviço fiável ou divisórias de ator fiável.

* A desativação da política de backup de um _serviço_ impede que todas as cópias de segurança periódicas de dados aconteçam em resultado da propagação desta política de backup às divisórias do _serviço_.

* Desativar a política de backup para uma _partição_ impede que todas as cópias de segurança periódicas ocorram devido à política de backup na partição.

* Embora desativar a cópia de segurança para `CleanBackup` uma entidade (aplicação/serviço/partição), pode ser definido como _verdadeiro_ para eliminar todas as cópias de segurança em armazenamento configurado.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Suspender & retomar a cópia de segurança
Determinada situação pode exigir a suspensão temporária da cópia de segurança periódica dos dados. Nesta situação, dependendo da exigência, a API de reserva pode ser utilizada numa _Aplicação,_ _Serviço_ou _Partição._ A suspensão periódica de backup é transitiva sobre a subárvore da hierarquia da aplicação a partir do ponto em que é aplicada. 

* Quando a suspensão é aplicada numa _Aplicação_ utilizando a API de backup de [aplicações suspensa,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) todos os serviços e divisórias ao abrigo desta aplicação são suspensos para cópia de segurança periódica de dados.

* Quando a suspensão é aplicada num _Serviço_ utilizando a API de backup de [serviço suspensa,](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) todas as divisórias sob este serviço são suspensas para cópia de segurança periódica de dados.

* Quando a suspensão é aplicada numa _Partição_ utilizando a API de backup de [divisória](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) suspensa, então suspende as divisórias ao abrigo deste serviço são suspensas para cópia de segurança periódica de dados.

Uma vez terminada a necessidade de suspensão, a cópia de segurança periódica de dados pode ser restaurada utilizando a respetiva API de reserva de retoma. A cópia de segurança periódica deve ser retomada na mesma _aplicação,_ _serviço_ou _partição_ onde foi suspensa.

* Se a suspensão foi aplicada numa _Aplicação,_ então deve ser retomada utilizando a API de backup de pedido de [retoma.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) 

* Se a suspensão foi aplicada num _Serviço,_ então deve ser retomada utilizando a API de backup do [serviço de retoma.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup)

* Se a suspensão foi aplicada numa _Partição,_ então deve ser retomada utilizando a API de backup de cópia de [retoma.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup)

### <a name="difference-between-suspend-and-disable-backups"></a>Diferença entre suspender e desativar backups
Deve ser utilizada cópia de segurança quando as cópias de segurança já não forem necessárias para uma determinada aplicação, serviço ou partição. Pode-se invocar o pedido de desativação, juntamente com o parâmetro de cópias de segurança limpas, o que significaria que todas as cópias de segurança existentes também são eliminadas. No entanto, a suspensão deve ser utilizada em cenários em que se queira desligar as cópias de segurança temporariamente, como quando o disco local fica cheio ou o envio de cópias de segurança está a falhar devido a um problema de rede conhecido, etc. 

Embora o desativação só possa ser invocado a um nível que tenha sido ativado anteriormente para cópia de segurança explicitamente, no entanto a suspensão pode ser aplicada a qualquer nível que esteja atualmente habilitado para cópia de segurança direta ou através de herança/hierarquia. Por exemplo, se a cópia de segurança estiver ativada a nível de aplicação, só se pode invocar desativar apenas ao nível da aplicação, no entanto, a suspensão pode ser invocada na aplicação, qualquer serviço ou partição ao abrigo dessa aplicação. 

## <a name="auto-restore-on-data-loss"></a>Restauração automática na perda de dados
A partição do serviço pode perder dados devido a falhas inesperadas. Por exemplo, o disco para duas das três réplicas para uma partição (incluindo a réplica primária) é corrompido ou limpo.

Quando o Tecido de Serviço deteta que a `OnDataLossAsync` partição está em perda de dados, invoca o método da interface na partição e espera que a partilha tome as medidas necessárias para sair da perda de dados. Nesta situação, se a política de `AutoRestoreOnDataLoss` backup eficaz `true` na divisória tiver uma bandeira definida para então o restauro é acionado automaticamente usando o mais recente backup disponível para esta partição.

## <a name="get-backup-configuration"></a>Obtenha configuração de backup
As APIs separadas são disponibilizadas para obter informações de configuração de backup numa _aplicação,_ _serviço_e âmbito _de partição._ [Obtenha informações de configuração](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo)de backup de aplicação, [obtenha informações](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo)de configuração de backup de serviço, e obtenha informações de configuração de backup de [divisórias](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) são estas APIs respectivamente. Principalmente, estas APIs devolvem a política de backup aplicável, âmbito no qual a política de backup é aplicada e detalhes de suspensão de backup. Segue-se uma breve descrição sobre os resultados devolvidos destas APIs.

- Informação de configuração de backup de aplicações: fornece os detalhes da política de backup aplicada na aplicação e todas as políticas sobre-montadas em serviços e divisórias pertencentes à aplicação. Inclui também a informação de suspensão para a aplicação e serviços de ti, e divisórias.

- Informação de configuração de backup de serviço: fornece os detalhes de uma política de backup eficaz no serviço e o âmbito a que esta política foi aplicada e todas as políticas sobre-montadas nas suas divisórias. Inclui também as informações de suspensão para o serviço e as suas divisórias.

- Informações de configuração de backup de divisórias: fornece os detalhes de uma política de backup eficaz na partilha e o âmbito a que esta política foi aplicada. Também inclui as informações de suspensão para as divisórias.

## <a name="list-available-backups"></a>Lista de backups disponíveis

As cópias de segurança disponíveis podem ser listadas através da Lista de Cópia seletiva API. O resultado da chamada da API inclui itens de informação de backup relacionados com todas as cópias de segurança disponíveis no armazenamento de backup, que está configurado na política de backup aplicável. Diferentes variantes desta API são fornecidas para listar backups disponíveis pertencentes a uma aplicação, serviço ou partição. Estas APIs suportam obter a _mais recente_ cópia de segurança disponível de todas as divisórias aplicáveis, ou filtragem de backups com base na data de _início_ e data _de fim_.

Estas APIs também suportam a paginação dos resultados, quando o parâmetro _MaxResults_ é definido para um número não-zero positivo, então o API devolve itens máximos de informação de backup _MaxResults._ No caso de existirem mais itens de informação de backup disponíveis do que o valor _MaxResults,_ então um token de continuação é devolvido. O parâmetro de ficha de continuação válido pode ser usado para obter o próximo conjunto de resultados. Quando o valor simbólico de continuação válido é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Nenhum sinal de continuação é incluído na resposta quando todos os resultados disponíveis são devolvidos.

Segue-se a breve informação sobre variantes suportadas.

- [Obter Lista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist)de Backup application : Devolve uma lista de backups disponíveis para cada partição pertencente a uma determinada aplicação de Tecido de Serviço.

- [Obtenha lista](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist)de backup de serviço : Devolve uma lista de backups disponíveis para cada partição pertencente ao serviço de Tecido de Serviço.
 
- Obtenha lista de [backup de divisórias:](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist)Devolve uma lista de backups disponíveis para a partição especificada.

## <a name="next-steps"></a>Passos seguintes
- [Cópia de segurança restaurar referência REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/backup-policy-association-example.png
