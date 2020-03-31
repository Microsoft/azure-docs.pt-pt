---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597863"
---
O Azure Files oferece dois diferentes níveis de armazenamento, premium e standard, para permitir adaptar as suas ações aos requisitos de desempenho e preço do seu cenário:

- Ações de **ficheiropremium**: As ações de ficheiropremium são apoiadas por unidades de estado sólido (SSDs) e são implantadas no tipo de conta de **armazenamento de FileStorage.** As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações io, para cargas de trabalho intensivas em OI. Isto torna-os adequados para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. As ações de ficheiropremium só estão disponíveis num modelo de faturação provisionado. Para obter mais informações sobre o modelo de faturação previsto para ações de ficheiros premium, consulte o [fornecimento de compreensão para ações](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)de ficheiros premium .
- **Ações**de ficheiro padrão : As ações de ficheiro sem fim são apoiadas por discos rígidos (HDDs) e são implantadas no tipo de conta de armazenamento da **versão geral 2 (GPv2).** As ações de ficheiros padrão proporcionam um desempenho fiável para cargas de trabalho IO menos sensíveis à variabilidade do desempenho, tais como partilhas de ficheiros de uso geral e ambientes de dev/teste. As ações padrão de ficheiro saem apenas disponíveis num modelo de faturação pay-as-you-go.