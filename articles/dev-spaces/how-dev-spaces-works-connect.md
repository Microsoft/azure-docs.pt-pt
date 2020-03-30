---
title: Como ligar o seu computador de desenvolvimento ao seu cluster AKS funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos fo usando os Espaços Azure Dev para ligar o seu computador de desenvolvimento ao seu cluster de serviço Azure Kubernetes
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241715"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>Como ligar o seu computador de desenvolvimento ao seu cluster AKS funciona

Com a Azure Dev Spaces, pode ligar o seu computador de desenvolvimento ao seu cluster AKS, permitindo-lhe executar e depurar código no seu computador de desenvolvimento como se estivesse a funcionar no cluster. A Azure Dev Spaces redireciona o tráfego entre o seu cluster AKS conectado, executando uma cápsula no seu cluster que funciona como um agente remoto para redirecionar o tráfego entre a sua máquina de desenvolvimento e o cluster. Esta redirecionamento de tráfego permite que o código no seu computador de desenvolvimento e serviços em execução no seu cluster AKS se comuniquem como se estivessem no mesmo cluster AKS. Esta ligação também permite executar e depurar código com ou sem um recipiente no seu computador de desenvolvimento. Ligar o computador de desenvolvimento ao seu cluster ajuda-o a desenvolver rapidamente a sua aplicação e a realizar testes de ponta a ponta.

## <a name="connecting-to-your-cluster"></a>Ligação ao seu cluster

Liga-se ao seu cluster AKS existente utilizando o [Código de Estúdio Visual][vs-code] com a extensão [Dev Spaces azure][azds-vs-code] instalada em funcionamento no MacOS ou no Windows 10. Ao estabelecer uma ligação, tem a opção de redirecionar todo o tráfego de e para um casulo novo ou existente no cluster para o seu computador de desenvolvimento.

> [!NOTE]
> Ao utilizar o Código do Estúdio Visual para se ligar ao seu cluster, a extensão Dos Espaços Azure Dev dá-lhe a opção de redirecionar um serviço para o seu computador de desenvolvimento. Esta opção é uma forma conveniente de identificar uma cápsula para reorientação. Toda a reorientação entre o seu cluster AKS e o seu computador de desenvolvimento é para uma cápsula.

A ligação ao seu cluster não requer que tenha espaços Azure Dev ativados no seu cluster. Em vez disso, quando a extensão dos Espaços Azure Dev estabelece uma ligação ao seu cluster, é:

* Substitui o recipiente no vagão do cluster AKS por um contentor de agente remoto que redireciona o tráfego para o seu computador de desenvolvimento. Ao redirecionar uma nova cápsula, a Azure Dev Spaces cria uma nova cápsula no seu cluster AKS com o agente remoto.
* Executa [a porta kubectl][kubectl-port-forward] no seu computador de desenvolvimento para encaminhar o tráfego do seu computador de desenvolvimento para o agente remoto que corre no seu cluster.
* Recolhe informações ambientais do seu cluster utilizando o agente remoto. Esta informação ambiental inclui variáveis ambientais, serviços visíveis, suportes de volume e suportes secretos.
* Configura o ambiente no terminal de Código de Estúdio Visual para que o serviço no seu computador de desenvolvimento possa aceder às mesmas variáveis que se estivesse a funcionar no cluster.  
* Atualiza o ficheiro dos anfitriões para mapear serviços no seu cluster AKS para endereços IP locais no seu computador de desenvolvimento. Estas entradas de ficheiros dos anfitriões permitem que o código seja recorrido no seu computador de desenvolvimento para fazer pedidos a outros serviços em execução no seu cluster. Para atualizar o ficheiro dos anfitriões, o Azure Dev Spaces pedirá o acesso do administrador no seu computador de desenvolvimento quando estiver ligado ao seu cluster.

