---
title: Contentores confidenciais no Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre o suporte de contentores não modificados em contentores confidenciais.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 35518a90ff3db2b951e0310970afd6d78dd25807
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122208"
---
# <a name="confidential-containers"></a>Contentores confidenciais

## <a name="overview"></a>Descrição geral

Permitir que os desenvolvedores tragam uma **aplicação de estivador existente (nova ou existente)** e executá-la de forma segura no Serviço Azure Kubernetes (AKS) através do suporte confidencial dos nós de computação.

Os contentores confidenciais ajudam a proteger:

- integridade dos dados 
- confidencialidade dos dados
- integridade do código
- proteção de código de contentor através de encriptação
- garantias baseadas em hardware
- permitir executar aplicações existentes
- criar raiz de hardware de confiança

Um Ambiente de Execução Fidedigna (TEE) baseado em hardware é um componente importante que é usado para fornecer fortes garantias através de medições de hardware e software a partir de componentes de base de computação fidedigna (TCB). As verificações destas medições ajudam na validação do cálculo esperado e verificam qualquer adulteração das aplicações do contentor.

Os contentores confidenciais suportam aplicações personalizadas desenvolvidas com **Python, Java, Node JS, etc. ou aplicações de software embaladas como NGINX, Redis Cache, MemCache,** e assim por diante, para serem executadas sem modificação em AKS.

Os contentores confidenciais são o caminho mais rápido para a confidencialidade dos contentores, incluindo a proteção do contentor através da encriptação, permitindo levantar e deslocar sem alterações mínimas na sua lógica de negócio.

![A conversão confidencial do contentor](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Facilitadores confidenciais de contentores

Para executar um recipiente de estivador existente, as aplicações em nós de computação confidencial requerem uma camada de abstração ou software SGX para alavancar o conjunto especial de instruções do CPU. O software SGX também permite que o código de aplicações sensíveis seja protegido e crie uma execução direta ao CPU para remover o SO convidado, o HOST OS ou o Hypervisor. Esta proteção reduz as áreas de ataque de superfície e vulnerabilidades com sistema operativo ou camadas hipervisoras.

Os contentores confidenciais são totalmente suportados em AKS e ativados através de projetos Azure Partners e Open Source Software (OSS). Os desenvolvedores podem escolher fornecedores de software com base nas funcionalidades, integração nos serviços Azure e suporte de ferramentas.

## <a name="partner-enablers"></a>Facilitadores de Parceiros
> [!NOTE]
> As soluções abaixo são oferecidas através da Azure Partners e podem incorrer em taxas de licenciamento. Verifique os termos do software do parceiro de forma independente. 

### <a name="fortanix"></a>Fortanix

[A Fortanix](https://www.fortanix.com/) oferece aos desenvolvedores uma escolha de um portal e experiência baseada em CLI para levar as suas aplicações contentorizadas e encostá-las a recipientes confidenciais capazes de SGX sem necessidade de modificar ou recompiler a aplicação. A Fortanix proporciona a flexibilidade para executar e gerir o conjunto mais amplo de aplicações, incluindo aplicações existentes, novas aplicações nativas do enclave e aplicações pré-embaladas. Os utilizadores podem começar com [o Enclave Manager](https://em.fortanix.com/) UI ou REST [APIs](https://www.fortanix.com/api/em/) para criar recipientes confidenciais seguindo o guia [Quick Start](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) para o Serviço Azure Kubernetes.

![Processo de implantação de Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[O SCONE](https://scontain.com/index.html?lang=en) suporta políticas de segurança que podem gerar certificados, chaves e segredos, e garante que só são visíveis para atestar serviços de uma aplicação. Desta forma, os serviços de uma aplicação atestam-se automaticamente através do TLS - sem a necessidade de modificar as aplicações nem o TLS. Isto é explicado com a ajuda de uma simples aplicação Flask aqui: https://sconedocs.github.io/flask_demo/  

O SCONE pode converter a maioria dos binários existentes em aplicações que funcionam dentro de enclaves sem precisar de alterar a aplicação ou de recompiler essa aplicação. O SCONE também protege as línguas interpretadas como python encriptando tanto os ficheiros de dados como os ficheiros de código Python. Com a ajuda de uma política de segurança SCONE, pode-se proteger os ficheiros encriptados contra acessos, modificações e reversão não autorizados. Como "sconificar" uma aplicação python existente é explicado [aqui](https://sconedocs.github.io/sconify_image/)

![Fluxo de Scontain](./media/confidential-containers/scone-workflow.png)

As implementações de Scone em nós de computação confidencial com AKS são totalmente suportadas e integradas. Começa com uma aplicação de amostra aqui https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[A Anjuna](https://www.anjuna.io/) fornece software de plataforma SGX que lhe permite executar contentores não modificados em AKS. Saiba mais sobre a funcionalidade e confira [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)as aplicações da amostra.

Começa com uma amostra redis Cache e aplicação personalizada Python [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Processo de Anjuna](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Facilitadores de OSS 
> [!NOTE]
> As soluções abaixo são oferecidas através de Open Source Projects e não estão diretamente afiliadas à Azure Confidential Computing (ACC) ou à Microsoft.  

### <a name="graphene"></a>Grafeno

[Graphene](https://grapheneproject.io/) é um so-convidado leve, projetado para executar uma única aplicação Linux com requisitos mínimos de anfitrião. O Graphene pode executar aplicações num ambiente isolado com benefícios comparáveis à execução de um sistema operativo completo e tem um bom suporte de ferramentas para converter a aplicação existente de contentores estivadores em contentores blindados graphene (GSC).

Começa com uma aplicação de amostra e implantação em AKS [aqui](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) é uma biblioteca de vários processos de memória ,OS (LibOS) para Intel SGX. Permite que aplicações antigas funcionam em SGX com pouca ou nenhuma modificação ao código fonte. Occlum protege transparentemente a confidencialidade das cargas de trabalho dos utilizadores, permitindo uma fácil elevação e mudança para aplicações de estivadores existentes.

Occlum suporta implementações AKS. Siga as instruções de implementação com várias aplicações de amostra [aqui](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demonstração de Contentores Confidenciais
Veja a demonstração confidencial de cuidados de saúde com recipientes confidenciais. A amostra está disponível [aqui.](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md) 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Entrar em contato

Tem dúvidas com a sua implementação ou quer tornar-se um facilitador? Enviar um e-mail para acconaks@microsoft.com

## <a name="reference-links"></a>Links de referência

[Microsoft Azure Attestation](../attestation/overview.md)

[Máquinas Virtuais DCsv2](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
