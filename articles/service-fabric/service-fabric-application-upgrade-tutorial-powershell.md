---
title: Upgrade de app de tecido de serviço usando PowerShell
description: Este artigo percorre a experiência de implementar uma aplicação Service Fabric, alterar o código e lançar uma atualização usando o PowerShell.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d277df6959ea3e7985514f81faed520f163c6012
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195889"
---
# <a name="service-fabric-application-upgrade-using-powershell"></a>Upgrade de aplicação de tecido de serviço usando PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

A abordagem de atualização mais utilizada e recomendada é a atualização de rolamento monitorizada.  A Azure Service Fabric monitoriza a saúde da aplicação que está a ser atualizada com base num conjunto de políticas de saúde. Uma vez atualizado um domínio de atualização (UD), o Service Fabric avalia a saúde da aplicação e ou procede ao domínio de atualização seguinte ou falha a atualização dependendo das políticas de saúde.

Uma atualização de aplicações monitorizada pode ser realizada utilizando as APIs geridas ou nativas, PowerShell, Azure CLI, Java ou REST. Para obter instruções sobre a realização de uma atualização utilizando o Visual Studio, consulte a [atualização da sua aplicação utilizando o Visual Studio](service-fabric-application-upgrade-tutorial.md).

Com o Service Fabric a monitorizar as atualizações de rolling, o administrador de aplicação pode configurar a política de avaliação de saúde que o Service Fabric utiliza para determinar se a aplicação é saudável. Além disso, o administrador pode configurar as medidas a tomar quando a avaliação de saúde falhar (por exemplo, fazer uma reversão automática.) Esta secção passa por uma atualização monitorizada para uma das amostras de SDK que utiliza o PowerShell. 

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>Passo 1: Construir e implementar a amostra de Objetos Visuais
Construir e publicar a aplicação clicando corretamente no projeto de aplicação, **VisualObjectsApplication,** e selecionando o comando **Publicar.**  Para mais informações, consulte tutorial de upgrade de [aplicação Service Fabric](service-fabric-application-upgrade-tutorial.md).  Em alternativa, pode utilizar o PowerShell para implementar a sua aplicação.

> [!NOTE]
> Antes de qualquer um dos comandos de tecido de serviço poder ser utilizado `Connect-ServiceFabricCluster` no PowerShell, primeiro é necessário ligar-se ao cluster utilizando o cmdlet. Da mesma forma, presume-se que o Cluster já foi montado na sua máquina local. Consulte o artigo sobre a criação do seu ambiente de desenvolvimento do [Tecido de Serviço.](service-fabric-get-started.md)
> 
> 

Depois de construir o projeto no Estúdio Visual, pode utilizar o pacote de comando [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) para copiar o pacote de aplicações para a ImageStore. Se pretender verificar o pacote de aplicações localmente, utilize o [Cmdlet Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage) O próximo passo é registar a aplicação no tempo de execução do Tecido de Serviço utilizando o [cmdlet Register-ServiceFabricApplicationType.](/powershell/module/servicefabric/register-servicefabricapplicationtype) O passo seguinte é iniciar uma instância da aplicação utilizando o cmdlet [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)  Estes três passos são análogos à utilização do item do menu **Deploy** no Estúdio Visual.  Uma vez concluído o fornecimento, deve limpar o pacote de aplicação copiado da loja de imagens de forma a reduzir os recursos consumidos.  Se um tipo de pedido já não for necessário, este não deve ser registado pelo mesmo motivo. Consulte [a Implementação e remova as aplicações utilizando](service-fabric-application-upgrade-tutorial-powershell.md) o PowerShell para obter mais informações.

Agora, pode usar [o Service Fabric Explorer para ver o cluster e a aplicação.](service-fabric-visualizing-your-cluster.md) A aplicação tem um serviço web que pode `http://localhost:8081/visualobjects` ser navegado no Internet Explorer digitando na barra de endereços.  Deve ver alguns objetos visuais flutuantes a moverem-se no ecrã.  Além disso, pode utilizar o [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) para verificar o estado da aplicação.

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar a amostra de Objetos Visuais
Note-se que com a versão que foi implantada no Passo 1, os objetos visuais não giram. Vamos atualizar esta aplicação para uma onde os objetos visuais também giram.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o ficheiro StatefulVisualObjectActor.cs. Dentro desse ficheiro, navegue até ao método, `MoveObject`comente `this.State.Move()`e não faça comentários. `this.State.Move(true)` Esta alteração gira os objetos após a atualização do serviço.

Precisamos também de atualizar o ficheiro *ServiceManifest.xml* (em PacoteRoot) do projeto **VisualObjects.ActorService**. Atualize o *CodePackage* e a versão de serviço para 2.0 e as linhas correspondentes no ficheiro *ServiceManifest.xml.*
Pode utilizar a opção Visual Studio *Edit Manifest Files* depois de clicar na solução para efazer as alterações de ficheiro manifesto.

Após as alterações serem feitas, o manifesto deve parecer o seguinte (porções destacadas mostram as alterações):

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

Agora, o ficheiro *ApplicationManifest.xml* (encontrado no âmbito do projeto **VisualObjects** sob a solução **VisualObjects)** é atualizado para a versão 2.0 do projeto **VisualObjects.ActorService.** Além disso, a versão Aplicação é atualizada para 2.0.0.0 a partir de 1.0.0.0. O *ApplicationManifest.xml* deve parecer o seguinte corte:

```xml
<ApplicationManifestxmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```

