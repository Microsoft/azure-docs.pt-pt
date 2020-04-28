---
title: Cópia de Segurança e Restauro do Service Fabric
description: Documentação conceptual para backup e restauro de tecido de serviço, um serviço para configurar backup de serviços fiáveis e atores confiáveis.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.openlocfilehash: ac6bb14517b67a4b308460583e8c9fb99a2df9f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75922781"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Backup e restaurar Serviços Fiáveis e Atores Fiáveis
O Azure Service Fabric é uma plataforma de alta disponibilidade que replica o estado em vários nós para manter esta elevada disponibilidade.  Assim, mesmo que um nó no cluster falhe, os serviços continuam disponíveis. Embora este despedimento incorporado fornecido pela plataforma possa ser suficiente para alguns, em certos casos é desejável que o serviço confirme dados (a uma loja externa).

> [!NOTE]
> É fundamental fazer backup e restaurar os seus dados (e testar que funciona como esperado) para que possa recuperar dos cenários de perda de dados.
> 

> [!NOTE]
> A Microsoft recomenda utilizar a [cópia de segurança periódica e restaurar](service-fabric-backuprestoreservice-quickstart-azurecluster.md) para configurar a cópia de segurança de dados de serviços fiáveis e atores fiáveis. 
> 


Por exemplo, um serviço pode querer fazer o backup de dados de forma a proteger dos seguintes cenários:

- Em caso de perda permanente de todo um cluster de Tecido de Serviço.
- Perda permanente da maioria das réplicas de uma partição de serviço
- Erros administrativos em que o Estado é acidentalmente apagado ou corrompido. Por exemplo, isto pode acontecer se um administrador com privilégiosuficiente apagar erroneamente o serviço.
- Insetos no serviço que causam corrupção de dados. Por exemplo, isto pode acontecer quando uma atualização de código de serviço começa a escrever dados defeituosos para uma Coleção Fiável. Neste caso, tanto o código como os dados podem ter de ser revertidos para um estado anterior.
- Processamento de dados offline. Pode ser conveniente ter o processamento offline de dados para inteligência empresarial que acontece separadamente do serviço que gera os dados.

A função Backup/Restore permite que os serviços construídos sobre a API de Serviços Fiáveis criem e restaure as cópias de segurança. As APIs de backup fornecidas pela plataforma permitem a cópia de segurança do estado de uma partição de serviço, sem bloquear as operações de leitura ou escrita. As APIs de restauro permitem restaurar o estado de uma partição de serviço supor-se a partir de um backup escolhido.

## <a name="types-of-backup"></a>Tipos de Backup
Existem duas opções de backup: Full and Incremental.
Uma cópia de segurança completa é uma cópia de segurança que contém todos os dados necessários para recriar o estado da réplica: pontos de verificação e todos os registos de registo.
Uma vez que tem os postos de controlo e o registo, uma cópia de segurança completa pode ser restaurada por si só.

O problema com os backups completos surge quando os postos de controlo são grandes.
Por exemplo, uma réplica com 16 GB de estado terá pontos de verificação que somam aproximadamente 16 GB.
Se tivermos um Objetivo de Ponto de Recuperação de cinco minutos, a réplica precisa de ser apoiada a cada cinco minutos.
Cada vez que recua, precisa de copiar 16 GB de pontos de `CheckpointThresholdInMB`verificação, para além de 50 MB (utilização configurável) de registos.

