---
title: Como funciona a criação de um cluster para a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a criação de um cluster de serviço Azure Kubernetes para a Azure Dev Spaces
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 60f6c466e7c016ccd07e37b2f0d1ce9989fb8f9f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995892"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Como funciona a criação de um cluster para a Azure Dev Spaces

A Azure Dev Spaces fornece-lhe múltiplas formas de iterar e depurar rapidamente aplicações kubernetes e colaborar com a sua equipa num cluster do Serviço Azure Kubernetes (AKS). Uma maneira é ativar o Azure Dev Spaces no seu cluster AKS para que possa [executar serviços diretamente no seu cluster][how-it-works-up] e usar capacidades adicionais de [networking e encaminhamento][how-it-works-routing]. Este artigo descreve o que acontece quando prepara o seu cluster e permite o Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Prepare o seu cluster AKS

Para preparar o seu cluster AKS para espaços Dev, verifique se o seu cluster AKS está numa região [apoiada pela Azure Dev Spaces][supported-regions] e está a executar kubernetes 1.10.3 ou mais tarde. Pode ativar os espaços Azure Dev a partir do Azure CLI funcionando `az aks use-dev-spaces` .

Para um exemplo de criação de um cluster AKS para dev Spaces, veja o [desenvolvimento da equipa rapidamente.][quickstart-team]

Quando o Azure Dev Spaces está ativado no seu cluster AKS, instala o controlador para o seu cluster. O controlador reside fora do seu aglomerado AKS. Impulsiona o comportamento e a comunicação entre a ferramenta do lado do cliente e o cluster AKS. Uma vez ativado, pode interagir com o controlador utilizando a ferramenta do lado do cliente.

O controlador executa as seguintes ações:

* Gere a criação e seleção de espaço de v.
* Instala o gráfico Helm da sua aplicação e cria objetos Kubernetes.
* Constrói a imagem do contentor da sua aplicação.
* Implementa a sua aplicação para a AKS.
* As construções incrementais e reiniciaquando o código de origem muda.
* Gere registos e vestígios HTTP.
* Para a frente stdout e stderr para a ferramenta do lado do cliente.
* Configura o encaminhamento para aplicações dentro de um espaço, bem como em espaços de pais e filhos.

O controlador é um recurso Azure separado fora do seu cluster e faz o seguinte aos recursos do seu cluster:

* Cria ou designa um espaço de nome Kubernetes para usar como espaço de v.
* Remove qualquer espaço de nome Kubernetes chamado *azds,* se existir, e cria um novo.
* Implementa uma configuração de webhook Kubernetes.
* Implementa um servidor de admissão webhook.

Utiliza o mesmo diretor de serviço que o seu cluster AKS utiliza para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Espaços Azure Dev preparam aglomerado](media/how-dev-spaces-works/prepare-cluster.svg)

Para utilizar os Espaços Azure Dev, deve haver pelo menos um espaço de v. A Azure Dev Spaces utiliza espaços de nome Kubernetes dentro do seu cluster AKS para espaços de v. Quando um controlador está a ser instalado, ele pede-lhe para criar um novo espaço de nome Kubernetes ou escolher um espaço de nome existente para usar como o seu primeiro espaço de dev. Por padrão, o controlador oferece-se para atualizar o espaço de nome padrão *default* kubernetes existente para o seu primeiro espaço de dev.

Quando um espaço de nome é designado como um espaço de v, o controlador adiciona a etiqueta *azds.io/space=true* a esse espaço de nome para identificá-lo como um espaço de v. O espaço de v inicial que cria ou designa é selecionado por padrão depois de preparar o seu cluster. Quando um espaço é selecionado, é utilizado pela Azure Dev Spaces para criar novas cargas de trabalho.

Pode utilizar a ferramenta do lado do cliente para criar novos espaços de dev e remover os espaços de dev existentes. Devido a uma limitação em Kubernetes, o espaço de v *padrão* não pode ser removido. O controlador também remove quaisquer espaços de nome kubernetes *existentes chamados azds* para evitar conflitos com o `azds` comando usado pela ferramenta do lado do cliente.

