---
title: Cópia de segurança do Service Fabric e restauro | Documentos da Microsoft
description: Documentação conceptual para o restauro e cópia de segurança do Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: subramar,zhol
ms.assetid: 91ea6ca4-cc2a-4155-9823-dcbd0b996349
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: d01d2f18ed35d1752f97f405ae7f7bfb4708ca0d
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570050"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Cópia de segurança e restaurar Reliable Services e Reliable Actors
O Azure Service Fabric é uma plataforma de elevada disponibilidade que replica o estado em vários nós para manter este elevada disponibilidade.  Assim, mesmo que um nó no cluster falhar, os serviços continuam disponíveis. Embora esta redundância incorporados fornecida pela plataforma pode ser suficiente para alguns, em certos casos é desejável para o serviço de cópia de segurança de dados (para um repositório externo).

> [!NOTE]
> É fundamental para criar cópias de segurança e restauro dos dados (e testar se funciona conforme esperado) para que possa recuperar a partir de cenários de perda de dados.
> 

> [!NOTE]
> A Microsoft recomenda usar [periódica backup e restauração](service-fabric-backuprestoreservice-quickstart-azurecluster.md) para configurar a cópia de segurança de dados de com monitoração de estado de Reliable services e Reliable Actors. 
> 


Por exemplo, um serviço pode querer criar cópias de segurança para proteger contra os seguintes cenários:

- Em caso de perda permanente de um cluster do Service Fabric inteiro.
- Perda permanente de a maioria das réplicas de uma partição de serviço
- Erros administrativos no qual o estado acidentalmente obtém excluído ou danificado. Por exemplo, isto pode acontecer se um administrador com privilégios suficientes erroneamente elimina o serviço.
- Bugs no serviço que danificar os dados. Por exemplo, isto pode acontecer quando uma atualização do código de serviço é iniciado a escrita de dados com falhas para uma coleção fiável. Nesse caso, o código e os dados podem ter a ser revertida para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter um processamento offline de dados para business intelligence que acontece em separado do serviço que gera os dados.

A funcionalidade de cópia de segurança/restauro permite que os serviços criados sobre a API de serviços fiáveis para criar e restaurar cópias de segurança. As APIs de cópia de segurança fornecidas pela plataforma permitem que um ou mais backups de estado de uma partição de serviço, sem bloqueio de leitura ou operações de escrita. O restauro APIs permitem que o estado de uma partição de serviço para ser restaurado a partir de uma cópia de segurança escolhida.

## <a name="types-of-backup"></a>Tipos de cópia de segurança
Existem duas opções de cópia de segurança: Completas e incrementais.
Um backup completo é uma cópia de segurança que contém todos os dados necessários para recriar o estado da réplica: os pontos de verificação e todos os registros de log.
Uma vez que tem os pontos de verificação e o registo, uma cópia de segurança completa pode ser restaurada por si só.

O problema com cópias de segurança completas surge quando os pontos de verificação são grandes.
Por exemplo, uma réplica que tenha 16 GB de estado irá ter pontos de verificação que adicionam até aproximadamente 16 GB.
Se tivermos um objetivo de ponto de recuperação de cinco minutos, a réplica tem de ser copiadas a cada cinco minutos.
Sempre que ele faz o backup tem de copiar 16 GB de pontos de verificação, além de 50 MB (usando configuráveis `CheckpointThresholdInMB`) valor de registos.

