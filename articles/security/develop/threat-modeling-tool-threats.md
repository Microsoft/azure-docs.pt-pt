---
title: Ameaças-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: A página categoria de ameaça para a Microsoft Threat Modeling Tool, que contém categorias para todas as ameaças geradas expostas.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727836"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Ameaças de Microsoft Threat Modeling Tool

O Threat Modeling Tool é um dos principais elementos do SDL (Microsoft Security Development Lifecycle). Ele permite que os arquitetos de software identifiquem e reduzam possíveis problemas de segurança no início, quando são relativamente fáceis e econômicos de resolver. Como resultado, ele reduz consideravelmente o custo total de desenvolvimento. Além disso, projetamos a ferramenta com especialistas não relacionados à segurança em mente, tornando a modelagem de ameaças mais fácil para todos os desenvolvedores, fornecendo orientação clara sobre como criar e analisar modelos de ameaças.

> Visite o **[Threat Modeling Tool](threat-modeling-tool.md)** para começar hoje mesmo!

O Threat Modeling Tool ajuda a responder a determinadas perguntas, como as seguintes:

* Como um invasor pode alterar os dados de autenticação?
* Qual é o impacto se um invasor puder ler os dados de perfil do usuário?
* O que acontece se o acesso for negado ao banco de dados de perfil do usuário?

## <a name="stride-model"></a>Modelo STRIDE

Para melhor ajudá-lo a formular esses tipos de perguntas pontuais, a Microsoft usa o modelo STRIDE, que categoriza diferentes tipos de ameaças e simplifica as conversas gerais de segurança.

| Category | Descrição |
| -------- | ----------- |
| **Falsificação** | Envolve acesso ilegal e, em seguida, o uso de informações de autenticação de outro usuário, como nome de usuário e senha |
| **Violação** | Envolve a modificação mal-intencionada de dados. Os exemplos incluem alterações não autorizadas feitas em dados persistentes, como os que são mantidos em um banco de dados, e a alteração deles, conforme eles fluem entre dois computadores em uma rede aberta, como a Internet |
| **Repúdio** | Associados a usuários que negam a execução de uma ação sem que outras partes tenham qualquer maneira de provar de outra forma — por exemplo, um usuário executa uma operação ilegal em um sistema que não tem a capacidade de rastrear as operações proibidas. Não repúdio refere-se à capacidade de um sistema de combater ameaças de repúdio. Por exemplo, um usuário que adquire um item pode ter que assinar o item após o recebimento. O fornecedor pode usar o recibo assinado como evidência de que o usuário recebeu o pacote |
| **Divulgação de informações** | Envolve a exposição de informações a indivíduos que não devem ter acesso a ela — por exemplo, a capacidade de os usuários de ler um arquivo ao qual eles não receberam acesso ou a capacidade de um invasor de ler dados em trânsito entre dois computadores |
| **Negação de serviço** | Ataques de DoS (negação de serviço) negam serviço a usuários válidos — por exemplo, tornando um servidor Web temporariamente indisponível ou inutilizável. Você deve se proteger contra determinados tipos de ameaças DoS, simplesmente para melhorar a disponibilidade e a confiabilidade do sistema |
| **Elevação de privilégio** | Um usuário sem privilégios obtém acesso privilegiado e, portanto, tem acesso suficiente para comprometer ou destruir todo o sistema. A elevação de ameaças de privilégio inclui as situações em que um invasor penetra efetivamente em todas as defesas do sistema e se torna parte do sistema confiável em si, uma situação perigosa de fato |

## <a name="next-steps"></a>Passos seguintes

Continue a **[Threat Modeling Tool atenuações](threat-modeling-tool-mitigations.md)** para aprender as diferentes maneiras de mitigar essas ameaças com o Azure.