O servidor de admissão de webhook Kubernetes é utilizado para injetar cápsulas com três contentores durante a implementação para instrumentação: um recipiente de procuração de espaços devespaços, um recipiente de devspaces-proxy-init e um recipiente de construção de espaços devespaços. **Todos estes três contentores funcionam com acesso de raiz no seu cluster AKS.** Também usam o mesmo diretor de serviço que o seu cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Servidor de admissão webhook Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O contentor de procuração de espaços devespaços é um recipiente para carros laterais que transporta todo o tráfego de TCP para dentro e para fora do recipiente de aplicação e ajuda a realizar o encaminhamento. O contentor de procuração de espaços devespaços redireciona mensagens HTTP se determinados espaços estiverem a ser utilizados. Por exemplo, pode ajudar a encaminhar mensagens HTTP entre aplicações em espaços para pais e filhos. Todo o tráfego não HTTP passa por devspaces-proxy não modificado. O recipiente de procuração de espaços devespaços também regista todas as mensagens HTTP de entrada e saída e envia-as para a ferramenta do lado do cliente como vestígios. Estes vestígios podem então ser vistos pelo desenvolvedor para inspecionar o comportamento da aplicação.

O recipiente de devspaces-proxy-init é um [recipiente init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona regras adicionais de encaminhamento com base na hierarquia espacial ao recipiente da sua aplicação. Adiciona regras de encaminhamento atualizando o ficheiro */etc/resolve.conf* do recipiente de aplicação e a configuração dos iptables antes de começar. As atualizações para */etc/resolve.conf* permitem a resolução de Serviços DNS nos espaços-mãe. As atualizações de configuração iptables garantem que todo o tráfego de TCP dentro e fora do contentor da aplicação são encaminhados embora devspaces-proxy. Todas as atualizações do devspaces-proxy-init acontecem além das regras que a Kubernetes adiciona.

O recipiente de construção de espaços devespaços é um recipiente init e tem o código fonte do projeto e a tomada Docker montada. O código fonte do projeto e o acesso ao Docker permitem que o recipiente de aplicação seja construído diretamente pela cápsula.

> [!NOTE]
> A Azure Dev Spaces usa o mesmo nó para construir o contentor da sua aplicação e executá-lo. Como resultado, a Azure Dev Spaces não necessita de um registo externo de contentores para a construção e execução da sua aplicação.

O servidor de admissão de webhook kubernetes ouve qualquer novo casulo criado no cluster AKS. Se a cápsula for implantada para qualquer espaço de nome com a etiqueta *azds.io/space=true,* injeta essa cápsula com os recipientes adicionais. O recipiente de construção de espaços de vespaços só é injetado se o recipiente da aplicação for executado utilizando a ferramenta do lado do cliente.

Assim que tiver preparado o seu cluster AKS, pode utilizar a ferramenta do lado do cliente para preparar e executar o seu código no seu espaço de dev.

## <a name="client-side-tooling"></a>Ferramentas do lado do cliente

A ferramenta do lado do cliente permite ao utilizador:
* Gere um ficheiro de configuração dockerfile, Helm e Azure Dev Spaces para a aplicação.
* Crie espaços de dev para pais e filhos.
* Diga ao controlador para construir e iniciar a sua aplicação.

Enquanto a sua aplicação está em execução, a ferramenta do lado do cliente também:
* Recebe e exibe stdout e stderr da sua aplicação em execução no AKS.
* Utiliza o [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir o acesso à web à sua aplicação usando http: \/ /localhost.
* Anexa um desbugger à sua aplicação de execução no AKS.
* Sincroniza o código fonte no seu espaço de dev quando é detetada uma alteração para construções incrementais, permitindo uma rápida iteração.
* Permite-lhe ligar a sua máquina de desenvolvimento diretamente ao seu cluster AKS.

Pode utilizar a ferramenta do lado do cliente a partir da linha de comando como parte do `azds` comando. Também pode utilizar a ferramenta do lado do cliente com:

* Código de estúdio visual utilizando a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Estúdio Visual com a carga de trabalho do Desenvolvimento Azure.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a utilização da ferramenta do lado do cliente para preparar e executar o seu código no seu espaço de v, veja [como funciona um projeto para a Azure Dev Spaces.][how-it-works-prep]

Para começar a usar o Azure Dev Spaces para desenvolvimento de equipas, veja o desenvolvimento da [equipa em Azure Dev Spaces][quickstart-team] rapidamente.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md