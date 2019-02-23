---
title: Criar uma aplicação .NET no Service Fabric no Azure | Microsoft Docs
description: Neste início rápido, vai criar uma aplicação .NET para o Azure, utilizando o exemplo de aplicação de serviços fiáveis do Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: azure-vs
ms.date: 03/26/2018
ms.author: mikhegn
ms.custom: mvc, devcenter, vs-azure
ms.openlocfilehash: 8e83da53d0b2f71abc1f74a0ca8fbc2405e75bda
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736588"
---
# <a name="quickstart-deploy-a-net-reliable-services-application-to-service-fabric"></a>Início rápido: Implementar uma aplicação de reliable services .NET para o Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis.

Este início rápido mostra como implementar a sua primeira aplicação .NET no Service Fabric. Quando tiver terminado, terá uma aplicação de votações com um front-end da Web ASP.NET que guarda os resultados das votações num serviço de back-end com estado no cluster.

![Captura de Ecrã da Aplicação](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Com esta aplicação, vai aprender a:

* Criar uma aplicação com .NET e o Service Fabric
* Utilizar o ASP.NET Core como um front-end da Web
* Armazenar dados da aplicação num serviço com estado
* Depurar a sua aplicação localmente
* Aumentar horizontalmente a aplicação em vários nós
* Realizar atualizações sem interrupção de aplicações

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. [Instale o Visual Studio 2017](https://www.visualstudio.com/) com as cargas de trabalho de **desenvolvimento no Azure** e **desenvolvimento na Web e em ASP.NET**.
2. [Instale o Git](https://git-scm.com/)
3. [Instale o SDK do Microsoft Azure Service Fabric](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Execute o comando seguinte para permitir que o Visual Studio implemente no cluster do Service Fabric local:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```
    
## <a name="build-a-cluster"></a>Compilar um cluster

Depois de instalar o tempo de execução, SDKs, ferramentas do Visual Studio, Docker e ter em execução do Docker, crie um cluster de desenvolvimento local de cinco nós.

> [!Note]
> O motivo para ter o Docker em execução quando criar o cluster é para que o cluster ser criado com funcionalidades de contentor ativadas. Se o Docker não está em execução, terá de recriar o cluster de ativação de funcionalidades de contentor.
> Embora desnecessário para este início rápido específico, a instrução para ter o Docker em execução quando criar o cluster é incluída como uma prática recomendada.
> Teste se o Docker está em execução ao abrir uma janela de terminal e ao executar `docker ps` para ver se ocorre um erro. Se a resposta não indicar um erro, o Docker está a ser executado e está pronto para compilar um cluster.

1. Abra uma nova janela do PowerShell elevada como administrador.
2. Execute o seguinte comando do PowerShell para criar um cluster de desenvolvimento:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
3. Execute o seguinte comando para iniciar a ferramenta de Gestor de local cluster:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```

>[!NOTE]
> O exemplo de aplicação neste guia de introdução utiliza funcionalidades que não estão disponíveis no Windows 7.
>

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Executar a aplicação localmente

Clique com o botão direito do rato no ícone do Visual Studio, no Menu Iniciar, e escolha **Executar como administrador**. Para ligar o depurador aos seus serviços, tem de executar o Visual Studio como administrador.

Abra a solução **Voting.sln** do Visual Studio no repositório que clonou.

Por predefinição, a aplicação de votação escuta na porta 8080.  A porta da aplicação está definida no ficheiro */VotingWeb/PackageRoot/ServiceManifest.xml*.  Pode atualizar o atributo **Port** do elemento **Endpoint** para alterá-la.  Para implementar e executar a aplicação localmente, a porta da aplicação tem de estar aberta e disponível no seu computador.  Se alterar a porta, substitua o valor da porta nova da aplicação por “8080” ao longo deste artigo.

Para implementar a aplicação, prima **F5**.

> [!NOTE]
> Na janela de saída do Visual Studio, verá a mensagem "O URL da aplicação não está definido ou não é um URL de HTTP/HTTPS para que o navegador não será aberto para a aplicação".  Esta mensagem não indica um erro, mas sim que o browser não se vai abrir automaticamente.

Quando a implementação estiver concluída, inicie um browser e abra `http://localhost:8080` para ver a web front-end da aplicação.

![Front-end da aplicação](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

Agora, pode adicionar um conjunto de opções de voto e começar a recolher votos. A aplicação é executada e armazena todos os dados no seu cluster do Service Fabric, sem que seja preciso uma base de dados separada.

## <a name="walk-through-the-voting-sample-application"></a>Percorrer a aplicação de votação de exemplo

A aplicação de votação é composta por dois serviços:

* Serviço de front-end da Web (VotingWeb) - um serviço de front-end da Web ASP.NET Core, que serve a página Web e expõe as APIs da Web para comunicar com o serviço de back-end.
* Serviço de back-end (VotingData) - um serviço Web ASP.NET Core, que expõe uma API para armazenar os resultados da votação num dicionário fiável que é persistido no disco.

![Diagrama da aplicação](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Quando vota na aplicação, ocorrem os seguintes eventos:

1. Um JavaScript envia o pedido de voto para a API Web no serviço de front-end Web como pedido HTTP PUT.

2. O serviço de front-end da Web utiliza um proxy para localizar e reencaminhar um pedido HTTP PUT para o serviço de back-end.

3. O serviço de back-end recebe o pedido de entrada e armazena os resultados atualizados num dicionário fiável, que é replicado para vários nós dentro do cluster e persistido no disco. Todos os dados da aplicação são armazenados no cluster, pelo que não é necessária uma base de dados.

## <a name="debug-in-visual-studio"></a>Depurar no Visual Studio

A aplicação deve estar em execução OK, mas pode utilizar o depurador para ver como as partes das chaves do trabalho de aplicação. Ao depurar a aplicação no Visual Studio, vai utilizar um cluster de desenvolvimento local do Service Fabric. Tem a opção de ajustar a sua experiência de depuração para o seu cenário. Nesta aplicação, os dados são armazenados no serviço de back-end através de um dicionário fiável. O Visual Studio remove a aplicação por predefinição, quando para o depurador. Remover a aplicação faz com que os dados no serviço de back-end sejam também removidos. Para persistir os dados entre as sessões de depuração, pode alterar o **Modo de Depuração da Aplicação** como propriedade no projeto **Voting** no Visual Studio.

Para ver o que acontece no código, conclua os passos seguintes:

1. Abra o ficheiro **/VotingWeb/Controllers/VotesController.cs** e defina um ponto de interrupção no método **Put** da API Web (linha 69). Pode procurar o ficheiro no Explorador de Soluções no Visual Studio.

2. Abra o ficheiro **/VotingData/Controllers/VoteDataController.cs** e defina um ponto de interrupção no método **Put** desta API Web (linha 54).

3. Regresse ao browser e clique numa opção de votação ou adicione uma opção nova. Atingiu o primeiro ponto de interrupção no controlador de API do front-end da Web.
    * É aqui que o JavaScript no browser envia um pedido para o controlador de API Web no serviço do front-end.

    ![Adicionar Serviço de Front-End de Votação](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    * Primeiro, construa o URL para o ReverseProxy para o nosso serviço de back-end **(1)**.
    * Em seguida, envie o Pedido HTTP PUT para ReverseProxy **(2)**.
    * Por último, devolva a resposta do serviço de back-end para o cliente **(3)**.

4. Prima **F5** para continuar
    - Se lhe for pedido pelo browser, conceda permissões para o Modo de Depuração de leitura e execução ao grupo ServiceFabricAllowedUsers.
    - Está agora no ponto de interrupção do serviço de back-end.

    ![Adicionar Serviço de Back-End de Votação](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    * Na primeira linha no método **(1)**, `StateManager` obtém ou adiciona um dicionário fiável chamado `counts`.
    * Todas as interações com os valores num dicionário fiável requer uma transação; a utilização da declaração **(2)** cria essa transação.
    * Na transação, atualize o valor da chave relevante para a opção de votação e consolide a operação **(3)**. Quando é devolvido o método de consolidação, os dados são atualizados no dicionário e replicados para outros nós do cluster. Os dados estão agora armazenados em segurança no cluster e o serviço de back-end pode fazer a ativação pós-falha para outros nós, mantendo os dados disponíveis.
5. Prima **F5** para continuar

Para parar a sessão de depuração, prima **Shift + F5**.

## <a name="perform-a-rolling-application-upgrade"></a>Realizar atualizações sem interrupção de aplicações

O Service Fabric implementa as atualizações novas na sua aplicação de forma segura. As atualizações sem interrupção evitam períodos de indisponibilidade, bem como reversão automática, caso ocorram erros.

Para atualizar a aplicação, faça o seguinte:

1. Abra o ficheiro **/VotingWeb/Views/Home/Index.cshtml** no Visual Studio.
2. Altere o cabeçalho da página ao adicionar ou atualizar o texto. Por exemplo, altere o cabeçalho para "Exemplo de Aplicação de Votação v2" do Service Fabric".
3. Guarde o ficheiro.
4. Clique com o botão direito do rato em **Voting**, no Explorador de Soluções, e escolha **Publish** (Publicar). É apresentada a caixa de diálogo Publicar.
5. Clique no botão **Manifest Version** (Versão do Manifesto) para alterar a versão do serviço e da aplicação.
6. Altere a versão do elemento **Code** em **VotingWebPkg** para "2.0.0", por exemplo, e clique em **Save** (Guardar).

    ![Caixa de Diálogo Alterar Versão](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Na **publicar a aplicação do Service Fabric** caixa de diálogo, verifique o **atualizar a caixa de verificação de aplicação**.
8.  Alteração **perfil de destino** ao **PublishProfiles\Local.5Node.xml** e certifique-se de que **ponto final de ligação** está definido como **Local Cluster**. 
9. Selecione **atualizar a aplicação**.

    ![Caixa de Diálogo Publicar Definição Atualizar](./media/service-fabric-quickstart-dotnet/upgrade-app.png)

10. Clique em **Publicar**.

    Pode continuar a utilizar a aplicação enquanto a atualização estiver em execução. Uma vez que tem duas instâncias do serviço em execução no cluster, alguns dos seus pedidos poderão receber uma versão atualizada da aplicação e outros continuar a versão antiga.

11. Abra o browser e navegue para o endereço de cluster na porta 19080. Por exemplo, `http://localhost:19080/`.
12. Clique no nó **Applications** (Aplicações) na vista de árvore e em **Upgrades in Progress** (Atualizações em Curso), no painel do lado direito. Pode ver de que forma é que a atualização é aplicada nos domínios de atualização do seu cluster e confirmar que cada domínio está em bom estado de funcionamento antes de avançar para o seguinte. Após o estado de funcionamento de um domínio ter sido verificado, o domínio de atualização aparece a verde na barra de progresso.
    ![Vista Atualizar no Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    O Service Fabric torna as atualizações seguras, ao aguardar dois minutos após atualizar o serviço em cada nó do cluster. A atualização completa deverá demorar aproximadamente oito minutos.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Criar uma aplicação com .NET e o Service Fabric
* Utilizar o ASP.NET Core como um front-end da Web
* Armazenar dados da aplicação num serviço com estado
* Depurar a sua aplicação localmente
* Aumentar horizontalmente a aplicação em vários nós
* Realizar atualizações sem interrupção de aplicações

Para saber mais sobre o Service Fabric e .NET, veja este tutorial:
> [!div class="nextstepaction"]
> [Aplicação .NET no Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
