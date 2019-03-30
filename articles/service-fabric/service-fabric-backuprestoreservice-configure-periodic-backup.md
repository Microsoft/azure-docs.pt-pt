---
title: Configuração cópia de segurança periódica compreensão no Azure Service Fabric | Documentos da Microsoft
description: Utilize a cópia de segurança periódica do Service Fabric e restaurar a funcionalidade para ativar a cópia de segurança de dados periódica de dados da sua aplicação.
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
ms.date: 05/01/2018
ms.author: hrushib
ms.openlocfilehash: 31c5feac577dc5e9e0eed9ced9ccfe25c12d3086
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670494"
---
# <a name="understanding-periodic-backup-configuration-in-azure-service-fabric"></a>Noções básicas sobre a configuração de cópia de segurança periódica no Azure Service Fabric

Configurar cópia de segurança periódica dos seus serviços com estado fiável ou Reliable Actors é composta pelos seguintes passos:

1. **Criação de políticas de cópia de segurança**: Neste passo, uma ou mais políticas de cópia de segurança são criadas consoante os requisitos.

2. **Ativar cópia de segurança**: Neste passo, associa políticas de cópia de segurança criadas no **passo 1** para as entidades necessárias, _aplicativo_, _serviço_, ou um _partição_.

## <a name="create-backup-policy"></a>Criar política de cópia de segurança

Uma política de cópia de segurança inclui as seguintes configurações:

* **Auto restauro na perda de dados**: Especifica se pretende acionar restauro automaticamente ao utilizar a mais recente cópia de segurança disponível caso a partição de experiências de um evento de perda de dados.

* **Cópias de segurança incrementais Max**: Define o número máximo de cópias de segurança incrementais para ficar entre duas cópias de segurança completas. Cópias de segurança incrementais máx. Especifique o limite superior. Uma cópia de segurança completa pode ser executada antes do número especificado de cópias de segurança incrementais é concluído em uma das seguintes condições

    1. A réplica nunca apresentou uma cópia de segurança completa, uma vez que ele se tornou primário.

    2. Alguns dos registros de log, uma vez que a última cópia de segurança foram truncada.

    3. Réplica passado o limite de MaxAccumulatedBackupLogSizeInMB.

* **Agenda de cópia de segurança**: A hora ou a frequência no qual pretende efetuar cópias de segurança periódicas. Um pode agendar cópias de segurança recorrentes intervalo especificado ou numa hora fixa diárias / semanais.

    1. **Com base na frequência de cópia de segurança agendada**: Este tipo de agenda deve ser utilizado se a necessidade é fazer a cópia de segurança de dados em intervalos fixos. Intervalo de tempo pretendido entre duas cópias de segurança consecutivas é definido usando o formato de ISO8601. Com base na frequência de cópia de segurança agendada oferece suporte a resolução de intervalo para o minuto.
        ```json
        {
            "ScheduleKind": "FrequencyBased",
            "Interval": "PT10M"
        }
        ```

    2. **Agenda de cópia de segurança baseados no tempo**: Este tipo de agenda deve ser utilizado se a necessidade é fazer a cópia de segurança de dados em alturas específicas do dia ou da semana. Tipo de frequência da agenda pode ser diária ou semanal.
        1. **_Diária_ agenda de cópia de segurança baseados no tempo**: Este tipo de agenda deve ser utilizado se o id de necessidade de fazer a cópia de segurança de dados em alturas específicas do dia. Para especificar isso, defina `ScheduleFrequencyType` para _diária_; e defina `RunTimes` a lista de tempo pretendido durante o dia no formato de ISO8601, data especificada juntamente com o tempo será ignorado. Por exemplo, `0001-01-01T18:00:00` representa _das 18:00_ todos os dias, a ignorar a parte da data _0001-01-01_. Exemplo abaixo ilustra a configuração de cópia de segurança diária do acionador na _9:00_ e _das 18:00_ todos os dias.

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

        2. **_Semanal_ agenda de cópia de segurança baseados no tempo**: Este tipo de agenda deve ser utilizado se o id de necessidade de fazer a cópia de segurança de dados em alturas específicas do dia. Para especificar isso, defina `ScheduleFrequencyType` para _semanal_; defina `RunDays` à lista de dias numa semana quando cópia de segurança tem de ser acionados e definir `RunTimes` a lista de tempo pretendido durante o dia no formato de ISO8601, data especificada, juntamente com o tempo será ignorada. Lista de dias de uma semana quando acionar a cópia de segurança periódica. Exemplo abaixo ilustra a configuração de cópia de segurança diária do acionador na _9:00_ e _das 18:00_ durante a segunda a sexta-feira.

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

