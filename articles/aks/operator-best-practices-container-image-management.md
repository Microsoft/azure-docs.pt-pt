---
title: Práticas recomendadas do operador – gerenciamento de imagens de contentor dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para saber como gerir e proteger as imagens de contentor no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: ea39bceaa6b58e84def9635436d902002e33cd14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514517"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para gerenciamento de imagens de contentor e de segurança no Azure Kubernetes Service (AKS)

À medida que desenvolve e executar aplicações no Azure Kubernetes Service (AKS), a segurança dos contentores e imagens de contentor é uma consideração fundamental. Imagens de base de contentores que incluem Desatualizadas ou tempos de execução do aplicativo sem patch apresentam um risco de segurança e o vetor de ataque possíveis. Para minimizar esses riscos, deve integrar ferramentas que procurar e remediar problemas nos seus contentores no momento da compilação, bem como tempo de execução. Quanto mais cedo no processo que a vulnerabilidade ou a imagem base desatualizada é capturada, mais seguro do cluster. Neste artigo, *contentores* significa que ambas as imagens de contentor armazenadas num registo de contentor e os contentores em execução.

Este artigo se concentra em como proteger os seus contentores no AKS. Saiba como:

> [!div class="checklist"]
> * Procurar e remediar vulnerabilidades de imagem
> * Acionar e voltar a implementar imagens de contentor quando uma imagem de base é atualizada automaticamente

Também pode ler as melhores práticas para [segurança do cluster] [ best-practices-cluster-security] e para [pod segurança][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e tempo de execução

**Melhores diretrizes de práticas** - analisar as imagens de contentor para vulnerabilidades e apenas implementar imagens que passaram a validação. Regularmente, Atualize as imagens bases e o tempo de execução do aplicativo, em seguida, voltar a implementar cargas de trabalho do cluster do AKS.

Uma preocupação com a adoção de cargas de trabalho baseadas em contentores é verificar a segurança das imagens e o tempo de execução usadas para criar seus próprios aplicativos. Como se cria-se de que não sejam vulnerabilidades de segurança para as suas implementações? O fluxo de trabalho de implantação deve incluir um processo para analisar imagens de contentores com ferramentas, como [Twistlock] [ twistlock] ou [Aqua][aqua]e, em seguida, Permitir apenas verificadas imagens a serem implantadas.

![Analisar e remediar as imagens de contentor, validar e implantar](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Um exemplo do mundo real, pode utilizar um pipeline de implementação contínua (CI/CD) e integração contínua para automatizar as verificações de imagem, verificação e implementações. O Azure Container Registry inclui estes recursos de verificação de vulnerabilidades.

## <a name="automatically-build-new-images-on-base-image-update"></a>Criar automaticamente novas imagens na atualização da imagem base

**Melhores diretrizes de práticas** - como utilizar imagens de base para imagens do aplicativo, use a Automação para criar novas imagens quando a imagem base é atualizada. Como essas imagens bases incluem, geralmente, correções de segurança, atualize quaisquer imagens de contentor da aplicação a jusante.

Sempre que uma imagem de base é atualizada, também devem ser atualizadas quaisquer imagens de contentor downstream. Este processo de compilação deve ser integrado de validação e pipelines de implantação, como [Pipelines do Azure] [ azure-pipelines] ou o Jenkins. Esses pipelines certifica-se de que as aplicações continuem em execução nas imagens com base atualizadas. Assim que as imagens de contentor do aplicativo são validadas, as implementações do AKS, em seguida, podem ser atualizadas para executar as imagens mais recentes e seguras.

Tarefas de registo de contentor do Azure também pode atualizar automaticamente imagens de contentor quando a imagem base é atualizada. Esta funcionalidade permite-lhe criar um pequeno número de imagens de base e regularmente mantê-los atualizados com correções de bugs e de segurança.

Para obter mais informações sobre atualizações de imagem base, consulte [automatizar imagem baseia-se na atualização da imagem base com tarefas do Azure Container Registry][acr-base-image-update].

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se sobre como proteger os seus contentores. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Automatizar compilações de imagem na atualização da imagem base com tarefas de registo de contentor do Azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
