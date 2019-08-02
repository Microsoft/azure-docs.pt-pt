---
title: Introdução aos ambientes de equipe baseado em máquina de Virtual de ciência de dados - Azure | Documentos da Microsoft
description: Padrões para implementar a VM de ciência de dados num ambiente de equipe empresarial.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise geoespacial, o processo de ciência de dados de equipa, aprendizagem aprofundada
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 81aac356f49f7bf8dc57f77054d10aa3e09d473f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565266"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>Análise de equipe baseado em Máquina Virtual de ciência de dados e ambiente de IA 
O [máquina de Virtual de ciência de dados](overview.md) (DSVM) fornece um ambiente avançado na plataforma do Azure com o software criados previamente para inteligência artificial (IA) e análise de dados. 

Tradicionalmente, a DSVM tem sido utilizada como uma área de trabalho de análise individual. Cientistas de dados individuais obtêm produtividade com essa noção partilhada de seus ambientes de analytics pré-criados. Como as equipes de análise de grande planearem os seus ambientes de análise para os seus cientistas de dados e programadores de inteligência artificial, um dos temas recorrentes é uma infraestrutura de análise compartilhado para desenvolvimento e experimentação. Esta infraestrutura é gerida em conformidade com TI empresariais políticas que também a facilitar a colaboração e a consistência entre as equipas de ciência/análise de dados. 

Uma infraestrutura partilhada também permite que a TI utilizar melhor o ambiente de análise. Algumas organizações chamam a infraestrutura de ciência/análise de dados baseada em equipas uma "área de analytics". Permite que os cientistas de dados aceder a vários recursos de dados para compreender os dados, executar experimentações, validar hipóteses e rapidamente criar modelos preditivos sem afetar o ambiente de produção. 

Uma vez que a DSVM opera no nível de infraestrutura do Azure, os administradores de TI prontamente podem configurar a DSVM para operar em conformidade com as políticas de TI da empresa. A DSVM oferece total flexibilidade na implementação de várias arquiteturas de partilha com o acesso a recursos de dados empresariais de uma forma controlada. 

Esta secção descreve alguns padrões e diretrizes que pode utilizar para implementar a DSVM como uma infraestrutura de ciência de dados baseados em equipes. Os blocos de construção para estes padrões vêm de infraestrutura do Azure como um serviço (IaaS), para que estas são aplicadas a todas as VMs do Azure. O foco desta série de artigos é aplicar esses recursos de infraestrutura do Azure standard para a VM de ciência de dados. 

Alguns dos principais blocos de construção de um ambiente de análise de equipa de empresariais são:

* [Conjunto de dimensionado automaticamente de máquinas de virtuais de ciência de dados](dsvm-pools.md)
* [Acesso a uma área de trabalho a partir de qualquer um dos DSVMs no agrupamento e identidade comum](dsvm-common-identity.md)
* [Proteger o acesso às origens de dados](dsvm-secure-access-keys.md)


Esta série de artigos fornece orientações e ponteiros para cada um dos itens anteriores. Apesar de não abranger todas as considerações e necessidades na implantação DSVM em configurações de empresas de grande porte. Esta é outra documentação do Azure que pode utilizar durante a implementação de instâncias DSVM na sua empresa: 

* [Segurança da rede](https://docs.microsoft.com/azure/security/azure-network-security)
* [Monitorização](https://docs.microsoft.com/azure/virtual-machines/windows/monitor) e [gestão](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [Registo e auditoria](https://docs.microsoft.com/azure/security/azure-log-audit)
* [Controlo de acesso baseado em funções](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [Definição de política e de imposição](../../governance/policy/overview.md)
* [Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [Encriptação](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [Deteção de dados e de governação](https://docs.microsoft.com/azure/data-catalog/)

O [Centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/) fornece uma arquitetura de ponto-a-ponto detalhada e padrões para criar e gerir a sua infraestrutura de análise baseada na cloud. 
