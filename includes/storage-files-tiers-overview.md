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
ms.openlocfilehash: 62c2193a0045cc2ee35930e87b360446e94ba861
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864716"
---
A Azure Files oferece quatro níveis diferentes de armazenamento, premium, transação otimizado, quente e fresco para permitir-lhe adaptar as suas ações aos requisitos de desempenho e preço do seu cenário:

- **Premium**: As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs) e são implantadas no tipo de **conta de armazenamento de filestorage.** As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações de IO, para cargas de trabalho intensivas em IO. Isto torna-os adequados para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. 
- **Transação otimizada**: As ações de ficheiros otimizadas de transações permitem transações pesadas de cargas de trabalho que não necessitam da latência oferecida pelas ações de ficheiros premium. As ações de ficheiros otimizadas de transações são oferecidas no hardware de armazenamento padrão apoiado por discos rígidos (HDDs) e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.** Este nível de armazenamento tem sido historicamente chamado de "padrão", no entanto isto refere-se ao tipo de mídia de armazenamento em vez do próprio nível (o quente e o frio também são níveis "standard", porque estão em hardware de armazenamento padrão).
- **Hot**: As ações de ficheiros quentes oferecem armazenamento otimizado para cenários gerais de partilha de ficheiros, tais como ações da equipa e Azure File Sync. As ações de ficheiros quentes são oferecidas no hardware de armazenamento padrão apoiado por HDDs e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.**
- **Cool**: As ações de ficheiros cool oferecem armazenamento eficiente em termos de custos otimizados para cenários de armazenamento de arquivo online. O Azure File Sync também pode ser um bom ajuste para cargas de trabalho mais baixas. As ações de ficheiros cool são oferecidas no hardware de armazenamento padrão apoiado por HDDs e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.**

As ações de ficheiros premium só estão disponíveis num modelo de faturação provisionado. Para obter mais informações sobre o modelo de faturação previsto para ações de ficheiros premium, consulte [a compreensão das ações de ficheiros premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). As ações de ficheiros standard, incluindo ações de ficheiros otimizadas, quentes e legais, estão disponíveis num pagamento à medida que forem modelo.

As ações de ficheiros quentes e frescas estão atualmente disponíveis no subconjunto seguinte das regiões públicas (as ações de ficheiros otimizadas de transações estão disponíveis em todas as regiões do Azure):

- Austrália Central
- Austrália Central 2
- Leste da Austrália
- Austrália Sudeste
- França Central
- Sul de França
- Alemanha Norte (público)
- Alemanha Central Ocidental (público)
- Índia Central
- Índia do Sul
- Oeste da Índia
- Leste do Japão
- Oeste do Japão
- Coreia do Sul Central
- Sul da Coreia do Sul
- Leste da Noruega
- Oeste da Noruega
- África do Sul Norte
- África do Sul
- Centro dos Emirados Árabes Unidos
- Uae Norte
- Sul do Reino Unido
- Oeste do Reino Unido

Para implementar uma partilha de ficheiros quente ou legal, consulte [Criar uma partilha de ficheiros quente ou legal](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 