---
title: Ameaças - Microsoft Threat Modeling Tool - Azure [ Microsoft Docs
description: Página da categoria de ameaça para a Ferramenta de Modelação de Ameaças da Microsoft, contendo categorias para todas as ameaças geradas expostas.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68727836"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Ameaças de ferramentas de modelação de ameaças da Microsoft Threat

A Ferramenta de Modelação de Ameaças é um elemento central do Ciclo de Vida de Desenvolvimento de Segurança da Microsoft (SDL). Permite que os arquitetos de software identifiquem e mitiem potenciais problemas de segurança mais cedo, quando são relativamente fáceis e rentáveis para resolver. Como resultado, reduz consideravelmente o custo total do desenvolvimento. Além disso, desenhamos a ferramenta com especialistas não-de-segurança em mente, tornando a modelação de ameaças mais fácil para todos os desenvolvedores, fornecendo orientações claras sobre a criação e análise de modelos de ameaças.

> Visite a Ferramenta de **[Modelação de Ameaças](threat-modeling-tool.md)** para começar hoje!

A Ferramenta de Modelação de Ameaças ajuda-o a responder a determinadas questões, como as abaixo:

* Como pode um intruso alterar os dados de autenticação?
* Qual é o impacto se um intruso pode ler os dados do perfil do utilizador?
* O que acontece se o acesso for negado à base de dados de perfil do utilizador?

## <a name="stride-model"></a>Modelo STRIDE

Para melhor ajudá-lo a formular este tipo de questões pontiagudas, a Microsoft usa o modelo STRIDE, que categoriza diferentes tipos de ameaças e simplifica as conversas de segurança globais.

| Categoria | Descrição |
| -------- | ----------- |
| **Spoofing** | Envolve aceder ilegalmente e, em seguida, usar informações de autenticação de outro utilizador, como o nome de utilizador e a palavra-passe |
| **Adulteração** | Envolve a modificação maliciosa de dados. Exemplos incluem alterações não autorizadas feitas a dados persistentes, como os mantidos numa base de dados, e a alteração de dados à medida que estes fluem entre dois computadores através de uma rede aberta, como a Internet |
| **Repúdio** | Associado a utilizadores que negam realizar uma ação sem que outras partes tenham alguma forma de provar o contrário — por exemplo, um utilizador realiza uma operação ilegal num sistema que carece da capacidade de rastrear as operações proibidas. O não-repúdio refere-se à capacidade de um sistema de combater ameaças de repúdio. Por exemplo, um utilizador que adquira um artigo pode ter de assinar para o artigo no momento do recibo. O vendedor pode então usar o recibo assinado como prova de que o utilizador recebeu o pacote |
| **Divulgação de Informação** | Envolve a exposição de informação a indivíduos que não devem ter acesso a ela — por exemplo, a capacidade dos utilizadores de lerem um ficheiro a que não lhes foi concedido acesso, ou a capacidade de um intruso ler dados em trânsito entre dois computadores |
| **Negação de Serviço** | Os ataques de negação de serviço (DoS) negam o serviço a utilizadores válidos — por exemplo, tornando um servidor Web temporariamente indisponível ou inutilizável. Deve proteger-se contra certos tipos de ameaças do DoS simplesmente para melhorar a disponibilidade e a fiabilidade do sistema |
| **Elevação do Privilégio** | Um utilizador desfavorecido obtém acesso privilegiado e, assim, tem acesso suficiente para comprometer ou destruir todo o sistema. A elevação das ameaças de privilégios inclui as situações em que um intruso efetivamente penetrou em todas as defesas do sistema e tornou-se parte do próprio sistema de confiança, uma situação perigosa, na verdade, uma situação perigosa. |

## <a name="next-steps"></a>Passos seguintes

Proceda a Mitigações de Ferramentas de **[Modelação de Ameaças](threat-modeling-tool-mitigations.md)** para aprender as diferentes formas de mitigar estas ameaças com o Azure.