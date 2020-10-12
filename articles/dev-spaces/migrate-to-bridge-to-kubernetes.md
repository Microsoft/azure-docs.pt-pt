---
title: Migrar para o Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 09/21/2020
ms.topic: conceptual
description: Descreve os processos que alimentam a Azure Dev Spaces
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, Ponte para Kubernetes
ms.openlocfilehash: b585ee20efb7b377a041152996ef41d8c59c539e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997171"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrar para o Bridge to Kubernetes

A ponte para Kubernetes oferece uma alternativa de peso mais leve a muitos dos cenários de desenvolvimento que funcionam com a Azure Dev Spaces. Bridge to Kubernetes é uma experiência do lado do cliente usando extensões em [Visual Studio][vs]e Visual   Studio [Code][vsc].  

A Ponte para Kubernetes ajuda a sua experiência de desenvolvimento, permitindo que uma aplicação de Kubernetes estabelecida inclua um serviço em execução na sua estação de trabalho de desenvolvimento local. Ao contrário de Dev Spaces, Bridge to Kubernetes reduz as complexidades do loop interno ao desviar-se da necessidade de criar configurações de Docker e Kubernetes, permitindo que os desenvolvedores se concentrem exclusivamente na lógica de negócio do seu código de microserviço.

A Ponte para Kubernetes ajuda-o a iterar o código que funciona no seu computador de desenvolvimento enquanto consome dependências e configuração existente a partir do seu ambiente Kubernetes. Em contraste, a Azure Dev Spaces implanta o seu microserviço no ambiente Kubernetes antes de poder desorlipar remotamente o seu serviço e iterar no seu código.

Este artigo fornece uma comparação entre Azure Dev Spaces e Bridge to Kubernetes, bem como instruções para migrar de Azure Dev Spaces para Bridge to Kubernetes.

## <a name="development-approaches"></a>Abordagens de Desenvolvimento

![Abordagens de desenvolvimento](media/migrate-to-btk/development-approaches.svg)

A Azure Dev Spaces ajudou os desenvolvedores de Kubernetes a trabalhar com código a funcionar diretamente no seu cluster AKS, evitando a necessidade de um ambiente local que não se assemelhasse ao ambiente implantado. Esta abordagem melhorou certos aspetos do desenvolvimento, mas também introduziu um pré-requisito de aprendizagem e manutenção de conceitos adicionais como Docker, Kubernetes e Helm antes de começar a usar Azure Dev Spaces.

A Ponte para Kubernetes permite que os desenvolvedores trabalhem diretamente nos seus computadores de desenvolvimento enquanto interagem com o resto do seu cluster. Esta abordagem tira partido da familiaridade e rapidez do código de execução diretamente nos seus computadores de desenvolvimento, partilhando as dependências e o ambiente fornecidos pelo seu cluster. Esta abordagem também tira partido da fidelidade e da escala que vem da corrida em Kubernetes.

## <a name="feature-comparison"></a>Comparação de recursos

Azure Dev Spaces e Bridge to Kubernetes têm características semelhantes, também diferem em várias áreas:

| Requisito  | Azure Dev Spaces  | Bridge to Kubernetes  |
|---------------|-------------------|--------------------------------|
| Azure Kubernetes Service | Em 15 regiões de Azure | Qualquer região de serviço da AKS    |
| **Segurança** |
| Acesso de segurança necessário no seu cluster  | Colaborador do Cluster AKS  | Kubernetes RBAC -Atualização de implantação   |
| Acesso de segurança necessário no seu computador de desenvolvimento  | N/D  | Administrador local / sudo   |
| **Usabilidade** |
| Independente dos artefactos de Kubernetes e Docker  | Não  | Sim   |
| Reversão automática de alterações, pós-depuro  | Não  | Sim   |
| **Ambientes** |
| Obras com Visual Studio 2019  | Sim  | Sim   |
| Trabalha com Código de Estúdio Visual  | Sim  | Sim   |
| Trabalha com o CLI  | Sim  | Não   |
| **Compatibilidade do Sistema Operativo** |
| Funciona no Windows 10  | Sim  | Sim  |
| Trabalha em Linux  | Sim  | Sim  |
| Trabalha no macOS  | Sim  | Sim  |
| **Capacidades** |
| Isolamento do desenvolvedor ou desenvolvimento de equipas  | Sim  | Sim  |
| Variáveis ambientais seletivamente substituindo  | Não  | Sim  |
| Criação de Dockerfile e Gráfico helm  | Sim  | Não  |
| Implementação persistente do código para Kubernetes  | Sim  | Não  |
| Depuragem remota em uma cápsula Kubernetes  | Sim  | Não  |
| Depuragem local, ligada a Kubernetes  | Não  | Sim  |
| Depurando vários serviços ao mesmo tempo, na mesma estação de trabalho  | Sim  | Sim  |