* **Armazenamento de cópia de segurança**: Especifica a localização para carregar as cópias de segurança. O armazenamento pode ser qualquer um dos armazenamento de Blobs do Azure ou partilha de ficheiros.
    1. **Armazenamento de Blobs do Azure**: Este tipo de armazenamento deve ser selecionado quando a necessidade é armazenar gerado cópias de segurança no Azure. Ambos _autónomo_ e _baseada no Azure_ clusters podem utilizar este tipo de armazenamento. Descrição para este tipo de armazenamento requer a cadeia de ligação e o nome do contentor onde as cópias de segurança devem ser carregados. Se o contentor com o nome especificado não estiver disponível, em seguida, é criada durante o carregamento de uma cópia de segurança.
        ```json
        {
            "StorageKind": "AzureBlobStore",
            "FriendlyName": "Azure_storagesample",
            "ConnectionString": "<Put your Azure blob store connection string here>",
            "ContainerName": "BackupContainer"
        }
        ```

    2. **Partilha de ficheiros**: Este tipo de armazenamento deve ser selecionado para _autónomo_ clusters quando a necessidade é armazenar dados no local de cópia de segurança. Descrição para este tipo de armazenamento requer o caminho de partilha de ficheiros em que as cópias de segurança têm de ser carregado. Acesso à partilha de ficheiros pode ser configurado através de uma das seguintes opções
        1. _Autenticação Windows integrada_, onde o acesso à partilha de ficheiros é fornecido a todos os computadores que pertencem ao cluster do Service Fabric. Neste caso, defina seguintes campos para configurar _partilha de ficheiros_ com base em armazenamento de cópia de segurança.

            ```json
            {
                "StorageKind": "FileShare",
                "FriendlyName": "Sample_FileShare",
                "Path": "\\\\StorageServer\\BackupStore"
            }
            ```

        2. _Proteção partilha de ficheiros com o nome de utilizador e palavra-passe_, onde o acesso à partilha de ficheiros é fornecido a utilizadores específicos. Especificação de armazenamento de partilha de ficheiros também fornece a capacidade para especificar o nome de usuário secundário e a palavra-passe secundária para fornecer credenciais de retorno no caso de falha de autenticação com o nome de utilizador principal e a palavra-passe primária. Neste caso, defina seguintes campos para configurar _partilha de ficheiros_ com base em armazenamento de cópia de segurança.

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
> Certifique-se de que a fiabilidade de armazenamento cumpre ou excede os requisitos de confiabilidade de dados de cópia de segurança.
>

* **Política de retenção**: Especifica a política para reter cópias de segurança no armazenamento configurado. Política de retenção apenas básica é suportada.
    1. **Política de retenção básica**: Permite que esta política de retenção para garantir que a utilização de armazenamento ideal, removendo ficheiros de cópia de segurança que são não mais necessários. `RetentionDuration` pode ser especificado para definir o intervalo de tempo para o qual cópias de segurança têm de ser mantidos no armazenamento. `MinimumNumberOfBackups` é um parâmetro opcional que pode ser especificado para se certificar de que o número especificado de cópias de segurança é sempre retido irrespective do `RetentionDuration`. Exemplo abaixo ilustra a configuração para reter cópias de segurança para _10_ dias e não aceita o número de cópias de segurança para ir abaixo _20_.

        ```json
        {
            "RetentionPolicyType": "Basic",
            "RetentionDuration" : "P10D",
            "MinimumNumberOfBackups": 20
        }
        ```

