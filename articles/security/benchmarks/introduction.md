---
title: Introdução ao benchmark de segurança do Azure
description: Introdução ao benchmark de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 3169959250eb45346456c64e606f25efb0ea44f0
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934234"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução ao benchmark de segurança do Azure

Você pode ter vários anos ou até mesmo décadas de experiência com computação local. Você sabe como proteger essas implantações; Mas a nuvem é diferente. Como saber se suas implantações na nuvem estão protegidas? Quais são as diferenças entre as práticas de segurança para sistemas locais e implantações na nuvem?

Há uma grande coleção de White papers, práticas recomendadas, arquiteturas de referência, diretrizes da Web, ferramentas de software livre, soluções comerciais, feeds de inteligência e muito mais, que podem ser usadas para ajudar a proteger a nuvem. Qual opção você deve usar? O que você pode fazer para obter um nível aceitável de segurança na nuvem? 

Uma das melhores maneiras de proteger suas implantações na nuvem é se concentrar nas recomendações de benchmark de segurança na nuvem. Recomendações de benchmark, para proteger qualquer serviço, comece com um entendimento fundamental do risco de segurança cibernética e como gerenciá-lo. Você pode usar essa compreensão adotando as recomendações de segurança de benchmark do seu provedor de serviços de nuvem para ajudar a selecionar configurações de segurança específicas em seu ambiente. 

O benchmark de segurança do Azure inclui uma coleção de recomendações de segurança de alto impacto que você pode usar para ajudar a proteger a maioria dos serviços que você usa no Azure. Você pode considerar essas recomendações como "geral" ou "organizacional", pois elas são aplicáveis à maioria dos serviços do Azure. As recomendações de benchmark de segurança do Azure são então personalizadas para cada serviço do Azure, e essas diretrizes personalizadas estão contidas em artigos de recomendações de serviço. 

A documentação de benchmark de segurança do Azure especifica controles de segurança e recomendações de serviço.

- **Controles de segurança**: as recomendações de benchmark de segurança do Azure são categorizadas por controles de segurança. Os controles de segurança representam requisitos de segurança independentes de fornecedor de alto nível, como segurança de rede e proteção de dados. Cada controle de segurança tem um conjunto de recomendações de segurança e instruções que ajudam a Implment essas recomendações. 
- **Recomendações de serviço**: quando disponível, as recomendações de parâmetro de comparação para os serviços do Azure incluirão recomendações de benchmark de segurança do Azure que são adaptadas especificamente para esse serviço. 

Os termos "controle", "parâmetro de comparação" e "linha de base" são usados frequentemente na documentação de benchmark de segurança do Azure e é importante entender como o Azure usa esses termos. 

| Duração | Descrição | Exemplo |
|--|--|--|
| Controlo | Um **controle** é uma descrição de alto nível de um recurso ou atividade que precisa ser resolvida e não é específica para uma tecnologia ou implementação. | A proteção de dados é um dos controles de segurança. Esse controle contém ações específicas que precisam ser abordadas para ajudar a garantir que os dados sejam protegidos. |
| Referência | Um parâmetro de **comparação** contém recomendações de segurança para uma tecnologia específica, como o Azure. As recomendações são categorizadas pelo controle ao qual elas pertencem. | O benchmark de segurança do Azure consiste nas recomendações de segurança específicas para a plataforma Azure  |
| Linha de base | Uma **linha de base** são os requisitos de segurança para uma organização. Os requisitos de segurança são baseados nas recomendações de benchmark. Cada organização decide quais recomendações de benchmark incluir em sua linha de base. | A empresa contoso cria sua linha de base de segurança, optando por exigir recomendações específicas no benchmark de segurança do Azure. |

Agradecemos seus comentários sobre o benchmark de segurança do Azure! Incentivamos você a fornecer comentários na área de comentários abaixo. Se você preferir compartilhar sua entrada de forma mais particular com a equipe de benchmark de segurança do Azure, você será bem-vindo a preencher o formulário em https://aka.ms/AzSecBenchmark 