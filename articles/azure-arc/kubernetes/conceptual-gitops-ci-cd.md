---
title: Fluxo de trabalho CI/CD utilizando GitOps - Azure Arc habilitado Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Este artigo fornece uma visão geral conceptual de um fluxo de trabalho CI/CD usando GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contentores, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121784"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Fluxo de trabalho CI/CD usando GitOps - Azure Arc habilitado Kubernetes

Os modernos implantadores kubernetes abrigam múltiplas aplicações, clusters e ambientes. Com o GitOps, você pode gerir estas configurações complexas mais facilmente, rastreando o estado desejado dos ambientes Kubernetes declarativamente com Git. Usando ferramentas git comuns para rastrear o estado do cluster, você pode aumentar a responsabilidade, facilitar a investigação de falhas e permitir a automação para gerir ambientes.

Esta visão geral conceptual explica gitOps como uma realidade em toda a aplicação mudar o ciclo de vida usando Azure Arc, Azure Repos e Azure Pipelines. [Salte para um exemplo](#example-workflow) de uma única mudança de aplicação para ambientes Kubernetes controlados por GitOps.

## <a name="architecture"></a>Arquitetura

Considere uma aplicação implantada num ou mais ambientes Kubernetes.

![GitOps arquitetura CI/CD](./media/gitops-arch.png)

### <a name="application-repo"></a>Repo de aplicação
O repo de aplicação contém o código de aplicação em que os desenvolvedores trabalham durante o seu loop interno. Os modelos de implementação da aplicação vivem neste repo de uma forma genérica, como Helm ou Kustomize. Valores específicos do ambiente não são armazenados. As alterações a este repo invocam um pipeline de Relações Públicas ou CI que inicia o processo de implantação.
### <a name="container-registry"></a>Container Registry
O registo do contentor contém todas as imagens de primeira e de terceiros utilizadas nos ambientes de Kubernetes. Marque imagens de aplicação de primeira parte com tags legíveis humanas e o git compromete-se usado para construir a imagem. Cache imagens de terceiros para segurança, velocidade e resiliência. Desajei um plano para testes atempadamente e integração de atualizações de segurança. Para mais informações, consulte o [ACR Consume e mantenha](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) o guia de conteúdos públicos como exemplo.
### <a name="pr-pipeline"></a>Oleoduto PR
Os pRs para o repo de aplicação são fechados numa execução bem sucedida do gasoduto de RP. Este gasoduto executa os portões de qualidade básica, tais como o linco e os testes unitários no código de aplicação. O pipeline testa a aplicação e os modelos Dockerfiles e Helm utilizados para a implantação num ambiente kubernetes. As imagens do estivador devem ser construídas e testadas, mas não empurradas. Mantenha a duração do gasoduto relativamente curta para permitir uma iteração rápida.
### <a name="ci-pipeline"></a>Gasoduto CI
O gasoduto CI da aplicação executa todas as etapas do gasoduto pr e expande os controlos de teste e de implantação. O gasoduto pode ser executado para cada compromisso, ou em uma cadência regular com um grupo de compromissos. Nesta fase, realize testes de aplicação que são demasiado longos para um oleoduto de Relações Públicas. Empurre as imagens do Docker para o registo do contentor depois de construir em preparação para a implantação. O modelo substituído pode ser ligado com um conjunto de valores de teste. As imagens utilizadas no tempo de funcionação de serviço devem ser forradas, construídas e testadas neste momento. Especificamente na construção do CI, são publicados artefactos para que o passo do CD seja consumido em preparação para a implantação.
### <a name="flux"></a>Flux
O Flux é um serviço que funciona em cada cluster e é responsável pela manutenção do estado desejado. O serviço frequentemente sonda o GitOps repo para alterações no seu cluster e aplica-os.
### <a name="cd-pipeline"></a>CD Pipeline
O gasoduto de CD é automaticamente acionado por construções de CI bem sucedidas. Utiliza os modelos anteriormente publicados, substitui os valores ambientais e abre um PR ao repo GitOps para solicitar uma alteração ao estado desejado de um ou mais clusters Kubernetes. Os administradores de clusters analisam o Pr de mudança de estado e aprovam a fusão para o repo GitOps. O oleoduto aguarda então a conclusão do Pr, o que permite ao Flux captar a mudança de estado.
### <a name="gitops-repo"></a>GitOps repo
O repo GitOps representa o estado atual desejado de todos os ambientes em todos os aglomerados. Qualquer alteração a esta repo é captada pelo serviço Flux em cada cluster e implantada. Os PRs são criados com alterações ao estado desejado, revistos e fundidos. Estes PRs contêm alterações tanto nos modelos de implantação como nos manifestos de Kubernetes renderizados resultantes. Manifestos renderizados de baixo nível permitem uma inspeção mais cuidadosa de alterações tipicamente invisíveis ao nível do modelo.
### <a name="kubernetes-clusters"></a>Clusters do Kubernetes
Pelo menos um arco Azure habilitado a clusters Kubernetes serve os diferentes ambientes necessários pela aplicação. Por exemplo, um único cluster pode servir um ambiente dev e QA através de diferentes espaços de nome. Um segundo aglomerado pode proporcionar uma separação mais fácil dos ambientes e um controlo mais fino.
## <a name="example-workflow"></a>Fluxo de trabalho exemplo
Como desenvolvedora de aplicações, Alice:
* Escreve código de aplicação.
* Determina como executar a aplicação num contentor docker.
* Define os modelos que executam o contentor e os serviços dependentes num cluster Kubernetes.

Embora Alice saiba que a aplicação precisa da capacidade de funcionar em vários ambientes, ela não sabe as configurações específicas para cada ambiente.

Suponha que Alice queira fazer uma alteração de aplicação que altere a imagem do Docker usada no modelo de implementação da aplicação.

1. Alice muda o modelo de implementação, empurra-o para um ramo remoto no repo da aplicação, e abre um PR para revisão.
2. Alice pede à sua equipa para rever a mudança.
    * O gasoduto de relações públicas executa a validação.
    * Depois de um pipeline bem sucedido, a equipa desiste e a mudança é fundida.
3. O oleoduto do CI valida a mudança de Alice e completa com sucesso.
    * A mudança é segura para ser implantada no cluster, e os artefactos são guardados para o gasoduto ci.
4. A mudança de Alice funde-se e aciona o oleoduto de CD.
    * O oleoduto de CD capta os artefactos armazenados pelo gasoduto de Alice.
    * O pipeline de CD substitui os modelos por valores específicos do ambiente e encena quaisquer alterações em relação ao estado de cluster existente no repo GitOps.
    * O oleoduto de CD cria um PR para o GitOps repo com as alterações desejadas para o estado de cluster.
5. A equipa da Alice analisa e aprova o relações públicas.
    * A alteração é fundida no ramo-alvo correspondente ao ambiente.
6. Em poucos minutos, o Flux nota uma mudança no repo dos GitOps e puxa a mudança de Alice.
    * Devido à mudança de imagem do Docker, o pod da aplicação requer uma atualização.
    * O fluxo aplica a alteração ao cluster.
7. Alice testa o ponto final da aplicação para verificar se a implementação foi concluída com sucesso.
   > [!NOTE]
   > Para mais ambientes direcionados para a implantação, o oleoduto de CD itera criando um PR para o ambiente seguinte e repete os passos 4-7. O processo muitos precisam de aprovação extra para implementações ou ambientes mais arriscados, como uma mudança relacionada com a segurança ou um ambiente de produção.
8.  Uma vez que todos os ambientes tenham recebido implementações bem sucedidas, o gasoduto completa.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a criação de ligações entre o seu cluster e um repositório Git como um [recurso de configuração com o Arco Azure habilitado a Kubernetes](./conceptual-configurations.md)
