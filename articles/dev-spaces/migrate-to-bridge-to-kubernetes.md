---
title: Migrar para o Bridge to Kubernetes
services: azure-dev-spaces
ms.date: 10/21/2020
ms.topic: conceptual
description: Descreve o processo de migração de Azure Dev Spaces para Bridge to Kubernetes
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, Ponte para Kubernetes
ms.openlocfilehash: d48814df30c17f9b51d8642efa0960a26bbd24f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888526"
---
# <a name="migrating-to-bridge-to-kubernetes"></a>Migrar para o Bridge to Kubernetes

> [!IMPORTANT]
> AZure Dev Spaces será reformado a 31 de outubro de 2023. Os clientes devem passar a usar bridge para Kubernetes, uma ferramenta de desenvolvimento de clientes.
>
> O objetivo da Azure Dev Spaces era facilitar o desenvolvimento dos utilizadores em Kubernetes. Uma compensação significativa na abordagem da Azure Dev Spaces estava a sobrecarregar os utilizadores para compreenderem as configurações de Docker e Kubernetes, bem como os conceitos de implantação de Kubernetes. Com o tempo, tornou-se também claro que a abordagem de Azure Dev Spaces não diminuiu efetivamente a velocidade de desenvolvimento do loop interno em Kubernetes. A ponte para Kubernetes diminui efetivamente a velocidade do desenvolvimento do loop interno e evita encargos desnecessários para os utilizadores.
>
> A missão principal permanece inalterada: Construir as melhores experiências para desenvolver, testar e depurar código de microserviços no contexto da aplicação maior.

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
| Independente dos artefactos de Kubernetes e Docker  | No  | Yes   |
| Reversão automática de alterações, pós-depuro  | No  | Yes   |
| **Ferramenta de cliente suportada** |
| Obras com Visual Studio 2019  | Yes  | Yes   |
| Trabalha com Código de Estúdio Visual  | Yes  | Yes   |
| Trabalha com um CLI  | Yes  | No   |
| **Compatibilidade do Sistema Operativo** |
| Funciona no Windows 10  | Yes  | Yes  |
| Trabalha em Linux  | Yes  | Yes  |
| Trabalha no macOS  | Yes  | Yes  |
| **Capacidades** |
| Isolamento do desenvolvedor ou desenvolvimento de equipas  | Yes  | Yes  |
| Variáveis ambientais seletivamente substituindo  | No  | Yes  |
| Criação de Dockerfile e Gráfico helm  | Yes  | No  |
| Implementação persistente do código para Kubernetes  | Yes  | No  |
| Depuragem remota em uma cápsula Kubernetes  | Yes  | No  |
| Depuragem local, ligada a Kubernetes  | No  | Yes  |
| Depurando vários serviços ao mesmo tempo, na mesma estação de trabalho  | Yes  | Yes  |

## <a name="kubernetes-inner-loop-development"></a>Desenvolvimento do loop interno de Kubernetes

A maior diferença entre Azure Dev Spaces e Bridge to Kubernetes é onde o seu código funciona. O Azure Dev Spaces ajuda a desenvolver e depurar o seu código de microserviço, mas requer que execute esse código no seu cluster. A Ponte para Kubernetes permite-lhe desenvolver e depurar o seu código de microserviço diretamente no seu computador de desenvolvimento, ainda no contexto da aplicação maior em execução em Kubernetes. A ponte para Kubernetes estende o perímetro do aglomerado de Kubernetes e permite que os processos locais herdem a configuração de Kubernetes.

![Desenvolvimento do loop interno](media/migrate-to-btk/btk-graphic-non-isolated.gif)

Ao utilizar bridge to Kubernetes, é estabelecida uma ligação de rede entre o seu computador de desenvolvimento e o seu cluster.Durante o tempo de vida desta ligação, é adicionado um proxy ao seu cluster no lugar da sua implementação Kubernetes que redireciona pedidos para o serviço para o seu computador de desenvolvimento. Quando desligar, a implementação da aplicação voltará a utilizar a versão original da implementação em execução no cluster. Esta abordagem difere de como funciona a Azure Dev Spaces em que o código é sincronizado com o cluster, construído e depois executado. AZure Dev Spaces também não reverte o seu código.

