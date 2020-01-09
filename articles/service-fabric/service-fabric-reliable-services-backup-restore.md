---
title: Backup e restauração de Service Fabric
description: Documentação conceitual para Service Fabric backup e restauração, um serviço para configurar o backup de serviços confiáveis com estado e Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: 712069a34b6bc5d8aa4bcbab3fdbf9fc9cd8958b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645553"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Backup e restauração Reliable Services e Reliable Actors
O Azure Service Fabric é uma plataforma de alta disponibilidade que Replica o estado em vários nós para manter essa alta disponibilidade.  Portanto, mesmo se um nó do cluster falhar, os serviços continuarão disponíveis. Embora essa redundância interna fornecida pela plataforma possa ser suficiente para alguns, em alguns casos, é desejável que o serviço faça backup de dados (em um repositório externo).

> [!NOTE]
> É essencial fazer backup e restaurar seus dados (e testar se ele funciona conforme o esperado) para que você possa se recuperar de cenários de perda de dados.
> 

> [!NOTE]
> A Microsoft recomenda usar [backup e restauração periódicos](service-fabric-backuprestoreservice-quickstart-azurecluster.md) para configurar o backup de dados de serviços confiáveis com estado e Reliable Actors. 
> 


Por exemplo, um serviço pode querer fazer backup de dados para proteger os seguintes cenários:

- No caso de perda permanente de um cluster de Service Fabric inteiro.
- Perda permanente de uma maioria das réplicas de uma partição de serviço
- Erros administrativos nos quais o estado é acidentalmente excluído ou corrompido. Por exemplo, isso pode acontecer se um administrador com privilégios suficientes excluir erroneamente o serviço.
- Bugs no serviço que causam corrupção de dados. Por exemplo, isso pode acontecer quando uma atualização de código de serviço começa a gravar dados com falha em uma coleção confiável. Nesse caso, o código e os dados podem precisar ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para business intelligence que aconteça separadamente do serviço que gera os dados.

O recurso de backup/restauração permite que os serviços criados na API de Reliable Services criem e restaurem backups. As APIs de backup fornecidas pela plataforma permitem backups do estado de uma partição de serviço, sem bloquear operações de leitura ou gravação. As APIs de restauração permitem que o estado de uma partição de serviço seja restaurado a partir de um backup escolhido.

## <a name="types-of-backup"></a>Tipos de backup
Há duas opções de backup: completa e incremental.
Um backup completo é um backup que contém todos os dados necessários para recriar o estado da réplica: pontos de verificação e todos os registros de log.
Como ele tem os pontos de verificação e o log, um backup completo pode ser restaurado por si só.

O problema com backups completos surge quando os pontos de verificação são grandes.
Por exemplo, uma réplica que tem 16 GB de estado terá pontos de verificação que somam aproximadamente 16 GB.
Se tivermos um objetivo de ponto de recuperação de cinco minutos, a réplica precisará ser submetida a backup a cada cinco minutos.
Cada vez que ele faz backup, ele precisa copiar 16 GB de pontos de verificação, além de 50 MB (configuráveis usando `CheckpointThresholdInMB`) de logs.

