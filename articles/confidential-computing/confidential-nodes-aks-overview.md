---
title: Nós de computação confidencial na pré-visualização pública do Serviço Azure Kubernetes (AKS)
description: Nódes de computação confidencial na AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: a009cd7763b4a4dc0c502d4c47a20d6fdffe61d7
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125446"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Nós de computação confidencial no Serviço Azure Kubernetes (pré-visualização pública)

[A computação confidencial Azure](overview.md) permite-lhe proteger os seus dados sensíveis enquanto está em uso. As infraestruturas subjacentes protegem estes dados de outras aplicações, administradores e fornecedores de nuvem com um ambiente de contentores de execução fidedignos apoiados por hardware.

## <a name="overview"></a>Descrição geral

O Azure Kubernetes Service (AKS) suporta a adição de [nós de computação confidencial DCsv2 alimentados](confidential-computing-enclaves.md) pela Intel SGX. Estes nós funcionam pode executar cargas de trabalho sensíveis dentro de um ambiente de execução fidedigno baseado em hardware (TEE), permitindo que o código de nível de utilizador aloque regiões privadas de memória. Estas regiões privadas de memória são chamadas enclaves. Os enclaves são projetados para proteger código e dados de processos em execução com maior privilégio. O modelo de execução SGX remove as camadas intermédias de Guest OS, Host OS e Hypervisor. O *modelo de execução isolado de hardware por contentor* permite que as aplicações executem diretamente com o CPU, mantendo o bloco especial de memória encriptado. Os nós de computação confidencial ajudam com a postura de segurança geral das aplicações de contentores na AKS e uma grande adição à estratégia de contentores em profundidade. 

![visão geral do nó sgx](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funcionalidades de nó confidencial da AKS

- Isolamento de contentores baseados em hardware e nível de processo através do ambiente de execução fidedigna SGX (TEE) 
- Aglomerados de piscina de nó heterogéneo (misturar piscinas de nó confidenciais e não confidenciais)
- Agendamento de pods baseado em cache de página encriptada (EPC)
- Condutor SGX DCAP pré-instalado
- Intel FSGS Patch pré-instalado
- Suporta o consumo de CPU baseado em podscaling e autoscalagem de cluster
- Fora do ajudante de atestado proc através do daemonset AKS
- Suporte de contentores Linux através de nós de trabalhadores Ubuntu 18.04 Gen 2 VM

## <a name="aks-provided-daemon-sets-addon"></a>Conjuntos AKS Providenciados Daemon (addon)

#### <a name="sgx-device-plugin"></a>Plugin de dispositivo SGX <a id="sgx-plugin"></a>

O Plugin de Dispositivo SGX implementa a interface plugin do dispositivo Kubernetes para a memória EPC. Efetivamente, este plugin faz da memória EPC um tipo adicional de recursos em Kubernetes. Os utilizadores podem especificar limites neste recurso tal como outros recursos. Além da função de agendamento, o plugin do dispositivo ajuda a atribuir permissões do controlador do dispositivo SGX a recipientes confidenciais de carga de trabalho. Uma amostra de implementação da amostra baseada na memória do EPC `kubernetes.azure.com/sgx_epc_mem_in_MiB` () amostra está [aqui](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Serviço de ajuda de cotação SGX <a id="sgx-quote"></a>

As aplicações do enclave que executam atestado remoto precisam gerar um QUOTE. O QUOTE fornece uma prova criptográfica da identidade e do estado da aplicação, e do ambiente em que o enclave está em execução. A geração QUOTE baseia-se em certos componentes de software fidedignos da Intel, que fazem parte dos Componentes de Software da Plataforma SGX (PSW/DCAP). Este PSW é embalado como um conjunto de daemon que corre por nó. Pode ser alavancado ao solicitar a atestation QUOTE a partir de aplicações do enclave. A utilização do serviço AKS fornecido ajudará a manter melhor a compatibilidade entre a PSW e outros componentes SW no hospedeiro. [Leia mais](confidential-nodes-out-of-proc-attestation.md) sobre o seu uso e detalhes de recursos.

## <a name="programming--application-models"></a>Modelos de aplicação & de programação

### <a name="confidential-containers"></a>Contentores confidenciais

[Os contentores confidenciais](confidential-containers.md) executam os programas existentes e o tempo de **execução da linguagem** de programação mais comum (Python, Nó, Java, etc.), juntamente com as dependências da biblioteca existentes, sem qualquer modificação ou recompilação do código de origem. Este modelo é o modelo mais rápido de confidencialidade ativado através de Projetos Open Source & Azure Partners. As imagens de contentores que são feitas prontas criadas para funcionar nos enclaves seguros são denominadas contentores confidenciais.

### <a name="enclave-aware-containers"></a>Recipientes conscientes do enclave

A AKS apoia aplicações programadas para executar em nós confidenciais e utilizar **um conjunto de instruções especial** disponibilizado através dos SDKs e dos quadros. Este modelo de aplicação fornece a maior parte do controlo às suas aplicações com uma base de computação fidedigna mais baixa (TCB). [Leia mais](enclave-aware-containers.md) sobre os recipientes de consciência do enclave.

## <a name="next-steps"></a>Passos Seguintes

[Implementar cluster AKS com nódoas de computação confidencial](./confidential-nodes-aks-get-started.md)

[Amostras confidenciais de recipiente de arranque rápido](https://github.com/Azure-Samples/confidential-container-samples)

[Lista DCsv2 SKU](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
