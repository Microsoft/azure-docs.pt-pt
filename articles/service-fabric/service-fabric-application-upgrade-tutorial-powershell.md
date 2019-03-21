---
title: Atualização de aplicação do Service Fabric com o PowerShell | Documentos da Microsoft
description: Este artigo explica a experiência de implementação de uma aplicação do Service Fabric, alterar o código e lançar uma atualização com o PowerShell.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 9bc75748-96b0-49ca-8d8a-41fe08398f25
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: bbee63888e3edbd1e8395593ac34009bbe73e87e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880651"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Atualização da aplicação de Service Fabric com o PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O mais frequentemente utilizadas e abordagem de atualização recomendada é a atualização sem interrupção monitorizada.  O Azure Service Fabric monitoriza o estado de funcionamento do aplicativo a ser atualizado com base num conjunto de políticas de estado de funcionamento. Depois de um domínio de atualização (UD) estiver atualizado, o Service Fabric avalia o estado de funcionamento do aplicativo e prossegue para o domínio de atualização seguinte ou falha da atualização consoante as políticas de estado de funcionamento.

Uma atualização de aplicação monitorizada pode ser efetuada utilizando o gerenciadas ou nativas APIs, PowerShell, CLI do Azure, Java ou REST. Para obter instruções sobre como executar uma atualização com o Visual Studio, consulte [atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com atualizações sem interrupção do Service Fabric monitorizado, o administrador do aplicativo pode configurar a política de avaliação do Estado de funcionamento que o Service Fabric utiliza para determinar se a aplicação está em bom estada. Além disso, o administrador pode configurar a ação a tomar quando a avaliação de estado de funcionamento falha (por exemplo, fazer uma reversão automática.) Esta secção descreve uma atualização monitorizada para uma das amostras do SDK que utiliza o PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Criar e implementar o exemplo de objetos visuais
Criar e publicar a aplicação ao clicar com o botão direito no projeto de aplicação, **VisualObjectsApplication,** e selecionando a **Publish** comando.  Para obter mais informações, consulte [tutorial de atualização de aplicação do Service Fabric](service-fabric-application-upgrade-tutorial.md).  Em alternativa, pode utilizar o PowerShell para implementar a sua aplicação.

> [!NOTE]
> Antes de qualquer um dos comandos do Service Fabric podem ser utilizados no PowerShell, primeiro tem de ligar ao cluster com o `Connect-ServiceFabricCluster` cmdlet. Da mesma forma, é assumido que o Cluster tiver sido configurado no seu computador local. Consulte o artigo sobre [como configurar o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started.md).
> 
> 

Depois de compilar o projeto no Visual Studio, pode utilizar o comando do PowerShell [cópia ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) para copiar o pacote de aplicação para o ImageStore. Se pretender verificar o pacote de aplicação localmente, utilize o [teste ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) cmdlet. A próxima etapa é registrar o aplicativo para o runtime do Service Fabric com o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) cmdlet. O passo seguinte é iniciar uma instância da aplicação com o [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) cmdlet.  Estes três passos são análogos para utilizar o **Deploy** item de menu no Visual Studio.  Assim que o aprovisionamento estiver concluído, deverá limpar o pacote de aplicação copiados do arquivo de imagens para reduzir os recursos consumidos.  Se um tipo de aplicação já não for necessário, deve ser anulado o registo pela mesma razão. Ver [implementar e remover aplicações com o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) para obter mais informações.

