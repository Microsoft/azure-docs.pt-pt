---
title: Melhores práticas do operador - Gestão de imagem de contentores nos Serviços Azure Kubernetes (AKS)
description: Aprenda as melhores práticas do operador do cluster para gerir e proteger imagens de contentores no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105124"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para gestão de imagem de contentores e segurança no Serviço Azure Kubernetes (AKS)

A segurança da imagem do contentor e do contentor é uma das principais prioridades enquanto desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS). Os recipientes com imagens base desatualizadas ou tempos de execução de aplicações não remutizados introduzem um risco de segurança e um possível vetor de ataque. 

Minimizar os riscos integrando e executando ferramentas de digitalização e reparação nos seus recipientes em tempo de construção e execução. Quanto mais cedo apanhares a vulnerabilidade ou a imagem de base desatualizada, mais seguro é o teu cluster. 

Neste artigo, *"contentores"* significa ambos:
* As imagens do contentor guardadas num registo de contentores.
* Os contentores de corrida.

Este artigo centra-se em como proteger os seus contentores em AKS. Saiba como:

> [!div class="checklist"]
> * Procure e remediar as vulnerabilidades de imagem.
> * Desencadeie e reimplante automaticamente as imagens do recipiente quando uma imagem base é atualizada.

Também pode ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para a segurança [do casulo.][best-practices-pod-security]

Também pode utilizar a [segurança do contentor no Centro de Segurança][security-center-containers] para ajudar a digitalizar os seus recipientes para obter vulnerabilidades. [A integração do Registo de Contentores Azure][security-center-acr] com o Security Center ajuda a proteger as suas imagens e o registo de vulnerabilidades.

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e corra tempo

> **Orientação de melhor prática** 
>
> Digitalize as imagens do seu contentor para obter vulnerabilidades. Apenas implemente imagens validadas. Atualize regularmente as imagens base e o tempo de execução da aplicação. Reposicionar cargas de trabalho no cluster AKS.

Ao adotar cargas de trabalho baseadas em contentores, deverá verificar a segurança das imagens e do tempo de funcionamento utilizados para construir as suas próprias aplicações. Como evitar introduzir vulnerabilidades de segurança nas suas implementações? 
* Inclua no seu fluxo de trabalho de implantação um processo para digitalizar imagens de contentores usando ferramentas como [Twistlock][twistlock] ou [Aqua][aqua].
* Só permita a implantação de imagens verificadas.

![Digitalizar e remediar imagens de contentores, validar e implantar](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Por exemplo, pode utilizar um gasoduto de integração contínua e de implementação contínua (CI/CD) para automatizar as imagens, verificação e implementações. O Registo de Contentores Azure inclui estas vulnerabilidades de digitalização de capacidades.

## <a name="automatically-build-new-images-on-base-image-update"></a>Construa automaticamente novas imagens na atualização da imagem base

> **Orientação de melhor prática** 
>
> Ao utilizar imagens base para imagens de aplicações, utilize a automatização para construir novas imagens quando a imagem base for atualizada. Uma vez que as imagens de base atualizadas normalmente incluem correções de segurança, atualize quaisquer imagens de contentores de aplicação a jusante.

Cada vez que uma imagem base é atualizada, também deve atualizar quaisquer imagens de contentores a jusante. Integre este processo de construção em oleodutos de validação e implantação, tais como [Azure Pipelines][azure-pipelines] ou Jenkins. Estes oleodutos asseguram que as suas aplicações continuam a funcionar nas imagens atualizadas. Uma vez validadas as imagens do contentor da aplicação, as implementações AKS podem ser atualizadas para executar as imagens mais recentes e seguras.

As tarefas de registo de contentores Azure também podem atualizar automaticamente as imagens dos contentores quando a imagem base é atualizada. Com esta funcionalidade, você constrói algumas imagens base e mantém-nas atualizadas com correções de bugs e segurança.

Para obter mais informações sobre atualizações de imagens base, consulte [a imagem do Automatismo na atualização da imagem base com as Tarefas de Registo de Contentores Azure][acr-base-image-update].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger os seus recipientes. Para implementar algumas destas áreas, consulte os seguintes artigos:

* [A imagem automatizada baseia-se na atualização da imagem base com as tarefas de registo de contentores do Azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md