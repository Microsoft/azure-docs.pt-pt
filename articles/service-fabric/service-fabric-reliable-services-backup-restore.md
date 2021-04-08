---
title: Cópia de Segurança e Restauro do Service Fabric
description: Documentação conceptual para Backup e Restauro de Tecido de Serviço, um serviço para configurar backup de serviços estatais confiáveis e atores fiáveis.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791616"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Backup e restaurar Serviços Fiáveis e Atores Fiáveis
A Azure Service Fabric é uma plataforma de alta disponibilidade que replica o estado em vários nós para manter esta alta disponibilidade.  Assim, mesmo que um nó no cluster falhe, os serviços continuam disponíveis. Embora este despedimento incorporado fornecido pela plataforma possa ser suficiente para alguns, em certos casos é desejável que o serviço suporte dados (para uma loja externa).

> [!NOTE]
> É fundamental fazer backup e restaurar os seus dados (e testar que funciona como esperado) para que possa recuperar de cenários de perda de dados.
> 

> [!NOTE]
> A Microsoft recomenda a utilização [de backup periódico e restauro](service-fabric-backuprestoreservice-quickstart-azurecluster.md) para configurar a cópia de segurança de dados de serviços estatais fidedigtos e atores fiáveis. 
> 


Por exemplo, um serviço pode querer fazer o back up dados para proteger dos seguintes cenários:

- Em caso de perda permanente de todo um cluster de Tecidos de Serviço.
- Perda permanente da maioria das réplicas de uma divisória de serviço
- Erros administrativos em que o Estado é acidentalmente apagado ou corrompido. Por exemplo, isto pode acontecer se um administrador com privilégio suficiente apagar erroneamente o serviço.
- Insetos no serviço que causam corrupção de dados. Por exemplo, isto pode acontecer quando uma atualização de código de serviço começa a escrever dados defeituosos para uma Coleção Fiável. Neste caso, tanto o código como os dados podem ter de ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para inteligência empresarial que acontece separadamente do serviço que gera os dados.

A funcionalidade Backup/Restore permite que os serviços construídos na API de Serviços Fiáveis criem e restaurem cópias de segurança. As APIs de backup fornecidas pela plataforma permitem o backup(s) do estado de uma partição de serviço, sem bloquear operações de leitura ou escrita. As APIs de restauro permitem restaurar o estado de uma partição de serviço a partir de uma cópia de segurança escolhida.

## <a name="types-of-backup"></a>Tipos de backup
Existem duas opções de backup: Full e Incremental.
Uma cópia de segurança completa é uma cópia de segurança que contém todos os dados necessários para recriar o estado da réplica: pontos de verificação e todos os registos de registos.
Uma vez que tem os pontos de verificação e o log, uma cópia de segurança completa pode ser restaurada por si só.

O problema com as cópias de segurança completas surge quando os postos de controlo são grandes.
Por exemplo, uma réplica que tenha 16 GB de estado terá postos de controlo que somam aproximadamente 16 GB.
Se tivermos um Objetivo de Ponto de Recuperação de cinco minutos, a réplica precisa de ser apoiada a cada cinco minutos.
Cada vez que recua, tem de copiar 16 GB de pontos de verificação para além de 50 MB (configurável) `CheckpointThresholdInMB` de registos.

