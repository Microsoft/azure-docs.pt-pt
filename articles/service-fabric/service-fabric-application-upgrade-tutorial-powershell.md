---
title: Upgrade da App de Tecido de Serviço usando PowerShell
description: Este artigo percorre a experiência de implementar uma aplicação de Tecido de Serviço, alterar o código e lançar uma atualização utilizando o PowerShell.
ms.topic: conceptual
ms.date: 8/5/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b038f4a41ee02960a02c4445b65eb2360a75761
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792036"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade de aplicação de tecido de serviço usando PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A abordagem de atualização mais utilizada e recomendada é a atualização de rolamento monitorizada.  A Azure Service Fabric monitoriza a saúde da aplicação que está a ser atualizada com base num conjunto de políticas de saúde. Uma vez que um domínio de atualização (UD) é atualizado, o Service Fabric avalia a saúde da aplicação e procede ao domínio da atualização seguinte ou falha a atualização dependendo das políticas de saúde.

Uma atualização de aplicações monitorizada pode ser realizada usando as APIs geridas ou nativas, PowerShell, Azure CLI, Java ou REST. Para obter instruções sobre a realização de uma atualização utilizando o Visual Studio, consulte [atualizar a sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com o Service Fabric monitorizado a fazer upgrades, o administrador de aplicação pode configurar a política de avaliação de saúde que o Service Fabric utiliza para determinar se a aplicação é saudável. Além disso, o administrador pode configurar as medidas a tomar quando a avaliação de saúde falhar (por exemplo, fazer um revés automático.) Esta secção passa por uma atualização monitorizada para uma das amostras SDK que utiliza o PowerShell. 

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s não são preservados através de uma atualização de aplicações. Para preservar os parâmetros de aplicação atuais, o utilizador deve obter os parâmetros primeiro e passá-los para a chamada de API de upgrade como abaixo:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Construir e implantar a amostra de Objetos Visuais
Construa e publique a aplicação clicando à direita no projeto de **aplicação, VisualObjectsApplication,** e selecionando o comando **Publicar.**  Para mais informações, consulte [o tutorial de atualização de aplicações do Service Fabric.](service-fabric-application-upgrade-tutorial.md)  Em alternativa, pode utilizar o PowerShell para implementar a sua aplicação.

> [!NOTE]
> Antes de qualquer um dos comandos de Tecido de Serviço poder ser utilizado no PowerShell, primeiro tem de se ligar ao cluster utilizando o `Connect-ServiceFabricCluster` cmdlet. Da mesma forma, presume-se que o Cluster já foi montado na sua máquina local. Consulte o artigo sobre [a configuração do seu ambiente de desenvolvimento do Tecido de Serviço.](service-fabric-get-started.md)
> 
> 

Depois de construir o projeto no Visual Studio, pode utilizar o comando PowerShell [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) para copiar o pacote de aplicações para a ImageStore. Se quiser verificar localmente o pacote de aplicações, utilize o [cmdlet Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) O próximo passo é registar a aplicação no tempo de funcionamento do Tecido de Serviço utilizando o [cmdlet Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) O passo seguinte é iniciar uma instância da aplicação utilizando o [cmdlet New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication)  Estes três passos são análogos à utilização do item do menu **Implementar** no Estúdio Visual.  Uma vez concluído o fornecimento, deve limpar o pacote de aplicações copiado da loja de imagens de forma a reduzir os recursos consumidos.  Se um tipo de aplicação deixar de ser necessário, este deve não ser registado pelo mesmo motivo. Consulte [implementar e remover aplicações utilizando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) para obter mais informações.

Agora, pode utilizar [o Service Fabric Explorer para visualizar o cluster e a aplicação.](service-fabric-visualizing-your-cluster.md) A aplicação tem um serviço web que pode ser navegado no Internet Explorer digitando `http://localhost:8081/visualobjects` na barra de endereços.  Deve ver alguns objetos visuais flutuantes a moverem-se no ecrã.  Além disso, pode utilizar [o Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) para verificar o estado da aplicação.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar a amostra de Objetos Visuais
Pode notar que com a versão que foi implantada no Passo 1, os objetos visuais não giram. Vamos atualizar esta aplicação para uma em que os objetos visuais também giram.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o ficheiro StatefulVisualObjectActor.cs. Dentro desse ficheiro, navegue para o `MoveObject` método, comente `this.State.Move()` e descomprometa. `this.State.Move(true)` Esta alteração gira os objetos após a atualização do serviço.

Precisamos também de atualizar o ficheiro *ServiceManifest.xml* (em PackageRoot) do projeto **VisualObjects.ActorService**. Atualize o *CodePackage* e a versão de serviço para 2.0 e as linhas correspondentes no ficheiro *ServiceManifest.xml.*
Pode utilizar a opção *Ficheiros Manifestos de Edição* de Estúdio Visual depois de clicar na solução para fazer as alterações de ficheiros manifestos.

Após a eção das alterações, o manifesto deve parecer o seguinte (partes destacadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora, o ficheiro *ApplicationManifest.xml* (encontrado no âmbito do projeto **VisualObjects** no âmbito da solução **VisualObjects)** é atualizado para a versão 2.0 do projeto **VisualObjects.ActorService.** Além disso, a versão Aplicação é atualizada para 2.0.0.0 a partir de 1.0.0.0. O *ApplicationManifest.xml* deve parecer o seguinte corte:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, construa o projeto selecionando apenas o projeto **ActorService** e, em seguida, clicando à direita e selecionando a opção **Build** em Visual Studio. Se selecionar **Reconstruir tudo,** deverá atualizar as versões para todos os projetos, uma vez que o código teria mudado. Em seguida, vamos embalar a aplicação atualizada clicando à direita no **_VisualObjectsApplication_*_, selecionando o Menu de Tecido*** de Serviço e escolhendo _ Pacote . Esta ação cria um pacote de aplicações que pode ser implementado.  A sua aplicação atualizada está pronta para ser implantada.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3: Decidir sobre políticas de saúde e atualizar parâmetros
Familiarize-se com os [parâmetros de upgrade](service-fabric-application-upgrade-parameters.md) da aplicação e o processo de [upgrade](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de upgrade, time-outs e critério de saúde aplicados. Para esta passagem, o critério de avaliação da saúde do serviço é definido para os valores padrão (e recomendado), o que significa que todos os serviços e instâncias devem ser *saudáveis* após a atualização.  

No entanto, vamos aumentar o *HealthCheckStableDuration* para 180 segundos (de modo a que os serviços estejam saudáveis durante pelo menos 120 segundos antes da atualização avançar para o domínio da atualização seguinte).  Vamos também definir o *UpgradeDomainTimeout* para 1200 segundos e o *UpgradeTimeout* para 3000 segundos.

Finalmente, vamos também definir o *UpgradeFailureAction* para reverter. Esta opção requer que o Service Fabric revoe a aplicação para a versão anterior se encontrar problemas durante a atualização. Assim, ao iniciar a atualização (no passo 4), são especificados os seguintes parâmetros:

Falha Reação = Recuo

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar aplicação para upgrade
Agora a aplicação está construída e pronta para ser atualizada. Se abrir uma janela PowerShell como administrador e escrever [Get-ServiceFabricApplication,](/powershell/module/servicefabric/get-servicefabricapplication)deve informá-lo de que é o tipo de aplicação 1.0.0.0 de **VisualObjects** que foi implantado.  

O pacote de aplicações é armazenado sob o seguinte caminho relativo onde descompromete o Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*. Deverá encontrar uma pasta "Pacote" nesse diretório, onde o pacote de aplicações está armazenado. Verifique os tempos para se certificar de que é a construção mais recente (pode ser necessário modificar os caminhos de forma adequada).

Agora vamos copiar o pacote de aplicações atualizado para a Loja de Imagem de Tecido de Serviço (onde os pacotes de aplicação são armazenados pela Service Fabric). O parâmetro *ApplicationPackagePathInImageStore* informa o Service Fabric onde pode encontrar o pacote de aplicações. Colocamos a aplicação atualizada em "VisualObjects \_ V2" com o seguinte comando (pode ser necessário modificar os caminhos novamente adequadamente).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

O próximo passo é registar esta aplicação com o Service Fabric, que pode ser realizado utilizando o comando [Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não for bem sucedido, é provável que precise de uma reconstrução de todos os serviços. Como mencionado no Passo 2, poderá também ter de atualizar a sua versão WebService.

Recomenda-se que remova o pacote de aplicações após o registo da aplicação com sucesso.  A eliminação de pacotes de aplicações da loja de imagens liberta recursos do sistema.  Manter pacotes de aplicações não reutilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização da aplicação
Agora, estamos prontos para iniciar a atualização da aplicação usando o comando [Start-ServiceFabricApplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome da aplicação é o mesmo que foi descrito no ficheiro *ApplicationManifest.xml.* O Service Fabric utiliza este nome para identificar qual a aplicação que está a ser atualizada. Se definir os intervalos para serem demasiado curtos, poderá encontrar uma mensagem de falha que indique o problema. Consulte a secção de resolução de problemas ou aumente os intervalos.

Agora, à medida que a atualização da aplicação prossegue, pode monitorizá-la utilizando o Service Fabric Explorer, ou utilizando o comando [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Em poucos minutos, o estado que obteve utilizando o comando Anterior PowerShell, deve indicar que todos os domínios de atualização foram atualizados (concluídos). E deve descobrir que os objetos visuais na janela do seu navegador começaram a girar!

Pode tentar fazer o upgrade da versão 2 para a versão 3, ou da versão 2 para a versão 1 como exercício. Passar da versão 2 para a versão 1 também é considerado uma atualização. Brinque com os intervalos e as políticas de saúde para se familiarizar com eles. Quando estiver a ser implantado num cluster Azure, os parâmetros devem ser definidos adequadamente. É bom definir os intervalos de tempo conservadoramente.

## <a name="next-steps"></a>Passos seguintes
[A atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md) acompanha-o através de uma atualização de aplicações utilizando o Visual Studio.

Controle como a sua aplicação atualiza utilizando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [serialização de dados.](service-fabric-application-upgrade-data-serialization.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [tópicos avançados.](service-fabric-application-upgrade-advanced.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos nas [atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).
