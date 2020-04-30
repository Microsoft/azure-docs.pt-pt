---
title: Análise de equipa e ambiente de IA
titleSuffix: Azure Data Science Virtual Machine
description: Padrões para implantar o VM de Ciência de Dados num ambiente de equipa empresarial.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 1f3a24afb2238c2448cb19b3889467a1b1819724
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460550"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Análise de equipa baseada em máquinas virtuais de ciência de dados e ambiente de IA 
A [Máquina Virtual](overview.md) de Ciência de Dados (DSVM) proporciona um ambiente rico na plataforma Azure, com software pré-construído para inteligência artificial (IA) e análise de dados.

Tradicionalmente, o DSVM tem sido usado como um ambiente de trabalho individual de análise. Os cientistas de dados individuais ganham produtividade com este ambiente de análise partilhado e pré-construído. À medida que as grandes equipas de análise planeiam ambientes para os seus cientistas de dados e desenvolvedores de IA, um dos temas recorrentes é uma infraestrutura de análise partilhada para desenvolvimento e experimentação. Esta infraestrutura é gerida de acordo com as políticas de TI da empresa que também facilitam a colaboração e consistência entre as equipas de ciência e análise de dados.

Uma infraestrutura partilhada permite uma melhor utilização de TI do ambiente de análise. Algumas organizações chamam à infraestrutura de dados/análise baseada em equipa uma *caixa de areia analítica.* Permite aos cientistas de dados aceder a vários ativos de dados para entender rapidamente os dados. Este ambiente de caixa de areia também ajuda os cientistas de dados a executar experiências, a validar hipóteses e a construir modelos preditivos sem afetar o ambiente de produção.

Uma vez que o DSVM opera ao nível da infraestrutura Azure, os administradores de TI podem configurar prontamente o DSVM para operar em conformidade com as políticas de TI da empresa. O DSVM oferece total flexibilidade na implementação de várias arquiteturas de partilha, ao mesmo tempo que oferece acesso a ativos de dados corporativos de forma controlada.

Esta secção discute alguns padrões e diretrizes que pode usar para implementar o DSVM como uma infraestrutura de ciência de dados baseada em equipa. Como os blocos de construção para estes padrões provêm da infraestrutura azure como um serviço (IaaS), aplicam-se a quaisquer VMs Azure. Esta série de artigos centra-se na aplicação destas capacidades de infraestrutura padrão do Azure ao DSVM.

Os principais blocos de construção de um ambiente de análise de equipas empresariais incluem:

* [Uma piscina auto-dimensionada de DSVMs](dsvm-pools.md)
* [Identidade comum e acesso a um espaço de trabalho de qualquer um dos DSVMs na piscina](dsvm-common-identity.md)
* [Acesso seguro a fontes de dados](dsvm-secure-access-keys.md)


Esta série fornece orientações e indicações para cada um dos tópicos anteriores. Não cobre todas as considerações e requisitos para a implementação de DSVMs em grandes configurações empresariais. Aqui estão outros recursos Azure que pode utilizar durante a implementação de instâncias de DSVM na sua empresa:

* [Segurança da rede](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [Monitorização](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestão](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registo e auditoria](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Definição e execução de políticas](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [Encriptação](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview.md)
* [Descoberta e governação de dados](https://docs.microsoft.com/azure/data-catalog/)

Finalmente, o [Azure Architecture Center](https://docs.microsoft.com/azure/architecture/) oferece uma arquitetura e modelos detalhados de ponta a ponta para construir e gerir a sua infraestrutura de análise baseada em nuvem.