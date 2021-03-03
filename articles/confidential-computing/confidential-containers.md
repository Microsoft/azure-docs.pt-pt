---
title: Contentores confidenciais no Serviço Azure Kubernetes (AKS)
description: Saiba mais sobre o suporte de contentores não modificados em contentores confidenciais.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: a5db93f096c73679c1b6b6ae464897db843f2e8b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706270"
---
# <a name="confidential-containers"></a>Contentores confidenciais

## <a name="overview"></a>Descrição Geral

Permitir que os desenvolvedores tragam uma **aplicação de estivador existente (nova ou existente)** e executá-la de forma segura no Serviço Azure Kubernetes (AKS) através do suporte confidencial dos nós de computação.

Os contentores confidenciais ajudam a proteger:

- integridade dos dados 
- confidencialidade dos dados
- integridade do código
- proteção de código de contentor através de encriptação
- garantias baseadas em hardware
- permitir executar aplicações existentes
- criar raiz de hardware de confiança
- remover administrador de hospedeiro, administrador Kubernetes, hipervisor do limite de confiança

Um Ambiente de Execução Fidedigna (TEE) baseado em hardware é um componente importante que é usado para fornecer fortes garantias através de medições de hardware e software a partir de componentes de base de computação fidedigna (TCB). As verificações destas medições ajudam na validação do cálculo esperado e verificam qualquer adulteração das aplicações do contentor.

Os contentores confidenciais suportam aplicações personalizadas desenvolvidas com **Python, Java, Node JS, etc. ou aplicações de contentores embalados como NGINX, Redis Cache, MemCache,** etc., para serem executadas sem modificação em AKS com nós de computação confidencial.

Os contentores confidenciais são o caminho mais rápido para a confidencialidade dos contentores e só exigirão a reembalagem das aplicações existentes do contentor estivador e não exigirão alterações ao código de aplicação. Os contentores confidenciais são aplicações de contentores de estivadores que são embaladas para funcionar num TEE. Alguns facilitadores confidenciais de contentores também oferecem encriptação de contentores que podem ajudar a proteger o código do recipiente durante o armazenamento e transporte e enquanto montado no hospedeiro. A encriptação do recipiente permite-lhe ir mais longe e proteger o código/modelo embalado no recipiente com a sua chave de desencriptação anexada ao TEE.

