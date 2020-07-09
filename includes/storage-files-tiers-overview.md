---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77597863"
---
A Azure Files oferece dois diferentes níveis de armazenamento, premium e standard, para lhe permitir adaptar as suas ações aos requisitos de desempenho e preço do seu cenário:

- **Ações de ficheiro premium**: As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs) e são implantadas no tipo **de conta de armazenamento fileStorage.** As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações de IO, para cargas de trabalho intensivas em IO. Isto torna-os adequados para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. As ações de ficheiros premium só estão disponíveis num modelo de faturação provisionado. Para obter mais informações sobre o modelo de faturação previsto para ações de ficheiros premium, consulte [a compreensão das ações de ficheiros premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Ações de ficheiros padrão**: As ações de ficheiros standard são apoiadas por discos rígidos (HDDs) e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.** As ações de ficheiros standard proporcionam um desempenho fiável para cargas de trabalho io menos sensíveis à variabilidade do desempenho, tais como ações de ficheiros de uso geral e ambientes dev/teste. As ações de ficheiros padrão só estão disponíveis num modelo de faturação paga.