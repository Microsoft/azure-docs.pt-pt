---
title: Análise de equipa e ambiente de IA
titleSuffix: Azure Data Science Virtual Machine
description: Padrões para implantar o VM da Ciência de Dados num ambiente de equipa empresarial.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise geoespacial, processo de ciência de dados de equipa
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: a22b253081a6695758a737a506620f2f56e90227
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519699"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Análise de equipe baseada em máquina virtual de ciência de dados e ambiente de IA 
A [Data Science Virtual Machine](overview.md) (DSVM) fornece um ambiente rico na plataforma Azure, com software pré-construído para inteligência artificial (IA) e análise de dados.

Tradicionalmente, o DSVM tem sido usado como um ambiente de trabalho de análise individual. Os cientistas de dados individuais ganham produtividade com este ambiente de análise partilhado e pré-construído. Como as grandes equipas de análise planeiam ambientes para os seus cientistas de dados e desenvolvedores de IA, um dos temas recorrentes é uma infraestrutura de análise partilhada para desenvolvimento e experimentação. Esta infraestrutura é gerida de acordo com as políticas de TI empresariais que também facilitam a colaboração e consistência entre as equipas de ciência de dados e análise.

Uma infraestrutura partilhada permite uma melhor utilização das TI do ambiente de análise. Algumas organizações chamam à infraestrutura de dados/análise de dados baseada em equipa uma *caixa de areia analítica.* Permite que os cientistas de dados acedam a vários ativos de dados para entender rapidamente os dados. Este ambiente de caixa de areia também ajuda os cientistas de dados a executar experiências, validar hipóteses e construir modelos preditivos sem afetar o ambiente de produção.

Uma vez que o DSVM opera ao nível da infraestrutura Azure, os administradores de TI podem facilmente configurar o DSVM para operar em conformidade com as políticas de TI da empresa. O DSVM oferece total flexibilidade na implementação de várias arquiteturas de partilha, ao mesmo tempo que oferece acesso a ativos de dados corporativos de forma controlada.

Esta secção discute alguns padrões e diretrizes que pode usar para implantar o DSVM como uma infraestrutura de ciência de dados baseada em equipa. Como os blocos de construção para estes padrões provêm da infraestrutura Azure como um serviço (IaaS), eles aplicam-se a quaisquer VMs Azure. Esta série de artigos centra-se na aplicação destas capacidades padrão de infraestrutura Azure ao DSVM.

Os principais blocos de construção de um ambiente de análise de equipas empresariais incluem:

* [Uma piscina auto-achaada de DSVMs](dsvm-pools.md)
* [Identidade comum e acesso a um espaço de trabalho de qualquer um dos DSVMs na piscina](dsvm-common-identity.md)
* [Acesso seguro a fontes de dados](dsvm-secure-access-keys.md)


Esta série fornece orientações e ponteiros para cada um dos tópicos anteriores. Não cobre todas as considerações e requisitos para a implementação de DSVMs em grandes configurações empresariais. Aqui estão alguns outros recursos Azure que pode usar ao implementar instâncias DSVM na sua empresa:

* [Segurança da rede](../../security/fundamentals/network-overview.md)
* [Monitorização](../../azure-monitor/insights/monitor-vm-azure.md) e [gestão](../../virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Registo e auditoria](../../security/fundamentals/log-audit.md)
* [Controlo de acesso baseado em funções do Azure (RBAC do Azure)](../../role-based-access-control/overview.md)
* [Definição de políticas e execução](../../governance/policy/overview.md)
* [Antimalware](../../security/fundamentals/antimalware.md)
* [Encriptação](../../virtual-machines/windows/disk-encryption-overview.md)
* [Descoberta de dados e governação](../../data-catalog/index.yml)

Finalmente, o [Azure Architecture Center](/azure/architecture/) fornece uma arquitetura detalhada e modelos de ponta a ponta para construir e gerir a sua infraestrutura de análise baseada na nuvem.