---
title: Como funciona a criação de um cluster para a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve como funciona a criação de um cluster de serviçoS Azure Kubernetes para a Azure Dev Spaces
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91972684"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Como funciona a criação de um cluster para a Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces fornece-lhe múltiplas formas de iteração rápida e depuração rápida de aplicações kubernetes e colaborar com a sua equipa num cluster Azure Kubernetes Service (AKS). Uma maneira é ativar os Espaços Azure Dev no seu cluster AKS para que possa [executar serviços diretamente no seu cluster][how-it-works-up] e utilizar [capacidades adicionais de networking e encaminhamento][how-it-works-routing]. Este artigo descreve o que acontece quando prepara o seu cluster e ativa os espaços Azure Dev.

## <a name="prepare-your-aks-cluster"></a>Prepare o seu cluster AKS

Para preparar o seu cluster AKS para Espaços Dev, verifique se o seu cluster AKS está numa região [apoiada pela Azure Dev Spaces][supported-regions] e está a executar Kubernetes 1.10.3 ou mais tarde. Pode ativar os espaços Azure Dev a partir do CLI Azure funcionando `az aks use-dev-spaces` .

Quando o Azure Dev Spaces estiver ativado no seu cluster AKS, instala o controlador para o seu cluster. O controlador reside fora do seu aglomerado AKS. Impulsiona o comportamento e a comunicação entre a ferramenta do lado do cliente e o cluster AKS. Uma vez ativado, pode interagir com o controlador utilizando a ferramenta do lado do cliente.

O controlador executa as seguintes ações:

* Gere a criação e seleção do espaço dev.
* Instala o gráfico Helm da sua aplicação e cria objetos Kubernetes.
* Constrói a imagem do contentor da sua aplicação.
* Implementa a sua aplicação para AKS.
* Faz construções incrementais e reinicia quando o seu código fonte muda.
* Gere registos e vestígios HTTP.
* Encaminha a sestramento e stderr para a ferramenta do lado do cliente.
* Configura o encaminhamento para aplicações dentro de um espaço, bem como em espaços de pais e filhos.

O controlador é um recurso Azure separado fora do seu cluster e faz o seguinte aos recursos no seu cluster:

* Cria ou designa um espaço de nome Kubernetes para usar como espaço dev.
* Remove qualquer espaço de nome Kubernetes chamado *azds*, se existir, e cria um novo.
* Implementa uma configuração de webhook Kubernetes.
* Implementa um servidor de admissão webhook.

Utiliza o mesmo principal de serviço que o seu cluster AKS utiliza para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Azure Dev Spaces preparam cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Para utilizar os Espaços Azure Dev, deve haver pelo menos um espaço dev. A Azure Dev Spaces utiliza espaços de nome Kubernetes dentro do seu cluster AKS para espaços dev. Quando um controlador está a ser instalado, pede-lhe que crie um novo espaço de nomes Kubernetes ou escolha um espaço de nome existente para usar como seu primeiro espaço dev. Por predefinição, o controlador oferece-se para atualizar o espaço de nomes Kubernetes *existente* para o seu primeiro espaço dev.

Quando um espaço de nome é designado como um espaço dev, o controlador adiciona a etiqueta *azds.io/space=true* a esse espaço de nome para identificá-lo como um espaço dev. O espaço dev inicial que cria ou designa é selecionado por padrão depois de preparar o seu cluster. Quando um espaço é selecionado, é usado pela Azure Dev Spaces para criar novas cargas de trabalho.

Pode utilizar a ferramenta do lado do cliente para criar novos espaços dev e remover os espaços dev existentes. Devido a uma limitação em Kubernetes, o espaço dev *padrão* não pode ser removido. O controlador também remove quaisquer espaços de nome Kubernetes existentes *chamados azds* para evitar conflitos com o `azds` comando usado pela ferramenta do lado do cliente.

O servidor de admissão de Webhook Kubernetes é utilizado para injetar cápsulas com três recipientes durante a implantação para instrumentação: um recipiente devspaces-proxy, um recipiente de devspaces-proxy-init e um recipiente de construção de devspaces. **Todos estes três contentores funcionam com acesso à raiz no seu cluster AKS.** Também usam o mesmo principal de serviço que o seu cluster AKS usa para fazer chamadas de serviço para outros componentes do Azure Dev Spaces.

