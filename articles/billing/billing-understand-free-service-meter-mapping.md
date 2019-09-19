---
title: Mapeamento de Serviço a Medidor para a conta gratuita do Azure
description: Compreenda o mapeamento de serviço a medidor para serviços incluídos com a conta gratuita.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 8022c065d73aafc53d3dcb77e79c3e6320e0ce39
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490410"
---
# <a name="understand-free-service-to-meter-mapping"></a>Compreender o mapeamento gratuito de serviço a medidor

Todos os serviços do Azure emitem a utilização em relação aos medidores que o sistema de faturação do Azure utiliza para cobrar os serviços aos utilizadores. Para compreender melhor a utilização do serviço gratuito, vamos analisar o mapeamento de serviço a medidor dos serviços. Para saber como criar serviços gratuitos, veja [Criar serviços gratuitos com a conta gratuita do Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-eligible-services"></a>Mapeamento de serviço a medidor para serviços elegíveis

|    Serviço   | Nome do Medidor no portal do Azure | Nome do Medidor no ficheiro de utilização/API | ID do Medidor |
| ------------ | -------------------------- | -------------------------| -------- |
| VM do Linux B1S | Horas de Computação – VM Standard_B1 | Horas de Computação – Gratuito | 8260cba2-4437-47d1-a31e-2561cd370f50
| VM do Windows B1S | Horas de Computação – VM Standard_B1 (Windows) | Horas de Computação – Gratuito | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| VM B1S – Endereços IP Públicos  | Horas de Endereço IP – Endereços IP Públicos | Horas de Endereço IP – Gratuito | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Armazenamento (GB) – Cosmos DB | Armazenamento (GB) – Gratuito | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 Unidades de Pedido (Horas) – Cosmos DB | 100 Unidades de Pedido (Horas) – Gratuito | 5d638a6f-e221-41cf-ae3f-0f81d368cef6
| Armazenamento de Ficheiros | E/S Padrão - Ficheiros (GB) – Localmente Redundante | E/S Padrão - Ficheiros (GB) – Gratuito | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Armazenamento de Ficheiros | E/S Padrão - Unidades de Operação de Leitura de Ficheiros (por 10 mil) | E/S Padrão - Unidades de Operação de Leitura de Ficheiros (por 10 mil) – Gratuito | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Armazenamento de Ficheiros | E/S Padrão - Unidades de Operação de Escrita de Ficheiros (por 10 mil) | E/S Padrão - Unidades de Operação de Escrita de Ficheiros (por 10 mil) – Gratuito | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Armazenamento de Ficheiros | E/S Padrão - Unidades de Operação de Protocolo de Ficheiros (por 10 mil) | E/S Padrão - Unidades de Operação de Protocolo de Ficheiros (por 10 mil) – Gratuito | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Armazenamento de Ficheiros | E/S Padrão - Unidades de Operação de Lista de Ficheiros (por 10 mil) | E/S Padrão - Unidades de Operação de Lista de Ficheiros (por 10 mil) – Gratuito | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Armazenamento de Blobs de Blocos de Acesso Frequente | E/S Padrão - Operações de Leitura de Blobs de Blocos de Acesso Frequente (por 10 mil) | E/S Padrão - Operações de Leitura de Blobs de Blocos de Acesso Frequente (por 10 mil) – Gratuito |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Armazenamento de Blobs de Blocos de Acesso Frequente | E/S Padrão - Blob de Blocos de Acesso Frequente (GB) – Localmente Redundante | E/S Padrão - Blob de Blocos de Acesso Frequente (GB) – Gratuito | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Armazenamento de Blobs de Blocos de Acesso Frequente | E/S Padrão - Operações de Escrita de Blobs de Blocos de Acesso Frequente (por 10 mil) | E/S Padrão - Operações de Escrita de Blobs de Blocos de Acesso Frequente (por 10 mil) – Gratuito | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Armazenamento de Blobs de Blocos de Acesso Frequente  | E/S Padrão - Operações de Lista/Escrita de Blobs de Blocos de Acesso Frequente (por 10 mil) | E/S Padrão - Operações de Lista/Escrita de Blobs de Blocos de Acesso Frequente (por 10 mil) – Gratuito | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disco Gerido <sup>1</sup>  | Disco Gerido Standard/Instantâneos (GB) – Localmente Redundante | Disco Gerido Standard/Instantâneos (GB) – Gratuito | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disco Gerido <sup>1</sup>  | Operações de Discos Geridos Standard (por 10 mil) | Operações de Discos Geridos Standard (por 10 mil) – Gratuito | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disco Gerido <sup>1</sup>  | Armazenamento Premium – Blob de Páginas/P6 (Unidades) – Localmente Redundante | Armazenamento Premium – Blob de Páginas/P6 (Unidades) – Gratuito | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Dias de Base de Dados Standard S0 – Base de Dados SQL | Dias de Base de Dados Standard S0 – Gratuito | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Partilhado – Largura de banda<sup>2</sup> | Transferências de Dados de Saída (GB) | Transferências de Dados de Saída (GB) – Gratuito | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

<sup>1</sup> Se criar uma máquina virtual do Windows e escolher um disco gerido, consumirá o medidor de disco gerido como parte da máquina virtual.

<sup>2</sup> Os medidores partilhados podem ser consumidos através de vários serviços. Por exemplo, tanto as Máquinas virtuais como o Armazenamento emitem a utilização em relação ao medidor de Transferência de Dados de Saída (GB).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- [Atualizar a subscrição](billing-upgrade-azure-subscription.md)
