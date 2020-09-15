---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 034f5c487750600910cb32f73bcc6ea243b3ec6f
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563460"
---
A Azure Files oferece quatro níveis diferentes de armazenamento, premium, transação otimizado, quente e fresco para permitir-lhe adaptar as suas ações aos requisitos de desempenho e preço do seu cenário:

- **Premium**: As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs) e são implantadas no tipo de **conta de armazenamento de filestorage.** As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações de IO, para cargas de trabalho intensivas em IO. As ações de ficheiros premium são adequadas para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. As ações de ficheiros premium podem ser utilizadas tanto com os protocolos Do Bloco de Mensagens do Servidor (SMB) como do Sistema de Ficheiros de Rede (NFS).
- **Transação otimizada**: As ações de ficheiros otimizadas de transações permitem transações pesadas de cargas de trabalho que não necessitam da latência oferecida pelas ações de ficheiros premium. As ações de ficheiros otimizadas de transações são oferecidas no hardware de armazenamento padrão apoiado por discos rígidos (HDDs) e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.** A transação otimizada tem sido historicamente chamada de "padrão", no entanto isto refere-se ao tipo de mídia de armazenamento em vez do próprio nível (o quente e o frio também são níveis "standard", porque estão no hardware de armazenamento padrão).
- **Hot**: As ações de ficheiros quentes oferecem armazenamento otimizado para cenários gerais de partilha de ficheiros, tais como ações da equipa e Azure File Sync. As ações de ficheiros quentes são oferecidas no hardware de armazenamento padrão apoiado por HDDs e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.**
- **Cool**: As ações de ficheiros cool oferecem armazenamento eficiente em termos de custos otimizados para cenários de armazenamento de arquivo online. O Azure File Sync também pode ser um bom ajuste para cargas de trabalho mais baixas. As ações de ficheiros cool são oferecidas no hardware de armazenamento padrão apoiado por HDDs e são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral.**

As ações de ficheiros premium só estão disponíveis num modelo de faturação provisionado. Para obter mais informações sobre o modelo de faturação previsto para ações de ficheiros premium, consulte [a compreensão das ações de ficheiros premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares). As ações de ficheiros standard, incluindo ações de ficheiros otimizadas, quentes e legais, estão disponíveis através do pagamento à medida que vai fazendo a faturação.

As ações de ficheiros quentes e frescos estão disponíveis em todas as regiões públicas do Azure. As ações de ficheiros otimizadas de transações estão disponíveis em todas as regiões do Azure, incluindo regiões de nuvem nacionais.

Para implementar uma partilha de ficheiros quente ou legal, consulte [Criar uma partilha de ficheiros quente ou legal](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share). 