Se tiver espaços Azure Dev ativados no seu cluster, também tem a opção de utilizar a [reorientação de tráfego oferecida pela Azure Dev Spaces][how-it-works-routing]. A reorientação de tráfego oferecida pela Azure Dev Spaces permite-lhe ligar-se a uma cópia do seu serviço em funcionamento num espaço de dev infantil. A utilização de um espaço de dev infantil ajuda-o a evitar perturbar outras pessoas que trabalham no espaço de dev dos pais, uma vez que está apenas a redirecionar o tráfego direcionado para a instância do seu serviço no espaço infantil, deixando a instância espacial dos pais do serviço inalterada.

Assim que se conecta ao seu cluster, o tráfego é encaminhado para o seu computador de desenvolvimento, independentemente de ter o seu serviço em funcionamento no seu computador de desenvolvimento.

## <a name="running-code-on-your-development-computer"></a>Código de execução no seu computador de desenvolvimento

Depois de estabelecer uma ligação ao seu cluster AKS, pode executar qualquer código de forma nativa no seu computador, sem contentorização. Qualquer tráfego de rede que o agente remoto receba é redirecionado para a porta local especificada durante a ligação para que o seu código de funcionamento nativo possa aceitar e processar esse tráfego. As variáveis, volumes e segredos ambientais do seu cluster são disponibilizados para codificar o seu computador de desenvolvimento. Além disso, devido às entradas de ficheiros dos anfitriões e reencaminhamento de portas adicionados ao seu computador de desenvolvimento pela Azure Dev Spaces, o seu código pode enviar tráfego de rede para serviços em funcionamento no seu cluster usando os nomes de serviço do seu cluster, e que o tráfego é encaminhado para o serviços que estão funcionando no seu cluster.

Uma vez que o seu código está a ser executado no seu computador de desenvolvimento, tem a flexibilidade para utilizar qualquer ferramenta que normalmente utilize para o desenvolvimento para executar o seu código e desinbugijá-lo. O tráfego é encaminhado entre o seu computador de desenvolvimento e o seu cluster durante todo o tempo em que está conectado. Esta ligação persistente permite-lhe iniciar, parar e reiniciar o seu código o quanto necessário sem ter de restabelecer uma ligação.

Além disso, a Azure Dev Spaces fornece uma forma de replicar variáveis ambientais e ficheiros montados disponíveis para pods no seu cluster AKS no seu computador de desenvolvimento através do ficheiro *azds-local.env.* Também pode usar este ficheiro para criar novas variáveis ambientais e suportes de volume.

## <a name="additional-configuration-with-azds-localenv"></a>Configuração adicional com azds-local.env

O ficheiro *azds-local.env* permite-lhe replicar variáveis ambientais e ficheiros montados disponíveis para as suas cápsulas no seu cluster AKS. Pode especificar as seguintes ações num ficheiro *azds-local.env:*

* Baixe um volume e descarregue o caminho para esse volume como uma variável ambiental.
* Descarregue um ficheiro ou conjunto de ficheiros individuais a partir de um volume e monte-o no seu computador de desenvolvimento.
* Disponibilize um serviço independentemente do cluster a que esteja ligado.

Aqui está um exemplo *azds-local.env* arquivo:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

Não é criado automaticamente um ficheiro predefinido *azds-local.env,* pelo que deve criar manualmente o ficheiro na raiz do seu projeto.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registos

Quando ligados ao seu cluster AKS, os registos de diagnóstico do seu cluster são registados no [diretório temporário][azds-tmp-dir]do seu computador de desenvolvimento . Utilizando o Código do Estúdio Visual, também pode utilizar o comando de *informações* de diagnóstico do Show para imprimir as variáveis ambientais atuais e as entradas de DNS do seu cluster AKS.

## <a name="next-steps"></a>Passos seguintes

Para começar a ligar o seu computador de desenvolvimento local ao seu cluster AKS, consulte Connect o seu computador de [desenvolvimento a um cluster AKS][connect].

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
