---
title: Nós de computação confidencial no Serviço Azure Kubernetes (AKS)
description: Nódes de computação confidencial na AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9ca98c032a7c8bd1820a92bff77079a61c515d65
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653385"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Nódes de computação confidencial no Serviço Azure Kubernetes

[A computação confidencial Azure](overview.md) permite-lhe proteger os seus dados sensíveis enquanto está em uso. A infraestrutura de computação confidencial subjacente protege estes dados de outras aplicações, administradores e fornecedores de nuvem com um ambiente de contentores de execução fidedignos apoiados por hardware. A adição de nós de computação confidencial permite-lhe direcionar a aplicação do contentor para funcionar num ambiente isolado, protegido e atestável.

## <a name="overview"></a>Descrição Geral

O Azure Kubernetes Service (AKS) suporta a adição de [nós de computação confidencial DCsv2 alimentados](confidential-computing-enclaves.md) pela Intel SGX. Estes gódes permitem executar cargas de trabalho sensíveis dentro de um ambiente de execução fidedigno baseado em hardware (TEE). Tee's permite que o código de nível de utilizador a partir de contentores aloque regiões privadas de memória para executar o código diretamente com CPU. Estas regiões privadas de memória que executam diretamente com CPU são chamadas enclaves. Os enclaves ajudam a proteger a confidencialidade dos dados, a integridade dos dados e a integridade do código de outros processos em execução nos mesmos nós. O modelo de execução Intel SGX também remove as camadas intermédias de Guest OS, Host OS e Hypervisor, reduzindo assim a área da superfície de ataque. O *modelo de execução isolado de hardware por contentor* num nó permite que as aplicações executem diretamente com o CPU, mantendo o bloco especial de memória encriptado por recipiente. Nós de computação confidencial com contentores confidenciais são uma grande adição ao seu planeamento de segurança zero e estratégia de contentores em profundidade.

![visão geral do nó sgx](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funcionalidades de nó confidencial da AKS

- Isolamento de contentores baseados em hardware e nível de processo através do ambiente de execução fidedigna da Intel SGX (TEE) 
- Aglomerados de piscina de nó heterogéneo (misturar piscinas de nó confidenciais e não confidenciais)
- Agendamento de pods baseado em cache de página encriptada (EPC) (requer complemento)
- Controlador Intel SGX DCAP pré-instalado
- Autoscalagem de pod horizontal baseada em consumo de CPU e autoscalagem de cluster
- Suporte de contentores Linux através de nós de trabalhadores Ubuntu 18.04 Gen 2 VM

## <a name="confidential-computing-add-on-for-aks"></a>Complemento de computação confidencial para AKS
A funcionalidade de add-on permite uma capacidade extra em AKS ao executar piscinas confidenciais de nó de computação no cluster. Este addon permite as funcionalidades abaixo.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Plugin de dispositivo Azure para Intel SGX <a id="sgx-plugin"></a>

O plugin do dispositivo implementa a interface plugin do dispositivo Kubernetes para a memória de Cache de Página Encriptada (EPC) e expõe os controladores do dispositivo dos nós. Efetivamente, este plugin faz a memória EPC como outro tipo de recurso em Kubernetes. Os utilizadores podem especificar limites neste recurso tal como outros recursos. Além da função de agendamento, o plugin do dispositivo ajuda a atribuir permissões do controlador do dispositivo Intel SGX a recipientes confidenciais de carga de trabalho. Com este desenvolvedor de plugins pode evitar a montagem dos volumes do controlador Intel SGX nos ficheiros de implantação. Uma amostra de implementação da amostra baseada na memória do EPC `kubernetes.azure.com/sgx_epc_mem_in_MiB` () amostra está [aqui](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modelos de programação

### <a name="confidential-containers"></a>Contentores confidenciais

[Os contentores confidenciais](confidential-containers.md) ajudam-no a executar as aplicações de contentores não modificadas existentes da maioria das **línguas de programação comuns** (Python, Nó, Java, etc.) confidencialmente. Este modelo de embalagem não necessita de modificações ou recompilações do código de origem. Este é o método mais rápido de confidencialidade que poderia ser alcançado através da embalagem dos seus recipientes estivadores standard com Open-Source Projects ou Azure Partner Solutions. Neste modelo de embalagem e execução todas as partes da aplicação do contentor são carregadas no limite de confiança (enclave). Este modelo funciona bem para aplicações de contentores fora da prateleira disponíveis no mercado ou aplicações personalizadas atualmente em execução em nós de finalidade geral.

### <a name="enclave-aware-containers"></a>Recipientes conscientes do enclave
Os nós de computação confidencial em AKS também suportam recipientes programados para funcionar num enclave para utilizar um **conjunto de instruções especiais** disponíveis no CPU. Este modelo de programação permite um controlo mais apertado do seu fluxo de execução e requer a utilização de SDKs e quadros especiais. Este modelo de programação proporciona a maior parte do controlo do fluxo de aplicação com uma base de computação fidedigna mais baixa (TCB). O desenvolvimento do contentor consciente do enclave envolve peças não confiáveis e fidedignas para a aplicação do recipiente, permitindo-lhe gerir a memória regular e a memória de Cache de Página Encriptada (EPC) onde o enclave é executado. [Leia mais](enclave-aware-containers.md) sobre os recipientes de consciência do enclave.

## <a name="next-steps"></a>Passos Seguintes

[Implementar cluster AKS com nódoas de computação confidencial](./confidential-nodes-aks-get-started.md)

[Amostras confidenciais de recipiente de arranque rápido](https://github.com/Azure-Samples/confidential-container-samples)

[Lista DCsv2 SKU](../virtual-machines/dcv2-series.md)

[Defesa em profundidade com sessão de webinar de contentores confidenciais](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
