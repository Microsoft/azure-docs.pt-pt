---
title: Tutorial de upgrade de aplicativo sintetizado de tecido de serviço
description: Este artigo percorre a experiência de implementar uma aplicação Service Fabric, alterar o código e lançar uma atualização utilizando o Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464820"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Tutorial de upgrade de aplicação service Fabric usando Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

O Azure Service Fabric simplifica o processo de modernização das aplicações em nuvem, garantindo que apenas os serviços alterados são atualizados e que a saúde da aplicação é monitorizada durante todo o processo de upgrade. Também reverte automaticamente a aplicação para a versão anterior ao encontrar problemas. As atualizações da aplicação Service Fabric são zero tempo de *inatividade,* uma vez que a aplicação pode ser atualizada sem tempo de inatividade. Este tutorial cobre como completar um upgrade rolante do Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Passo 1: Construir e publicar a amostra de Objetos Visuais
Primeiro, descarregue a aplicação [De objetos visuais](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) do GitHub. Em seguida, construa e publique a aplicação clicando no projeto de aplicação, **VisualObjects,** e selecionando o comando **Publicar** no item do menu Service Fabric.

![Menu de contexto para uma aplicação de Tecido de Serviço][image1]

A seleção **De publicação** traz um pop-up e pode definir o **perfil Target** para **PublishProfiles\Local.xml**. A janela deve parecer a seguinte antes de clicar em **Publicar**.

![Publicação de uma aplicação de Tecido de Serviço][image2]

Agora pode clicar em **Publicar** na caixa de diálogo. Pode utilizar [o Service Fabric Explorer para ver o cluster e a aplicação.](service-fabric-visualizing-your-cluster.md) A aplicação Objetos Visuais tem um serviço [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) web a que pode ir digitando na barra de endereços do seu navegador.  Deve ver 10 objetos visuais flutuantes a moverem-se no ecrã.

**NOTA:** Se a `Cloud.xml` implementação do perfil (Tecido de Serviço Azure), a aplicação deve estar disponível em **http://{ServiceFabricName}.{ Região}.cloudapp.azure.com:8081/objetos visuais/**. Certifique-se de `8081/TCP` que configurado no Balancer de carga (encontre o Balancer de carga no mesmo grupo de recursos que a instância do Tecido de Serviço).

## <a name="step-2-update-the-visual-objects-sample"></a>Passo 2: Atualizar a amostra de Objetos Visuais
Note-se que com a versão que foi implantada no passo 1, os objetos visuais não giram. Vamos atualizar esta aplicação para uma onde os objetos visuais também giram.

Selecione o projeto VisualObjects.ActorService dentro da solução VisualObjects e abra o ficheiro **VisualObjectActor.cs.** Dentro desse ficheiro, vá `MoveObject`ao `visualObject.Move(false)`método, comente, e não faça comentários. `visualObject.Move(true)` Esta alteração de código gira os objetos após a atualização do serviço.  **Agora pode construir (não reconstruir) a solução,** que constrói os projetos modificados. Se selecionar *Reconstruir tudo,* tem de atualizar as versões para todos os projetos.

Também precisamos de ver a nossa aplicação. Para efazer as alterações da versão depois de clicar no direito do projeto **VisualObjects,** pode utilizar a opção **Versões Manifesto de Edição** de Estúdio Visual. A seleção desta opção traz a caixa de diálogo para versões de edição da seguinte forma:

![Caixa de diálogo de versão][image3]

Atualize as versões para os projetos modificados e os seus pacotes de código, juntamente com a aplicação para a versão 2.0.0. Após as alterações serem feitas, o manifesto deve parecer o seguinte (porções ousadas mostram as alterações):

![Atualizando versões][image4]

As ferramentas do Estúdio Visual podem fazer rollups automáticos de versões ao selecionar **automaticamente atualizações de aplicações e versões**de serviço . Se utilizar o [SemVer,](http://www.semver.org)precisa de atualizar a versão do código e/ou do pacote de configuração se essa opção for selecionada.

Guarde as alterações e verifique agora a **caixa de atualização da aplicação.**

## <a name="step-3--upgrade-your-application"></a>Passo 3: Atualize a sua aplicação
Familiarize-se com os parâmetros de atualização da [aplicação](service-fabric-application-upgrade-parameters.md) e o processo de [upgrade](service-fabric-application-upgrade.md) para obter uma boa compreensão dos vários parâmetros de upgrade, intervalos e critérios de saúde que podem ser aplicados. Para esta passagem, o critério de avaliação da saúde do serviço está definido para o padrão (modo não monitorizado). Pode configurar estas definições selecionando **configurar as definições** de atualização e, em seguida, modificando os parâmetros conforme desejado.

Agora estamos prontos para iniciar a atualização da aplicação selecionando **Publicar**. Esta opção atualiza a sua aplicação para a versão 2.0.0, na qual os objetos giram. O Serviço Fabric atualiza um domínio de atualização de cada vez (alguns objetos são atualizados primeiro, seguidos por outros), e o serviço permanece acessível durante a atualização. O acesso ao serviço pode ser verificado através do seu cliente (browser).  

Agora, à medida que a atualização da aplicação prossegue, pode monitorizá-la com o Service Fabric Explorer, utilizando o separador **Upgrades in Progress** sob as aplicações.

Em poucos minutos, todos os domínios de atualização devem ser atualizados (concluídos), e a janela de saída do Estúdio Visual também deve indicar que a atualização está concluída. E deve descobrir que *todos os* objetos visuais na janela do seu navegador estão agora a girar!

É possível que tente alterar as versões e passar da versão 2.0.0 para a versão 3.0.0 como exercício, ou mesmo da versão 2.0.0 de volta para a versão 1.0.0. Brinque com intervalos e políticas de saúde para se familiarizar com eles. Ao implantar-se num aglomerado Azure em oposição a um aglomerado local, os parâmetros utilizados podem ter de diferir. Recomendamos que estabeleça os intervalos de forma conservadora.

## <a name="next-steps"></a>Passos seguintes
[Atualizar a sua aplicação utilizando o PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) passa por si através de uma atualização de aplicações utilizando o PowerShell.

Controle a forma como a sua aplicação é atualizada utilizando parâmetros de [atualização](service-fabric-application-upgrade-parameters.md).

Torne as atualizações da sua aplicação compatíveis aprendendo a utilizar a [serialização](service-fabric-application-upgrade-data-serialization.md)de dados .

Saiba como utilizar funcionalidades avançadas enquanto atualiza a sua aplicação, referindo-se a [tópicos Avançados](service-fabric-application-upgrade-advanced.md).

Corrija problemas comuns nas atualizações de aplicações, referindo-se aos passos nas atualizações de [aplicações de resolução de problemas.](service-fabric-application-upgrade-troubleshooting.md)

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
