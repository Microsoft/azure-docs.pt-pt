---
title: Contentores conscientes do enclave em Azure
description: suporte para recipientes prontos para a aplicação do enclave no Serviço Azure Kubernetes (AKS)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 01f10d3ba83d23478a7113cf4935181ee34959e3
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125291"
---
# <a name="enclave-aware-containers"></a>Contentores conscientes do enclave

Um enclave é uma região de memória protegida que fornece confidencialidade para dados e execução de código. É um exemplo de um Ambiente de Execução Fidedigna (TEE) que é protegido por hardware. Os nós de computação confidencial na AKS utilizam [extensões de proteção de software intel (SGX)](https://software.intel.com/sgx) para criar ambientes enclaves isolados nos nós entre cada aplicação de contentores.

Tal como as máquinas virtuais Intel SGX, as aplicações de contentores que são desenvolvidas para funcionar em enclaves têm dois componentes:

- Um componente não-fidedquir o anfitrião e
- Um componente de confiança (chamado enclave).

![Arquitetura do contentor de consciência do enclave](./media/enclave-aware-containers/enclaveawarecontainer.png)

A arquitetura da aplicação de contentores conscientes do enclave dá-lhe mais controlo sobre a implementação, mantendo a pegada de código no enclave baixa. Minimizar o código que funciona no enclave ajuda a reduzir as áreas de superfície de ataque.   

## <a name="enablers"></a>Facilitadores

### <a name="open-enclave-sdk"></a>SDK Open Enclave
Open Enclave SDK é uma biblioteca de código aberto agnóstico de hardware para o desenvolvimento de aplicações C, C++ que utilizam Ambientes de Execução Fidedigna baseados em Hardware. A implementação atual fornece suporte para Intel SGX, bem como suporte de pré-visualização para [OP-TEE OS em Arm TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Começa com a aplicação de contentores open enclave [aqui](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
A Intel mantém o kit de desenvolvimento de software para a construção de aplicações SGX para cargas de carga de contentores Linux e Windows. Os contentores windows não são atualmente suportados por nós de computação confidencial da AKS.

Começar com aplicações baseadas em Intel SGX [aqui](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>Quadro confidencial do consórcio (CCF)
O Quadro do Consórcio Confidencial (CCF) é um quadro de código aberto para a construção de uma nova categoria de aplicações seguras, altamente disponíveis e performantes que se focam no cálculo e dados multi-partidários. A CCF pode permitir redes confidenciais de alta escala que satisfaçam os principais requisitos da empresa, fornecendo um meio para acelerar a produção e a adoção de tecnologia de blockchain baseada em consórcios e tecnologia de computação multipartidária.

Começa com a computação confidencial da Azure e a CCF [aqui](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Runtime de ONNX de Inferência Confidencial

O tempo de funcionamento ONNX baseado em enclave de código aberto estabelece um canal seguro entre o cliente e o serviço de inferência - garantindo que nem o pedido nem a resposta podem sair do enclave seguro. 

Esta solução permite-lhe trazer o modelo treinado ml existente e executá-lo de forma confidencial, proporcionando confiança entre o cliente e o servidor através de atestados e verificações. 

Começa com o elevador do modelo ML e muda para o tempo de execução ONNX [aqui](https://aka.ms/confidentialinference)

### <a name="edgeless-rt"></a>RT sem bordas

Edgeless RT é um projeto de código aberto que se baseia no Open Enclave SDK. Adiciona suporte para go e funcionalidades C++ adicionais. Começa com uma aplicação Go simples e confidencial utilizando o seu ambiente familiar vs Code [aqui.](https://github.com/edgelesssys/edgelessrt) Para aplicações edgeless em AKS siga as instruções [aqui](https://github.com/edgelesssys/edgelessrt/blob/master/docs/ERTAzureAKSDeployment.md)


## <a name="container-based-sample-implementations"></a>Implementações de amostras baseadas em contentores

[Amostras de azul para contentores conscientes do enclave em AKS](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

<!-- LINKS - external -->
[Atestado de Azure](../attestation/overview.md)


<!-- LINKS - internal -->
Máquina virtual [DC](/confidential-computing/virtual-machine-solutions) 
 [Contentores confidenciais](/confidential-computing/containercompute/confidential-containers)