## <a name="kubernetes-inner-loop-development"></a>Desenvolvimento do loop interno de Kubernetes

A maior diferença entre Azure Dev Spaces e Bridge to Kubernetes é onde o seu código funciona. O Azure Dev Spaces ajuda a desenvolver e depurar o seu código de microserviço, mas requer que execute esse código no seu cluster. A Ponte para Kubernetes permite-lhe desenvolver e depurar o seu código de microserviço diretamente no seu computador de desenvolvimento, ainda no contexto da aplicação maior em execução em Kubernetes. A ponte para Kubernetes estende o perímetro do aglomerado de Kubernetes e permite que os processos locais herdem a configuração de Kubernetes.

![Desenvolvimento do loop interno](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Ao utilizar bridge to Kubernetes, é estabelecida uma ligação de rede entre o seu computador de desenvolvimento e o seu cluster.Durante o tempo de vida desta ligação, é adicionado um proxy ao seu cluster no lugar da sua implementação Kubernetes que redireciona pedidos para o serviço para o seu computador de desenvolvimento. Quando desligar, a implementação da aplicação voltará a utilizar a versão original da implementação em execução no cluster. Esta abordagem difere de como funciona a Azure Dev Spaces em que o código é sincronizado com o cluster, construído e depois executado. AZure Dev Spaces também não reverte o seu código.

A Ponte para Kubernetes tem a flexibilidade para trabalhar com aplicações em execução em Kubernetes, independentemente do seu método de implantação. Se utilizar o CI/CD para construir e executar a sua aplicação, independentemente de utilizar ferramentas estabelecidas ou scripts personalizados, ainda pode utilizar Bridge to Kubernetes para desenvolver e depurar o seu código.

> [!TIP]
> A [extensão microsoft Kubernetes][kubernetes-extension] permite-lhe desenvolver rapidamente manifestos kubernetes com Intellisense e ajuda gráficos de andaimes Helm.  

### <a name="use-visual-studio-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Use o Estúdio Visual para a transição para Bridge para Kubernetes a partir de Azure Dev Spaces

1. Atualize o seu Visual Studio IDE para a versão 16.7 ou superior e instale a extensão Bridge to Kubernetes a partir do [Visual Studio Marketplace][vs-marketplace].
1. Desative o controlador Azure Dev Spaces utilizando o portal Azure Ou o [Azure Dev Spaces CLI][azds-delete].
1. Retire o `azds.yaml` ficheiro do seu projeto.
1. Reimplantar a sua aplicação.
1. Configure a Ponte para Kubernetes na sua aplicação implementada. Para obter mais informações sobre a utilização de Bridge to Kubernetes em Visual Studio, consulte [Use Bridge to Kubernetes][use-btk-vs].
1. Comece a depurar no Visual Studio usando o perfil de depuragem de Bridge to Kubernetes recém-criado.

### <a name="use-visual-studio-code-to-transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Use código de estúdio visual para a transição para Bridge para Kubernetes de Azure Dev Spaces

1. Instale [a extensão da Ponte para Kubernetes.][vsc-marketplace]
1. Desative o controlador Azure Dev Spaces utilizando o portal Azure Ou o [Azure Dev Spaces CLI][azds-delete].
1. Retire o `azds.yaml` ficheiro do seu projeto.
1. Reimplantar a sua aplicação.
1. Configure a Ponte para Kubernetes na sua aplicação implementada. Para obter mais informações sobre a utilização de Bridge to Kubernetes em Código de Estúdio Visual, consulte [Use Bridge to Kubernetes][use-btk-vsc].
1. Comece a depurar no Visual Studio Code usando o perfil de lançamento da Ponte para Kubernetes recém-criada.

## <a name="team-development-in-a-shared-cluster"></a>Desenvolvimento de equipa em um cluster compartilhado

Também pode utilizar o encaminhamento específico do desenvolvedor com Bridge to Kubernetes. O cenário de desenvolvimento da equipa Azure Dev Spaces utiliza vários espaços de nomes Kubernetes para isolar um serviço do resto da aplicação usando o conceito de espaços de nome de pais e crianças. Bridge to Kubernetes oferece a mesma capacidade, mas com características de desempenho melhoradas e dentro do mesmo espaço de nome de aplicação.

Tanto a Bridge to Kubernetes como a Azure Dev Spaces requerem que os cabeçalhos HTTP estejam presentes e propagados ao longo da aplicação. Se já configurar a sua aplicação para lidar com a propagação do cabeçalho para Azure Dev Spaces, então o cabeçalho precisa de ser atualizado. Para a transição para Bridge para Kubernetes a partir de Azure Dev Spaces, atualize o cabeçalho configurado de *azds-route-as* para *kubernetes-route-as*.

## <a name="evaluate-bridge-to-kubernetes"></a>Avaliar Ponte para Kubernetes

Se você gostaria de experimentar bridge to Kubernetes antes de desativar Azure Dev Spaces no seu cluster, a maneira mais fácil é usar um novo cluster. Se tentar utilizar Azure Dev Spaces e Bridge to Kubernetes ao mesmo tempo no mesmo cluster, encontrará problemas ao utilizar as funcionalidades de encaminhamento em ambos.

### <a name="use-visual-studio-to-evaluate-bridge-to-kubernetes"></a>Use o Estúdio Visual para avaliar Bridge to Kubernetes

1. Atualize o seu Visual Studio IDE para a versão 16.7 ou superior e instale a extensão Bridge to Kubernetes a partir do [Visual Studio Marketplace][vs-marketplace].
1. Crie um novo cluster AKS e implemente a sua aplicação. Também pode utilizar uma [aplicação de amostra.][btk-sample-app]
1. Configure a Ponte para Kubernetes na sua aplicação implementada. Para obter mais informações sobre a utilização de Bridge to Kubernetes em Visual Studio, consulte [Use Bridge to Kubernetes][use-btk-vs].
1. Comece a depurar no Visual Studio usando o perfil de depuragem de Bridge to Kubernetes recém-criado.

### <a name="use-visual-studio-code-to-evaluate-bridge-to-kubernetes"></a>Use código de estúdio visual para avaliar Bridge to Kubernetes

1. Instale [a extensão da Ponte para Kubernetes.][vsc-marketplace]
1. Crie um novo cluster AKS e implemente a sua aplicação. Também pode utilizar uma [aplicação de amostra.][btk-sample-app]
1. Configure a Ponte para Kubernetes na sua aplicação implementada. Para obter mais informações sobre a utilização de Bridge to Kubernetes em Código de Estúdio Visual, consulte [Use Bridge to Kubernetes][use-btk-vsc].
1. Comece a depurar no Visual Studio usando o perfil de lançamento da Ponte para Kubernetes recém-criada.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como funciona a Bridge to Kubernetes.

> [!div class="nextstepaction"]
> [Como funciona a Ponte para Kubernetes][how-it-works-bridge-to-kubernetes]


[azds-delete]: how-to/install-dev-spaces.md#remove-azure-dev-spaces-using-the-cli
[kubernetes-extension]: https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes?view=vs-2019#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/