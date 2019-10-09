---
title: Tutorial de atualização do aplicativo Service Fabric | Microsoft Docs
description: Este artigo descreve a experiência de implantar um aplicativo Service Fabric, alterar o código e distribuir uma atualização usando o Visual Studio.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 5e693a219c4a430f742ebd27878518ebb99ce5da
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167376"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de atualização de aplicativo Service Fabric usando o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O Azure Service Fabric simplifica o processo de atualização de aplicativos em nuvem, garantindo que apenas os serviços alterados sejam atualizados e que a integridade do aplicativo seja monitorada durante todo o processo de atualização. Ele também reverte automaticamente o aplicativo para a versão anterior após encontrar problemas. Service Fabric atualizações de aplicativo têm *zero tempo de inatividade*, pois o aplicativo pode ser atualizado sem tempo de inatividade. Este tutorial aborda como concluir uma atualização sem interrupção do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passo 1: Criar e publicar o exemplo de objetos visuais
Primeiro, baixe o aplicativo [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) do github. Em seguida, crie e publique o aplicativo clicando com o botão direito do mouse no projeto do aplicativo, **VisualObjects**e selecionando o comando **publicar** no item de menu Service Fabric.

![Menu de contexto para um aplicativo Service Fabric][image1]

Selecionar **publicar** abre um pop-up e você pode definir o **perfil de destino** como **PublishProfiles\Local.xml**. A janela deve ser parecida com a seguinte antes de clicar em **publicar**.

![Publicando um aplicativo Service Fabric][image2]

Agora você pode clicar em **publicar** na caixa de diálogo. Você pode usar [Service Fabric Explorer para exibir o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo Visual Objects tem um serviço Web que você pode acessar digitando [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) na barra de endereços do seu navegador.  Você deve ver 10 objetos visuais flutuantes passando pela tela.

**NOTA:** Se estiver implantando no perfil `Cloud.xml` (Service Fabric do Azure), o aplicativo deverá estar disponível em **http://{Perfabricname}. { Região}. cloudapp. Azure. com: 8081/visualobjects/** . Verifique se você tem `8081/TCP` configurado na Load Balancer (Localize o Load Balancer no mesmo grupo de recursos que a instância de Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar o exemplo de objetos visuais
Você pode observar que, com a versão que foi implantada na etapa 1, os objetos visuais não giram. Vamos atualizar esse aplicativo para um onde os objetos visuais também giram.

Selecione o projeto VisualObjects. ActorService na solução VisualObjects e abra o arquivo **VisualObjectActor.cs** . Dentro desse arquivo, vá para o método `MoveObject`, comente `visualObject.Move(false)` e remova a marca de comentário `visualObject.Move(true)`. Essa alteração de código gira os objetos depois que o serviço é atualizado.  **Agora você pode compilar (não recompilar) a solução**, que cria os projetos modificados. Se você selecionar *Recompilar tudo*, precisará atualizar as versões de todos os projetos.

Também precisamos fazer a versão de nosso aplicativo. Para fazer as alterações de versão depois de clicar com o botão direito do mouse no projeto **VisualObjects** , você pode usar a opção **Editar versões de manifesto** do Visual Studio. A seleção dessa opção abre a caixa de diálogo para as versões de edição da seguinte maneira:

![Caixa de diálogo controle de versão][image3]

Atualize as versões dos projetos modificados e seus pacotes de código, juntamente com o aplicativo para a versão 2.0.0. Depois que as alterações forem feitas, o manifesto deverá ser semelhante ao seguinte (as partes em negrito mostram as alterações):

![Atualizando versões][image4]

As ferramentas do Visual Studio podem fazer rollups automáticos de versões na seleção de **atualizar automaticamente as versões de aplicativo e serviço**. Se você usar [SemVer](http://www.semver.org), precisará atualizar apenas o código e/ou a versão do pacote de configuração se essa opção estiver selecionada.

Salve as alterações e agora marque a caixa **atualizar o aplicativo** .

## <a name="step-3--upgrade-your-application"></a>Passo 3:  Atualizar a sua aplicação
Familiarize-se com os [parâmetros de atualização de aplicativos](service-fabric-application-upgrade-parameters.md) e o processo de [atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critérios de integridade que podem ser aplicados. Para este passo a passos, o critério de avaliação de integridade do serviço é definido como o padrão (modo não monitorado). Você pode definir essas configurações selecionando **definir configurações de atualização** e, em seguida, modificando os parâmetros conforme desejado.

Agora, estamos configurados para iniciar a atualização do aplicativo selecionando **publicar**. Essa opção atualiza seu aplicativo para a versão 2.0.0, na qual os objetos giram. Service Fabric atualiza um domínio de atualização de cada vez (alguns objetos são atualizados primeiro, seguidos por outros) e o serviço permanece acessível durante a atualização. O acesso ao serviço pode ser verificado pelo cliente (navegador).  

Agora, à medida que a atualização do aplicativo continua, você pode monitorá-lo com Service Fabric Explorer, usando a guia **atualizações em andamento** nos aplicativos.

Em alguns minutos, todos os domínios de atualização devem ser atualizados (concluídos) e a janela de saída do Visual Studio também deve declarar que a atualização foi concluída. E você deve descobrir que *todos* os objetos visuais na janela do navegador estão agora girando!

Talvez você queira tentar alterar as versões e migrar da versão 2.0.0 para a versão 3.0.0 como um exercício ou até mesmo da versão 2.0.0 de volta para a versão 1.0.0. Jogue com tempos limite e políticas de integridade para se familiarizar com eles. Ao implantar em um cluster do Azure, em oposição a um cluster local, os parâmetros usados podem ter diferentes diferenças. Recomendamos que você defina os tempos limite de forma conservadora.

## <a name="next-steps"></a>Passos seguintes
[Atualizar seu aplicativo usando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) orienta você durante uma atualização de aplicativo usando o PowerShell.

Controle como seu aplicativo é atualizado usando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Faça com que o aplicativo seja atualizado de forma compatível aprendendo a usar a [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como usar a funcionalidade avançada ao atualizar seu aplicativo consultando [Tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns em atualizações de aplicativos consultando as etapas em [solução de problemas de atualizações de aplicativos](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
