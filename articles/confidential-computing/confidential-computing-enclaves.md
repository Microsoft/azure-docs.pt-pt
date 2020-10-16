---
title: Máquinas virtuais de computação confidencial em Azure
description: Saiba mais sobre o hardware da Intel SGX para ativar as suas cargas de trabalho confidenciais de computação.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: a7e3ade66aa4ebf7584e03b75f85c48b44537d97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997843"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Visão geral da Azure confidential computing machines (VMs)


O Azure é o primeiro fornecedor de nuvem a oferecer computação confidencial num ambiente virtualizado. Desenvolvemos máquinas virtuais que funcionam como uma camada de abstração entre o hardware e a sua aplicação. Pode executar cargas de trabalho em escala e com opções de redundância e disponibilidade.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Máquinas Virtuais ativadas pela Intel SGX

Em máquinas virtuais de computação confidencial Azure, uma parte do hardware do CPU é reservada para uma parte do código e dados na sua aplicação. Esta parte restrita é o enclave. 

![Modelo VM](media/overview/hardware-backed-enclave.png)

A infraestrutura de computação confidencial Azure é atualmente composta por uma especialidade SKU de máquinas virtuais (VMs). Estes VMs funcionam em processadores Intel com extensão de guarda de software (Intel SGX). [O Intel SGX](https://intel.com/sgx) é o componente que permite uma maior proteção que iluminamos com computação confidencial. 

Hoje, a Azure oferece a [Série DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) construída sobre a tecnologia Intel SGX para criação de enclave baseado em hardware. Pode construir aplicações seguras baseadas em enclaves para executar na série de VMs DCsv2 para proteger os dados da sua aplicação e código em uso. 

[Leia mais](virtual-machine-solutions.md) sobre a implementação de máquinas virtuais confidenciais de computação Azure com enclaves de confiança baseados em hardware.

## <a name="enclaves"></a>Enclaves

Os enclaves são partes seguras do processador e memória do hardware. Não há como ver dados ou código dentro do enclave, mesmo com um depuração. Se as tentativas de código não fidedidas modificarem o conteúdo na memória do enclave, o ambiente fica desativado e as operações são negadas.

Fundamentalmente, pense num enclave como uma caixa segura. Coloca-se código e dados encriptados na caixa. Do lado de fora da caixa, não se vê nada. Você dá ao enclave uma chave para desencriptar os dados, os dados são então processados e encriptados novamente, antes de serem enviados para fora do enclave.

Cada enclave tem um tamanho definido de cache de página encriptada (EPC) que determina a quantidade de memória que cada enclave pode conter. As máquinas virtuais DCsv2 maiores têm mais memória EPC. Leia a página [de especificações do DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) para o máximo EPC por tamanho VM.



### <a name="developing-applications-to-run-inside-enclaves"></a>Desenvolvimento de aplicações para executar dentro de enclaves
Ao desenvolver aplicações, pode utilizar [ferramentas](application-development.md) de software para proteger partes do seu código e dados dentro do enclave. Estas ferramentas garantirão que o seu código e dados não podem ser vistos ou modificados por ninguém fora do ambiente de confiança. 

## <a name="next-steps"></a>Passos Seguintes
- [Leia as melhores práticas](virtual-machine-solutions.md) para implementar soluções em máquinas virtuais de computação confidencial Azure.
- [Implementar uma máquina virtual DCsv2-Series](quick-create-portal.md)
- [Desenvolver uma aplicação consciente do enclave](application-development.md) utilizando o OE SDK