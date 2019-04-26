---
title: Práticas recomendadas do operador – gerenciamento de imagens de contentor dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para saber como gerir e proteger as imagens de contentor no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464820"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para gerenciamento de imagens de contentor e de segurança no Azure Kubernetes Service (AKS)

À medida que desenvolve e executar aplicações no Azure Kubernetes Service (AKS), a segurança dos contentores e imagens de contentor é uma consideração fundamental. Imagens de base de contentores que incluem Desatualizadas ou tempos de execução do aplicativo sem patch apresentam um risco de segurança e o vetor de ataque possíveis. Para minimizar esses riscos, deve integrar ferramentas que procurar e remediar problemas nos seus contentores no momento da compilação, bem como tempo de execução. Quanto mais cedo no processo que a vulnerabilidade ou a imagem base desatualizada é capturada, mais seguro do cluster. Neste artigo, *contentores* significa que ambas as imagens de contentor armazenadas num registo de contentor e os contentores em execução.

Este artigo se concentra em como proteger os seus contentores no AKS. Saiba como:

> [!div class="checklist"]
> * Procurar e remediar vulnerabilidades de imagem
> * Utilizar um registo fidedigno com imagens de contentor assinado digitalmente
> * Acionar e voltar a implementar imagens de contentor quando uma imagem de base é atualizada automaticamente

Também pode ler as melhores práticas para [segurança do cluster] [ best-practices-cluster-security] e para [pod segurança][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e tempo de execução

**Melhores diretrizes de práticas** - analisar as imagens de contentor para vulnerabilidades e apenas implementar imagens que passaram a validação. Regularmente, Atualize as imagens bases e o tempo de execução do aplicativo, em seguida, voltar a implementar cargas de trabalho do cluster do AKS.

Uma preocupação com a adoção de cargas de trabalho baseadas em contentores é verificar a segurança das imagens e o tempo de execução usadas para criar seus próprios aplicativos. Como se cria-se de que não sejam vulnerabilidades de segurança para as suas implementações? O fluxo de trabalho de implantação deve incluir um processo para analisar imagens de contentores com ferramentas, como [Twistlock] [ twistlock] ou [Aqua][aqua]e, em seguida, Permitir apenas verificadas imagens a serem implantadas.

![Analisar e remediar as imagens de contentor, validar e implantar](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Um exemplo do mundo real, pode utilizar um pipeline de implementação contínua (CI/CD) e integração contínua para automatizar as verificações de imagem, verificação e implementações. O Azure Container Registry inclui estes recursos de verificação de vulnerabilidades.

## <a name="use-a-trusted-registry"></a>Utilizar um registo fidedigno

**Melhores diretrizes de práticas** - limitar os registos de imagem que pods e podem utilizar implementações. Permitir apenas registos confiáveis em que valida e controla as imagens que estão disponíveis.

Para segurança adicional, pode também assinar digitalmente as imagens de contentor exatamente como pode assinar digitalmente o código da aplicação. , Em seguida, permite apenas a AKS para implementar imagens assinadas. Este processo fornece uma camada adicional de segurança em que limite o AKS para apenas extrair imagens digitalmente assinadas e fidedignas por si, não apenas imagens que passam numa verificação de vulnerabilidade. Também Certifique-se de que a imagem de contentor não foi adulterada e substituída por uma imagem com o mesmo nome exato.

Registos fidedignos que fornecem as imagens de contentor assinado digitalmente adicionam complexidade ao seu ambiente, mas poderão ser necessários para determinadas política ou a conformidade a normas. O Azure Container Registry suporta a utilização de registos fidedignos e assinados imagens.

Para obter mais informações sobre imagens assinadas digitalmente, consulte [conteúdo de confiança no Azure Container Registry][acr-content-trust].

## <a name="automatically-build-new-images-on-base-image-update"></a>Criar automaticamente novas imagens na atualização da imagem base

**Melhores diretrizes de práticas** - como utilizar imagens de base para imagens do aplicativo, use a Automação para criar novas imagens quando a imagem base é atualizada. Como essas imagens bases incluem, geralmente, correções de segurança, atualize quaisquer imagens de contentor da aplicação a jusante.

Sempre que uma imagem de base é atualizada, também devem ser atualizadas quaisquer imagens de contentor downstream. Este processo de compilação deve ser integrado de validação e pipelines de implantação, como [Pipelines do Azure] [ azure-pipelines] ou o Jenkins. Esses pipelines certifica-se de que as aplicações continuem em execução nas imagens com base atualizadas. Assim que as imagens de contentor do aplicativo são validadas, as implementações do AKS, em seguida, podem ser atualizadas para executar as imagens mais recentes e seguras.

Tarefas de registo de contentor do Azure também pode atualizar automaticamente imagens de contentor quando a imagem base é atualizada. Esta funcionalidade permite-lhe criar um pequeno número de imagens de base e regularmente mantê-los atualizados com correções de bugs e de segurança.

Para obter mais informações sobre atualizações de imagem base, consulte [automatizar imagem baseia-se na atualização da imagem base com tarefas do Azure Container Registry][acr-base-image-update].

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se sobre como proteger os seus contentores. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Automatizar compilações de imagem na atualização da imagem base com tarefas de registo de contentor do Azure][acr-base-image-update]
* [Confiança de conteúdo no Azure Container Registry][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
