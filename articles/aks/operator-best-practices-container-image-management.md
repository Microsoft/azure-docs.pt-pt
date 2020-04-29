---
title: Práticas do operador - Gestão de imagem em contentores nos Serviços Azure Kubernetes (AKS)
description: Conheça as melhores práticas do operador de cluster para gerir e proteger imagens de contentores no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77594752"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Boas práticas para gestão e segurança de imagem de contentores no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), a segurança dos seus contentores e imagens de contentores é uma consideração fundamental. Os recipientes que incluem imagens base desatualizadas ou tempos de execução de aplicações não remendados introduzem um risco de segurança e um possível vetor de ataque. Para minimizar estes riscos, deve integrar ferramentas que analisem e remediam problemas nos seus contentores no momento da construção, bem como o tempo de execução. Quanto mais cedo no processo, a vulnerabilidade ou a imagem de base desatualizada forem capturadas, mais segura o cluster. Neste artigo, os *contentores* significam tanto as imagens do contentor armazenadas num registo de contentores como os contentores em funcionamento.

Este artigo centra-se em como proteger os seus contentores no AKS. Saiba como:

> [!div class="checklist"]
> * Scaneie e remediar vulnerabilidades de imagem
> * Acionar e redistribuir automaticamente imagens de contentores quando uma imagem base é atualizada

Também pode ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para a segurança [do pod][best-practices-pod-security].

Também pode utilizar a segurança do [contentor no Centro][security-center-containers] de Segurança para ajudar a digitalizar os seus contentores para obter vulnerabilidades.  Existe também integração no Registo de [Contentores Azure][security-center-acr] com o Centro de Segurança para ajudar a proteger as suas imagens e registo de vulnerabilidades.

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e corra o tempo

**Orientação de boas práticas** - Scaneie as imagens do seu recipiente para obter vulnerabilidades, e apenas implemente imagens que tenham passado pela validação. Atualize regularmente as imagens base e o tempo de execução da aplicação e, em seguida, reimplante as cargas de trabalho no cluster AKS.

Uma das preocupações com a adoção de cargas de trabalho baseadas em contentores é verificar a segurança das imagens e o tempo de execução utilizado para construir as suas próprias aplicações. Como se certifica de que não introduz vulnerabilidades de segurança nas suas implementações? O seu fluxo de trabalho de implementação deve incluir um processo para digitalizar imagens de contentores utilizando ferramentas como [Twistlock][twistlock] ou [Aqua,][aqua]e, em seguida, apenas permitir a implementação de imagens verificadas.

![Digitalizar e remediar imagens de contentores, validar e implementar](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Num exemplo real, pode utilizar um oleoduto de integração contínua e implantação contínua (CI/CD) para automatizar as imagens, verificação e implementações. O Registo de Contentores Azure inclui estas vulnerabilidades de digitalização.

## <a name="automatically-build-new-images-on-base-image-update"></a>Construa automaticamente novas imagens na atualização de imagem base

**Orientação das melhores práticas** - À medida que utiliza imagens base para imagens de aplicação, utilize a automatização para construir novas imagens quando a imagem base for atualizada. Como essas imagens base normalmente incluem correções de segurança, atualize quaisquer imagens de contentores de aplicação a jusante.

Cada vez que uma imagem base é atualizada, quaisquer imagens de contentores a jusante também devem ser atualizadas. Este processo de construção deve ser integrado em gasodutos de validação e implantação, como [o Azure Pipelines][azure-pipelines] ou o Jenkins. Estes oleodutos garantem que as suas aplicações continuam a ser executadas nas imagens atualizadas. Uma vez validadas as imagens do contentor de aplicações, as implementações aks podem ser atualizadas para executar as imagens mais recentes e seguras.

As tarefas de registo de contentores Azure também podem atualizar automaticamente as imagens do contentor quando a imagem base é atualizada. Esta funcionalidade permite-lhe construir um pequeno número de imagens base e mantê-las regularmente atualizadas com correções de bugs e segurança.

Para obter mais informações sobre as atualizações de imagem base, consulte a [imagem automate atualização de imagem base com as Tarefas de Registo de Contentores De Azure][acr-base-image-update].

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger os seus contentores. Para implementar algumas destas áreas, consulte os seguintes artigos:

* [Imagem automatizada baseia-se na atualização de imagem base com tarefas de registo de contentores azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration