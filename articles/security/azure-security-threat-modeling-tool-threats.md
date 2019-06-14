---
title: Ameaças - ferramenta de modelagem de ameaças Microsoft - Azure | Documentos da Microsoft
description: Página de categoria de ameaça para a ferramenta de modelagem de ameaças da Microsoft, que contém as categorias para todos os expostos gerado ameaças.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611564"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Ameaças de ferramenta de modelagem de ameaças na Microsoft

A ferramenta de modelagem de ameaças é um elemento central do Microsoft Security Development Lifecycle (SDL). Ele permite que arquitetos de software identificar e mitigar potenciais problemas de segurança no início, quando são relativamente fácil e económica para resolver. Como resultado, reduz consideravelmente o custo total de desenvolvimento. Além disso, projetamos a ferramenta de com não-especialistas em segurança em mente, facilitando a Modelagem de ameaças para todos os desenvolvedores ao fornecer uma orientação clara sobre como criar e analisar os modelos de ameaças.

> Visite o **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** para começar já hoje!

A ferramenta de modelagem de ameaças ajuda a responder às perguntas, tais como as apresentadas abaixo:

* Como é que um invasor pode alterar os dados de autenticação?
* O que é o impacto, se um invasor puder ler os dados de perfil do usuário?
* O que acontece se o acesso é negado para a base de dados de perfil do usuário?

## <a name="stride-model"></a>Modelo STRIDE

A melhor ajuda formular esses tipos de perguntas apontados, a Microsoft utiliza o modelo STRIDE, que categoriza os diferentes tipos de ameaças e simplifica as conversas de segurança geral.

| Category | Descrição |
| -------- | ----------- |
| **Spoofing** | Envolve mudanças aceder e, em seguida, utilizar informações de autenticação de outro utilizador, como o nome de utilizador e palavra-passe |
| **Adulteração** | Envolve a modificação maliciosa de dados. Os exemplos incluem alterações não autorizadas efetuadas aos dados persistentes, como que mantidos num banco de dados e a alteração de dados à medida que fluem entre dois computadores ao longo de uma rede aberta, como a Internet |
| **Rejeição** | Associados a utilizadores que se recusar a executar uma ação sem outras partes ter qualquer forma de provar o contrário — por exemplo, um utilizador realiza uma operação ilegal num sistema que não possui a capacidade para rastrear as operações proibidas. Não rejeição refere-se para a capacidade de um sistema a ameaças de repúdio de contador. Por exemplo, um utilizador a quem compra um item poderá ter de iniciar sessão para o item após a receção. O fornecedor, em seguida, pode utilizar o recebimento assinado como prova que o utilizador recebeu o pacote |
| **Divulgação de informações** | Envolve a exposição de informações aos indivíduos que não devem para ter acesso aos mesmos — por exemplo, a capacidade de utilizadores para ler um ficheiro que não lhes foi concedido acesso a, ou a capacidade de um intruso para ler os dados em trânsito entre dois computadores |
| **Negação de serviço** | Serviço de negação de ataques denial of service (DoS) a usuários válidos — por exemplo, ao tornar um servidor Web temporariamente indisponível ou não utilizável. Tem de proteger contra determinados tipos de ameaças DoS simplesmente para melhorar a disponibilidade do sistema e fiabilidade |
| **Elevação de privilégios** | Um utilizador sem privilégios obtém acesso com privilégios e, deste modo, tem acesso suficiente para comprometer ou destruir todo o sistema. Elevação de ameaças de privilégio incluem nessas situações em que um invasor tem efetivamente penetrated todas as defesas de sistema e faça parte do confiáveis do sistema em si, de fato uma situação perigosa |

## <a name="next-steps"></a>Passos Seguintes

Avance para **[atenuações de ferramenta de modelagem de ameaças](./azure-security-threat-modeling-tool-mitigations.md)** para aprender as várias formas, pode minimizar essas ameaças com o Azure.