Agora, pode usar [Service Fabric Explorer para ver o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo tem um serviço web que pode navegar no Internet Explorer, escrevendo [ http://localhost:8081/visualobjects ](http://localhost:8081/visualobjects) na barra de endereço.  Deverá ver alguns objetos visuais flutuantes em movimento na tela.  Além disso, pode usar [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) para verificar o estado do aplicativo.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualização do exemplo de objetos visuais
Pode observar que com a versão que foi implementada no passo 1, os objetos visual não gire. Vamos atualizar esta aplicação a uma em que os objetos visual também girar.

Selecione o projeto de VisualObjects.ActorService dentro da solução de VisualObjects e abra o ficheiro de StatefulVisualObjectActor.cs. Dentro desse arquivo, navegue para o método `MoveObject`, comente `this.State.Move()`e anule os comentários `this.State.Move(true)`. Esta alteração gira os objetos depois do serviço é atualizado.

Também precisamos de atualizar o *servicemanifest. XML* ficheiro (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualização do *CodePackage* e a versão de serviço para 2.0 e as linhas correspondentes a *servicemanifest. XML* ficheiro.
Pode usar o Visual Studio *editar ficheiros de manifesto* opção depois que clique com botão direito na solução para fazer as alterações de arquivo de manifesto.

Depois das alterações são feitas, o manifesto deve ter um aspeto semelhante ao seguinte (realçados partes mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora o *Applicationmanifest* ficheiro (encontra-se no **VisualObjects** projeto sob o **VisualObjects** solução) é atualizado para a versão 2.0 do **VisualObjects.ActorService** projeto. Além disso, a versão da aplicação é atualizada para 2.0.0.0 de 1.0.0.0. O *Applicationmanifest* deve ser semelhante à seguinte fragmento:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, crie o projeto ao selecionar apenas o **ActorService** projeto e, em seguida, clicar com botão direito e selecionando a **criar** opção no Visual Studio. Se selecionou **recriar todas**, deve atualizar as versões para todos os projetos, uma vez que o código seria foram alterados. Em seguida, vamos empacotar a aplicação atualizada, clicando na ***VisualObjectsApplication***, selecionar o Menu de recursos de infraestrutura do serviço e escolher **pacote**. Esta ação cria um pacote de aplicação que pode ser implementado.  A aplicação atualizada está pronta para ser implementada.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3:  Opte por utilizar as políticas de estado de funcionamento e parâmetros de atualização
Familiarize-se com o [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critério de estado de funcionamento aplicada. Nestas instruções, o critério de avaliação de estado de funcionamento do serviço é definido como a predefinição (e recomendado) valores, o que significa que todas as instâncias e serviços devem ser *bom estado de funcionamento* após a atualização.  

No entanto, vamos aumentar a *HealthCheckStableDuration* para 180 segundos (para que os serviços estão em bom Estados de, pelo menos, 120 segundos, antes que a atualização prossiga para o domínio de atualização seguinte).  Vamos também definir o *UpgradeDomainTimeout* ser 1200 segundos e o *UpgradeTimeout* seja 3000 segundos.

Por fim, vamos também definir o *UpgradeFailureAction* para reversão. Esta opção requer que o Service Fabric reverter a aplicação para a versão anterior se encontrar algum problema durante a atualização. Assim, quando iniciar a atualização (no passo 4), os parâmetros seguintes são especificados:

FailureAction = reversão

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar a aplicação para atualização
Agora o aplicativo é criado e pronto a ser atualizado. Se abrir uma janela do PowerShell como um administrador e escreva [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps), ele deve informá-lo de que é o tipo de aplicação 1.0.0.0 da **VisualObjects** foi implementado.  

O pacote de aplicação é armazenado no seguinte caminho relativo em que o SDK do Service Fabric - descompactou *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Deve encontrar uma pasta de "Pacote" nesse diretório, onde o pacote de aplicação é armazenado. Verifique os carimbos de data / para se certificar de que é a compilação mais recente (poderá ter de modificar os caminhos adequadamente também).

Agora vamos copiar o pacote de aplicação atualizada para o ImageStore de recursos de infraestrutura do serviço (onde os pacotes de aplicações são armazenados pelo Service Fabric). O parâmetro *ApplicationPackagePathInImageStore* informa o Service Fabric, onde pode encontrar o pacote de aplicação. Nós as pusemos a aplicação atualizada "VisualObjects\_V2" com o seguinte comando (poderá ter de modificar os caminhos novamente adequadamente).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

A próxima etapa é registar esta aplicação com o Service Fabric, que pode ser efetuada utilizando o [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) comando:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não tiver êxito, é provável que precisa de uma reconstrução de todos os serviços. Como mencionado no passo 2, poderá ter de atualizar a versão de serviço Web também.

É recomendado que remova o pacote de aplicação depois do aplicativo for registado com êxito.  A eliminação de pacotes de aplicação do arquivo de imagens liberta os recursos do sistema.  Manter os pacotes de aplicações não utilizadas consome armazenamento em disco e leva a problemas de desempenho do aplicativo.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização da aplicação
Agora, está tudo pronto para iniciar a atualização da aplicação com o [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome da aplicação é o mesmo, tal como foi descrito na *Applicationmanifest* ficheiro. O Service Fabric utiliza este nome para identificar qual aplicativo está a ser atualizado. Se definir o tempo limite de ser demasiado curto, talvez encontre uma mensagem de falha que indica o problema. Consulte a secção de resolução de problemas ou aumentar os tempos limite.

Agora, como a receita de atualização do aplicativo, pode monitorizá-lo a utilizar o Service Fabric Explorer, ou utilizando o [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) comando do PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Dentro de alguns minutos, o estado de que recebeu com o comando anterior do PowerShell, deve indicar que todos os domínios de atualização foram atualizados (concluído). E deve notar que os objetos visuais na janela do browser começaram a rotação!

Pode experimentar atualizar da versão 2 para a versão 3 ou da versão 2 à versão 1 como um exercício. Mover da versão 2 à versão 1 também é considerada uma atualização. Brincar com tempos limite e diretivas de integridade para tornar-se familiarizar com eles. Quando estiver a implementar num cluster do Azure, os parâmetros tem de estar definidas corretamente. É bom definir o tempo limite de moderadamente.

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a sua aplicação com o Visual Studio](service-fabric-application-upgrade-tutorial.md) explica-lhe uma atualização da aplicação com o Visual Studio.

Controlar a forma como a aplicação seja atualizada com o [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Tornar as atualizações de aplicações compatíveis, aprendendo a usar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [as atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).

