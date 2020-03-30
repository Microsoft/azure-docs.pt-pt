---
title: Introdução à Referência de Segurança do Azure
description: Introdução do Benchmark de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945324"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução de referência de segurança azure

Pode ter vários anos ou mesmo décadas de experiência com computação no local. Sabe como assegurar essas implantações; mas a nuvem é diferente. Como sabe se as suas implantações na nuvem estão seguras? Quais são as diferenças entre as práticas de segurança dos sistemas no local e as implantações em nuvem?

Há uma grande coleção de papéis brancos, boas práticas, arquiteturas de referência, orientação web, ferramentas de código aberto, soluções comerciais, feeds de inteligência, e muito mais, que podem ser usados para ajudar a proteger a nuvem. Que opção deve usar? O que se pode fazer para ter um nível aceitável de segurança na nuvem? 

Uma das melhores formas de garantir as suas implementações na nuvem é concentrar-se em recomendações de referência de segurança na nuvem. As recomendações de referência, para garantir qualquer serviço, começam com uma compreensão fundamental do risco de cibersegurança e como geri-lo. Em seguida, pode utilizar este entendimento adotando recomendações de segurança de referência do seu fornecedor de serviços na nuvem para ajudar a selecionar configurações específicas de configuração de segurança no seu ambiente. 

O Azure Security Benchmark inclui uma coleção de recomendações de segurança de alto impacto que pode usar para ajudar a garantir a maioria dos serviços que utiliza em Azure. Pode pensar nestas recomendações como "gerais" ou "organizacionais", uma vez que são aplicáveis à maioria dos serviços do Azure. As recomendações do Azure Security Benchmark são então personalizadas para cada serviço Azure, e esta orientação personalizada está contida em artigos de recomendações de serviço. 

A documentação azure Security Benchmark especifica controlos de segurança e recomendações de serviço.

- **Controlos de Segurança**: As recomendações de referência de segurança Azure são categorizadas por controlos de segurança. Os controlos de segurança representam requisitos de segurança agnósticos de alto nível, tais como segurança da rede e proteção de dados. Cada controlo de segurança tem um conjunto de recomendações e instruções de segurança que o ajudam a implementar essas recomendações. 
- **Recomendações de serviço**: Quando disponível, as recomendações de referência para os serviços Azure incluirão recomendações de benchmark de segurança Azure que são adaptadas especificamente para esse serviço. 

Os termos "Controlo", "Benchmark" e "Baseline" são frequentemente utilizados na documentação de Benchmark de Segurança Azure e é importante entender como o Azure utiliza esses termos. 

| Termo | Descrição | Exemplo |
|--|--|--|
| Controlo | Um **controlo** é uma descrição de alto nível de uma funcionalidade ou atividade que precisa de ser abordada e não é específica de uma tecnologia ou implementação. | A Proteção de Dados é um dos controlos de segurança. Este controlo contém ações específicas que precisam de ser abordadas para ajudar a garantir que os dados estão protegidos. |
| Referência | Um **benchmark** contém recomendações de segurança para uma tecnologia específica, como o Azure. As recomendações são categorizadas pelo controlo a que pertencem. | O referencial azure security compreende as recomendações de segurança específicas da plataforma Azure  |
| Linha de base | Uma **linha de base** são os requisitos de segurança para uma organização. Os requisitos de segurança baseiam-se em recomendações de referência. Cada organização decide quais as recomendações de referência a incluir na sua linha de base. | A empresa Contoso cria a sua linha de base de segurança optando por exigir recomendações específicas no Azure Security Benchmark. |

Congratulamo-nos com o seu feedback sobre o Azure Security Benchmark! Encorajamo-lo a fornecer comentários na área de feedback abaixo. Se preferir partilhar a sua entrada mais privada mente com a equipa azure Security Benchmark, é bem-vindo a preencher o formulário emhttps://aka.ms/AzSecBenchmark 