![Exemplo de cópia de segurança completa.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para esse problema é cópias de segurança incrementais, em que o cópia de segurança contém apenas os registros de log alterados desde a última cópia de segurança.

![Exemplo de cópia de segurança incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Uma vez que as cópias de segurança incrementais são apenas as alterações desde a última cópia de segurança (não inclui os pontos de verificação), eles tendem a ser mais rápida, mas não é possível restaurar por conta própria.
Para restaurar uma cópia de segurança incremental, toda a cadeia de cópia de segurança é necessária.
Uma cadeia de cópia de segurança é uma cadeia de cópias de segurança começando com uma cópia de segurança completa e seguida por um número de cópias de segurança incrementais contíguos.

## <a name="backup-reliable-services"></a>Cópia de segurança do Reliable Services
O autor de serviço tem total controle sobre quando efetuar cópias de segurança e onde serão armazenadas as cópias de segurança.

Para iniciar uma cópia de segurança, o serviço tem de invocar a função de membro herdado `BackupAsync`.  
As cópias de segurança podem ser feitas apenas a partir de réplicas primárias e necessitam que o estado de escrita para ser concedida.

Conforme mostrado abaixo, `BackupAsync` aceita um `BackupDescription` objeto, onde um pode especificar uma cópia de segurança completa ou incremental, bem como uma função de retorno de chamada, `Func<< BackupInfo, CancellationToken, Task<bool>>>` que é invocado quando a pasta de cópia de segurança tiver sido criada localmente e está pronta para ser movido para algumas armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Pedido para efetuar uma cópia de segurança incremental pode falhar com `FabricMissingFullBackupException`. Esta exceção indica que está acontecendo uma das seguintes ações:

- a réplica nunca apresentou uma cópia de segurança completa, uma vez que ele se tornou primário,
- Alguns dos registros de log, uma vez que a última cópia de segurança foi truncada ou
- réplica transmitida a `MaxAccumulatedBackupLogSizeInMB` limite.

Os utilizadores podem aumentar a probabilidade de ser capaz de fazer cópias de segurança incrementais configurando `MinLogSizeInMB` ou `TruncationThresholdFactor`.
Aumentar estes valores aumenta a por utilização do disco de réplica.
Para obter mais informações, consulte [fiável de serviços de configuração](service-fabric-reliable-services-configuration.md)

`BackupInfo` Fornece informações sobre a cópia de segurança, incluindo a localização da pasta onde o tempo de execução guardado a cópia de segurança (`BackupInfo.Directory`). A função de retorno de chamada pode mover o `BackupInfo.Directory` para um repositório externo ou noutro local.  Esta função também retorna um booleano que indica se foi consegue mover com êxito a pasta de cópia de segurança para a localização de destino.

O código a seguir demonstra como o `BackupCallbackAsync` método pode ser utilizado para carregar a cópia de segurança para o armazenamento do Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo anterior, `ExternalBackupStore` é a classe de exemplo que é utilizada para a interface com o armazenamento de Blobs do Azure, e `UploadBackupFolderAsync` é o método que compacta a pasta e o coloca no armazenamento de Blobs do Azure.

Tenha em atenção que:

  - Pode haver apenas uma operação de cópia de segurança em trânsito por réplica num determinado momento. Mais do que uma `BackupAsync` chamada por vez lançará `FabricBackupInProgressException` para limitar as cópias de segurança em utilização para um.
  - Se uma réplica falhar enquanto uma cópia de segurança está em curso, a cópia de segurança já não tenha sido concluída. Assim, quando concluir a ativação pós-falha, é responsabilidade do serviço para reiniciar a cópia de segurança invocando `BackupAsync` conforme necessário.

## <a name="restore-reliable-services"></a>Restaurar Reliable Services
Em geral, os casos, quando poderá ter de efetuar uma operação de restauro enquadram-se em uma destas categorias:

  - O serviço de perda de dados de partição. Por exemplo, o disco para duas de três réplicas para uma partição (incluindo a réplica primária) obtém danificado ou eliminado. A nova principal poderá ter de restaurar dados a partir de uma cópia de segurança.
  - Todo o serviço é perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam ser restaurados.
  - O serviço de aplicativo danificado os dados replicados (por exemplo, devido a um erro de aplicação). Neste caso, o serviço tem de ser atualizado ou revertida para remover a causa dos danos e não corromper dados devem ser restaurados.

Embora muitas abordagens são possíveis, oferecemos alguns exemplos sobre como utilizar `RestoreAsync` para recuperar a partir de cenários acima.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição no Reliable Services
Neste caso, o tempo de execução seria detectar a perda de dados e invocar automaticamente o `OnDataLossAsync` API.

O autor de serviço tem de efetuar o seguinte para recuperar:

  - Substituir o método de classe base virtual `OnDataLossAsync`.
  - Localize a cópia de segurança mais recente na localização externa que contém cópias de segurança do serviço.
  - Transferir a cópia de segurança mais recente (e descomprimir a cópia de segurança para a pasta de cópia de segurança se tenha sido compactada).
  - O `OnDataLossAsync` método fornece uma `RestoreContext`. Chamar o `RestoreAsync` API fornecido no `RestoreContext`.
  - Devolve true se o restauro foi um sucesso.

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

`RestoreDescription` transmitido para o `RestoreContext.RestoreAsync` chamada contém um membro chamado `BackupFolderPath`.
Ao restaurar uma única cópia de segurança completa, isso `BackupFolderPath` deve ser definido como o caminho local da pasta que contém a cópia de segurança completa.
Ao restaurar uma cópia de segurança completa e um número de cópias de segurança incrementais, `BackupFolderPath` deve ser definido como o caminho local da pasta que contém não apenas a cópia de segurança completa, mas também todas as cópias de segurança incrementais.
`RestoreAsync` chamada pode emitir `FabricMissingFullBackupException` se o `BackupFolderPath` fornecido não contém uma cópia de segurança completa.
Ele também pode emitir `ArgumentException` se `BackupFolderPath` tem uma cadeia quebrada de cópias de segurança incrementais.
Por exemplo, se ele contém a cópia de segurança completa, a primeira incremental e a terceira cópia de segurança incremental, mas não a segunda cópia de segurança incremental.

> [!NOTE]
> Por predefinição, o RestorePolicy é definida para Safe.  Isso significa que o `RestoreAsync` API irá falhar com ArgumentException se detetar que a pasta de cópia de segurança contém um Estado que é mais antigo do que ou igual ao estado contido nesta réplica.  `RestorePolicy.Force` pode ser usado para ignorar esta verificação de segurança. Isto é especificado como parte do `RestoreDescription`.
> 

## <a name="deleted-or-lost-service"></a>Serviço perdido ou excluído
Se um serviço for removido, tem primeiro de recriar o serviço antes dos dados podem ser restaurados.  É importante criar o serviço com a mesma configuração, por exemplo, a criação de partições de esquema, para que os dados podem ser restaurados de forma totalmente integrada.  Assim que o serviço estiver em execução, a API para restaurar os dados (`OnDataLossAsync` acima) tem de ser invocado em cada partição deste serviço. Uma forma de conseguir isso é através de [FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) em cada partição.  

A partir deste ponto, a implementação é o mesmo cenário acima. Cada partição tem de restaurar o backup de relevante mais recentes a partir da loja externa. Uma limitação é que o ID de partição pode agora alterado, uma vez que o tempo de execução cria partição IDs dinamicamente. Portanto, o serviço precisa armazenar o nome de serviço e informações de partição adequado para identificar a cópia de segurança mais recente correta para restaurar a partir de cada partição.

> [!NOTE]
> Não é recomendado utilizar `FabricClient.ServiceManager.InvokeDataLossAsync` em cada partição para restaurar todo o serviço, uma vez que o que pode corromper o estado do cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados de aplicativo danificado
Se a atualização da aplicação recentemente implementado tem um bug, o que pode causar danos em dados. Por exemplo, uma atualização da aplicação pode começar a atualizar todos os registos de número de telefone num dicionário fiável com um código de área inválido.  Neste caso, os números de telefone inválido serão replicados, uma vez que o Service Fabric não está ciente da natureza dos dados que estão a ser armazenados.

A primeira coisa a fazer depois de detectar esse um bug sérios que faz com que a Corrupção de dados é fixar o serviço ao nível da aplicação e, se possível, atualize para a versão do código da aplicação que não tenha o bug.  No entanto, mesmo depois do código do serviço é fixa, os dados ainda poderão estar danificados e, portanto, os dados poderão ter de ser restaurada.  Nesses casos, podem não ser suficiente restaurar a cópia de segurança mais recente, uma vez que as cópias de segurança mais recente também poderão estar danificadas.  Assim, precisa localizar a última cópia de segurança que foi efetuada antes dos dados de obteve danificados.

Se não tiver a certeza de que as cópias de segurança estão danificadas, poderia implementar um novo cluster do Service Fabric e restaurar as cópias de segurança de partições afetadas fiquem apenas como o anterior "Eliminadas ou perdido de serviço" cenário.  Para cada partição, comece a restaurar as cópias de segurança da mais recente para o menos. Depois de localizar uma cópia de segurança que não tenha a existência de danos, mover/excluir todas as cópias de segurança desta partição que foram mais recentes (que essa cópia de segurança). Repita este processo para cada partição. Agora, quando `OnDataLossAsync` denomina-se na partição do cluster de produção, a última cópia de segurança encontrada no arquivo externo que vai ser escolhido pelo processo de acima.

Agora, os passos em "eliminadas ou perdido de serviço" seção pode ser usada para restaurar o estado do serviço para o estado antes do código com bugs danificado o estado.

Tenha em atenção que:

  - Ao restaurar, é provável que a cópia de segurança a ser restaurada é mais antiga do que o estado da partição antes dos dados foram perdidos. Por este motivo, deve restaurar apenas como último recurso para recuperar o máximo possível dos dados.
  - A cadeia de caracteres que representa o caminho da pasta de cópia de segurança e os caminhos de ficheiros dentro da pasta de cópia de segurança pode ser superior a 255 caracteres, consoante o caminho de FabricDataRoot e extensão do nome do tipo de aplicação. Isso pode causar alguns métodos de .NET, como `Directory.Move`, para lançar o `PathTooLongException` exceção. Uma solução alternativa é chamar diretamente kernel32 APIs, como `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Criar cópias de segurança e restaurar Reliable Actors


Reliable Actors Framework se baseia no Reliable Services. ActorService, que hospeda o actor(s) é um serviço fiável com estado. Por este motivo, todas as cópias de segurança e restauro funcionalidades disponíveis no Reliable Services também estão disponível para Reliable Actors (exceto os comportamentos que são específico do fornecedor de estado). Uma vez que as cópias de segurança serão executadas numa base por partição, Estados de todos os atores nessa partição será uma cópia de segurança (e o restauro é semelhante e irá ocorrer numa base por partição). Para efetuar a cópia de segurança/restauro, o proprietário do serviço deve criar uma classe de serviço de ator personalizado que é derivada da classe ActorService e, em seguida, fazer cópia de segurança/restauro semelhante a Reliable Services conforme descrito acima em secções anteriores.

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

Quando cria uma classe de serviço de ator personalizado, terá de registar que também ao registar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O fornecedor de estado predefinido dos Reliable Actors é `KvsActorStateProvider`. Cópia de segurança incremental não está ativada por predefinição para `KvsActorStateProvider`. Pode ativar a cópia de segurança incremental através da criação de `KvsActorStateProvider` com a definição adequada em seu construtor e, em seguida, passa ao construtor ActorService, conforme mostrado no seguinte fragmento de código:

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

Depois que tiver sido ativada a cópia de segurança incremental, fazer uma cópia de segurança incremental pode falhar com FabricMissingFullBackupException por um dos seguintes motivos e terá de realizar um backup completo antes de colocar um ou mais backups incremental:

  - A réplica nunca apresentou uma cópia de segurança completa, uma vez que se tornaram primária.
  - Alguns dos registros de logs foram truncados, uma vez que a última cópia de segurança.

Quando a cópia de segurança incremental é ativada, `KvsActorStateProvider` não utiliza o circular buffer para gerenciar seus registros de log e a trunca periodicamente. Se nenhuma cópia de segurança for retirada por utilizador durante um período de 45 minutos, o sistema trunca automaticamente os registros de log. Esse intervalo pode ser configurado com a especificação `logTruncationIntervalInMinutes` em `KvsActorStateProvider` construtor (similar ao ativar a cópia de segurança incremental). Os registros de log também podem obter truncados se a réplica primária tem de criar outra réplica com o envio de todos os seus dados.

Ao efetuar o restauro a partir de uma cadeia de cópia de segurança, semelhante a Reliable Services, o BackupFolderPath deve conter subdiretórios com um subdiretório contendo a cópia de segurança completa e outros subdiretórios que contém um ou mais backups incremental. A API de restauro irá gerar FabricException com mensagem de erro apropriada se a validação da cadeia de cópia de segurança falhar. 

> [!NOTE]
> `KvsActorStateProvider` Atualmente, ignora a opção RestorePolicy.Safe. Suporte para esta funcionalidade está previsto numa versão futura.
> 

## <a name="testing-back-up-and-restore"></a>Teste de cópia de segurança e restauro
É importante garantir que os dados críticos é a cópia de segurança e podem ser restaurados a partir. Isso pode ser feito invocando o `Start-ServiceFabricPartitionDataLoss` cmdlet do PowerShell que pode induzi-las a perda de dados numa partição específica para testar se os dados de cópia de segurança e restaurar a funcionalidade para o seu serviço está a funcionar conforme esperado.  Também é possível invocar a perda de dados e restaurar a partir de que o evento também através de programação.

> [!NOTE]
> Pode encontrar uma implementação de exemplo da cópia de segurança e restaurar a funcionalidade na aplicação Web de referência no GitHub. Examine o `Inventory.Service` serviço para obter mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Nos bastidores: obter mais detalhes sobre a cópia de segurança e restauro
Eis mais alguns detalhes sobre a cópia de segurança e restauro.

### <a name="backup"></a>Backup
O Reliable State Manager fornece a capacidade de criar cópias de segurança sem bloquear qualquer leitura ou operações de escrita. Para fazer isso, ele utiliza um mecanismo de persistência de registo e ponto de verificação.  O Gestor de estado de Reliable demora difusas pontos de verificação (leves) em determinados pontos para liberar a pressão do registo transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é chamado, o Gerenciador de estado fiável instruirá todos os objetos confiáveis para copiar seus arquivos de ponto de verificação mais recente para uma pasta de cópia de segurança local.  Em seguida, o Gestor de estado de Reliable copia todos os registros de log, começando com o ponteiro"start" para o registro de log mais recente para a pasta de cópia de segurança.  Uma vez que todos os registros de log até o registo mais recente do registo estão incluídos na cópia de segurança e o Gestor de estado de Reliable preserva o registo de escrita-ahead, o Gestor de estado confiável garante que todas as transações que são confirmadas (`CommitAsync` devolveu com êxito ) estão incluídas na cópia de segurança.

Qualquer transação que se compromete após `BackupAsync` foi chamado Maio ou não estar na cópia de segurança.  Assim que a pasta de cópia de segurança local que tiverem sido povoada pela plataforma (ou seja, cópia de segurança local é concluída pelo tempo de execução), retorno de chamada de cópia de segurança do serviço é invocado.  Esse retorno de chamada é responsável por mover a pasta de cópia de segurança para uma localização externa, como o armazenamento do Azure.

### <a name="restore"></a>Restauro
O Reliable State Manager fornece a capacidade de restaurar a partir de uma cópia de segurança utilizando o `RestoreAsync` API.  
O `RestoreAsync` método no `RestoreContext` pode ser chamado apenas dentro do `OnDataLossAsync` método.
Bool devolvido pelo `OnDataLossAsync` indica se o serviço restaurada seu estado de uma origem externa.
Se o `OnDataLossAsync` retorna true, o Service Fabric irá reconstruir a todas as outras réplicas deste primário. Service Fabric garante que as réplicas que irão receber `OnDataLossAsync` chamar primeiro transição para a função primária, mas não são concedidos a ler Estado ou gravar status.
Isso implica que para implementadores de StatefulService `RunAsync` não será chamado até `OnDataLossAsync` concluir com êxito.
Em seguida, `OnDataLossAsync` serão chamados na nova principal.
Até que um serviço é concluída esta API com êxito (retornando true ou false) e termina a reconfiguração relevante, a API irá manter a ser chamada uma de cada vez.

`RestoreAsync` primeiro ignora todos os Estados existente em que ele foi chamado na réplica primária. Em seguida, o Gerenciador de estado fiável cria todos os objetos confiáveis que existe na pasta de cópia de segurança. Em seguida, os objetos confiáveis são instruídos para restaurar a partir dos pontos de verificação na pasta de cópia de segurança. Por fim, o Gestor de estado de Reliable recupera o seu próprio Estado dos registros de log na pasta de cópia de segurança e efetua a recuperação. Como parte do processo de recuperação, as operações a partir de "ponto de partida" que realizaram registros de log na pasta de cópia de segurança são reproduzidas aos objetos fiáveis. Este passo garante que o estado recuperado é consistente.

## <a name="next-steps"></a>Passos Seguintes
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Guia de introdução de serviços fiável](service-fabric-reliable-services-quick-start.md)
  - [Notificações do Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Configuração de Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Referência do desenvolvedor do Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Cópia de segurança e restauro periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