![Exemplo de backup completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para esse problema é backups incrementais, em que o backup contém apenas os registros de log alterados desde o último backup.

![Exemplo de backup incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Como os backups incrementais são apenas alterações desde o último backup (não inclui os pontos de verificação), eles tendem a ser mais rápidos, mas não podem ser restaurados por conta própria.
Para restaurar um backup incremental, a cadeia de backup inteira é necessária.
Uma cadeia de backup é uma cadeia de backups que começa com um backup completo e seguida por um número de backups incrementais contíguos.

## <a name="backup-reliable-services"></a>Reliable Services de backup
O autor do serviço tem controle total de quando fazer backups e onde os backups serão armazenados.

Para iniciar um backup, o serviço precisa invocar a função membro herdada `BackupAsync`.  
Os backups só podem ser feitos de réplicas primárias e exigem o status de gravação para serem concedidos.

Como mostrado abaixo, `BackupAsync` usa um objeto `BackupDescription`, onde um pode especificar um backup completo ou incremental, bem como uma função de retorno de chamada, `Func<< BackupInfo, CancellationToken, Task<bool>>>` invocado quando a pasta de backup foi criada localmente e está pronta para ser movida para algum armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

A solicitação para fazer um backup incremental pode falhar com `FabricMissingFullBackupException`. Essa exceção indica que um dos seguintes itens está ocorrendo:

- a réplica nunca fez um backup completo, pois se tornou primária,
- alguns dos registros de log desde o último backup foram truncados ou
- a réplica ultrapassou o limite de `MaxAccumulatedBackupLogSizeInMB`.

Os usuários podem aumentar a probabilidade de poder fazer backups incrementais Configurando `MinLogSizeInMB` ou `TruncationThresholdFactor`.
Aumentar esses valores aumenta o uso de disco por réplica.
Para obter mais informações, consulte [configuração de Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` fornece informações sobre o backup, incluindo o local da pasta em que o tempo de execução salvou o backup (`BackupInfo.Directory`). A função de retorno de chamada pode mover o `BackupInfo.Directory` para um repositório externo ou outro local.  Essa função também retorna um bool que indica se foi possível mover com êxito a pasta de backup para seu local de destino.

O código a seguir demonstra como o método `BackupCallbackAsync` pode ser usado para carregar o backup no armazenamento do Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo anterior, `ExternalBackupStore` é a classe de exemplo usada para a interface com o armazenamento de BLOBs do Azure e `UploadBackupFolderAsync` é o método que compacta a pasta e a coloca no repositório de blob do Azure.

Tenha em atenção que:

  - Pode haver apenas uma operação de backup em trânsito por réplica em um determinado momento. Mais de uma chamada de `BackupAsync` de cada vez gerará `FabricBackupInProgressException` para limitar os backups de em andamento a um.
  - Se uma réplica falhar enquanto um backup estiver em andamento, talvez o backup não tenha sido concluído. Assim, após a conclusão do failover, é responsabilidade do serviço reiniciar o backup invocando `BackupAsync` conforme necessário.

## <a name="restore-reliable-services"></a>Restaurar Reliable Services
Em geral, os casos em que talvez você precise executar uma operação de restauração se enquadram em uma destas categorias:

  - A partição de serviço perdeu os dados. Por exemplo, o disco de duas de três réplicas para uma partição (incluindo a réplica primária) é corrompido ou apagado. O novo primário pode precisar restaurar dados de um backup.
  - O serviço inteiro é perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam ser restaurados.
  - O serviço replicou os dados de aplicativo corrompidos (por exemplo, devido a um bug de aplicativo). Nesse caso, o serviço precisa ser atualizado ou revertido para remover a causa do dano, e os dados não corrompidos têm que ser restaurados.

Embora muitas abordagens sejam possíveis, oferecemos alguns exemplos de como usar `RestoreAsync` para se recuperar dos cenários acima.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição no Reliable Services
Nesse caso, o tempo de execução detectará automaticamente a perda de dados e invocará a API `OnDataLossAsync`.

O autor do serviço precisa executar o seguinte para recuperar:

  - Substitua o método de classe base virtual `OnDataLossAsync`.
  - Localize o backup mais recente no local externo que contém os backups do serviço.
  - Baixe o backup mais recente (e descompacte o backup na pasta de backup se ele tiver sido compactado).
  - O método `OnDataLossAsync` fornece um `RestoreContext`. Chame a API de `RestoreAsync` no `RestoreContext`fornecido.
  - Retorna true se a restauração foi um sucesso.

Veja a seguir um exemplo de implementação do método `OnDataLossAsync`:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` passado para a chamada `RestoreContext.RestoreAsync` contém um membro chamado `BackupFolderPath`.
Ao restaurar um único backup completo, esse `BackupFolderPath` deve ser definido como o caminho local da pasta que contém o backup completo.
Ao restaurar um backup completo e vários backups incrementais, `BackupFolderPath` deve ser definido como o caminho local da pasta que não só contém o backup completo, mas também todos os backups incrementais.
`RestoreAsync` chamada poderá gerar `FabricMissingFullBackupException` se a `BackupFolderPath` fornecida não contiver um backup completo.
Ele também pode gerar `ArgumentException` se `BackupFolderPath` tiver uma cadeia quebrada de backups incrementais.
Por exemplo, se ele contiver o backup completo, o primeiro incremental e o terceiro backup incremental, mas não o segundo backup incremental.

> [!NOTE]
> O RestorePolicy é definido como seguro por padrão.  Isso significa que a API de `RestoreAsync` falhará com ArgumentException se detectar que a pasta de backup contém um estado que é mais antigo ou igual ao estado contido nesta réplica.  `RestorePolicy.Force` pode ser usado para ignorar essa verificação de segurança. Isso é especificado como parte de `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Serviço excluído ou perdido
Se um serviço for removido, você deverá primeiro recriar o serviço antes que os dados possam ser restaurados.  É importante criar o serviço com a mesma configuração, por exemplo, o esquema de particionamento, para que os dados possam ser restaurados diretamente.  Depois que o serviço estiver ativo, a API para restaurar dados (`OnDataLossAsync` acima) precisará ser invocada em cada partição desse serviço. Uma maneira de conseguir isso é usando [FabricClient. TestManagementClient. StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) em todas as partições.  

A partir desse ponto, a implementação é a mesma do cenário acima. Cada partição precisa restaurar o backup mais recente relevante do armazenamento externo. Uma limitação é que a ID da partição pode ter sido alterada, já que o tempo de execução cria IDs de partição dinamicamente. Portanto, o serviço precisa armazenar as informações de partição e o nome do serviço apropriados para identificar o backup mais recente a ser restaurado de cada partição.

> [!NOTE]
> Não é recomendável usar `FabricClient.ServiceManager.InvokeDataLossAsync` em cada partição para restaurar o serviço inteiro, já que isso pode corromper o estado do cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados de aplicativo corrompidos
Se a atualização do aplicativo implantado recentemente tiver um bug, isso poderá causar a corrupção dos dados. Por exemplo, uma atualização de aplicativo pode começar a atualizar cada registro de número de telefone em um dicionário confiável com um código de área inválido.  Nesse caso, os números de telefone inválidos serão replicados, pois Service Fabric não está ciente da natureza dos dados que estão sendo armazenados.

A primeira coisa a fazer depois de detectar um bug flagrantes que causa corrupção de dados é congelar o serviço no nível do aplicativo e, se possível, atualizar para a versão do código do aplicativo que não tem o bug.  No entanto, mesmo depois que o código do serviço é corrigido, os dados ainda podem estar corrompidos e, portanto, talvez seja necessário restaurar os dados.  Nesses casos, pode não ser suficiente restaurar o backup mais recente, já que os backups mais recentes também podem estar corrompidos.  Portanto, você precisa localizar o último backup feito antes que os dados tenham sido corrompidos.

Se não tiver certeza de quais backups estão corrompidos, você poderá implantar um novo cluster de Service Fabric e restaurar os backups de partições afetadas, assim como o cenário de "serviço excluído ou perdido" acima.  Para cada partição, comece a restaurar os backups do mais recente para o menos. Depois de encontrar um backup que não tenha a corrupção, mova/exclua todos os backups desta partição que foram mais recentes (do que esse backup). Repita esse processo para cada partição. Agora, quando `OnDataLossAsync` for chamado na partição no cluster de produção, o último backup encontrado no repositório externo será aquele escolhido pelo processo acima.

Agora, as etapas na seção "serviço excluído ou perdido" podem ser usadas para restaurar o estado do serviço para o estado anterior à corrupção do estado do código de bugs.

Tenha em atenção que:

  - Quando você restaura, há uma chance de que o backup que está sendo restaurado seja mais antigo do que o estado da partição antes que os dados sejam perdidos. Por isso, você deve restaurar apenas como último recurso para recuperar o máximo de dados possível.
  - A cadeia de caracteres que representa o caminho da pasta de backup e os caminhos dos arquivos dentro da pasta de backup pode ter mais de 255 caracteres, dependendo do caminho do FabricDataRoot e do comprimento do nome do tipo de aplicativo. Isso pode causar alguns métodos .NET, como `Directory.Move`, para gerar a exceção `PathTooLongException`. Uma solução alternativa é chamar diretamente as APIs do Kernel32, como `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Fazer backup e restaurar Reliable Actors


O Reliable Actors Framework é criado sobre Reliable Services. O ActorService, que hospeda os atores, é um serviço confiável com estado. Portanto, todas as funcionalidades de backup e restauração disponíveis no Reliable Services também estão disponíveis para Reliable Actors (exceto comportamentos que são específicos do provedor de estado). Como os backups serão feitos por partição, os Estados de todos os atores nessa partição serão submetidos a backup (e a restauração será semelhante e acontecerá em uma base por partição). Para executar backup/restauração, o proprietário do serviço deve criar uma classe de serviço de ator personalizada que derive da classe ActorService e, em seguida, faça backup/restauração semelhante a Reliable Services conforme descrito acima nas seções anteriores.

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

Ao criar uma classe de serviço de ator personalizada, você precisa registrá-la também ao registrar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O provedor de estado padrão para Reliable Actors é `KvsActorStateProvider`. O backup incremental não está habilitado por padrão para `KvsActorStateProvider`. Você pode habilitar o backup incremental criando `KvsActorStateProvider` com a configuração apropriada em seu construtor e, em seguida, passando-o para o Construtor ActorService, conforme mostrado no seguinte trecho de código:

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

Após a habilitação do backup incremental, fazer um backup incremental pode falhar com o FabricMissingFullBackupException por um dos seguintes motivos e você precisará fazer um backup completo antes de fazer backups incrementais:

  - A réplica nunca fez um backup completo desde que se tornou primária.
  - Alguns dos registros de log foram truncados desde que o último backup foi feito.

Quando o backup incremental está habilitado, o `KvsActorStateProvider` não usa o buffer circular para gerenciar seus registros de log e o trunca periodicamente. Se nenhum backup for feito pelo usuário por um período de 45 minutos, o sistema truncará automaticamente os registros de log. Esse intervalo pode ser configurado especificando `logTruncationIntervalInMinutes` no Construtor `KvsActorStateProvider` (semelhante ao de habilitar o backup incremental). Os registros de log também poderão ser truncados se a réplica primária precisar criar outra réplica enviando todos os seus dados.

Ao fazer a restauração de uma cadeia de backup, semelhante à Reliable Services, o BackupFolderPath deve conter subdiretórios com um subdiretório contendo backup completo e outros subdiretórios contendo backups incrementais. A API de restauração gerará Fabricexception com a mensagem de erro apropriada se a validação da cadeia de backup falhar. 

> [!NOTE]
> Atualmente, `KvsActorStateProvider` ignora a opção RestorePolicy. Safe. O suporte para esse recurso está planejado em uma versão futura.
> 

## <a name="testing-back-up-and-restore"></a>Testando o backup e a restauração
É importante garantir que os dados críticos sejam submetidos a backup e possam ser restaurados do. Isso pode ser feito invocando o cmdlet `Start-ServiceFabricPartitionDataLoss` no PowerShell que pode induzir a perda de dados em uma determinada partição para testar se a funcionalidade de backup e restauração de dados do serviço está funcionando conforme o esperado.  Também é possível invocar a perda de dados e restaurá-las por meio de programação a partir desse evento também.

> [!NOTE]
> Você pode encontrar uma implementação de exemplo de funcionalidade de backup e restauração no aplicativo Web Reference no GitHub. Consulte o serviço de `Inventory.Service` para obter mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Nos bastidores: mais detalhes sobre backup e restauração
Veja mais alguns detalhes sobre backup e restauração.

### <a name="backup"></a>Backup
O Gerenciador de estado confiável fornece a capacidade de criar backups consistentes sem bloquear nenhuma operação de leitura ou gravação. Para fazer isso, ele utiliza um mecanismo de ponto de verificação e de persistência de log.  O Gerenciador de estado confiável usa pontos de verificação difuso (leves) em determinados pontos para aliviar a pressão do log transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é chamado, o Gerenciador de estado confiável instrui todos os objetos confiáveis a copiarem seus arquivos de ponto de verificação mais recentes para uma pasta de backup local.  Em seguida, o Gerenciador de estado confiável copia todos os registros de log, começando do "ponteiro de início" para o registro de log mais recente na pasta de backup.  Como todos os registros de log até o registro de log mais recente estão incluídos no backup e o Gerenciador de estado confiável preserva o registro em log write-ahead, o Gerenciador de estado confiável garante que todas as transações confirmadas (`CommitAsync` retornados com êxito) sejam incluídas no backup.

Qualquer transação que confirma após `BackupAsync` foi chamada pode ou não estar no backup.  Depois que a pasta de backup local tiver sido populada pela plataforma (ou seja, o backup local será concluído pelo tempo de execução), o retorno de chamada de backup do serviço será invocado.  Esse retorno de chamada é responsável por mover a pasta de backup para um local externo, como o armazenamento do Azure.

### <a name="restore"></a>Restauro
O Gerenciador de estado confiável fornece a capacidade de restaurar a partir de um backup usando a API `RestoreAsync`.  
O método `RestoreAsync` em `RestoreContext` pode ser chamado somente dentro do método `OnDataLossAsync`.
O bool retornado por `OnDataLossAsync` indica se o serviço restaurou seu estado a partir de uma fonte externa.
Se o `OnDataLossAsync` retornar true, Service Fabric recriará todas as outras réplicas a partir desse primário. Service Fabric garante que as réplicas que receberão `OnDataLossAsync` chamar a primeira transição para a função primária, mas não receberão o status de leitura ou de gravação.
Isso implica que, para os implementadores de StatefulService, `RunAsync` não será chamado até que `OnDataLossAsync` seja concluído com êxito.
Em seguida, `OnDataLossAsync` será invocado no novo primário.
Até que um serviço Conclua essa API com êxito (retornando true ou false) e conclui a reconfiguração relevante, a API continuará sendo chamada uma de cada vez.

`RestoreAsync` primeiro descarta todos os Estados existentes na réplica primária em que ele foi chamado. Em seguida, o Gerenciador de estado confiável cria todos os objetos confiáveis que existem na pasta de backup. Em seguida, os objetos confiáveis são instruídos a restaurar de seus pontos de verificação na pasta de backup. Por fim, o Gerenciador de estado confiável recupera seu próprio estado dos registros de log na pasta de backup e executa a recuperação. Como parte do processo de recuperação, as operações que começam do "ponto de partida" que têm registros de log confirmados na pasta de backup são reproduzidas para os objetos confiáveis. Essa etapa garante que o estado recuperado seja consistente.

## <a name="next-steps"></a>Passos seguintes
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Guia de início rápido Reliable Services](service-fabric-reliable-services-quick-start.md)
  - [Notificações de Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Configuração de Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referência do desenvolvedor para coleções confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Cópia de segurança e restauro periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

