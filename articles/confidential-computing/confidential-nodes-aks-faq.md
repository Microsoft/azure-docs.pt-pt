---
title: Perguntas frequentes para suporte de nós confidenciais no Serviço Azure Kubernetes (AKS)
description: Encontre respostas a algumas das perguntas comuns sobre o Serviço Azure Kubernetes (AKS) & suporte de nós Azure Confidential Computing (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874489"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre nós de computação confidencial no Serviço Azure Kubernetes (AKS)

Este artigo aborda questões frequentes sobre os nódos de computação confidencial baseados na Intel SGX no Serviço Azure Kubernetes (AKS). Se tiver mais perguntas, envie um acconaks@microsoft.com e-mail.

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Que Acordo de Nível de Serviço (SLA) e Suporte Azure é fornecido durante a pré-visualização? 

O SLA não é fornecido durante a pré-visualização do produto, tal como definido [aqui](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). No entanto, o suporte do produto é fornecido através do suporte Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>O que é atestado e como podemos fazer atestado de aplicações em execução em enclaves? 

Attestation é o processo de demonstrar e validar que uma peça de software foi corretamente instantânea na plataforma de hardware específica. Também garante que os seus elementos de prova são verificáveis para dar garantias de que está a funcionar numa plataforma segura e que não foi adulterado. [Leia mais](attestation.md) sobre como o atestado é feito para aplicações de enclave.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Posso permitir networking acelerado com Azure com a azure computação confidencial AKS Clusters? 

Não. O Networking Acelerado não é suportado em nós de computação confidencial na AKS. Certifique-se de que a rede acelerada está desativada na sua implantação. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Posso trazer as minhas aplicações contentorizadas existentes e executá-la na AKS com a Azure Confidential Computing? 

Sim, reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes sobre os facilitadores da plataforma.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Qual a versão Intel SGX Driver instalada na imagem AKS? 

Atualmente, a Azure computação confidencial DCSv2 VMs são instaladas com Intel SGX DCAP 1.33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Posso abrir um bilhete de apoio ao Azure se me der conta de problemas? 

Yes. O suporte Azure é fornecido durante a pré-visualização. Não existe um SLA ligado porque o produto está em fase de pré-visualização.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Posso injetar scripts/personalizar os scripts para os Nós a provisionados pela AKS? 

Não. [Os nós de computação confidencial baseados no motor AKS suportam](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) nós de computação confidenciais que permitem instalações personalizadas.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Devo usar uma imagem base do Docker para começar com aplicações de enclave? 

Vários facilitadores (projetos ISVs e OSS) fornecem formas de permitir contentores confidenciais. Reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes e referências individuais às implementações.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Posso executar acc nodes com outros SKUs AKS padrão (construir um aglomerado de piscina de nó heterogéneo)? 

Sim, você pode executar diferentes piscinas de nós dentro do mesmo cluster AKS, incluindo nós ACC. Para direcionar as suas aplicações de enclave para um conjunto de nós específicos, considere adicionar seletores de nó ou aplicar limites EPC. Consulte mais detalhes sobre o início rápido dos nós confidenciais [aqui.](confidential-nodes-aks-get-started.md)

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Posso executar os nós windows e os contentores das janelas com o ACC? 

Neste momento, não. Contacte-nos se tiver nós windows ou necessidades de contentores. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>E se o tamanho do meu contentor for mais do que a memória EPC disponível? 

A memória EPC aplica-se à parte da sua aplicação programada para executar no enclave. O tamanho total do seu recipiente não é a forma correta de compará-lo com a memória EPC disponível no máximo. De facto, as máquinas DCSv2 com SGX permitem a memória máxima de VM de 32 GB onde a sua parte não fidedifusta da aplicação utilizaria. No entanto, se o seu recipiente consumir mais do que a memória EPC disponível, então o desempenho da parte do programa em execução no enclave pode ser impactado.

Para gerir melhor a memória EPC nos nós dos trabalhadores, considere a gestão de limites baseados na memória do EPC através de Kubernetes. Siga o exemplo abaixo como referência

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>O que acontece se o meu enclave consumir mais do que a memória EPC disponível? 

A memória total disponível do EPC é partilhada entre as aplicações do enclave nos mesmos VMs ou nós de trabalhadores. Se a sua aplicação utilizar a memória EPC mais do que disponível, o desempenho da aplicação poderá ser impactado. Por esta razão, recomendamos que coloque a toleração por aplicação no seu ficheiro yaml de implementação para gerir melhor a memória EPC disponível por nós de trabalhador, como mostrado nos exemplos acima. Em alternativa, pode sempre optar por subir no tamanho do número de trabalhadores VM ou adicionar mais nós. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Por que não posso fazer garfos () e executivos para executar vários processos na minha aplicação do enclave? 

Atualmente, os DCsv2 SKU VMs de computação confidencial da Azure suportam um espaço de endereço único para a execução do programa num enclave. O processo único é uma limitação atual projetada em torno de alta segurança. No entanto, os facilitadores de contentores confidenciais podem ter implementações alternativas para ultrapassar esta limitação.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Instala automaticamente algum daemonsets adicionais para expor os controladores SGX? 

Yes. O nome do daemonset é sgx-device-plugin e sgx-quote-helper. Leia mais sobre os seus respetivos propósitos [aqui.](confidential-nodes-aks-overview.md)  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>O que é o VM SKU que eu deveria escolher para nós de computação confidencial? 

DCSv2 SKUs. Os [SKUs DCSv2](../virtual-machines/dcv2-series.md) estão disponíveis nas [regiões apoiadas.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Ainda posso agendar e executar contentores não-enclaves em nós de computação confidencial? 

Yes. Os VMs também têm uma memória regular que pode executar cargas de trabalho padrão do contentor. Considere o modelo de segurança e ameaça das suas aplicações antes de decidir sobre os modelos de implementação.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Posso providenciar AKS com piscinas de nó DCSv2 através do portal Azure? 

Yes. O Azure CLI também pode ser usado como uma alternativa, como [documentado aqui.](confidential-nodes-aks-get-started.md)

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Que versão Ubuntu e geração VM são suportadas? 

18.04 na Gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Podemos alterar a versão atual do mergulhador Intel SGX DCAP na AKS? 

Não. Para realizar quaisquer instalações personalizadas, recomendamos que escolha as implementações [de nós de trabalho de computação confidencial aks-engine.](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Que versão de Kubernetes apoia e recomenda? 

Apoiamos e recomendamos a versão 1.16 e superior da Kubernetes 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Quais são as limitações atuais conhecidas ou limitações técnicas do produto em pré-visualização? 

- Suporta Ubuntu 18.04 Gen 2 VM Nós apenas 
- Sem suporte para nó de noções do Windows ou suporte a contentores do Windows
- O autoscaling horizontal da cápsula horizontal baseada em memória EPC não é suportado. O CPU e o escalonamento regular baseado na memória são suportados.
- Dev Spaces em AKS para aplicações confidenciais não é atualmente suportado

## <a name="next-steps"></a>Passos Seguintes
Reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes em torno de contentores confidenciais.