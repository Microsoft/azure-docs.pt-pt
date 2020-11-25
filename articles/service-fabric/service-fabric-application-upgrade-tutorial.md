---
title: Tutorial de atualização de aplicativos de tecido de serviço
description: Este artigo percorre a experiência de implementar uma aplicação de Tecido de Serviço, alterar o código e lançar uma atualização utilizando o Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996241"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de atualização de aplicativo de tecido de serviço usando o Estúdio Visual
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O Azure Service Fabric simplifica o processo de atualização das aplicações em nuvem, garantindo que apenas os serviços alterados são atualizados e que a saúde da aplicação é monitorizada ao longo do processo de upgrade. Também relemes automaticamente a aplicação para a versão anterior ao encontrar problemas. As atualizações da aplicação Service Fabric são *Zero Downtime,* uma vez que a aplicação pode ser atualizada sem tempo de inatividade. Este tutorial cobre como completar um upgrade rolante do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passo 1: Construir e publicar a amostra de Objetos Visuais
Em primeiro lugar, descarregue a aplicação [Objetos Visuais](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) do GitHub. Em seguida, construa e publique a aplicação clicando à direita no projeto de aplicação, **VisualObjects,** e selecionando o comando **Publicar** no item do menu 'Service Fabric'.

![Menu de contexto para uma aplicação de Tecido de Serviço][image1]

Selecionar **a Publish** traz um pop-up e pode definir o perfil **Target** para **PublishProfiles\Local.xml**. A janela deve parecer-se com o seguinte antes de clicar em **Publicar**.

![Publicar uma aplicação de Tecido de Serviço][image2]

Agora pode clicar em **Publicar** na caixa de diálogo. Pode utilizar [o Service Fabric Explorer para visualizar o cluster e a aplicação.](service-fabric-visualizing-your-cluster.md) A aplicação Objetos Visuais tem um serviço web ao que pode recorrer digitando `http://localhost:8081/visualobjects/` na barra de endereços do seu navegador.  Deve ver 10 objetos visuais flutuantes a moverem-se no ecrã.

**NOTA:** Se implementar para `Cloud.xml` o perfil (Azure Service Fabric), a aplicação deve então estar disponível em **http://{ServiceFabricName}.{ Região}.cloudapp.azure.com:8081/visualobjects/**. Certifique-se de que tem `8081/TCP` configurado no Balanceador de Carga (encontre o Balanceador de Carga no mesmo grupo de recursos que a instância de Tecido de Serviço).

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar a amostra de Objetos Visuais
Pode notar que com a versão que foi implantada no passo 1, os objetos visuais não giram. Vamos atualizar esta aplicação para uma em que os objetos visuais também giram.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o ficheiro **VisualObjectActor.cs.** Dentro desse ficheiro, vá ao `MoveObject` método, comente `visualObject.Move(false)` e descomprimir `visualObject.Move(true)` . Esta alteração de código gira os objetos após a atualização do serviço.  **Agora pode construir (não reconstruir) a solução,** que constrói os projetos modificados. Se selecionar *Reconstruir tudo,* tem de atualizar as versões para todos os projetos.

Também precisamos de ver versão da nossa aplicação. Para escoar as alterações da versão depois de clicar no projeto **VisualObjects,** pode utilizar a opção **Versões Manifesto de Edição de** Estúdio Visual. A seleção desta opção traz a caixa de diálogo para versões de edição da seguinte forma:

![Caixa de diálogo de versão][image3]

Atualize as versões para os projetos modificados e os seus pacotes de código, juntamente com a aplicação para a versão 2.0.0. Após a eção das alterações, o manifesto deve parecer o seguinte (partes ousadas mostram as alterações):

![Atualizações de versões][image4]

As ferramentas Visual Studio podem fazer rollups automáticos de versões ao selecionar automaticamente a atualização automática das **versões de aplicação e serviço**. Se utilizar [o SemVer,](http://www.semver.org)tem de atualizar a versão do pacote de código e/ou configuração sozinho se essa opção for selecionada.

Guarde as alterações e verifique agora a **caixa de Atualização da Aplicação.**

## <a name="step-3--upgrade-your-application"></a>Passo 3: Atualize a sua aplicação
Familiarize-se com os [parâmetros de upgrade](service-fabric-application-upgrade-parameters.md) da aplicação e o processo de [upgrade](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de upgrade, time-outs e critério de saúde que podem ser aplicados. Para esta passagem, o critério de avaliação da saúde do serviço é definido para o padrão (modo não monitorizado). Pode configurar estas definições selecionando Definições de **Atualização de Configurações de Configuração** e, em seguida, modificando os parâmetros conforme desejado.

Agora estamos prontos para iniciar a atualização da aplicação selecionando **Publish**. Esta opção atualiza a sua aplicação para a versão 2.0.0, na qual os objetos giram. O Service Fabric atualiza um domínio de atualização de cada vez (alguns objetos são atualizados primeiro, seguidos por outros), e o serviço permanece acessível durante a atualização. O acesso ao serviço pode ser verificado através do seu cliente (browser).  

Agora, à medida que a atualização da aplicação prossegue, pode monitorizá-la com o Service Fabric Explorer, utilizando o **separador Upgrades in Progress** ao abrigo das aplicações.

Em poucos minutos, todos os domínios de atualização devem ser atualizados (concluídos), e a janela de saída do Estúdio Visual também deve indicar que a atualização está concluída. E você deve descobrir que *todos os* objetos visuais na janela do seu navegador estão agora a girar!

Pode querer tentar alterar as versões e passar da versão 2.0.0 para a versão 3.0.0 como exercício, ou mesmo da versão 2.0.0 de volta para a versão 1.0.0. Brinque com os intervalos e as políticas de saúde para se familiarizar com eles. Ao implantar-se num cluster Azure em oposição a um cluster local, os parâmetros utilizados podem ter de diferir. Recomendamos que desfaseremos os intervalos de forma conservadora.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação utilizando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) acompanha-o através de uma atualização de aplicações utilizando o PowerShell.

Controle como a sua aplicação é atualizada utilizando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [serialização de dados.](service-fabric-application-upgrade-data-serialization.md)

Aprenda a utilizar funcionalidades avançadas ao atualizar a sua aplicação referindo-se a [tópicos avançados.](service-fabric-application-upgrade-advanced.md)

Corrija problemas comuns nas atualizações de aplicações referindo-se aos passos nas [atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
