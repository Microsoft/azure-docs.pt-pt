---
title: Ferramentas de desenvolvimento de computação confidencial Azure
description: Utilize ferramentas e bibliotecas para desenvolver aplicações para computação confidencial
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551390"
---
# <a name="application-development-on-intel-sgx"></a>Desenvolvimento de aplicações na Intel SGX 


A infraestrutura de computação confidencial requer ferramentas e software específicos. Esta página discute especificamente conceitos relacionados com o desenvolvimento de aplicações para máquinas virtuais de computação confidencial Azure que estão a decorrer no Intel SGX. Antes de ler esta página, [leia a introdução de máquinas virtuais e enclaves Intel SGX.](confidential-computing-enclaves.md) 

Para aproveitar o poder dos enclaves e ambientes isolados, você precisará usar ferramentas que suportem a computação confidencial. Existem várias ferramentas que apoiam o desenvolvimento de aplicações do enclave. Por exemplo, pode utilizar estes quadros de código aberto: 

- [O Kit de Desenvolvimento de Software Open Enclave (OE SDK)](#oe-sdk)
- [O Quadro Confidencial do Consórcio (CCF)](#ccf)

## <a name="overview"></a>Descrição Geral

Uma aplicação construída com enclaves é dividida de duas maneiras:

1. Um componente "não-fidedquirso" (o hospedeiro)
1. Um componente "confiável" (o enclave)


![Programação de aplicações](media/application-development/oe-sdk.png)


**O hospedeiro** é onde a sua aplicação do enclave está em cima e é um ambiente não-fidedquirso. O código do enclave implantado no hospedeiro não pode ser acedido pelo hospedeiro. 

**O enclave** é onde o código de aplicação e os seus dados/memória em cache são executados. Devem ocorrer computações seguras nos enclaves para garantir segredos e dados sensíveis, permanecer protegidos. 


Durante o design da aplicação, é importante identificar e determinar que parte da aplicação precisa de ser executada nos enclaves. O código que escolhe colocar no componente de confiança está isolado do resto da sua aplicação. Uma vez que o enclave é inicializado e o código é carregado para a memória, esse código não pode ser lido ou alterado a partir dos componentes não fidedidos. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Kit aberto de desenvolvimento de software enclave (OE SDK) <a id="oe-sdk"></a>

Utilize uma biblioteca ou estrutura suportada pelo seu fornecedor se quiser escrever código que funciona num enclave. O [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) é um SDK de código aberto que permite a abstração em diferentes hardware confidenciais computação. 

O OE SDK é construído para ser uma única camada de abstração sobre qualquer hardware em qualquer CSP. O OE SDK pode ser usado em cima de máquinas virtuais de computação confidencial Azure para criar e executar aplicações em cima de enclaves.

## <a name="confidential-consortium-framework-ccf"></a>Quadro confidencial do consórcio (CCF) <a id="ccf"></a>

O [CCF](https://github.com/Microsoft/CCF) é uma rede distribuída de nós, cada um com os seus próprios enclaves. A rede de nó de confiança permite-lhe executar um livro de contabilidade distribuído. O livro-razão fornece componentes seguros e fiáveis para o protocolo a utilizar. 

![Nónões CCF](media/application-development/ccf.png)

Este quadro de código aberto permite uma elevada confidencialidade e uma governação do consórcio para blockchain. Com cada nó usando TEEs, pode garantir um consenso seguro e processamento de transações.


## <a name="next-steps"></a>Passos seguintes 
- [Implementar uma máquina virtual de DCsv2-Series de computação confidencial](quick-create-portal.md)
- [Descarregue e instale o OE SDK e comece a desenvolver aplicações](https://github.com/openenclave/openenclave)