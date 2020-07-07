---
title: Introdução à Referência de Segurança do Azure
description: Introdução do Benchmark de Segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 883042c94576014c3bdeabd38516d9531c8fd3dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82606862"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução de referência de segurança Azure

Pode ter vários anos ou mesmo décadas de experiência com computação no local. Sabe como assegurar esses destacamentos; mas a nuvem é diferente. Como sabe se as suas implementações em nuvem estão seguras? Quais são as diferenças entre as práticas de segurança para sistemas no local e implantações em nuvem?

Há uma grande coleção de livros brancos, boas práticas, arquiteturas de referência, orientação web, ferramentas de código aberto, soluções comerciais, feeds de inteligência, e muito mais, que podem ser usados para ajudar a proteger a nuvem. Que opção deve usar? O que se pode fazer para obter um nível aceitável de segurança na nuvem? 

Uma das melhores formas de proteger as suas implementações em nuvem é focar-se em recomendações de referência de segurança na nuvem. Recomendações de referência, para garantir qualquer serviço, começam com uma compreensão fundamental do risco de cibersegurança e como geri-lo. Em seguida, pode utilizar este entendimento adotando recomendações de segurança de referência do seu fornecedor de serviços na nuvem para ajudar a selecionar configurações específicas de configuração de segurança no seu ambiente. 

O Azure Security Benchmark inclui uma coleção de recomendações de segurança de alto impacto que pode usar para ajudar a garantir a maioria dos serviços que utiliza no Azure. Pode pensar nestas recomendações como "gerais" ou "organizacionais" como são aplicáveis à maioria dos serviços da Azure. As recomendações do Azure Security Benchmark são então personalizadas para cada serviço Azure, e esta orientação personalizada está contida em artigos de recomendações de serviço. 

A documentação Azure Security Benchmark especifica controlos de segurança e recomendações de serviço.

- **Controlos de**Segurança : As recomendações do Azure Security Benchmark são categorizadas por controlos de segurança. Os controlos de segurança representam requisitos de segurança agnósticos de alto nível do fornecedor, tais como a segurança da rede e a proteção de dados. Cada controlo de segurança tem um conjunto de recomendações de segurança e instruções que o ajudam a implementar essas recomendações. 
- **Recomendações de serviço**: Quando disponível, as recomendações de referência para os serviços Azure incluirão recomendações de Benchmark de Segurança Azure que são adaptadas especificamente para esse serviço. 

Os termos "Control", "Benchmark" e "Baseline" são usados frequentemente na documentação do Benchmark de Segurança Azure e é importante entender como a Azure utiliza esses termos. 

| Termo | Descrição | Exemplo |
|--|--|--|
| Controlar | Um **controlo** é uma descrição de alto nível de uma característica ou atividade que precisa de ser abordada e não é específica de uma tecnologia ou implementação. | A Proteção de Dados é um dos controlos de segurança. Este controlo contém ações específicas que precisam de ser abordadas para ajudar a garantir a proteção dos dados. |
| Referência | Um **benchmark** contém recomendações de segurança para uma tecnologia específica, como a Azure. As recomendações são categorizadas pelo controlo a que pertencem. | O benchmark da Azure Security compreende as recomendações de segurança específicas da plataforma Azure  |
| Linha de base | Uma **linha de base** são os requisitos de segurança para uma organização. Os requisitos de segurança baseiam-se em recomendações de referência. Cada organização decide quais as recomendações de referência a incluir na sua linha de base. | A empresa Contoso cria a sua linha de base de segurança, optando por exigir recomendações específicas no Azure Security Benchmark. |

Congratulamo-nos com o seu feedback sobre o Azure Security Benchmark! Encorajamo-lo a fornecer comentários na área de comentários abaixo. Se preferir partilhar a sua entrada de forma mais privada com a equipa de Benchmark de Segurança Azure, é bem-vindo a preencher o formulário emhttps://aka.ms/AzSecBenchmark 
