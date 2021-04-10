---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101751109"
---
- Atualmente esta funcionalidade é suportada apenas para SSDs premium.
- Tem de fazer negócios com o seu VM ou separar o disco de um VM em execução antes de poder alterar o nível do disco.
- Os níveis de desempenho P60, P70 e P80 só podem ser utilizados por discos superiores a 4.096 GiB.
- O nível de desempenho de um disco só pode ser desclassificado uma vez a cada 12 horas.

## <a name="change-performance-tier-without-downtime-preview"></a>Alterar o nível de desempenho sem tempo de inatividade (pré-visualização)

Normalmente, teria de negociar o seu VM ou desvincular o seu disco para alterar o seu nível de desempenho. Mas se ativar esta funcionalidade de pré-visualização, não precisa de negociar o seu VM ou desvincular o disco para alterar o nível. Pode inscrever-se para a pré-visualização [aqui.](https://aka.ms/liveperftiersignup)

A pré-visualização tem as seguintes limitações:
- Disponível apenas na região eastUS2EUAP.
- Não disponível para discos partilhados
- Deve utilizar os modelos do Gestor de Recursos Azure com a `2020-12-01` API para alterar os níveis de desempenho sem tempo de inatividade.