![Exemplo de backup completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para este problema são cópias de segurança incrementais, onde o back up contém apenas os registos de registo alterados desde a última cópia de segurança.

![Exemplo de backup incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Uma vez que as cópias de segurança incrementais são apenas alterações uma vez que a última cópia de segurança (não inclui os pontos de verificação), tendem a ser mais rápidas, mas não podem ser restauradas por si próprias.
Para restaurar uma cópia de segurança incremental, toda a cadeia de backup é necessária.
Uma cadeia de backup é uma cadeia de backups começando com uma cópia de segurança completa e seguida por uma série de backups incrementais contíguos.

## <a name="backup-reliable-services"></a>Serviços fiáveis de backup
O autor do serviço tem o controlo total de quando fazer backups e onde as cópias de segurança serão armazenadas.

Para iniciar uma cópia de segurança, o serviço precisa de invocar a função de membro herdado `BackupAsync` .  
As cópias de segurança só podem ser feitas a partir de réplicas primárias, e requerem que o estatuto de escrita seja concedido.

Como mostrado abaixo, `BackupAsync` leva um `BackupDescription` objeto, onde se pode especificar uma cópia de segurança completa ou incremental, bem como uma função de retorno, `Func<< BackupInfo, CancellationToken, Task<bool>>>` que é invocada quando a pasta de backup foi criada localmente e está pronta para ser transferida para algum armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

O pedido de fazer uma cópia de segurança incremental pode falhar com `FabricMissingFullBackupException` . Esta exceção indica que uma das seguintes coisas está a acontecer:

- a réplica nunca teve um backup completo desde que se tornou primária,
- alguns dos registos de registo desde a última cópia de segurança foi truncado ou
- réplica passou o `MaxAccumulatedBackupLogSizeInMB` limite.

Os utilizadores podem aumentar a probabilidade de serem capazes de fazer cópias de segurança incrementais configurando `MinLogSizeInMB` ou `TruncationThresholdFactor` .
Aumentar estes valores aumenta o uso por réplica do disco.
Para mais informações, consulte [configuração de serviços fiáveis](service-fabric-reliable-services-configuration.md)

`BackupInfo` fornece informações sobre a cópia de segurança, incluindo a localização da pasta onde o tempo de execução guardou a cópia de segurança `BackupInfo.Directory` (). A função de retorno pode mover `BackupInfo.Directory` a função para uma loja externa ou outro local.  Esta função também devolve um bool que indica se foi capaz de mover com sucesso a pasta de backup para a sua localização alvo.

O seguinte código demonstra como o `BackupCallbackAsync` método pode ser usado para carregar a cópia de segurança para o Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo anterior, `ExternalBackupStore` é a classe de amostra que é usada para interagir com o armazenamento Azure Blob, e `UploadBackupFolderAsync` é o método que comprime a pasta e a coloca na loja Azure Blob.

Tenha em atenção que:

  - Só pode haver uma operação de reserva a bordo por réplica a qualquer momento. Mais de `BackupAsync` uma chamada de cada vez vai lançar para limitar os `FabricBackupInProgressException` backups de voo para um.
  - Se uma réplica falhar enquanto uma cópia de segurança está em andamento, a cópia de segurança pode não ter sido concluída. Assim, uma vez terminada a falha, é da responsabilidade do serviço reiniciar a cópia de segurança `BackupAsync` invocando, se necessário.

## <a name="restore-reliable-services"></a>Restaurar serviços fiáveis
Em geral, os casos em que poderá ser necessário realizar uma operação de restauro enquadram-se numa destas categorias:

  - A divisão de serviço perdeu dados. Por exemplo, o disco de duas de três réplicas para uma partição (incluindo a réplica primária) é corrompido ou limpo. A nova primária pode precisar de restaurar os dados a partir de uma cópia de segurança.
  - Todo o serviço está perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam de ser restaurados.
  - O serviço replicou dados de aplicações corruptos (por exemplo, por causa de um bug de aplicação). Neste caso, o serviço tem de ser atualizado ou revertido para remover a causa da corrupção, e os dados não-corruptos têm de ser restaurados.

Embora muitas abordagens sejam possíveis, oferecemos alguns exemplos sobre a utilização `RestoreAsync` para recuperar dos cenários acima referidos.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição em Serviços Fiáveis
Neste caso, o tempo de funcionação detetaria automaticamente a perda de dados e invocaria a `OnDataLossAsync` API.

O autor do serviço precisa de realizar o seguinte para recuperar:

  - Anular o método de classe base virtual `OnDataLossAsync` .
  - Encontre a cópia de segurança mais recente na localização externa que contém as cópias de segurança do serviço.
  - Descarregue a cópia de segurança mais recente (e descompromeça a cópia de segurança na pasta de reserva se tiver sido comprimido).
  - O `OnDataLossAsync` método fornece um `RestoreContext` . Ligue para a `RestoreAsync` API no `RestoreContext` fornecido.
  - Retorno verdadeiro se a restauração foi um sucesso.

Segue-se um exemplo de implementação do `OnDataLossAsync` método:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` passou para a `RestoreContext.RestoreAsync` chamada contém um membro chamado `BackupFolderPath` .
Ao restaurar uma única cópia de segurança completa, esta `BackupFolderPath` deve ser definida para o caminho local da pasta que contém a sua cópia de segurança completa.
Ao restaurar uma cópia de segurança completa e uma série de backups incrementais, `BackupFolderPath` deve ser definido para o caminho local da pasta que não só contém a cópia de segurança completa, mas também todas as cópias de segurança incrementais.
`RestoreAsync` a chamada pode ser lançada `FabricMissingFullBackupException` se o fornecido não conter uma cópia de segurança `BackupFolderPath` completa.
Também pode lançar `ArgumentException` se tiver uma cadeia quebrada de `BackupFolderPath` backups incrementais.
Por exemplo, se contiver a cópia de segurança completa, a primeira incremental e a terceira cópia de segurança incremental, mas não a segunda cópia de segurança incremental.

> [!NOTE]
> A RestorePolicy está definida como Segura por defeito.  Isto significa que a `RestoreAsync` API falhará com a ArgumentException se detetar que a pasta de backup contém um estado mais antigo ou igual ao estado contido nesta réplica.  `RestorePolicy.Force` pode ser usado para saltar esta verificação de segurança. Isto é especificado como parte de `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Serviço apagado ou perdido
Se um serviço for removido, deve primeiro recriar o serviço antes de os dados poderem ser restaurados.  É importante criar o serviço com a mesma configuração, por exemplo, o esquema de partição, para que os dados possam ser restaurados sem problemas.  Uma vez que o serviço esteja em cima, a API para restaurar os dados `OnDataLossAsync` (acima) tem de ser invocada em cada partição deste serviço. Uma forma de o conseguir é utilizando [FabricClient.TestManagementClient.StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) em cada divisória.  

A partir deste ponto, a implementação é a mesma que o cenário acima referido. Cada divisória precisa restaurar o mais recente backup relevante da loja externa. Uma ressalva é que o ID da partição pode ter mudado agora, uma vez que o tempo de execução cria IDs de partição dinamicamente. Assim, o serviço precisa de armazenar as informações e o nome de serviço apropriados para identificar a cópia de segurança mais recente correta para restaurar para cada partição.

> [!NOTE]
> Não é aconselhável utilizar `FabricClient.ServiceManager.InvokeDataLossAsync` em cada divisória para restaurar todo o serviço, uma vez que isso pode corromper o seu estado de agrupamento.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados de aplicações corruptas
Se a atualização de aplicações recentemente implementada tiver um bug, isso pode causar corrupção de dados. Por exemplo, uma atualização de aplicações pode começar a atualizar todos os registos de números de telefone num Dicionário Fiável com um código de área inválido.  Neste caso, os números de telefone inválidos serão replicados uma vez que o Service Fabric não tem conhecimento da natureza dos dados que estão a ser armazenados.

A primeira coisa a fazer depois de detetar um bug tão flagrante que causa corrupção de dados é congelar o serviço ao nível da aplicação e, se possível, atualizar para a versão do código de aplicação que não tem o bug.  No entanto, mesmo após a fixação do código de serviço, os dados podem ainda ser corruptos, pelo que os dados podem ter de ser restabelecidos.  Nestes casos, pode não ser suficiente para restaurar o mais recente backup, uma vez que os últimos backups também podem ser corruptos.  Assim, tem que encontrar a última cópia de segurança que foi feita antes que os dados foram corrompidos.

Se não tiver a certeza de quais as cópias de segurança que são corruptas, pode implantar um novo cluster de Tecidos de Serviço e restaurar as cópias de segurança das divisórias afetadas, tal como o cenário acima "Eliminado ou perdido serviço".  Para cada divisória, comece a restaurar os backups da mais recente ao mínimo. Assim que encontrar um backup que não tenha a corrupção, mova/elimine todos os backups desta partição que foram mais recentes (do que aquele backup). Repita este processo para cada divisória. Agora, quando `OnDataLossAsync` é chamado para a partição no cluster de produção, o último backup encontrado na loja externa será o escolhido pelo processo acima.

Agora, os passos na secção "Serviço apagado ou perdido" podem ser usados para restaurar o estado do serviço ao estado antes que o código do buggy corrompa o estado.

Tenha em atenção que:

  - Quando restauramos, há a possibilidade de a cópia de segurança ser restaurada ser mais antiga do que o estado da partição antes de os dados serem perdidos. Por isso, deve restaurar apenas como último recurso para recuperar o máximo de dados possível.
  - A cadeia que representa o caminho da pasta de reserva e os caminhos dos ficheiros dentro da pasta de backup podem ser superiores a 255 caracteres, dependendo do comprimento do caminho FabricDataRoot e do nome do Tipo de Aplicação. Isto pode fazer com que alguns métodos `Directory.Move` .NET, como, atirem a `PathTooLongException` exceção. Uma solução alternativa é chamar diretamente as APIs kernel32, `CopyFile` como.

## <a name="back-up-and-restore-reliable-actors"></a>Recuar e restaurar atores fiáveis


O Quadro de Atores Fiáveis é construído em cima de Serviços Fiáveis. O ActorService, que acolhe o(s) ator(s) é um serviço de confiança. Assim, toda a funcionalidade de backup e restauro disponível em Serviços Fidedigrendadores também está disponível para atores fiáveis (exceto comportamentos específicos do fornecedor do estado). Uma vez que os backups serão assumidos por partição, os estados para todos os intervenientes nessa partição serão apoiados (e a restauração é semelhante e acontecerá numa base de partição). Para realizar backup/restauro, o proprietário do serviço deve criar uma classe de serviço de ator personalizado que deriva da classe ActorService e, em seguida, fazer backup/restaurar similar a Reliable Services como descrito acima em secções anteriores.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Quando cria uma aula de serviço de ator personalizado, tem de registar isso também ao registar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O fornecedor estatal padrão para atores fiáveis é `KvsActorStateProvider` . A cópia de segurança incremental não é ativada por padrão para `KvsActorStateProvider` . Pode ativar a cópia de segurança incremental criando `KvsActorStateProvider` com a definição adequada no seu construtor e, em seguida, transmitindo-a ao construtor actorService, como mostrado no seguinte corte de código:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Após a ativação de uma cópia de segurança incremental, a tomada de uma cópia de segurança incremental pode falhar com a FabricMissingFullBackupException por uma das seguintes razões e terá de fazer uma cópia de segurança completa antes de fazer backup incremental):

  - A réplica nunca teve um apoio completo desde que se tornou primária.
  - Alguns dos registos foram truncados desde que o último reforço foi feito.

Quando a cópia de segurança incremental está ativada, `KvsActorStateProvider` não utiliza o tampão circular para gerir os registos de registos e trunca-os periodicamente. Se não for feita uma cópia de segurança por um período de 45 minutos, o sistema trunta automaticamente os registos de registo. Este intervalo pode ser configurado especificando `logTruncationIntervalInMinutes` em `KvsActorStateProvider` construtor (semelhante ao que permite a cópia de segurança incremental). Os registos de registos também podem ser truncados se a réplica primária precisar de construir outra réplica enviando todos os seus dados.

Ao restaurar a partir de uma cadeia de backup, semelhante a Reliable Services, o BackupFolderPath deve conter subdiretórios com uma subdirectória contendo cópias de segurança completas e outras subdiretórios que contenham backup incremental(s). A API de restauro lançará a FabricException com uma mensagem de erro apropriada se a validação da cadeia de backup falhar. 

> [!NOTE]
> `KvsActorStateProvider` atualmente ignora a opção RestorePolicy.Safe. O suporte a esta funcionalidade está previsto para o próximo lançamento.
> 

## <a name="testing-back-up-and-restore"></a>Testar back-up e restaurar
É importante garantir que os dados críticos estão a ser apoiados e podem ser restaurados. Isto pode ser feito invocando o `Start-ServiceFabricPartitionDataLoss` cmdlet no PowerShell que pode induzir a perda de dados numa determinada partição para testar se a cópia de segurança dos dados e restaurar a funcionalidade do seu serviço está a funcionar como esperado.  Também é possível invocar programáticamente a perda de dados e restaurar a partir desse evento também.

> [!NOTE]
> Pode encontrar uma implementação de amostra de cópia de segurança e restaurar a funcionalidade na Aplicação de Referência Web no GitHub. Por favor, olhe para o `Inventory.Service` serviço para mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Sob o capot: mais detalhes sobre backup e restauro
Aqui estão mais alguns detalhes sobre o backup e restauro.

### <a name="backup"></a>Backup
O Gestor de Estado Fiável fornece a capacidade de criar backups consistentes sem bloquear quaisquer operações de leitura ou escrita. Para tal, utiliza um mecanismo de persistência de pontos de controlo e registo.  O Gestor de Estado Fiável toma pontos de verificação felpudos (leves) em determinados pontos para aliviar a pressão do diário transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é chamado, o Gestor de Estado Fiável instrui todos os objetos fiáveis para copiarem os seus ficheiros de verificação mais recentes para uma pasta de backup local.  Em seguida, o Gestor de Estado Fiável copia todos os registos de registo, a partir do "ponto de partida" para o registo de registo mais recente na pasta de backup.  Uma vez que todos os registos de registo até ao registo mais recente estão incluídos na cópia de segurança e o Gestor de Estado Fiável preserva a marcação de registo sonoro, o Gestor de Estado Fiável garante que todas as transações que são cometidas `CommitAsync` (devolvidas com sucesso) estão incluídas na cópia de segurança.

Qualquer transação que se comprometa depois `BackupAsync` foi chamada pode ou não estar na cópia de segurança.  Uma vez que a pasta de backup local tenha sido povoada pela plataforma (isto é, a cópia de segurança local é completada pelo tempo de funcionamento), o backback de backup do serviço é invocado.  Esta chamada é responsável por mover a pasta de reserva para um local externo, como o Azure Storage.

### <a name="restore"></a>Restauro
O Gestor estatal fiável fornece a capacidade de restaurar a partir de uma cópia de segurança utilizando a `RestoreAsync` API.  
O `RestoreAsync` método em diante só pode ser chamado dentro do `RestoreContext` `OnDataLossAsync` método.
O bool devolvido `OnDataLossAsync` indica se o serviço restaurou o seu estado a partir de uma fonte externa.
Se o retorno for verdadeiro, o `OnDataLossAsync` Service Fabric reconstruirá todas as outras réplicas desta primária. O Service Fabric garante que as réplicas que receberão `OnDataLossAsync` a primeira transição de chamada para o papel principal, mas não lhes é concedido o estado de leitura ou o estado de escrita.
Isto implica que para os implementadores statefulService, `RunAsync` não serão chamados até `OnDataLossAsync` que termine com sucesso.
Então, `OnDataLossAsync` será invocado nas novas primárias.
Até que um serviço complete esta API com sucesso (devolvendo verdadeiro ou falso) e termine a reconfiguração relevante, a API continuará a ser chamada uma de cada vez.

`RestoreAsync` primeiro deixa cair todo o estado existente na réplica primária que foi chamado. Em seguida, o Gestor de Estado Fiável cria todos os objetos fiáveis que existem na pasta de reserva. Em seguida, os objetos Fidedignas são instruídos a restaurar a partir dos seus pontos de verificação na pasta de reserva. Finalmente, o Gestor de Estado Fiável recupera o seu próprio estado a partir dos registos de registos na pasta de backup e realiza a recuperação. Como parte do processo de recuperação, as operações a partir do "ponto de partida" que cometeram registos de registos na pasta de backup são reproduzidas nos objetos Fidedignas. Este passo garante que o estado recuperado é consistente.

## <a name="next-steps"></a>Passos seguintes
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Arranque rápido de serviços fiáveis](service-fabric-reliable-services-quick-start.md)
  - [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
  - [Configuração de Serviços Fiáveis](service-fabric-reliable-services-configuration.md)
  - [Referência do programador para Coleções Fiáveis](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Cópia de segurança e restauro periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
