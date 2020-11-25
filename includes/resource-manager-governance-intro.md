---
title: incluir ficheiro
description: incluir ficheiro
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 55b545471cbe45fe35e28879270e4340cf9d3834
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027622"
---
Ao implementar recursos no Azure, tem muita flexibilidade ao decidir os tipos de recursos a implementar, onde estão localizados e como configurá-los. No entanto, essa flexibilidade pode abrir mais opções do que gostaria de permitir na sua organização. Ao considerar implementar recursos no Azure, deve estar a questionar-se:

* Como satisfaça os requisitos legais para a soberania dos dados em determinados países/regiões?
* Como controlo os custos?
* Como garanto que alguém não altera um sistema crítico inadvertidamente?
* Como controlo os custos dos recursos e faturo-os com precisão?

Este artigo aborda essas perguntas. Mais concretamente:

> [!div class="checklist"]
> * Atribua utilizadores a funções e atribua as funções a um âmbito, para que os utilizadores tenham permissão para fazer ações esperadas, mas não mais ações.
> * Aplique as políticas que prescrevem convenções em recursos na sua subscrição.
> * Bloqueie recursos que são críticos para o seu sistema.
> * Marque recursos para que possa acompanhá-los por valores que façam sentido para a sua organização.

Este artigo concentra-se nas tarefas que realiza para implementar a governação. Para uma discussão mais abrangente sobre os conceitos, veja [Governação no Azure](../articles/governance/index.yml).