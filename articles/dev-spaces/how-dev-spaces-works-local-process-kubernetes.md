---
title: Como funciona o Processo Local com Kubernetes
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Descreve os processos de utilização do Processo Local com Kubernetes para ligar o seu computador de desenvolvimento ao seu cluster Kubernetes
keywords: Processo Local com Kubernetes, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores
ms.openlocfilehash: 443783eb7f5359318cf8efbec8b6466a80fa1e85
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84316601"
---
# <a name="how-local-process-with-kubernetes-works"></a>Como funciona o Processo Local com Kubernetes

O Processo Local com Kubernetes permite-lhe executar e depurar código no seu computador de desenvolvimento, ainda ligado ao seu cluster Kubernetes com o resto da sua aplicação ou serviços. Por exemplo, se tiver uma grande arquitetura de microserviços com muitos serviços e bases de dados interdependentes, replicar essas dependências no seu computador de desenvolvimento pode ser difícil. Além disso, a construção e implementação do código para o seu cluster Kubernetes para cada alteração de código durante o desenvolvimento do loop interno pode ser lenta, morosa e difícil de usar com um depurador.

O Processo Local com Kubernetes evita ter de construir e implementar o seu código no seu cluster, criando uma ligação diretamente entre o seu computador de desenvolvimento e o seu cluster. Ligar o seu computador de desenvolvimento ao seu cluster enquanto depura permite testar e desenvolver rapidamente o seu serviço no contexto da aplicação completa sem criar nenhuma configuração de Docker ou Kubernetes.

O Processo Local com Kubernetes redireciona o tráfego entre o seu cluster Kubernetes conectado e o seu computador de desenvolvimento. Esta reorientação de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster Kubernetes se comunique como se estivessem no mesmo cluster Kubernetes. O Processo Local com Kubernetes também fornece uma forma de replicar variáveis ambientais e volumes montados disponíveis para cápsulas no seu cluster Kubernetes no seu computador de desenvolvimento. Fornecer acesso a variáveis ambientais e volumes montados no seu computador de desenvolvimento permite-lhe trabalhar rapidamente no seu código sem ter replicado essas dependências manualmente.

## <a name="using-local-process-with-kubernetes"></a>Utilização do Processo Local com Kubernetes

Para utilizar o Processo Local com Kubernetes, precisa do [Código do Estúdio Visual][vs-code] com as extensões de Serviço [Azure Dev Spaces][azds-vs-code] e [Azure Kubernetes][az-aks-vs-code] instaladas e em execução no macOS ou Windows 10, bem como no [Azure Dev Spaces CLI instalado.][azds-cli] Também pode utilizar o [Visual Studio 2019][visual-studio] em execução no Windows 10 com as *cargas de trabalho de desenvolvimento ASP.NET e web* e desenvolvimento *Azure* instaladas e o *AzureDevSpacesTools.LocalKubernetesDebugging* feature bandeira ativada, bem como o [CLI dos Espaços Azure Dev instalados.][azds-cli] Quando utiliza o Processo Local com kubernetes para estabelecer uma ligação ao seu cluster Kubernetes, tem a opção de redirecionar todo o tráfego de e para uma cápsula existente no cluster para o seu computador de desenvolvimento.

> [!NOTE]
> Ao utilizar o Processo Local com Kubernetes, é solicitado que o nome do serviço redirecione para o seu computador de desenvolvimento. Esta opção é uma forma conveniente de identificar uma cápsula para reorientação. Toda a reorientação entre o seu cluster Kubernetes e o seu computador de desenvolvimento é para uma cápsula.

Quando o Processo Local com Kubernetes estabelece uma ligação ao seu cluster, ele:

* Pede-lhe que configuure o serviço para substituir no seu cluster, a porta no seu computador de desenvolvimento para usar para o seu código, e a tarefa de lançamento do seu código como uma ação única.
* Substitui o recipiente na cápsula do cluster por um contentor de agente remoto que redireciona o tráfego para o seu computador de desenvolvimento.
* Executa [o kubectl para a frente][kubectl-port-forward] no seu computador de desenvolvimento para encaminhar o tráfego do seu computador de desenvolvimento para o agente remoto que está a funcionar no seu cluster.
* Recolhe informações ambientais do seu cluster utilizando o agente remoto. Esta informação ambiental inclui variáveis ambientais, serviços visíveis, suportes de volume e suportes secretos.
* Configura o ambiente no Visual Studio ou visual Studio Code para que o serviço no seu computador de desenvolvimento possa aceder às mesmas variáveis que se estivesse a executar no cluster.  
* Atualiza o ficheiro dos anfitriões para mapear os serviços no seu cluster para endereços IP locais no seu computador de desenvolvimento. Estas entradas de ficheiros de anfitriões permitem que o código que executa no seu computador de desenvolvimento faça pedidos a outros serviços em execução no seu cluster. Para atualizar o ficheiro dos anfitriões, o Processo Local com Kubernetes pedirá acesso ao administrador no seu computador de desenvolvimento quando estiver ligado ao seu cluster.
* Começa a correr e a depurar o seu código no seu computador de desenvolvimento. Se necessário, o Processo Local com Kubernetes libertará as portas necessárias gratuitamente no seu computador de desenvolvimento, interrompendo serviços ou processos que estão atualmente a utilizar essas portas.

Depois de estabelecer uma ligação ao seu cluster, pode executar e depurar código de forma nativa no seu computador, sem contentorização, e o código pode interagir diretamente com o resto do seu cluster. Qualquer tráfego de rede que o agente remoto recebe é redirecionado para a porta local especificada durante a ligação para que o seu código de funcionamento nativo possa aceitar e processar esse tráfego. As variáveis ambientais, volumes e segredos do seu cluster são disponibilizados para código em execução no seu computador de desenvolvimento. Além disso, devido às entradas de ficheiros dos anfitriões e ao encaminhamento de portas adicionados ao seu computador de desenvolvimento por Processo Local com Kubernetes, o seu código pode enviar tráfego de rede para serviços em execução no seu cluster usando os nomes de serviço do seu cluster, e esse tráfego é encaminhado para os serviços que estão em execução no seu cluster. O tráfego é encaminhado entre o seu computador de desenvolvimento e o seu cluster durante todo o tempo em que está ligado.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

Ao utilizar o Processo Local com Kubernetes para se ligar ao seu cluster, os registos de diagnóstico do seu cluster são registados no [diretório temporário][azds-tmp-dir]do seu computador de desenvolvimento . Utilizando o Código do Estúdio Visual, também pode utilizar o comando *de informações de diagnóstico do Show* para imprimir as variáveis ambientais atuais e as entradas de DNS do seu cluster.

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar o Processo Local com Kubernetes para ligar ao seu computador de desenvolvimento local ao seu cluster, consulte [Use Local Process with Kubernetes com Visual Studio Code][local-process-kubernetes-vs-code] e Use Local Process with [Kubernetes with Visual Studio][local-process-kubernetes-vs].

[azds-cli]: how-to/install-dev-spaces.md#install-the-client-side-tools
[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-vs-code]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-aks-tools
[local-process-kubernetes-vs-code]: how-to/local-process-kubernetes-vs-code.md
[local-process-kubernetes-vs]: how-to/local-process-kubernetes-visual-studio.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[visual-studio]: https://www.visualstudio.com/vs/
[vs-code]: https://code.visualstudio.com/download