![Exemplo de backup completo.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

A solução para este problema são as cópias de segurança incrementais, onde o backup contém apenas os registos de registo alterados desde a última cópia de segurança.

![Exemplo de backup incremental.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Uma vez que as cópias de segurança incrementais são apenas alterações desde a última cópia de segurança (não inclui os pontos de verificação), tendem a ser mais rápidas, mas não podem ser restauradas por si próprias.
Para restaurar uma cópia de segurança incremental, é necessária toda a cadeia de backup.
Uma cadeia de backup é uma cadeia de backups começando com uma cópia de segurança completa e seguida por uma série de backups incrementais contíguos.

## <a name="backup-reliable-services"></a>Backup Reliable Services
O autor do serviço tem o controlo total de quando fazer backups e onde as cópias de segurança serão armazenadas.

Para iniciar uma cópia de segurança, o `BackupAsync`serviço precisa de invocar a função de membro herdado .  
As cópias de segurança só podem ser feitas a partir de réplicas primárias, e exigem que o estatuto de escrita seja concedido.

Como mostrado abaixo, `BackupAsync` leva `BackupDescription` um objeto, onde se pode especificar uma cópia de `Func<< BackupInfo, CancellationToken, Task<bool>>>` segurança completa ou incremental, bem como uma função de callback, que é invocada quando a pasta de backup foi criada localmente e está pronta para ser transferida para algum armazenamento externo.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

O pedido para obter uma `FabricMissingFullBackupException`cópia de segurança incremental pode falhar com . Esta exceção indica que uma das seguintes coisas está a acontecer:

- a réplica nunca teve um reforço completo desde que se tornou primária,
- alguns dos registos de registo desde que a última cópia de segurança foi truncado ou
- réplica passou `MaxAccumulatedBackupLogSizeInMB` o limite.

Os utilizadores podem aumentar a probabilidade de poderem `MinLogSizeInMB` fazer `TruncationThresholdFactor`backups incrementais configurando ou .
O aumento destes valores aumenta o uso do disco por réplica.
Para mais informações, consulte [Configuração de Serviços Fiáveis](service-fabric-reliable-services-configuration.md)

`BackupInfo`fornece informações sobre a cópia de segurança, incluindo a localização`BackupInfo.Directory`da pasta onde o tempo de execução guardou a cópia de segurança ( ). A função de `BackupInfo.Directory` chamada pode mover a para uma loja externa ou outro local.  Esta função também devolve um bool que indica se foi capaz de mover com sucesso a pasta de reserva para a sua localização alvo.

O seguinte código demonstra `BackupCallbackAsync` como o método pode ser usado para fazer o upload da cópia de segurança para o Armazenamento Azure:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

No exemplo anterior, `ExternalBackupStore` encontra-se a classe de amostra que é `UploadBackupFolderAsync` usada para interagir com o armazenamento do Azure Blob, e é o método que comprime a pasta e a coloca na loja Azure Blob.

Tenha em atenção que:

  - Só pode haver uma operação de reserva a bordo por réplica a qualquer momento. Mais de `BackupAsync` uma chamada de `FabricBackupInProgressException` cada vez vai ser lançada para limitar os backups de bordo a um.
  - Se uma réplica falhar enquanto uma cópia de segurança está em andamento, o backup pode não ter sido concluído. Assim, uma vez terminada a falha, é da responsabilidade do serviço `BackupAsync` reiniciar o backup invocando se necessário.

## <a name="restore-reliable-services"></a>Restaurar serviços fiáveis
Em geral, os casos em que poderá ser necessário efetuar uma operação de restauro enquadram-se numa destas categorias:

  - A divisória de serviço perdeu dados. Por exemplo, o disco para duas das três réplicas para uma partição (incluindo a réplica primária) é corrompido ou limpo. As novas primárias podem precisar de restaurar os dados de uma cópia de segurança.
  - Todo o serviço está perdido. Por exemplo, um administrador remove todo o serviço e, portanto, o serviço e os dados precisam de ser restaurados.
  - O serviço replicou dados de aplicação corruptos (por exemplo, por causa de um bug de aplicação). Neste caso, o serviço tem de ser atualizado ou revertido para eliminar a causa da corrupção, e os dados não corruptos têm de ser restaurados.

Embora muitas abordagens sejam possíveis, `RestoreAsync` oferecemos alguns exemplos sobre a utilização para recuperar dos cenários acima referidos.

## <a name="partition-data-loss-in-reliable-services"></a>Perda de dados de partição em Serviços Fiáveis
Neste caso, o tempo de execução detetaria `OnDataLossAsync` automaticamente a perda de dados e invocaria a API.

O autor do serviço precisa realizar o seguinte para recuperar:

  - Anular o método `OnDataLossAsync`de classe base virtual.
  - Encontre as últimas cópias de segurança no local externo que contém as cópias de segurança do serviço.
  - Baixe a última cópia de segurança (e descomprima a cópia de segurança na pasta de reserva se for comprimido).
  - O `OnDataLossAsync` método fornece `RestoreContext`um . Ligue `RestoreAsync` para a API no fornecido. `RestoreContext`
  - O regresso é verdadeiro se a restauração foi um sucesso.

Segue-se um exemplo `OnDataLossAsync` de implementação do método:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription`transmitida para `RestoreContext.RestoreAsync` a chamada contém `BackupFolderPath`um membro chamado .
Ao restaurar uma única `BackupFolderPath` cópia de segurança completa, esta deve ser definida para o caminho local da pasta que contém a sua cópia de segurança completa.
Ao restaurar uma cópia de segurança completa `BackupFolderPath` e uma série de backups incrementais, deve ser definido para o caminho local da pasta que não só contém a cópia de segurança completa, mas também todas as cópias de segurança incrementais.
`RestoreAsync`a chamada `FabricMissingFullBackupException` pode `BackupFolderPath` lançar se o fornecido não contiver uma cópia de segurança completa.
Também pode `ArgumentException` lançar `BackupFolderPath` se tiver uma cadeia quebrada de backups incrementais.
Por exemplo, se contiver a cópia de segurança completa, o primeiro incremental e o terceiro backup incremental, mas não a segunda cópia de segurança incremental.

> [!NOTE]
> A Política de Restauro está definida para Safe por padrão.  Isto significa `RestoreAsync` que a API falhará com a ArgumentException se detetar que a pasta de backup contém um estado mais antigo ou igual ao estado contido nesta réplica.  `RestorePolicy.Force`pode ser usado para saltar esta verificação de segurança. Isto é especificado `RestoreDescription`como parte de .
> 

## <a name="deleted-or-lost-service"></a>Serviço apagado ou perdido
Se um serviço for removido, primeiro deve recriar o serviço antes de os dados poderem ser restaurados.  É importante criar o serviço com a mesma configuração, por exemplo, esquema de partição, para que os dados possam ser restaurados sem problemas.  Uma vez que o serviço esteja em`OnDataLossAsync` cima, a API para restaurar os dados (acima) tem de ser invocada em todas as divisórias deste serviço. Uma das formas de o conseguir é utilizando [o FabricClient.TestManagementClient.StartPartitionDataLossAsync](https://msdn.microsoft.com/library/mt693569.aspx) em todas as divisórias.  

A partir deste ponto, a implementação é a mesma que o cenário acima referido. Cada partição precisa de restaurar a mais recente cópia de segurança relevante da loja externa. Uma ressalva é que o ID da partição pode agora ter mudado, uma vez que o tempo de execução cria iDs de partição dinamicamente. Assim, o serviço precisa armazenar as informações e o nome de serviço adequados para identificar a cópia de segurança correta para restaurar para cada partição.

> [!NOTE]
> Não é aconselhável `FabricClient.ServiceManager.InvokeDataLossAsync` utilizar em cada partição para restaurar todo o serviço, uma vez que isso pode corromper o seu estado de cluster.
> 

## <a name="replication-of-corrupt-application-data"></a>Replicação de dados de aplicação corruptos
Se a atualização de aplicações recentemente implementada tiver um bug, isso pode causar corrupção de dados. Por exemplo, uma atualização da aplicação pode começar a atualizar todos os registos de números de telefone num Dicionário Fiável com um código de área inválido.  Neste caso, os números de telefone inválidos serão replicados uma vez que o Tecido de Serviço não tem conhecimento da natureza dos dados que estão a ser armazenados.

A primeira coisa a fazer depois de detetar um bug tão flagrante que causa corrupção de dados é congelar o serviço ao nível da aplicação e, se possível, atualizar para a versão do código de aplicação que não tem o bug.  No entanto, mesmo depois de o código de serviço ser corrigido, os dados podem ainda ser corruptos, pelo que os dados podem ter de ser restaurados.  Nesses casos, pode não ser suficiente para restaurar o último backup, uma vez que os últimos backups também podem ser corruptos.  Assim, tens de encontrar a última cópia de segurança que foi feita antes dos dados terem sido corrompidos.

Se não tiver a certeza de quais cópias de segurança são corruptas, poderá implantar um novo cluster de Tecido de Serviço e restaurar as cópias de segurança das divisórias afetadas, tal como o cenário acima "Serviço Apagado ou Perdido".  Para cada partição, comece a restaurar os backups do mais recente para o mínimo. Assim que encontrar um backup que não tenha a corrupção, mova/elimine todos os backups desta partição que foram mais recentes (do que aquele backup). Repita este processo para cada partição. Agora, `OnDataLossAsync` quando for chamado na partição no cluster de produção, a última cópia de segurança encontrada na loja externa será a escolhida pelo processo acima.

Agora, os passos na secção "Serviço Apagado ou Perdido" podem ser usados para restaurar o estado do serviço ao Estado antes que o código de escuta corrompa o Estado.

Tenha em atenção que:

  - Quando restaura, há a possibilidade de a cópia de segurança ser restaurada ser mais antiga do que o estado da partição antes de os dados serem perdidos. Por isso, deve restaurar apenas como último recurso para recuperar o máximo de dados possível.
  - A cadeia que representa o caminho da pasta de backup e os caminhos dos ficheiros dentro da pasta de backup podem ser superiores a 255 caracteres, dependendo do caminho do FabricDataRoot e do comprimento do nome do tipo de aplicação. Isto pode fazer com que `Directory.Move`alguns `PathTooLongException` métodos .NET, como, atirea a exceção. Uma sobra é ligar diretamente para kernel32 APIs, como `CopyFile`.

## <a name="back-up-and-restore-reliable-actors"></a>Apoiar e restaurar atores fiáveis


O Quadro de Atores Fiáveis é construído em cima de Serviços Fiáveis. O ActorService, que acolhe o(s) ator é um serviço imponente e fiável. Assim, toda a funcionalidade de backup e restauro disponível em Serviços Fiáveis também está disponível para Atores Fiáveis (exceto comportamentos específicos do fornecedor estatal). Uma vez que os backups serão tomados por partição, os estados para todos os intervenientes nessa partição serão apoiados (e a restauração é semelhante e acontecerá por partilha). Para realizar backup/restauro, o proprietário do serviço deve criar uma classe de serviço de ator personalizado que deriva da classe ActorService e, em seguida, fazer backup/restaurar semelhante aos Serviços Fiáveis, como descrito acima em secções anteriores.

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

Quando se cria uma aula de serviço de ator personalizado, também é preciso registar isso ao registar o ator.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

O fornecedor estatal padrão `KvsActorStateProvider`para Atores Fiáveis é . A cópia de segurança incremental `KvsActorStateProvider`não é ativada por padrão para . Pode ativar a `KvsActorStateProvider` cópia de segurança incremental criando com a configuração adequada no seu construtor e, em seguida, transmitindo-a ao construtor actorService, como mostrado no seguinte código:

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

Depois de ativada a cópia de segurança incremental, a obtenção de uma cópia de segurança incremental pode falhar com a Falha de Cópia de Backup do Tecido Por uma das seguintes razões e terá de obter uma cópia de segurança completa antes de obter uma cópia de segurança incremental( s):

  - A réplica nunca recebeu um reforço completo desde que se tornou primária.
  - Alguns dos registos de registo foram truncados desde que a última cópia de segurança foi tirada.

Quando a cópia de `KvsActorStateProvider` segurança incremental está ativada, não utiliza o tampão circular para gerir os seus registos de registoe periodicamente trunca-lo. Se não for efetuada qualquer cópia de segurança pelo utilizador durante um período de 45 minutos, o sistema trunca automaticamente os registos de registo. Este intervalo pode ser configurado especificando `logTruncationIntervalInMinutes` no `KvsActorStateProvider` construtor (semelhante ao que permite a cópia de segurança incremental). Os registos de registo também podem ser truncados se a réplica primária precisar de construir outra réplica enviando todos os seus dados.

Ao então a restauração de uma cadeia de backup, semelhante aos Serviços Fiáveis, o BackupFolderPath deve conter subdiretórios com um subdiretório que contenha cópia sintetizada completa e outros subdiretórios que contenham backups incrementais. A API de restauro lançará a FabricException com uma mensagem de erro apropriada se a validação da cadeia de backup falhar. 

> [!NOTE]
> `KvsActorStateProvider`atualmente ignora a opção RestaurarPolítica.Safe. O suporte para esta funcionalidade está planeado para uma próxima versão.
> 

## <a name="testing-back-up-and-restore"></a>Testar Back up e Restaurar
É importante garantir que os dados críticos estão a ser apoiados e que podem ser restaurados. Isto pode ser feito `Start-ServiceFabricPartitionDataLoss` invocando o cmdlet no PowerShell que pode induzir a perda de dados numa determinada divisória para testar se a cópia de segurança de dados e restaurar a funcionalidade para o seu serviço está a funcionar como esperado.  Também é possível invocar programáticamente a perda de dados e restaurar também a partir desse evento.

> [!NOTE]
> Pode encontrar uma implementação de cópia de segurança e restaurar a funcionalidade na Aplicação de Referência Web no GitHub. Por favor, `Inventory.Service` olhe para o serviço para mais detalhes.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Sob o capuz: mais detalhes sobre backup e restauro
Aqui estão mais detalhes sobre reforços e restauros.

### <a name="backup"></a>Cópia de segurança
O Gestor de Estado Fiável fornece a capacidade de criar backups consistentes sem bloquear quaisquer operações de leitura ou escrita. Para tal, utiliza um mecanismo de controlo e persistência de registos.  O Gestor de Estado Fiável toma pontos de verificação fuzzy (leves) em determinados pontos para aliviar a pressão do registo transacional e melhorar os tempos de recuperação.  Quando `BackupAsync` é chamado, o Gestor de Estado Fiável instrui todos os objetos fiáveis para copiar os seus ficheiros de verificação mais recentes para uma pasta de backup local.  Em seguida, o Gestor de Estado Fiável copia todos os registos de registo, começando do "ponteiro de início" para o registo de registo mais recente na pasta de backup.  Uma vez que todos os registos de registo até ao registo de registo saem incluídos na cópia de segurança`CommitAsync` e o Gestor de Estado fiável preserva a exploração madeireira, o Gestor de Estado Fiável garante que todas as transações que são cometidas (retornaram com sucesso) estão incluídas na cópia de segurança.

Qualquer transação que `BackupAsync` cometa depois foi chamada pode ou não estar na cópia de segurança.  Uma vez que a pasta de backup local tenha sido povoada pela plataforma (isto é, a cópia de segurança local é concluída pelo tempo de execução), o backback de reserva do serviço é invocado.  Este backback é responsável por mover a pasta de reserva para um local externo, como o Armazenamento Azure.

### <a name="restore"></a>Restauro
O Gestor de Estado Fiável fornece a capacidade `RestoreAsync` de restaurar a partir de uma cópia de segurança utilizando a API.  
O `RestoreAsync` método `RestoreContext` só pode ser `OnDataLossAsync` chamado dentro do método.
O bool `OnDataLossAsync` devolvido indica se o serviço restaurou o seu estado a partir de uma fonte externa.
Se `OnDataLossAsync` os retornos forem verdadeiros, o Tecido de Serviço reconstruirá todas as outras réplicas desta primária. O Service Fabric garante que `OnDataLossAsync` as réplicas que receberão a primeira transição para o papel principal, mas que não são concedidas o estado de leitura ou o estado de escrita.
Isto implica que para os implementadores statefulService, `RunAsync` não será chamado até `OnDataLossAsync` que termine com sucesso.
Então, `OnDataLossAsync` será invocado nas novas primárias.
Até que um serviço complete esta API com sucesso (devolvendo verdadeira ou falsa) e termine a reconfiguração relevante, a API continuará a ser chamada uma de cada vez.

`RestoreAsync`primeira saqueia todo o estado existente na réplica primária que foi chamado. Em seguida, o Gestor de Estado Fiável cria todos os objetos fiáveis que existem na pasta de backup. Em seguida, os objetos Fiáveis são instruídos a restaurar a partir dos seus pontos de verificação na pasta de reserva. Finalmente, o Gestor de Estado Fiável recupera o seu próprio estado a partir dos registos de registo na pasta de backup e realiza a recuperação. Como parte do processo de recuperação, as operações a partir do "ponto de partida" que cometeram registos de registo na pasta de backup são reproduzidas para os objetos Fiáveis. Este passo garante que o estado recuperado é consistente.

## <a name="next-steps"></a>Passos seguintes
  - [Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Serviços fiáveis arranque rápido](service-fabric-reliable-services-quick-start.md)
  - [Notificações de Serviços Fiáveis](service-fabric-reliable-services-notifications.md)
  - [Configuração de Serviços Fiáveis](service-fabric-reliable-services-configuration.md)
  - [Referência do desenvolvedor para Coleções Fiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  - [Cópia de segurança e restauro periódicos no Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