Abaixo está o processo para contentores confidenciais do desenvolvimento para a implantação ![ O contentor confidencial como processar.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Facilitadores confidenciais de contentores
Para executar um recipiente de estivador existente não modificado requer um software SGX para que as chamadas de aplicação possam usar um conjunto especial de instruções de CPU disponibilizado para baixar a área de superfície do anexado e não ter dependência do Sistema de Observação do Hóspede. Uma vez embrulhados com software de execução SGX, os contentores lançam-se automaticamente nos enclaves protegidos, removendo assim o So de Hóspede, o Os anfitrião ou o Hypervisor do limite de confiança. Esta execução isolada num nó (Máquina Virtual) com encriptação de dados de memória apoiada pelo hardware reduz as áreas globais de ataque de superfície e reduz as vulnerabilidades com camadas de sistema operativo ou hipervisor.

Os contentores confidenciais são totalmente suportados em AKS e ativados através de projetos Azure Partners e Open-Source Software (OSS). Os desenvolvedores podem escolher fornecedores de software com base nas funcionalidades, integração nos serviços Azure e suporte de ferramentas.

## <a name="partner-enablers"></a>Facilitadores de Parceiros
> [!NOTE]
> As soluções abaixo são oferecidas através da Azure Partners e podem incorrer em taxas de licenciamento. Verifique os termos do software do parceiro de forma independente. 

### <a name="anjuna"></a>Anjuna

[A Anjuna](https://www.anjuna.io/) fornece software de plataforma SGX que lhe permite executar contentores não modificados em AKS. Saiba mais sobre a funcionalidade e confira [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)as aplicações da amostra.

Começa com uma amostra redis Cache e aplicação personalizada Python [aqui](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)

![Processo de Anjuna](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanix

[A Fortanix](https://www.fortanix.com/) oferece aos desenvolvedores uma escolha de um portal e experiência baseada em CLI para levar as suas aplicações contentorizadas e encostá-las a recipientes confidenciais capazes de SGX sem necessidade de modificar ou recompiler a aplicação. A Fortanix proporciona a flexibilidade para executar e gerir o conjunto mais amplo de aplicações, incluindo aplicações existentes, novas aplicações nativas do enclave e aplicações pré-embaladas. Os utilizadores podem começar com UI ou [REST APIs](https://www.fortanix.com/api/em/) [do Gestor de Computação Confidencial](https://em.fortanix.com/) para criar recipientes confidenciais seguindo o guia Quick [Start](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) para o Serviço Azure Kubernetes.

![Processo de implantação de Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[O SCONE](https://scontain.com/index.html?lang=en) suporta políticas de segurança que podem gerar certificados, chaves e segredos, e garante que só são visíveis para atestar serviços de uma aplicação. Desta forma, os serviços de uma aplicação atestam-se automaticamente através do TLS - sem a necessidade de modificar as aplicações nem o TLS. Isto é explicado com a ajuda de uma simples aplicação Flask aqui: https://sconedocs.github.io/flask_demo/  

O SCONE pode converter a maioria dos binários existentes em aplicações que funcionam dentro de enclaves sem precisar de alterar a aplicação ou de recompiler essa aplicação. O SCONE também protege as línguas interpretadas como python **encriptando** tanto os ficheiros de dados como os ficheiros de código Python. Com a ajuda de uma política de segurança SCONE, pode-se proteger os ficheiros encriptados contra acessos, modificações e reversão não autorizados. Como "sconificar" uma aplicação python existente é explicado [aqui](https://sconedocs.github.io/sconify_image/)

![Fluxo de Scontain](./media/confidential-containers/scone-workflow.png)

As implementações de Scone em nós de computação confidencial com AKS são totalmente suportadas e integradas com outros serviços Azure. Começa com uma aplicação de amostra aqui https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>Facilitadores de OSS 
> [!NOTE]
> As soluções abaixo são oferecidas através de Open-Source Projects e não estão diretamente afiliadas à Azure Confidential Computing (ACC) ou à Microsoft.  

### <a name="graphene"></a>Grafeno

[Graphene](https://grapheneproject.io/) é um so-convidado leve, projetado para executar uma única aplicação Linux com requisitos mínimos de anfitrião. O Graphene pode executar aplicações num ambiente isolado com benefícios comparáveis à execução de um sistema operativo completo e tem um bom suporte de ferramentas para converter a aplicação existente de contentores estivadores em contentores blindados graphene (GSC).

Começa com uma aplicação de amostra e implantação em AKS [aqui](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) é uma biblioteca de vários processos de memória ,OS (LibOS) para Intel SGX. Permite que aplicações antigas funcionam em SGX com pouca ou nenhuma modificação ao código fonte. Occlum protege transparentemente a confidencialidade das cargas de trabalho dos utilizadores, permitindo uma fácil elevação e mudança para aplicações de estivadores existentes.

Occlum suporta implementações AKS. Siga as instruções de implementação com várias aplicações de amostra [aqui](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Demonstração de Contentores Confidenciais
Veja a demonstração confidencial de cuidados de saúde com recipientes confidenciais. A amostra está disponível [aqui.](/azure/architecture/example-scenario/confidential/healthcare-inference) 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Entrar em contato

Tem dúvidas com a sua implementação ou quer tornar-se um facilitador? Envie um e-mail para a equipa de produtos **acconaks@microsoft.com**

## <a name="reference-links"></a>Links de referência

[Microsoft Azure Attestation](../attestation/overview.md)

[Máquinas Virtuais DCsv2](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)