Agora, construa o projeto selecionando apenas o projeto **ActorService,** e, em seguida, clicando à direita e selecionando a opção **Build** em Visual Studio. Se selecionar **Reconstruir tudo,** deve atualizar as versões para todos os projetos, uma vez que o código teria mudado. Em seguida, vamos embalar a aplicação atualizada clicando à direita na ***Aplicação VisualObjects,*** selecionando o Menu de Tecido de Serviço e escolhendo **pacote**. Esta ação cria um pacote de aplicações que pode ser implementado.  A sua aplicação atualizada está pronta para ser implementada.

## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>Passo 3: Decidir sobre as políticas de saúde e atualizar os parâmetros
Familiarize-se com os parâmetros de atualização da [aplicação](service-fabric-application-upgrade-parameters.md) e o processo de [upgrade](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de upgrade, intervalos de tempo e critérios de saúde aplicados. Para esta passagem, o critério de avaliação da saúde do serviço está definido para os valores padrão (e recomendados), o que significa que todos os serviços e instâncias devem ser *saudáveis* após a atualização.  

No entanto, vamos aumentar a *Duração Do Saúde* Estável para 180 segundos (de modo a que os serviços estejam saudáveis durante pelo menos 120 segundos antes da atualização passar para o próximo domínio de atualização).  Vamos também definir o *UpgradeDomainTimeout* para ser de 1200 segundos e o *UpgradeTimeout* para 3000 segundos.

Finalmente, vamos também definir o *UpgradeFailureAction* para reverter. Esta opção requer que o Service Fabric revire a aplicação para a versão anterior se encontrar algum problema durante a atualização. Assim, ao iniciar a atualização (no passo 4), são especificados os seguintes parâmetros:

FailureAction = Retrocesso

HealthCheckStableDurationSec = 180

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000

## <a name="step-4-prepare-application-for-upgrade"></a>Passo 4: Preparar a aplicação para a atualização
Agora a aplicação é construída e pronta para ser atualizada. Se abrir uma janela PowerShell como administrador e escrever [Get-ServiceFabricApplication,](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps)deve informá-lo de que é o tipo de aplicação tipo 1.0.0.0 de **VisualObjects** que foi implementado.  

O pacote de aplicações é armazenado sob o seguinte caminho relativo onde descomprimiu o SDK de Tecido de Serviço - *Amostras\Serviços\Stateful\VisualObjects\visualObjects\obj\x64\Debug*. Deve encontrar uma pasta "Pacote" nesse diretório, onde o pacote de aplicações está armazenado. Verifique os carimbos de tempo para garantir que é a construção mais recente (pode ser necessário modificar os caminhos adequadamente também).

Agora vamos copiar o pacote de aplicações atualizado para a Service Fabric ImageStore (onde os pacotes de aplicação são armazenados pela Service Fabric). O parâmetro *AplicaçãoPackagePathInImageStore* informa o Tecido de Serviço onde pode encontrar o pacote de aplicação. Colocamos a aplicação atualizada\_em "VisualObjects V2" com o seguinte comando (pode ser necessário modificar os caminhos de novo adequadamente).

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

O próximo passo é registar esta aplicação com o Tecido de Serviço, que pode ser realizado através do comando [Register-ServiceFabricApplicationType:](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Se o comando anterior não tiver sucesso, é provável que precise de uma reconstrução de todos os serviços. Como mencionado no Passo 2, poderá também ter de atualizar a sua versão WebService.

Recomenda-se que remova o pacote de aplicação após a inscrição registada com sucesso.  A apagar pacotes de aplicações da loja de imagens liberta recursos do sistema.  Manter pacotes de aplicação não utilizados consome armazenamento de discos e leva a problemas de desempenho da aplicação.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore "VisualObjects\_V2" -ImageStoreConnectionString fabric:ImageStore
```

## <a name="step-5-start-the-application-upgrade"></a>Passo 5: Iniciar a atualização da aplicação
Agora, estamos prontos para iniciar a atualização da aplicação utilizando o comando [Start-ServiceFabricApplicationUpgrade:](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps)

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


O nome da aplicação é o mesmo que foi descrito no ficheiro *ApplicationManifest.xml.* Service Fabric usa este nome para identificar qual a aplicação que está a ser atualizada. Se definir as horas para ser muito curto, poderá encontrar uma mensagem de falha que indique o problema. Consulte a secção de resolução de problemas ou aumente as intervalos.

Agora, à medida que a atualização da aplicação prossegue, pode monitorizá-la utilizando o Service Fabric Explorer, ou utilizando o comando [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) PowerShell: 

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects
```

Em poucos minutos, o estado que obteve utilizando o comando PowerShell anterior, deve indicar que todos os domínios de atualização foram atualizados (concluídos). E deve descobrir que os objetos visuais na janela do seu navegador começaram a girar!

Pode tentar atualizar da versão 2 para a versão 3, ou da versão 2 para a versão 1 como exercício. Passar da versão 2 para a versão 1 também é considerado um upgrade. Brinque com intervalos e políticas de saúde para se familiarizar com eles. Quando se está a implantar num cluster Azure, os parâmetros têm de ser definidos adequadamente. É bom definir os intervalos de tempo de forma conservadora.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação usando o Visual Studio](service-fabric-application-upgrade-tutorial.md) leva-o através de uma atualização de aplicações usando o Visual Studio.

Controle a forma como a sua aplicação melhora utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [serialização](service-fabric-application-upgrade-data-serialization.md)de dados .

Saiba como utilizar funcionalidades avançadas enquanto atualiza a sua aplicação, referindo-se a [tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de [aplicações de resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)

