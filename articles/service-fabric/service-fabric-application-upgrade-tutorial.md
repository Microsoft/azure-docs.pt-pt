---
title: Tutorial de atualização de aplicação do Service Fabric | Documentos da Microsoft
description: Este artigo explica a experiência de implementação de uma aplicação do Service Fabric, alterar o código e lançar uma atualização com o Visual Studio.
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
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615202"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de atualização de aplicação de Service Fabric com o Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O Azure Service Fabric simplifica o processo de atualização de aplicações na cloud ao garantir que apenas os serviços alterados são atualizados e que o estado de funcionamento da aplicação é monitorizado em todo o processo de atualização. Ele também automaticamente reverterá a aplicação para a versão anterior depois de encontrar problemas. As atualizações de aplicações do Service Fabric são *período de inatividade*, uma vez que o aplicativo pode ser atualizado sem períodos de indisponibilidade. Este tutorial abrange como concluir uma atualização sem interrupção do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passo 1: Criar e publicar o exemplo de objetos visuais
Em primeiro lugar, transfira o [objetos visuais](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) aplicação a partir do GitHub. Em seguida, criar e publicar a aplicação ao clicar com o botão direito no projeto de aplicação, **VisualObjects**e selecionando a **Publish** comando no item de menu de recursos de infraestrutura do serviço.

![Menu de contexto para uma aplicação do Service Fabric][image1]

Selecionando **Publish** abre um pop-up, e pode definir o **perfil de destino** para **PublishProfiles\Local.xml**. A janela deve ter um aspeto semelhante ao seguinte antes de clicar em **publicar**.

![Publicar uma aplicação do Service Fabric][image2]

Agora, pode clicar em **publicar** na caixa de diálogo. Pode usar [Service Fabric Explorer para ver o cluster e o aplicativo](service-fabric-visualizing-your-cluster.md). O aplicativo de objetos visuais tem um serviço web que pode aceder à, escrevendo [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) na barra de endereço do seu navegador.  Verá 10 objetos visuais flutuantes movimentar na tela.

**NOTA:** Se a implementar `Cloud.xml` perfil (do Azure Service Fabric), a aplicação, em seguida, deve estar disponível em **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Certifique-se de que tem `8081/TCP` configurado no balanceador de carga (localizar o Balanceador de carga no mesmo grupo de recursos que a instância do Service Fabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualização do exemplo de objetos visuais
Pode observar que com a versão que foi implementada no passo 1, os objetos visual não gire. Vamos atualizar esta aplicação a uma em que os objetos visual também girar.

Selecione o projeto de VisualObjects.ActorService dentro da solução de VisualObjects e abra o **VisualObjectActor.cs** ficheiro. Dentro desse arquivo, vá para o método `MoveObject`, comente `visualObject.Move(false)`e anule os comentários `visualObject.Move(true)`. Esta alteração de código gira os objetos depois do serviço é atualizado.  **Agora pode criar a solução (não reconstrução)**, que cria os projetos modificados. Se selecionou *recriar todas*, é necessário atualizar as versões para todos os projetos.

Também precisamos versão nosso aplicativo. Para fazer as alterações de versão, depois faça duplo clique no **VisualObjects** projeto, pode usar o Visual Studio **Editar versões do manifesto** opção. A seleção desta opção abre a caixa de diálogo para versões de edição da seguinte forma:

![Caixa de diálogo de controle de versão][image3]

Atualize as versões para os projetos modificados e seus pacotes do código, juntamente com a aplicação para a versão 2.0.0. Depois das alterações são feitas, o manifesto deve ter um aspeto semelhante ao seguinte (negrito partes mostram as alterações):

![A atualizar as versões][image4]

As ferramentas do Visual Studio podem fazer rollups automática das versões depois de selecionar **atualizar automaticamente a aplicação e versões de service**. Se usar [SemVer](http://www.semver.org), tem de atualizar o código de e/ou está selecionada a opção apenas se isso de versão do pacote de configuração.

Guardar as alterações e, agora verificar a **atualizar a aplicação** caixa.

## <a name="step-3--upgrade-your-application"></a>Passo 3:  Atualizar a sua aplicação
Familiarize-se com o [parâmetros de atualização de aplicativo](service-fabric-application-upgrade-parameters.md) e o [processo de atualização](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de atualização, tempos limite e critério de estado de funcionamento que pode ser aplicada. Nestas instruções, o critério de avaliação de estado de funcionamento do serviço está definido para a predefinição (modo de não monitorizado). Pode configurar estas definições, selecionando **configurar definições de atualização do** e, em seguida, modificar os parâmetros conforme pretendido.

Agora está pronto para iniciar a atualização da aplicação, selecionando **publicar**. Esta opção atualiza a sua aplicação para a versão 2.0.0, em que os objetos girar. Service Fabric é um domínio de atualização atualizado ao mesmo tempo (alguns objetos são atualizados em primeiro lugar, seguido por outros utilizadores) e o serviço permanece acessível durante a atualização. Acesso ao serviço pode ser verificado através de seu cliente (navegador).  

Agora, como a receita de atualização de aplicação, pode monitorizá-lo com o Service Fabric Explorer, utilizando o **atualizações em curso** separador em aplicativos.

Dentro de alguns minutos, todos os domínios de atualização devem ser atualizados (concluído) e a janela de saída do Visual Studio também deve indicar que a atualização estiver concluída. E deve notar que *todos os* os objetos visuais na janela do browser estão girando agora!

Talvez queira experimentar a alteração da versão e a mudança de versão 2.0.0 para versão 3.0.0 como um exercício, ou até mesmo a partir da versão 2.0.0 voltar para a versão 1.0.0. Brincar com tempos limite e diretivas de integridade para tornar-se familiarizar com eles. Ao implementar um cluster do Azure em vez de um cluster local, os parâmetros utilizados poderão ter de ser diferente. Recomendamos que defina o tempo limite de moderadamente.

## <a name="next-steps"></a>Passos Seguintes
[Atualizar a sua aplicação com o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) explica-lhe uma atualização da aplicação com o PowerShell.

Controlar como a sua aplicação é atualizada utilizando [parâmetros de atualização](service-fabric-application-upgrade-parameters.md).

Tornar as atualizações de aplicações compatíveis, aprendendo a usar [serialização de dados](service-fabric-application-upgrade-data-serialization.md).

Saiba como utilizar funcionalidades avançadas ao atualizar a sua aplicação por consultar [tópicos avançados](service-fabric-application-upgrade-advanced.md).

Corrigir problemas comuns em atualizações da aplicação ao referir-se os passos no [as atualizações de aplicações de resolução de problemas](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