A Ponte para Kubernetes tem a flexibilidade para trabalhar com aplicações em execução em Kubernetes, independentemente do seu método de implantação. Se utilizar o CI/CD para construir e executar a sua aplicação, independentemente de utilizar ferramentas estabelecidas ou scripts personalizados, ainda pode utilizar Bridge to Kubernetes para desenvolver e depurar o seu código.

> [!TIP]
> A [extensão microsoft Kubernetes][kubernetes-extension] permite-lhe desenvolver rapidamente manifestos kubernetes com Intellisense e ajuda gráficos de andaimes Helm.  

### <a name="transition-to-bridge-to-kubernetes-from-azure-dev-spaces"></a>Transição para Bridge para Kubernetes de Azure Dev Spaces

1. Se estiver a utilizar o Visual Studio, atualize o seu Visual Studio IDE para a versão 16.7 ou superior e instale a extensão Bridge to Kubernetes a partir do [Visual Studio Marketplace][vs-marketplace]. Se estiver a utilizar o Código do Estúdio Visual, instale a [extensão Bridge to Kubernetes][vsc-marketplace].
1. Desative o controlador Azure Dev Spaces utilizando o portal Azure Ou o [Azure Dev Spaces CLI][azds-delete].
1. Use [a casca de nuvem Azure](https://shell.azure.com). Ou no Mac, Linux ou Windows com bash instalado, abra um pedido de concha de bash. Certifique-se de que as seguintes ferramentas estão disponíveis no seu ambiente de linha de comando: Azure CLI, docker, kubectl, curl, alcatrão e gunzip.
1. Crie um registo de contentores ou use um existente. Pode criar um registo de contentores em Azure utilizando [o Registo do Contentor Azure](https://azure.microsoft.com/services/container-registry/) ou utilizando o Docker [Hub](https://hub.docker.com/). Ao utilizar a Azure Cloud Shell, apenas o Registo de Contentores Azure está disponível para hospedar imagens de estivadores.
1. Executar o roteiro de migração para converter os ativos da Azure Dev Spaces para bridge para os ativos de Kubernetes. O script constrói uma nova imagem compatível com Bridge to Kubernetes, envia-a para o registo designado e, em seguida, usa [Helm](https://helm.sh) para atualizar o cluster com a imagem. Deve fornecer o grupo de recursos, o nome do cluster AKS e um registo de contentores. Existem outras opções de linha de comando, como mostrado aqui:

   ```azure-cli
   curl -sL https://aka.ms/migrate-tool | bash -s -- -g ResourceGroupName -n AKSName -h ContainerRegistryName -r PathOfTheProject -y
   ```

   O roteiro suporta as seguintes bandeiras:

   ```cmd  
    -g Name of resource group of AKS Cluster [required]
    -n Name of AKS Cluster [required]
    -h Container registry name. Examples: ACR, Docker [required]
    -k Kubernetes namespace to deploy resources (uses 'default' otherwise)
    -r Path to root of the project that needs to be migrated (default = current working directory)
    -t Image name & tag in format 'name:tag' (default is 'projectName:stable')
    -i Enable a public endpoint to access your service over internet. (default is false)
    -c Docker build context path. (default = project root path passed to '-r' option)
    -y Doesn't prompt for non-tty terminals
    -d Helm Debug switch
   ```

1. Migrar manualmente quaisquer personalizações, tais como configurações variáveis ambientais, em *azds.yaml* para os valores do seu *projeto.arquivo yml.*
1. (opcional) Retire o `azds.yaml` ficheiro do seu projeto.
1. Configure a Ponte para Kubernetes na sua aplicação implementada. Para obter mais informações sobre a utilização de Bridge to Kubernetes em Visual Studio, consulte [Use Bridge to Kubernetes in Visual Studio][use-btk-vs]. Para obter o código VS, consulte [use bridge to Kubernetes no código VS][use-btk-vsc].
1. Comece a depurar usando o perfil de ponte recém-criado para Kubernetes debug/launch.
1. Pode executar o script novamente conforme necessário para recolocar o seu cluster.

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
[btk-sample-app]: /visualstudio/containers/bridge-to-kubernetes#install-the-sample-application
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[use-btk-vs]: /visualstudio/containers/bridge-to-kubernetes#connect-to-your-cluster-and-debug-a-service
[use-btk-vsc]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[vs]: https://visualstudio.microsoft.com/
[vsc-marketplace]: https://marketplace.visualstudio.com/items?itemName=mindaro.mindaro
[vs-marketplace]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.mindaro
[vsc]: https://code.visualstudio.com/
