---
title: Perguntas frequentes para suporte de nós confidenciais no Serviço Azure Kubernetes (AKS)
description: Encontre respostas a algumas das perguntas comuns sobre o Serviço Azure Kubernetes (AKS) & suporte de nós Azure Confidential Computing (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653402"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre nós de computação confidencial no Serviço Azure Kubernetes (AKS)

Este artigo aborda questões frequentes sobre os nódos de computação confidencial baseados na Intel SGX no Serviço Azure Kubernetes (AKS). Se tiver mais perguntas, envie um **acconaks@microsoft.com** e-mail.

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>Os nós de computação confidencial são de AKS em GA? ###
Yes

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>O que é atestado e como podemos fazer atestado de aplicações em execução em enclaves? ###
Attestation é o processo de demonstrar e validar que uma peça de software foi corretamente instantânea na plataforma de hardware específica. Também garante que os seus elementos de prova são verificáveis para dar garantias de que está a funcionar numa plataforma segura e que não foi adulterado. [Leia mais](attestation.md) sobre como o atestado é feito para aplicações de enclave.

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Posso permitir networking acelerado com Azure com a azure computação confidencial AKS Clusters? ###
N.º O Networking Acelerado não é suportado em máquinas virtuais DCSv2 que comem nós de computação confidencial em AKS. 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Posso trazer as minhas aplicações contentorizadas existentes e executá-la na AKS com a Azure Confidential Computing? ###
Sim, reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes sobre os facilitadores da plataforma.

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>Que versão da versão Intel SGX Driver está na imagem AKS para nós confidenciais? ### 
Atualmente, a Azure computação confidencial DCSv2 VMs são instaladas com Intel SGX DCAP 1.33. 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Posso injetar scripts/personalizar os scripts para os Nós a provisionados pela AKS? ###
N.º [Os nós de computação confidencial baseados no motor AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) suportam nós de computação confidenciais que permitem instalações personalizadas e têm controlo total sobre o seu plano de controlo Kubernetes.
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Devo usar uma imagem base do Docker para começar com aplicações de enclave? ###
Vários facilitadores (projetos ISVs e OSS) fornecem formas de permitir contentores confidenciais. Reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes e referências individuais às implementações.

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Posso executar acc nodes com outros SKUs AKS padrão (construir um aglomerado de piscina de nó heterogéneo)? ###

Sim, você pode executar diferentes piscinas de nós dentro do mesmo cluster AKS, incluindo nós ACC. Para direcionar as suas aplicações de enclave para um conjunto de nós específicos, considere adicionar seletores de nó ou aplicar limites EPC. Consulte mais detalhes sobre o início rápido dos nós confidenciais [aqui.](confidential-nodes-aks-get-started.md)

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Posso executar os nós windows e os contentores das janelas com o ACC? ###
Neste momento, não. Contacte a equipa de produtos *acconaks@microsoft.com* se tiver nós windows ou necessidades de contentores. 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>E se o tamanho do meu contentor for mais do que a memória EPC disponível? ###
A memória EPC aplica-se à parte da sua aplicação programada para executar no enclave. O tamanho total do seu recipiente não é a forma correta de compará-lo com a memória EPC disponível no máximo. De facto, as máquinas DCSv2 com SGX permitem a memória máxima de VM de 32 GB onde a sua parte não fidedifusta da aplicação utilizaria. No entanto, se o seu recipiente consumir mais do que a memória EPC disponível, então o desempenho da parte do programa em execução no enclave pode ser impactado.

Para gerir melhor a memória EPC nos nós dos trabalhadores, considere a gestão de limites baseados na memória do EPC através de Kubernetes. Siga o exemplo abaixo como referência.

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>O que acontece se o meu enclave consumir mais do que a memória EPC disponível? ###

A memória total disponível do EPC é partilhada entre as aplicações do enclave nos mesmos VMs ou nós de trabalhadores. Se a sua aplicação utilizar a memória EPC mais do que disponível, então o desempenho da aplicação pode ser impactado. Por esta razão, recomendamos que coloque a toleração por aplicação no seu ficheiro yaml de implementação para gerir melhor a memória EPC disponível por nós de trabalhador, como mostrado nos exemplos acima. Em alternativa, pode sempre optar por subir no tamanho do número de trabalhadores VM ou adicionar mais nós. 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Por que não posso fazer garfos () e executivos para executar vários processos na minha aplicação do enclave? ###

Atualmente, os DCsv2 SKU VMs de computação confidencial da Azure suportam um espaço de endereço único para a execução do programa num enclave. O processo único é uma limitação atual projetada em torno de alta segurança. No entanto, os facilitadores de contentores confidenciais podem ter implementações alternativas para ultrapassar esta limitação.
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>Instala automaticamente algum daemonset adicional para expor os controladores SGX? ###

Sim. O nome do daemonset é sgx-device-plugin. Leia mais sobre os seus respetivos propósitos [aqui.](confidential-nodes-aks-overview.md)  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>O que é o VM SKU que eu deveria escolher para nós de computação confidencial? ###

DCSv2 SKUs. Os [SKUs DCSv2](../virtual-machines/dcv2-series.md) estão disponíveis nas [regiões apoiadas.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Ainda posso agendar e executar contentores não-enclaves em nós de computação confidencial? ###

Sim. Os VMs também têm uma memória regular que pode executar cargas de trabalho padrão do contentor. Considere o modelo de segurança e ameaça das suas aplicações antes de decidir sobre os modelos de implementação.
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Posso providenciar AKS com piscinas de nó DCSv2 através do portal Azure? ###

Sim. O Azure CLI também pode ser usado como uma alternativa, como [documentado aqui.](confidential-nodes-aks-get-started.md)

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Que versão Ubuntu e geração VM são suportadas? ###
18.04 na Gen 2. 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Podemos alterar a versão atual do mergulhador Intel SGX DCAP na AKS? ###

N.º Para realizar quaisquer instalações personalizadas, recomendamos que escolha as implementações [de nós de trabalho de computação confidencial aks-engine.](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Que versão de Kubernetes apoia e recomenda? ###

Apoiamos e recomendamos a versão 1.16 ou superior da Kubernetes. 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>Quais são as limitações atuais conhecidas do produto? ###

- Suporta Ubuntu 18.04 Gen 2 VM Nós apenas 
- Sem suporte para nó de noções do Windows ou suporte a contentores do Windows
- O autoscaling horizontal da cápsula horizontal baseada em memória EPC não é suportado. O CPU e o escalonamento regular baseado na memória são suportados.
- Dev Spaces em AKS para aplicações confidenciais não são suportados atualmente

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>Só imagens assinadas e fidedignas serão carregadas no enclave para computação confidencial? ###
Não de forma nativa durante a inicialização do enclave, mas sim através da assinatura do processo de atestado pode ser validado. Ref [aqui.](../attestation/basic-concepts.md#benefits-of-policy-signing) 

### <a name="next-steps"></a>Passos Seguintes
Reveja a [página de contentores confidenciais](confidential-containers.md) para obter mais detalhes em torno de contentores confidenciais.
