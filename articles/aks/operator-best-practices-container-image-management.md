---
title: Melhores práticas do operador - Gestão de imagem de contentores nos Serviços Azure Kubernetes (AKS)
description: Aprenda as melhores práticas do operador do cluster para gerir e proteger imagens de contentores no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 1d2f5465356a94b9ad7014e75aa6fe1515411a81
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564922"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para gestão de imagem de contentores e segurança no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), a segurança dos seus contentores e imagens de contentores é uma consideração fundamental. Os recipientes que incluem imagens base desatualizadas ou tempos de execução de aplicações não reparados introduzem um risco de segurança e um possível vetor de ataque. Para minimizar estes riscos, deve integrar ferramentas que analisem e remediam problemas nos seus recipientes no tempo de construção, bem como tempo de execução. Quanto mais cedo no processo a vulnerabilidade ou a imagem base desatualizada for capturada, mais seguro é o cluster. Neste artigo, os *recipientes* significam tanto as imagens dos contentores armazenadas num registo de contentores como os contentores em funcionamento.

Este artigo centra-se em como proteger os seus contentores em AKS. Saiba como:

> [!div class="checklist"]
> * Procurar e corrigir vulnerabilidades de imagem
> * Desencadeie e reimplante automaticamente imagens de contentores quando uma imagem base é atualizada

Também pode ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para a segurança [do casulo.][best-practices-pod-security]

Também pode utilizar a [segurança do contentor no Centro de Segurança][security-center-containers] para ajudar a digitalizar os seus recipientes para obter vulnerabilidades.  Há também [integração do Registo de Contentores Azure][security-center-acr] com o Security Center para ajudar a proteger as suas imagens e o registo de vulnerabilidades.

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e corra tempo

**Orientação para as melhores práticas** - Verifique as imagens do seu contentor para obter vulnerabilidades e apenas implemente imagens que tenham passado a validação. Atualize regularmente as imagens base e o tempo de execução da aplicação e, em seguida, reimplante cargas de trabalho no cluster AKS.

Uma das preocupações com a adoção de cargas de trabalho baseadas em contentores é verificar a segurança das imagens e o tempo de funcionamento utilizado para construir as suas próprias aplicações. Como é que te certificas de que não introduzes vulnerabilidades de segurança nas tuas implementações? O seu fluxo de trabalho de implementação deve incluir um processo para digitalizar imagens de contentores usando ferramentas como [Twistlock][twistlock] ou [Aqua][aqua], e, em seguida, apenas permitir que imagens verificadas sejam implantadas.

![Digitalizar e remediar imagens de contentores, validar e implantar](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Num exemplo do mundo real, pode utilizar um gasoduto de integração contínua e implementação contínua (CI/CD) para automatizar as imagens, verificação e implementações. O Registo de Contentores Azure inclui estas vulnerabilidades de digitalização de capacidades.

## <a name="automatically-build-new-images-on-base-image-update"></a>Construa automaticamente novas imagens na atualização da imagem base

**Orientação para as melhores práticas** - Ao utilizar imagens base para imagens de aplicações, utilize a automatização para construir novas imagens quando a imagem base for atualizada. Como essas imagens base normalmente incluem correções de segurança, atualize quaisquer imagens de recipientes de aplicação a jusante.

Cada vez que uma imagem base é atualizada, quaisquer imagens de contentores a jusante também devem ser atualizadas. Este processo de construção deve ser integrado em oleodutos de validação e implantação, tais como [Azure Pipelines][azure-pipelines] ou Jenkins. Estes oleodutos garantem que as suas aplicações continuam a funcionar nas imagens atualizadas. Uma vez validadas as imagens do contentor da aplicação, as implementações AKS podem ser atualizadas para executar as imagens mais recentes e seguras.

As tarefas de registo de contentores Azure também podem atualizar automaticamente as imagens dos contentores quando a imagem base é atualizada. Esta funcionalidade permite-lhe construir um pequeno número de imagens base, e mantê-las atualizadas regularmente com correções de bugs e segurança.

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