![Azure Dev Spaces Kubernetes webhook servidor de admissão](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

O recipiente devspaces-proxy é um recipiente de sidecar que trata de todo o tráfego TCP dentro e fora do recipiente de aplicação e ajuda a executar o encaminhamento. O recipiente devspaces-proxy redireciona as mensagens HTTP se determinados espaços estiverem a ser utilizados. Por exemplo, pode ajudar a encaminhar mensagens HTTP entre aplicações em espaços parentais e infantis. Todo o tráfego não-HTTP passa através de devspaces-proxy unmodified. O recipiente devspaces-proxy também regista todas as mensagens HTTP de entrada e saída e envia-as para a ferramenta do lado do cliente como vestígios. Estes vestígios podem então ser vistos pelo desenvolvedor para inspecionar o comportamento da aplicação.

O recipiente de devspaces-proxy-init é um [recipiente init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) que adiciona regras adicionais de encaminhamento com base na hierarquia espacial ao contentor da sua aplicação. Adiciona regras de encaminhamento atualizando a configuração do ficheiro */etc/resolve.conf* do recipiente de aplicação e da configuração do iptables antes de começar. As atualizações para */etc/resolve.conf* permitem a resolução de serviços DNS em espaços-mãe. As atualizações de configuração do iptables garantem que todo o tráfego TCP dentro e fora do contentor da aplicação é encaminhado embora devspaces-proxy. Todas as atualizações de devspaces-proxy-init acontecem para além das regras que Kubernetes adiciona.

O recipiente de construção de devspaces é um recipiente init e tem o código fonte do projeto e tomada Docker montado. O código fonte do projeto e o acesso ao Docker permitem que o recipiente de aplicação seja construído diretamente pela cápsula.

> [!NOTE]
> A Azure Dev Spaces usa o mesmo nó para construir o contentor da sua aplicação e executá-lo. Como resultado, a Azure Dev Spaces não necessita de um registo externo de contentores para a construção e execução da sua aplicação.

O servidor de admissão de Webhook Kubernetes ouve qualquer novo pod que seja criado no cluster AKS. Se essa cápsula for implantada em qualquer espaço de nome com a etiqueta *azds.io/space=true,* injeta essa cápsula com os recipientes adicionais. O recipiente de construção de devspaces só é injetado se o recipiente da aplicação for executado utilizando a ferramenta do lado do cliente.

Uma vez preparado o seu cluster AKS, pode utilizar a ferramenta do lado do cliente para preparar e executar o seu código no seu espaço dev.

## <a name="client-side-tooling"></a>Ferramenta do lado do cliente

A ferramenta do lado do cliente permite ao utilizador:
* Gere um ficheiro de configuração dockerfile, Helm e Azure Dev Spaces para a aplicação.
* Criar espaços de dev para pais e filhos.
* Diga ao controlador para construir e iniciar a sua aplicação.

Enquanto a sua aplicação está em execução, a ferramenta do lado do cliente também:
* Recebe e exibe stdout e stderr da sua aplicação em execução em AKS.
* Utiliza [porta-a-frente](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) para permitir o acesso da Web à sua aplicação utilizando http: \/ /localhost.
* Anexa um depurado à sua aplicação de execução em AKS.
* Sincroniza o código fonte para o seu espaço dev quando é detetada uma alteração para construções incrementais, permitindo uma iteração rápida.
* Permite-lhe ligar a sua máquina de desenvolvimento diretamente ao seu cluster AKS.

Pode utilizar a ferramenta do lado do cliente a partir da linha de comando como parte do `azds` comando. Também pode utilizar a ferramenta do lado do cliente com:

* Código de Estúdio Visual utilizando a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Estúdio Visual com a carga de trabalho do Desenvolvimento Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a utilização da ferramenta do lado do cliente para preparar e executar o seu código no seu espaço dev, veja [como funciona a preparação de um projeto para a Azure Dev Spaces.][how-it-works-prep]


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service