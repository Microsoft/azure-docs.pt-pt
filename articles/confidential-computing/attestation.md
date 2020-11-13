---
title: Atestando enclaves em Azure
description: Saiba como pode usar o atestado para verificar se o seu ambiente confidencial de confiança na computação é seguro
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 86856d1f66b5b7d723c907c17d7179ffcd2b82ab
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565675"
---
# <a name="attesting-sgx-enclaves"></a>Atestar enclaves SGX

A computação confidencial no Azure oferece máquinas virtuais baseadas em Intel SGX que podem isolar uma parte do seu código ou dados. Ao trabalhar com estes [enclaves,](confidential-computing-enclaves.md)você vai querer obter a verificação e validação de que o seu ambiente de confiança é seguro. Esta verificação é o processo de atestado. 

## <a name="overview"></a>Descrição geral 

O Attestation permite que uma parte dependente tenha uma maior confiança de que o seu software está (1) a funcionar num enclave e (2) que o enclave está atualizado e seguro. Por exemplo, um enclave pede ao hardware subjacente para gerar uma credencial que inclua a prova de que o enclave existe na plataforma. O relatório pode então ser entregue a um segundo enclave que verifica que o relatório foi gerado na mesma plataforma.

![atestam código no enclave](media/attestation/attestation.png)



O atestado deve ser implementado utilizando um serviço de atestado seguro que seja compatível com o software do sistema e o silício. Alguns exemplos de serviços que pode usar são

- [Microsoft Azure Attestation (pré-visualização)](../attestation/overview.md) ou
- [Serviços de atestado e provisionamento da Intel](https://software.intel.com/sgx/attestation-services)


que são compatíveis com a infraestrutura intel SGX de computação confidencial da Azure. 

## <a name="next-steps"></a>Passos seguintes
Experimente as amostras do [Microsoft Azure Attestation para aplicações conscientes do enclave.](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)