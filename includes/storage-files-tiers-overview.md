---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: d3ba7baf79ee972ed7289a1aab93484108da70cf
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724247"
---
A Azure Files oferece quatro níveis diferentes de armazenamento, premium, transação otimizado, quente e fresco para permitir-lhe adaptar as suas ações aos requisitos de desempenho e preço do seu cenário:

- **Premium**: As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs) e proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações de IO, para cargas de trabalho intensivas de IO. As ações de ficheiros premium são adequadas para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. As ações de ficheiros premium podem ser utilizadas tanto com os protocolos Do Bloco de Mensagens do Servidor (SMB) como do Sistema de Ficheiros de Rede (NFS).
- **Transação otimizada**: As ações de ficheiros otimizadas de transações permitem transações pesadas de cargas de trabalho que não necessitam da latência oferecida pelas ações de ficheiros premium. As ações de ficheiros otimizadas de transações são oferecidas no hardware de armazenamento padrão apoiado por discos rígidos (HDDs). A transação otimizada tem sido historicamente chamada de "padrão", no entanto isto refere-se ao tipo de mídia de armazenamento em vez do próprio nível (o quente e o frio também são níveis "standard", porque estão no hardware de armazenamento padrão).
- **Hot**: As ações de ficheiros quentes oferecem armazenamento otimizado para cenários de partilha de ficheiros para fins gerais, tais como ações da equipa. As ações de ficheiros quentes são oferecidas no hardware de armazenamento padrão apoiado por HDDs.
- **Cool**: As ações de ficheiros cool oferecem armazenamento eficiente em termos de custos otimizados para cenários de armazenamento de arquivo online. As ações de ficheiros cool são oferecidas no hardware de armazenamento padrão apoiado por HDDs.

As ações de ficheiros premium são implantadas no tipo **de conta de armazenamento FileStorage** e só estão disponíveis num modelo de faturação provisionado. Para obter mais informações sobre o modelo de faturação previsto para ações de ficheiros premium, consulte [a compreensão das ações de ficheiros premium](../articles/storage/files/understanding-billing.md#provisioned-billing). As ações de ficheiros standard, incluindo ações de ficheiros otimizadas, quentes e cool, são implantadas no tipo de conta de armazenamento versão **2 (GPv2) de finalidade geral,** e estão disponíveis através de pagamento à medida que vai fazendo a faturação. As ações de ficheiros quentes e frescos estão disponíveis em todas as regiões do Governo Azure Public e Azure. As ações de ficheiros otimizadas de transações estão disponíveis em todas as regiões do Azure, incluindo as regiões da Azure China e Azure Germany.

Ao selecionar um nível de armazenamento para a sua carga de trabalho, considere os seus requisitos de desempenho e utilização. Se a sua carga de trabalho requer latência de um dígito, ou estiver a utilizar os meios de armazenamento SSD no local, o nível premium é provavelmente o melhor. Se a baixa latência não for tão preocupante, por exemplo, com ações de equipas montadas no local a partir do Azure ou em cache no local usando o Azure File Sync, o armazenamento padrão pode ser melhor do ponto de vista de custos.

Uma vez criada uma parte de ficheiro numa conta de armazenamento, não pode movê-la para níveis exclusivos para diferentes tipos de conta de armazenamento. Por exemplo, para mover uma parte de ficheiro otimizada de transação para o nível premium, tem de criar uma nova quota de ficheiro numa conta de armazenamento de FileStorage e copiar os dados da sua parte original para uma nova partilha de ficheiros na conta FileStorage. Recomendamos a utilização do AzCopy para copiar dados entre as ações de ficheiros Azure, mas também pode utilizar ferramentas como `robocopy` no Windows ou para `rsync` macOS e Linux. 

As ações de ficheiros implantadas nas contas de armazenamento do GPv2 podem ser movidas entre os níveis padrão (transação otimizada, quente e fria) sem criar uma nova conta de armazenamento e dados migratórios, mas incorrerá nos custos de transação quando alterar o seu nível. Quando você move uma ação de um nível mais quente para um nível mais frio, você vai incorrer na taxa de transação de escrita do nível mais frio para cada ficheiro na ação. Mover uma parte de ficheiro de um nível mais frio para um nível mais quente incorrerá na taxa de transação de leitura do nível legal para cada ficheiro da ação.

Consulte [a faturação dos Ficheiros Azure](../articles/storage/files/understanding-billing.md) para obter mais informações.