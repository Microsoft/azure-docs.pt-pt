---
title: Service Fabric atualizar o aplicativo usando o PowerShell | Microsoft Docs
description: Este artigo descreve a experiência de implantar um aplicativo Service Fabric, alterar o código e distribuir uma atualização usando o PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: db0627c72ea6e7e3b272b818697b8e61b485be78
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167516"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Service Fabric a atualização do aplicativo usando o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A abordagem de atualização mais usada e recomendada é a atualização sem interrupção monitorada.  O Azure Service Fabric monitora a integridade do aplicativo que está sendo atualizado com base em um conjunto de políticas de integridade. Depois que um domínio de atualização (UD) é atualizado, o Service Fabric avalia a integridade do aplicativo e prossegue para o próximo domínio de atualização ou falha na atualização, dependendo das políticas de integridade.

Uma atualização de aplicativo monitorada pode ser executada usando as APIs gerenciadas ou nativas, PowerShell, CLI do Azure, Java ou REST. Para obter instruções sobre como executar uma atualização usando o Visual Studio, consulte [atualizando seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com Service Fabric atualizações sem interrupção monitoradas, o administrador do aplicativo pode configurar a política de avaliação de integridade que o Service Fabric usa para determinar se o aplicativo está íntegro. Além disso, o administrador pode configurar a ação a ser executada quando a avaliação de integridade falhar (por exemplo, fazendo uma reversão automática). Esta seção percorre uma atualização monitorada para um dos exemplos de SDK que usa o PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Criar e implantar o exemplo de objetos visuais
Crie e publique o aplicativo clicando com o botão direito do mouse no projeto do aplicativo, **VisualObjectsApplication** e selecionando o comando **publicar** .  Para obter mais informações, consulte [Service Fabric tutorial de atualização de aplicativos](service-fabric-application-upgrade-tutorial.md).  Como alternativa, você pode usar o PowerShell para implantar seu aplicativo.

> [!NOTE]
> Antes que qualquer um dos comandos de Service Fabric possa ser usado no PowerShell, primeiro você precisa se conectar ao cluster usando o cmdlet `Connect-ServiceFabricCluster`. Da mesma forma, supõe-se que o cluster já tenha sido configurado no computador local. Consulte o artigo sobre como [configurar seu ambiente de desenvolvimento de Service Fabric](service-fabric-get-started.md).
> 
> 

Depois de criar o projeto no Visual Studio, você pode usar o comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) do PowerShell para copiar o pacote de aplicativos para o ImageStore. Se você quiser verificar o pacote do aplicativo localmente, use o cmdlet [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) . A próxima etapa é registrar o aplicativo no tempo de execução de Service Fabric usando o cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . A etapa a seguir é iniciar uma instância do aplicativo usando o cmdlet [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) .  Essas três etapas são análogas ao uso do item de menu **implantar** no Visual Studio.  Após a conclusão do provisionamento, você deve limpar o pacote de aplicativos copiados do repositório de imagens para reduzir os recursos consumidos.  Se um tipo de aplicativo não for mais necessário, ele deverá ter o registro cancelado pelo mesmo motivo. Consulte [implantar e remover aplicativos usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) para obter mais informações.

Agora, você pode usar [Service Fabric Explorer para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo tem um serviço Web que pode ser navegado no Internet Explorer digitando [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) na barra de endereços.  Você deve ver alguns objetos visuais flutuantes que se movimentam na tela.  Além disso, você pode usar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) para verificar o status do aplicativo.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar o exemplo de objetos visuais
Você pode observar que, com a versão que foi implantada na etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais também giram.

Selecione o projeto VisualObjects. ActorService na solução VisualObjects e abra o arquivo StatefulVisualObjectActor.cs. Dentro desse arquivo, navegue até o método `MoveObject`, comente `this.State.Move()` e remova a marca de comentário `this.State.Move(true)`. Essa alteração gira os objetos depois que o serviço é atualizado.

Também precisamos atualizar o arquivo *manifest. xml* (em PackageRoot) do projeto **VisualObjects. ActorService**. Atualize o *CodePackage* e a versão do serviço para 2,0 e as linhas correspondentes no arquivo do Service *manifest. xml* .
Você pode usar a opção *Editar arquivos de manifesto* do Visual Studio depois de clicar com o botão direito do mouse na solução para fazer as alterações no arquivo de manifesto.

