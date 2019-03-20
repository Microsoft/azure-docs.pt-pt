---
title: Serviço para o mapeamento de medidores para a conta gratuita do Azure | Documentos da Microsoft
description: Compreenda o serviço para mapeamento de medidores de serviços incluídos com a conta gratuita.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57889816"
---
# <a name="understand-free-service-to-meter-mapping"></a>Compreender o serviço gratuito para o mapeamento de medidor

Cada serviço do Azure emite utilização relativamente indicadores, o que o sistema de faturação do Azure utiliza a cobrar aos utilizadores para serviços. Para compreender melhor a utilização de serviços gratuitos, vamos analisar o serviço de mapeamento de medidores para estes serviços. Para saber como criar serviços gratuitos, veja [criar serviços gratuitos conta gratuita do Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Serviço para o medidor de mapeamento gratuitamente serviços elegíveis de conta 

|    Serviço   | Nome do medidor no portal do Azure | Nome do medidor no ficheiro/API de utilização | ID do Medidor |
| ------------ | -------------------------- | -------------------------| -------- |
| VM do Linux B1S | Horas de computação – Standard_B1 VM | Horas de computação - gratuitas | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | Horas de computação – Standard_B1 VM (Windows) | Horas de computação - gratuitas | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1s para VM - endereços IP públicos  | Horas de endereço IP - endereços IP públicos | Horas de endereço IP - gratuitas | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Armazenamento (GB) - Cosmos DB | Armazenamento (GB) - gratuito | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 unidades de pedido (horas) - Cosmos DB | 100 unidades de pedido (horas) - gratuito | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| Armazenamento de Ficheiros | E/s padrão - ficheiros (GB) - localmente redundante | E/s padrão - ficheiros (GB) - gratuito | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Leitura de Ficheiros (por 10 mil) | E/s padrão - leitura unidades de operação ficheiros (em 10 000s) - gratuito | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Escrita de Ficheiros (por 10 mil) | E/s padrão - ficheiro unidades de operação de escrita (em 10 000s) - gratuito | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Protocolo de Ficheiros (por 10 mil) | E/s padrão - ficheiro unidades de operação de protocolo (em 10 000s) - gratuito | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Lista de Ficheiros (por 10 mil) | E/s padrão - ficheiro unidades de operação de lista (em 10 000s) - gratuito | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Armazenamento de BLOBs de blocos de acesso frequente | E/s padrão - operações de leitura de BLOBs de blocos de acesso frequente (em 10 000s) | E/s padrão - operações (em 10 000s) - gratuitas de leitura de BLOBs de blocos de acesso frequente |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Armazenamento de BLOBs de blocos de acesso frequente | E/s padrão - Blob de blocos de acesso frequente (GB) - localmente redundante | E/s padrão - Blob de blocos de acesso frequente (GB) - gratuito | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Armazenamento de BLOBs de blocos de acesso frequente | E/s padrão - operações de escrita de BLOBs de blocos de acesso frequente (em 10 000s) | E/s padrão - blocos de acesso frequente operações de escrita de BLOBs (em 10 000s) - gratuito | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Armazenamento de BLOBs de blocos de acesso frequente  | E/s padrão - operações de escrita/lista de BLOBs de blocos de acesso frequente (em 10 000s) | E/s padrão - blocos de acesso frequente operações de escrita/lista de BLOBs (em 10 000s) - gratuito | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Disco gerido *  | Disco gerido Standard/instantâneos (GB) - localmente redundante | Disco gerido Standard/instantâneos (GB) - gratuito | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Disco gerido *  | Operações de disco gerido Standard (em 10 000s) | Operações de disco gerido Standard (em 10 000s) - gratuito | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Disco gerido *  | Armazenamento Premium - Blob de páginas/P6 (unidades) - localmente redundante | Armazenamento Premium - Blob de páginas/P6 (unidades) - gratuito | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Dias de base de dados Standard S0 - base de dados SQL | Dias de base de dados Standard S0 - gratuitos | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Partilhado - largura de banda * * | Transferências de Dados de Saída (GB) | Transferência de dados de saída (GB) - gratuito | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Se criar uma máquina virtual do Windows e selecione o disco gerido, irá consumir o medidor de disco gerido como parte da máquina virtual.

\** Medidores partilhados podem ser consumidos por meio de vários serviços. Por exemplo, máquinas virtuais e armazenamento emitem utilização contra medidor Out(GB) de transferência de dados.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