> [!IMPORTANT]
> Devido a um problema no tempo de execução, certifique-se de que a duração da retenção na política de retenção está configurada para ser inferior a 24 dias caso contrário, ele iria resultar no serviço de restauração de cópia de segurança de mensagens em fila para entrar em ativação pós-falha de réplica do quórum perda post.

## <a name="enable-periodic-backup"></a>Ativar cópia de segurança periódica
Depois de definir a política de cópia de segurança para atender a requisitos de cópia de segurança de dados, a política de cópia de segurança deve ser adequadamente associada a um _aplicativo_, ou _service_, ou um _partição_.

### <a name="hierarchical-propagation-of-backup-policy"></a>Propagação hierárquica de política de cópia de segurança
No Service Fabric, a relação entre a aplicação, serviço e as partições é hierárquica, conforme explicado [modelo de aplicativo](./service-fabric-application-model.md). Política de cópia de segurança pode ser associada com um _aplicativo_, _service_, ou uma _partição_ na hierarquia. Política de cópia de segurança hierarquicamente propaga para o próximo nível. Partindo do princípio de que existe apenas uma política de cópia de segurança criado e associado a um _aplicativo_, todas as partições com monitoração de estado que pertencem a todos os _Reliable services com monitorização de estado_ e _Reliable Actors_ das _aplicação_ vai ser uma cópia de segurança através da política de cópia de segurança. Ou se a política de cópia de segurança é associada um _Reliable Services com monitorização de estado_, todas as partições dele vão ser uma cópia de segurança através da política de cópia de segurança.

### <a name="overriding-backup-policy"></a>Substituir a política de cópia de segurança
Pode haver um cenário onde cópia de segurança de dados com a mesma agenda de cópia de segurança é necessária para todos os serviços da aplicação, exceto para serviços específicos em que a necessidade é ter a cópia de segurança de dados com maior frequência agenda ou fazer cópia de segurança para outra conta de armazenamento ou partilha de ficheiros. Para resolver tais cenários, o serviço de cópia de segurança de restauro fornece instalações a política de substituição propagada no âmbito do serviço e partição. Quando a política de cópia de segurança está associada ao _serviço_ ou _partição_, ele substitui a política de cópia de segurança propagada, se qualquer um.

### <a name="example"></a>Exemplo

Este exemplo utiliza a configuração com dois aplicativos, _MyApp_A_ e _MyApp_B_. Aplicativo _MyApp_A_ contém dois serviços com estado fiável, _SvcA1_ & _SvcA3_e um serviço Reliable Actor, _ActorA2_. _SvcA1_ contém três partições ao mesmo tempo _ActorA2_ e _SvcA3_ conter duas partições.  Aplicativo _MyApp_B_ contém três serviços com estado fiável, _SvcB1_, _SvcB2_, e _SvcB3_. _SvcB1_ e _SvcB2_ contém duas partições ao mesmo tempo _SvcB3_ contém três partições.

Partem do princípio de que são os seguintes requisitos de cópia de segurança de dados esses aplicativos

1. MyApp_A
    1. Criar cópia de segurança diária de dados para todas as partições de todos os _serviços com estado fiável_ e _Reliable Actors_ que pertencem à aplicação. Carregar dados de cópia de segurança para a localização _BackupStore1_.

    2. Um dos serviços, _SvcA3_, necessita de cópia de segurança de dados a cada hora.

    3. Tamanho de dados em partição _SvcA1_P2_ é mais do que o esperado e os dados de cópia de segurança devem ser armazenados para localização de armazenamento diferentes _BackupStore2_.

2. MyApp_B
    1. Criar a cópia de segurança de dados de todos os Domingos às 8:00 para todas as partições da _SvcB1_ serviço. Carregar dados de cópia de segurança para a localização _BackupStore1_.

    2. Criar a cópia de segurança de dados de todos os dias às 8:00 para a partição _SvcB2_P1_. Carregar dados de cópia de segurança para a localização _BackupStore1_.