Depois que as alterações forem feitas, o manifesto deverá ser semelhante ao seguinte (as partes destacadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora, o arquivo *ApplicationManifest. xml* (encontrado no projeto **VisualObjects** sob a solução **VisualObjects** ) é atualizado para a versão 2,0 do projeto **VisualObjects. ActorService** . Além disso, a versão do aplicativo é atualizada para 2.0.0.0 de 1.0.0.0. O *ApplicationManifest. xml* deve ser semelhante ao seguinte trecho:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, compile o projeto selecionando apenas o projeto **ActorService** e, em seguida, clicando com o botão direito do mouse e selecionando a opção **Build** no Visual Studio. Se você selecionar **Recompilar tudo**, deverá atualizar as versões de todos os projetos, já que o código teria mudado. Em seguida, vamos empacotar o aplicativo atualizado clicando com o botão direito do mouse em ***VisualObjectsApplication***, selecionando o menu Service Fabric e escolhendo **pacote**. Essa ação cria um pacote de aplicativos que pode ser implantado.  Seu aplicativo atualizado está pronto para ser implantado.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3:  Decidir sobre políticas de integridade e parâmetros de atualização
Familiarize-se com os [parâmetros de atualização de aplicativos](service-fabric-application-upgrade-parameters.md) e o processo de [atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critérios de integridade aplicados. Para este passo a passos, o critério de avaliação de integridade do serviço é definido para os valores padrão (e recomendados), o que significa que todos os serviços e instâncias devem ser *íntegros* após a atualização.  

No entanto, vamos aumentar o *HealthCheckStableDuration* para 180 segundos (para que os serviços estejam íntegros por pelo menos 120 segundos antes que a atualização prossiga para o próximo domínio de atualização).  Vamos também definir *UpgradeDomainTimeout* como 1200 segundos e *UpgradeTimeout* como 3000 segundos.

Por fim, também vamos definir *UpgradeFailureAction* como Rollback. Essa opção requer Service Fabric para reverter o aplicativo para a versão anterior se ele encontrar problemas durante a atualização. Assim, ao iniciar a atualização (na etapa 4), os seguintes parâmetros são especificados:

Falhaaction = reversão

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar o aplicativo para atualização
Agora o aplicativo foi criado e está pronto para ser atualizado. Se você abrir uma janela do PowerShell como administrador e digitar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), ele deverá informar que é o tipo de aplicativo 1.0.0.0 do **VisualObjects** que foi implantado.  

O pacote de aplicativos é armazenado sob o caminho relativo a seguir no qual você descompactou o Service Fabric SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Você deve encontrar uma pasta "pacote" nesse diretório, onde o pacote de aplicativos está armazenado. Verifique os carimbos de data/hora para garantir que ele seja o Build mais recente (talvez você precise modificar os caminhos adequadamente também).

Agora, vamos copiar o pacote de aplicativos atualizado para o Service Fabric ImageStore (em que os pacotes de aplicativos são armazenados por Service Fabric). O parâmetro *ApplicationPackagePathInImageStore* informa Service Fabric onde ele pode encontrar o pacote de aplicativos. Colocamos o aplicativo atualizado em "VisualObjects @ no__t-0V2" com o comando a seguir (talvez seja necessário modificar os caminhos novamente de forma adequada).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A próxima etapa é registrar esse aplicativo com Service Fabric, que pode ser executado usando o comando [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) :

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não for bem sucedido, é provável que você precise de uma recompilação de todos os serviços. Como mencionado na etapa 2, talvez você precise atualizar sua versão do WebService também.

É recomendável que você remova o pacote de aplicativos depois que o aplicativo for registrado com êxito.  A exclusão de pacotes de aplicativos do repositório de imagens libera os recursos do sistema.  Manter pacotes de aplicativos não utilizados consome armazenamento em disco e leva a problemas de desempenho de aplicativos.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização do aplicativo
Agora, estamos configurados para iniciar a atualização do aplicativo usando o comando [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome do aplicativo é o mesmo que foi descrito no arquivo *ApplicationManifest. xml* . Service Fabric usa esse nome para identificar qual aplicativo está sendo atualizado. Se você definir os tempos limite como muito curtos, poderá encontrar uma mensagem de falha que declara o problema. Consulte a seção de solução de problemas ou aumente os tempos limite.

Agora, à medida que a atualização do aplicativo continua, você pode monitorá-lo usando Service Fabric Explorer ou usando o comando [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) do PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Em alguns minutos, o status que você obteve usando o comando anterior do PowerShell deve indicar que todos os domínios de atualização foram atualizados (concluído). E você deve descobrir que os objetos visuais na janela do seu navegador começaram a girar!

Você pode tentar atualizar da versão 2 para a versão 3 ou da versão 2 para a versão 1 como um exercício. A mudança da versão 2 para a versão 1 também é considerada uma atualização. Jogue com tempos limite e políticas de integridade para se familiarizar com eles. Quando você está implantando em um cluster do Azure, os parâmetros precisam ser definidos adequadamente. É bom definir os tempos limite de forma conservadora.

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) orienta você durante uma atualização de aplicativo usando o Visual Studio.

Controle como o aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

