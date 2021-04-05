---
title: Desenvolver aplicações .NET Core com Código de Estúdio Visual
description: Este artigo mostra como construir, implementar e depurar aplicações de tecido de serviço de núcleo .NET usando o Código do Estúdio Visual.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678330"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicações de tecido de serviço C# com Código de Estúdio Visual

A [extensão de Serviços Fiáveis de Tecido de Serviço para O Código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a construção de aplicações de tecido de serviço .NET Core fabric em sistemas operativos Windows, Linux e macOS.

Este artigo mostra-lhe como construir, implementar e depurar uma aplicação de tecido de serviço .NET Core usando o Código de Estúdio Visual.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já instalou o Código VS, a extensão de Serviços Fiáveis de Tecido de Serviço para O Código VS e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, consulte ["Começar".](./service-fabric-get-started-vs-code.md#prerequisites)

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação CounterService no [Service Fabric .NET Core recebendo amostras iniciadas do repositório GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Para clonar o repositório para a sua máquina de desenvolvimento, executar o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação em Código VS

### <a name="windows"></a>Windows
Clique com o botão direito no ícone do Código VS no Menu Iniciar e escolha **Executar como administrador**. Para anexar o depurar aos seus serviços, tem de executar o Código VS como administrador.

### <a name="linux"></a>Linux
Utilizando o terminal, navegue para o caminho /service-fabric-dotnet-core-start-start/Services/CounterService a partir do diretório que a aplicação foi clonada localmente.

Executar o seguinte comando para abrir o Código VS como um utilizador de raiz para que o depurador possa anexar-se aos seus serviços.
```
sudo code . --user-data-dir='.'
```

A aplicação deve agora aparecer no seu espaço de trabalho vs Code.

![Aplicação de serviço de balcão no espaço de trabalho](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Criar a aplicação
1. Prima (Ctrl + Shift + p) para abrir a **Paleta de Comando** no Código VS.
2. Procure e selecione o **Tecido de Serviço: Constroem** o comando de aplicação. A produção de construção é enviada para o terminal integrado.

   ![Construa o comando de aplicação em código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação para o cluster local
Depois de ter construído a aplicação, pode implantá-la para o cluster local. 

1. A partir da **Paleta de Comando,** selecione o **tecido de serviço: Implementar o comando (Localhost).** A saída do processo de instalação é enviada para o terminal integrado.

   ![Implementar o comando de aplicação no Código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, lance um browser e abra o Service Fabric Explorer: http: \/ /localhost:19080/Explorer. Devias ver se a candidatura está a decorrer. Isto pode levar algum tempo, por isso, seja paciente. 

   ![Aplicação de serviço de balcão no Explorador de Tecido de Serviço](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Depois de verificar que a aplicação está em execução, lance um browser e abra esta página: http: \/ /localhost:31002. Esta é a parte frontal da aplicação. Refresque a página para ver o valor atual do contador à medida que este aumenta.

   ![Aplicação de serviço de balcão no Browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publique a aplicação num cluster de tecido de serviço Azure
Juntamente com a implementação da aplicação para o cluster local, também pode publicar a aplicação num cluster remoto de Tecido de Serviço Azure. 

1. Certifique-se de que construiu a sua aplicação utilizando as instruções acima. Atualize o ficheiro de configuração gerado `Cloud.json` com os detalhes do cluster remoto a que pretende publicar.

2. A partir da **Paleta de Comando,** selecione o **Tecido de Serviço: Publicar o comando de aplicação**. A saída do processo de instalação é enviada para o terminal integrado.

   ![Publicar comando de aplicação em Código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Quando a implementação estiver concluída, lance um browser e abra o Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . Devias ver se a candidatura está a decorrer. Isto pode levar algum tempo, por isso, seja paciente. 

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar as aplicações no Código VS, a aplicação deve estar a decorrer num cluster local. Os pontos de rutura podem então ser adicionados ao código.

Para definir um ponto de rutura e depurar, complete os seguintes passos:
1. No Explorer, abra o ficheiro */src/CounterServiceApplication/CounterService/CounterService.cs* e deite um ponto de rutura na linha 62 dentro do `RunAsync` método.
3. Clique no ícone Debug na Barra de **Atividade** para abrir a vista de depurar no Código VS. Clique no ícone de engrenagem na parte superior da vista de depurar e selecione **.NET Core** a partir do menu ambiente suspenso. O launch.jsno ficheiro abre. Pode fechar este ficheiro. Agora deve ver as escolhas de configuração no menu de configuração de depuração localizado ao lado do botão de funcionação (seta verde).

   ![Ícone de depurar no espaço de trabalho do código VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecione **.NET Core Attach** a partir do menu de configuração de depurrão.

   ![Screenshot que mostra .NET Core Attach selecionado no menu de configuração de depurg.](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Open Service Fabric Explorer num browser: http: \/ /localhost:19080/Explorer. Clique **em Aplicações** e faça um exercício para determinar o nó primário em que o CounterService está a funcionar. Na imagem abaixo o nó primário para o CounterService é o Nó 0.

   ![Nó primário para counterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. Em Código VS, clique no ícone de execução (seta verde) ao lado da configuração **de depurrão .NET Core Attach.** No diálogo de seleção do processo, selecione o processo CounterService que está a ser em execução no nó primário que identificou no passo 4.

   ![Processo Primário](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. O ponto de rutura no *ficheiro CounterService.cs* será atingido muito rapidamente. Em seguida, pode explorar os valores das variáveis locais. Utilize a barra de ferramentas Debug no topo do Código VS para continuar a execução, pisar linhas, entrar em métodos ou sair do método atual. 

   ![Valores de Debug](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Para terminar a sessão de depurar, clique no ícone de ficha na barra de ferramentas Debug na parte superior do Código VS..
   
   ![Desconexão do depurador](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Quando terminar de depurar, pode utilizar o **comando 'Service Fabric: Remover o comando 'Remover a aplicação's** para remover a aplicação CounterService do seu cluster local. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar aplicações de Tecido de Serviço Java com código VS.](./service-fabric-develop-java-applications-with-vs-code.md)