Para tratar esses requisitos de cópia de segurança de dados, as políticas de cópia de segurança BP_1 para BP_5 são criadas e cópia de segurança é ativada da seguinte forma.
1. MyApp_A
    1. Criar política de cópia de segurança _BP_1_, com base na frequência de agendamento de backup em que a frequência está definida como 24 horas. e o armazenamento configurado para utilizar a localização de armazenamento de cópia de segurança _BackupStore1_. Ativar esta política para o aplicativo _MyApp_A_ usando [ativar cópia de segurança de aplicação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableapplicationbackup) API. Esta ação ativa a cópia de segurança de dados através da política de cópia de segurança _BP_1_ para todas as partições de _serviços com estado fiável_ e _Reliable Actors_ pertencentes a aplicação  _MyApp_A_.

    2. Criar política de cópia de segurança _BP_2_, com frequência em que a frequência está definida como 1 cópia de segurança agendada horas. e o armazenamento configurado para utilizar a localização de armazenamento de cópia de segurança _BackupStore1_. Ativar esta política para o serviço _SvcA3_ usando [ativar cópia de segurança do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação substitui a política de propagadas _BP_1_ por explicitamente ativada a política de cópia de segurança _BP_2_ para todas as partições do serviço _SvcA3_ que leva a cópia de segurança de dados com cópia de segurança política _BP_2_ para estas partições.

    3. Criar política de cópia de segurança _BP_3_, com base na frequência de agendamento de backup em que a frequência está definida como 24 horas. e o armazenamento configurado para utilizar a localização de armazenamento de cópia de segurança _BackupStore2_. Ativar esta política de partição _SvcA1_P2_ usando [ativar cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API. Esta ação substitui a política de propagadas _BP_1_ por explicitamente ativada a política de cópia de segurança _BP_3_ para a partição _SvcA1_P2_.

2. MyApp_B
    1. Criar política de cópia de segurança _BP_4_com base no tempo de cópia de segurança agendada onde o tipo de frequência de agendamento está definido como semanal, dias de execução está definido como Domingo e tempos de execução está definido como 8 da Manhã. Armazenamento configurado para utilizar a localização de armazenamento de cópia de segurança _BackupStore1_. Ativar esta política para o serviço _SvcB1_ usando [ativar cópia de segurança do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enableservicebackup) API. Esta ação ativa a cópia de segurança de dados através da política de cópia de segurança _BP_4_ para todas as partições do serviço _SvcB1_.

    2. Criar política de cópia de segurança _BP_5_, com a agenda cópias de segurança baseados no tempo em que o tipo de frequência da agenda é definido como diariamente e tempos de execução está definido como 8 da Manhã. Armazenamento configurado para utilizar a localização de armazenamento de cópia de segurança _BackupStore1_. Ativar esta política de partição _SvcB2_P1_ usando [ativar cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-enablepartitionbackup) API. Esta ação ativa a cópia de segurança de dados através da política de cópia de segurança _BP_5_ para a partição _SvcB2_P1_.

Diagrama a seguir descreve as políticas de cópia de segurança explicitamente ativadas e propagadas políticas de cópia de segurança.

![Hierarquia de aplicação do Service Fabric][0]

## <a name="disable-backup"></a>Desativar cópia de segurança
Políticas de cópia de segurança podem ser desativadas quando não é necessário para dados de cópia de segurança. Política ativada ao criar cópias de segurança uma _aplicativo_ só pode ser desativado ao mesmo tempo _aplicativo_ usando [desativar a cópia de segurança de aplicação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableapplicationbackup) API, política de cópia de segurança ativada num _service_ pode ser desativado ao mesmo tempo _service_ usando [desativar a cópia de segurança do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disableservicebackup) API e a política ativada ao criar cópias de segurança um _partição_ pode ser desativado ao mesmo _partição_ usando [desativar a cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-disablepartitionbackup) API.

* Desativar a política de cópia de segurança para um _aplicativo_ interrompe todos os backups de dados periódica a acontecer como resultado de propagação da política de cópia de segurança para as partições do serviço com estado fiável ou partições de Reliable Actor.

* Desativar a política de cópia de segurança para um _serviço_ interrompe todos os backups de dados periódica a acontecer como resultado de propagação desta política de cópia de segurança para as partições da _serviço_.

* Desativar a política de cópia de segurança para um _partição_ interrompe todas as cópias de segurança periódica de dados acontecendo devido à política de cópia de segurança na partição.

* Ao desativar a cópia de segurança para um entity(application/service/partition) `CleanBackup` pode ser definido como _true_ para eliminar todas as cópias de segurança no armazenamento configurado.
    ```json
    {
        "CleanBackup": true 
    }
    ```

## <a name="suspend--resume-backup"></a>Suspender e retomar a cópia de segurança
Determinados situação pode exigir a suspensão temporária da cópia de segurança periódica de dados. Em tal situação, dependendo do requisito, suspender a cópia de segurança API pode ser utilizada numa _aplicativo_, _Service_, ou _partição_. Suspensão de cópia de segurança periódica é transitivo sobre a subárvore de hierarquia do aplicativo do ponto de que é aplicada. 

* Quando a suspensão é aplicada numa _aplicativo_ usando [suspender a aplicação de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendapplicationbackup) API, em seguida, todos os serviços e as partições a esta aplicação são suspensas para cópia de segurança periódica de dados.

* Quando a suspensão é aplicada numa _serviço_ usando [suspender o serviço de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendservicebackup) API, em seguida, todas as partições nesse serviço são suspensas para cópia de segurança periódica de dados.

* Quando a suspensão é aplicada numa _partição_ usando [suspender o Backup da partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-suspendpartitionbackup) API, em seguida, ele suspende as partições nesse serviço são suspensas para cópia de segurança periódica de dados.

Assim que a necessidade de suspensão terminar, em seguida, a cópia de segurança periódica de dados pode ser restaurada com API de backup do respectivo retomar. Cópia de segurança periódica necessário recomeçar no mesmo _aplicativo_, _service_, ou _partição_ onde foi suspensa.

* Se a suspensão foi aplicada num _aplicativo_, em seguida, deve ser retomada com [retomar cópia de segurança de aplicação](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeapplicationbackup) API. 

* Se a suspensão foi aplicada num _serviço_, em seguida, deve ser retomada com [retomar cópia de segurança do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumeservicebackup) API.

* Se a suspensão foi aplicada num _partição_, em seguida, deve ser retomada utilizando [retomar cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-resumepartitionbackup) API.

### <a name="difference-between-suspend-and-disable-backups"></a>Diferença entre suspender e desative as cópias de segurança
Desativar cópia de segurança deve ser usada quando as cópias de segurança já não são necessárias para uma determinada aplicação, serviço ou partição. Um pode invocar desativar pedido cópia de segurança, juntamente com o parâmetro de cópias de segurança limpa para ser verdade significaria que se todas as cópias de segurança existentes também são eliminadas. No entanto, suspender deve ser usado em cenários em que quer desativar as cópias de segurança temporariamente, como quando fica cheio disco local ou carregar a cópia de segurança está a falhar devido a problema de rede conhecido etc. 

Enquanto desativar pode ser invocado apenas num nível que era anteriormente ativado para cópia de segurança explicitamente no entanto suspensão pode ser aplicada em qualquer nível, que está atualmente ativada para cópia de segurança seja diretamente ou através de herança / hierarquia. Por exemplo, se a cópia de segurança é ativada ao nível da aplicação, um pode invocar desativar entretanto suspender apenas ao nível da aplicação pode ser invocado na aplicação, qualquer serviço ou a partição sob essa aplicação. 

## <a name="auto-restore-on-data-loss"></a>Restauro de automática na perda de dados
A partição de serviço pode perder dados devido a falhas inesperadas. Por exemplo, o disco para duas de três réplicas para uma partição (incluindo a réplica primária) obtém danificado ou eliminado.

Quando o Service Fabric detecta que a partição está em perda de dados, ele invoca `OnDataLossAsync` método na partição de interface e espera que a partição que tome as medidas necessárias para sair da perda de dados. Nesta situação, se a política de cópia de segurança efetiva na partição tiver `AutoRestoreOnDataLoss` sinalizador definido como `true` , em seguida, o restauro é ativado automaticamente utilizando a cópia de segurança mais recente disponível para esta partição.

## <a name="get-backup-configuration"></a>Obter configuração de cópia de segurança
APIs separadas são tornadas disponíveis para obter informações de configuração de cópia de segurança durante um _aplicativo_, _service_, e _partição_ âmbito. [Obter as informações de configuração de cópia de segurança de aplicativo](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackupconfigurationinfo), [Obtenha informações de configuração do serviço de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackupconfigurationinfo), e [obter informações de configuração de cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupconfigurationinfo) são essas APIs, respetivamente. Principalmente, essas APIs regressar a política de cópia de segurança aplicável, âmbito em que a política de cópia de segurança é aplicada e cópia de segurança de suspensão detalhes. Segue-se uma descrição resumida sobre os resultados devolvidos dessas APIs.

- Informações de configuração de cópia de segurança de aplicação: fornece os detalhes da política de cópia de segurança aplicada ao aplicativo e todas as políticas de excesso ridden em serviços e as partições que pertencem à aplicação. Também inclui as informações de suspensão para a aplicação e serviços e partições.

- Informações de configuração de cópia de segurança do serviço: mostra os detalhes da política de cópia de segurança em vigor no serviço e o âmbito em que esta política foi aplicada e todas as políticas de excesso ridden às respetivas partições. Ele também inclui as informações de suspensão para o serviço e respetivas partições.

- Informações de configuração de cópia de segurança de partição: mostra os detalhes da política de cópia de segurança em vigor na partição e o âmbito em que esta política foi aplicada. Ele também inclui as informações de suspensão para as partições.

## <a name="list-available-backups"></a>Lista cópias de segurança disponíveis

Cópias de segurança disponíveis podem ser apresentadas com a API de lista de cópia de segurança obter. Resultado da chamada de API inclui itens de cópia de segurança de informações relacionadas com todas as cópias de segurança disponíveis no armazenamento de cópia de segurança, o que está configurado na política de cópia de segurança aplicável. Diferentes variantes desta API sejam fornecidos à lista cópias de segurança disponíveis que pertencem a uma aplicação, serviço ou partição. Estas APIs suportam a obter o _mais recente_ cópia de segurança disponível de partições todos aplicáveis ou filtragem de cópias de segurança com base nos _data de início_ e _data de fim_.

Essas APIs também suportam a paginação dos resultados, quando _MaxResults_ parâmetro estiver definido como um número inteiro positivo diferente de zero, em seguida, a API devolve máxima _MaxResults_ itens de informações de cópia de segurança. No caso, existem mais itens de cópia de segurança de informações disponíveis do que o _MaxResults_ valor, em seguida, é devolvido um token de continuação. Continuação válida parâmetro token pode ser utilizado para obter o seguinte conjunto de resultados. Quando o valor de token de continuação válido é passado para a próxima chamada da API, a API devolve o próximo conjunto de resultados. Nenhum token de continuação está incluído na resposta quando todos os resultados disponíveis são devolvidos.

Segue-se as informações breves sobre variantes suportados.

- [Obter lista de cópia de segurança de aplicativo](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getapplicationbackuplist): Devolve uma lista de cópias de segurança disponíveis para cada partição que pertencem a determinada aplicação do Service Fabric.

- [Obter lista de cópia de segurança do serviço](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getservicebackuplist): Devolve uma lista de cópias de segurança disponíveis para cada partição que pertencem ao fornecido o serviço do Service Fabric.
 
- [Obter lista de cópia de segurança de partição](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackuplist): Devolve uma lista de cópias de segurança disponíveis para a partição especificada.

## <a name="next-steps"></a>Passos Seguintes
- [Referência da REST API de restauro de cópia de segurança](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/BackupPolicyAssociationExample.png
