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
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369196"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução de referência de segurança Azure

Novos serviços e funcionalidades são lançados diariamente no Azure, os desenvolvedores estão rapidamente a publicar novas aplicações em nuvem construídas sobre estes serviços, e os atacantes estão sempre à procura de novas formas de explorar recursos mal configurados. A nuvem move-se rapidamente, os desenvolvedores movem-se rápido, e os atacantes estão sempre em movimento. Como se mantém e certifica-se de que as suas implementações em nuvem estão seguras? Como é que as práticas de segurança para sistemas de nuvem são diferentes dos sistemas no local? Como monitorizar a consistência em muitas equipas de desenvolvimento independentes?

A Microsoft descobriu que usar *referências de segurança* pode ajudá-lo a proteger rapidamente as implementações na nuvem. Recomendações de benchmark do seu fornecedor de serviços na nuvem dão-lhe um ponto de partida para selecionar definições específicas de configuração de segurança no seu ambiente e permitem-lhe reduzir rapidamente o risco para a sua organização.

O Azure Security Benchmark inclui uma coleção de recomendações de segurança de alto impacto que pode usar para ajudar a proteger os serviços que utiliza no Azure:

- **Controlos de segurança**: Estas recomendações são geralmente aplicáveis em todos os seus serviços Azure e Azure. Cada recomendação identifica uma lista de partes interessadas que estão tipicamente envolvidas no planeamento, aprovação ou implementação do benchmark. 
- **Linhas de base de** serviço : Estes aplicam os controlos aos serviços individuais da Azure para fornecer recomendações sobre a configuração de segurança desse serviço.

## <a name="implement-the-azure-security-benchmark"></a>Implementar o Azure Security Benchmark
- **Planeie** a sua implementação de Benchmark de Segurança Azure, revendo a [documentação](overview.md) para os controlos da empresa e linhas de base específicas do serviço para planear o seu quadro de controlo e como mapeia para orientações como o quadro cis (Controls v7.1) e NIST (SP 800-53).
- **Monitorize** a sua conformidade com o estado de benchmark de segurança Azure (e outros conjuntos de controlo) utilizando o [painel de conformidade regulamentar](../../security-center/security-center-compliance-dashboard.md)do Azure Security Center .
- **Estabeleça guarda-costas** para automatizar configurações seguras e impor o cumprimento do Benchmark de Segurança Azure (e outros requisitos na sua organização) com a Azure Blueprints e a Azure Policy.
 
Note que o Azure Security Benchmark v2 está alinhado com [as Melhores Práticas de Segurança](/security/compass/microsoft-security-compass-introduction) da Microsoft (anteriormente Azure Security Compass) de modo que o Azure Security Benchmark fornece uma única visão consolidada das recomendações de segurança Azure da Microsoft.

## <a name="common-use-cases"></a>Casos de Uso Comum

O Azure Security Benchmark é frequentemente utilizado para enfrentar estes desafios comuns para clientes ou parceiros de serviço que são:
- Novo em Azure e estão à procura de boas práticas de segurança para garantir uma implementação segura.
- Melhorar a postura de segurança das implementações existentes do Azure para priorizar os riscos e mitigações de topo.
- Aprovação de serviços Azure para uso por tecnologia e uso de negócios para cumprir orientações de segurança específicas.
- Cumprir os requisitos regulamentares para clientes que são de indústrias governamentais ou altamente regulamentadas, como finanças e cuidados de saúde (ou fornecedores de serviços que precisam de construir sistemas para estes clientes). Estes clientes precisam de garantir que a sua configuração do Azure satisfaz as capacidades de segurança especificadas num quadro da indústria como o CIS, o NIST ou o PCI. O Azure Security Benchmark fornece uma abordagem eficiente com os controlos já pré-mapeados para estes critérios de referência da indústria.

## <a name="terminology"></a>Terminologia

Os termos "controle", "benchmark" e "linha de base" são usados frequentemente na documentação do Azure Security Benchmark e é importante entender como a Azure usa esses termos.


| Termo | Descrição | Exemplo |
|--|--|--|
| Controlo | Um controlo é uma descrição de alto nível de uma característica ou atividade que precisa de ser abordada e não é específica de uma tecnologia ou implementação. | A Proteção de Dados é um dos controlos de segurança. Este controlo contém ações específicas que devem ser endereçadas para ajudar a garantir a proteção dos dados. |
| Referência | Um benchmark contém recomendações de segurança para uma tecnologia específica, como a Azure. As recomendações são categorizadas pelo controlo a que pertencem. | O Azure Security Benchmark compreende as recomendações de segurança específicas da plataforma Azure |
| Linha de base | Uma linha de base é a implementação do benchmark no serviço Azure individual. Cada organização decide a recomendação de referência e as configurações correspondentes são necessárias no âmbito de implementação do Azure. | A empresa Contoso procura ativar as funcionalidades de segurança Azure SQL seguindo a configuração recomendada na linha de base de segurança Azure SQL.

Congratulamo-nos com o seu feedback sobre o Azure Security Benchmark! Encorajamo-lo a fornecer comentários na área de comentários abaixo. Se preferir partilhar a sua entrada de forma mais privada com a equipa de Benchmark de Segurança Azure, é bem-vindo a preencher o formulário em https://aka.ms/AzSecBenchmark
