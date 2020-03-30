---
title: Desenvolver aplicações .NET Core com Código de Estúdio Visual
description: Este artigo mostra como construir, implementar e depurar aplicações de tecido de serviço core .NET usando o Código do Estúdio Visual.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614528"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicações de Tecido de Serviço C# com Código de Estúdio Visual

A extensão de [Serviço sintetizador de serviços fiáveis para código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a construção de aplicações .NET Core Service Fabric nos sistemas operativos Windows, Linux e macOS.

Este artigo mostra-lhe como construir, implementar e depurar uma aplicação .NET Core Service Fabric usando o Código do Estúdio Visual.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já instalou o Código VS, a extensão de Serviços Fiáveis de Tecidos para código VS, e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, ver [Getting Started](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação CounterService no [Service Fabric .NET Core iniciando amostras de repositório GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Para clonar o repositório para a sua máquina de desenvolvimento, execute o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação no Código VS

### <a name="windows"></a>Windows
Clique no ícone do Código VS no Menu Iniciar e escolha **Executar como administrador**. Para anexar o depurador aos seus serviços, precisa executar o Código VS como administrador.

### <a name="linux"></a>Linux
Utilizando o terminal, navegue até ao caminho /service-fabric-dotnet-core-start-start/Services/CounterService a partir do diretório em que a aplicação foi clonada localmente.

Execute o seguinte comando para abrir o Código VS como um utilizador de raiz para que o debugger possa ligar aos seus serviços.
```
sudo code . --user-data-dir='.'
```

A aplicação deve agora aparecer no seu espaço de trabalho vs Código.

![Aplicação de contrasserviço no espaço de trabalho](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Criar a aplicação
1. Prima (CTRL + Shift + p) para abrir a Paleta de **Comando** no Código VS.
2. Procure e selecione o Tecido de Serviço: Construa o comando **da Aplicação.** A saída de construção é enviada para o terminal integrado.

   ![Comando de aplicação de construção em código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação para o cluster local
Depois de ter construído a aplicação, pode implantá-la para o aglomerado local. 

1. A partir da Paleta de **Comando,** selecione o tecido de **serviço: implementação de aplicação (localde a localização)** comando . A saída do processo de instalação é enviada para o terminal integrado.

   ![Enviar comando de aplicação no Código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, lance um navegador\/e abra o Service Fabric Explorer: http: /localhost:19080/Explorer. Devia ver que a candidatura está a decorrer. Isto pode levar algum tempo, por isso tenha paciência. 

   ![Aplicação de contrasserviço no Explorador de Tecido de Serviço](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Depois de verificar que a aplicação está em execução,\/lance um navegador e abra esta página: http: /localhost:31002. Esta é a frente web da aplicação. Refresque a página para ver o valor atual do contador à medida que aumenta.

   ![Aplicação de contrasserviço no Browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publique a aplicação a um cluster Azure Service Fabric
Além de implementar a aplicação para o cluster local, também pode publicar a aplicação para um cluster de tecido de serviço Azure remoto. 

1. Certifique-se de que construiu a sua aplicação utilizando as instruções acima. Atualize o ficheiro `Cloud.json` de configuração gerado com os detalhes do cluster remoto a que pretende publicar.

2. A partir da Paleta de **Comando,** selecione o **Tecido de Serviço: Publicar comando de aplicação**. A saída do processo de instalação é enviada para o terminal integrado.

   ![Publicar comando de aplicação no Código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Quando a implementação estiver concluída, lance um `https:<clusterurl>:19080/Explorer`navegador e abra o Service Fabric Explorer: . Devia ver que a candidatura está a decorrer. Isto pode levar algum tempo, por isso tenha paciência. 

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicações no Código VS, a aplicação deve estar em execução num cluster local. Os pontos de rutura podem então ser adicionados ao código.

Para definir um ponto de rutura e depuração, complete os seguintes passos:
1. No Explorer, abra o ficheiro */src/CounterServiceApplication/CounterService/CounterService.cs* e delineie um ponto de rutura na linha 62 dentro do `RunAsync` método.
3. Clique no ícone Debug na Barra de **Atividades** para abrir a vista de debugger no Código VS. Clique no ícone de engrenagem na parte superior da vista de debugger e selecione **.NET Core** a partir do menu de ambiente dropdown. O ficheiro launch.json abre. Pode fechar este ficheiro. Agora deve ver escolhas de configuração no menu de configuração de depuração localizado ao lado do botão de execução (seta verde).

   ![Ícone de depuração no espaço de trabalho do código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecione **.NET Core Attach** a partir do menu de configuração de depuração.

   ![Ícone de depuração no espaço de trabalho do código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Open Service Fabric Explorer em\/um navegador: http: /localhost:19080/Explorer. Clique em **Aplicações** e defina para determinar o nó primário em que o ContraService está a funcionar. Na imagem abaixo do nó principal para o Contrasserviço é o Nó 0.

   ![Nó primário para contrasserviço](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. No Código VS, clique no ícone de execução (seta verde) ao lado da configuração de depuração de **depuração .NET Core Attach.** No diálogo de seleção de processos, selecione o processo de Contrasserviço que está a decorrer no nó principal que identificou no passo 4.

   ![Processo Primário](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. O ponto de rutura no ficheiro *CounterService.cs* será atingido muito rapidamente. Em seguida, pode explorar os valores das variáveis locais. Utilize a barra de ferramentas Debug no topo do Código VS para continuar a execução, pisar linhas, pisar métodos ou sair do método atual. 

   ![Valores de depuração](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Para terminar a sessão de depuração, clique no ícone da ficha na barra de ferramentas Debug na parte superior do Código VS..
   
   ![Desligar do debugger](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Quando terminar de depurar, pode utilizar o Serviço Tecido: Remover o comando **da Aplicação** para remover a aplicação CounterService do seu cluster local. 

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [desenvolver e depurar aplicações](./service-fabric-develop-java-applications-with-vs-code.md)java service fabric com código VS .



