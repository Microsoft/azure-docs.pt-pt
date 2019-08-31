---
title: Ambiente do Team Analytics e do ia
titleSuffix: Azure Data Science Virtual Machine
description: Padrões para implementar a VM de ciência de dados num ambiente de equipe empresarial.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195659"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Análise de equipe baseado em Máquina Virtual de ciência de dados e ambiente de IA 
O [máquina virtual de ciência de dados](overview.md) (DSVM) fornece um ambiente avançado na plataforma do Azure, com software predefinido para ia (inteligência artificial) e análise de dados.

Tradicionalmente, a DSVM tem sido utilizada como uma área de trabalho de análise individual. Cientistas de dados individuais têm produtividade com esse ambiente de análise compartilhado e predefinido. Como equipes de análise de grande porte planejam ambientes para seus cientistas de dados e desenvolvedores de ia, um dos temas recorrentes é uma infraestrutura de análise compartilhada para desenvolvimento e experimentação. Essa infraestrutura é gerenciada em linha com políticas de ti corporativas que também facilitam a colaboração e a consistência entre as equipes de ciência de dados e de análise.

Uma infraestrutura compartilhada permite uma melhor utilização de ti do ambiente de análise. Algumas organizações chamam a infraestrutura de ciência/análise de dados baseada em equipe em uma *área restrita de análise*. Ele permite que os cientistas de dados acessem vários ativos de dados para entender rapidamente os dados. Esse ambiente de área restrita também ajuda os cientistas de dados a executar experimentos, validar as mesmas e criar modelos de previsão sem afetar o ambiente de produção.

Uma vez que a DSVM opera no nível de infraestrutura do Azure, os administradores de TI prontamente podem configurar a DSVM para operar em conformidade com as políticas de TI da empresa. O DSVM oferece flexibilidade total na implementação de várias arquiteturas de compartilhamento, além de oferecer acesso a ativos de dados corporativos de forma controlada.

Esta secção descreve alguns padrões e diretrizes que pode utilizar para implementar a DSVM como uma infraestrutura de ciência de dados baseados em equipes. Como os blocos de construção desses padrões são provenientes da IaaS (infraestrutura como serviço) do Azure, eles se aplicam a qualquer VM do Azure. Esta série de artigos se concentra na aplicação desses recursos de infraestrutura padrão do Azure ao DSVM.

Os principais blocos de construção de um ambiente do Enterprise Team Analytics incluem:

* [Um pool de DSVMs em escala](dsvm-pools.md)
* [Acesso a uma área de trabalho a partir de qualquer um dos DSVMs no agrupamento e identidade comum](dsvm-common-identity.md)
* [Proteger o acesso às origens de dados](dsvm-secure-access-keys.md)


Esta série fornece orientações e ponteiros para cada um dos tópicos anteriores. Ele não cobre todas as considerações e requisitos para a implantação de DSVMs em grandes configurações corporativas. Aqui estão alguns outros recursos do Azure que você pode usar ao implementar instâncias de DSVM em sua empresa:

* [Segurança da rede](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorização](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestão](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registo e auditoria](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Definição de política e de imposição](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Encriptação](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Deteção de dados e de governação](https://docs.microsoft.com/azure/data-catalog/)

Por fim, a [centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/) fornece uma arquitetura e modelos detalhados de ponta a ponta para criar e gerenciar sua infraestrutura de análise baseada em nuvem.