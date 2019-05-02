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
ms.openlocfilehash: d1e7fa1ed1649508f0d4923db8817d17ad556ca1
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951560"
---
Ao implementar recursos no Azure, tem muita flexibilidade ao decidir os tipos de recursos a implementar, onde estão localizados e como configurá-los. No entanto, essa flexibilidade pode abrir mais opções do que gostaria de permitir na sua organização. Ao considerar implementar recursos no Azure, deve estar a questionar-se:

* Como atender a requisitos legais de soberania de dados em determinados países/regiões?
* Como controlo os custos?
* Como garanto que alguém não altera um sistema crítico inadvertidamente?
* Como controlo os custos dos recursos e faturo-os com precisão?

Este artigo aborda essas perguntas. Mais concretamente:

> [!div class="checklist"]
> * Atribua utilizadores a funções e atribua as funções a um âmbito, para que os utilizadores tenham permissão para fazer ações esperadas, mas não mais ações.
> * Aplique as políticas que prescrevem convenções em recursos na sua subscrição.
> * Bloqueie recursos que são críticos para o seu sistema.
> * Marque recursos para que possa acompanhá-los por valores que façam sentido para a sua organização.

Este artigo concentra-se nas tarefas que realiza para implementar a governação. Para uma discussão mais abrangente sobre os conceitos, veja [Governação no Azure](../articles/security/governance-in-azure.md). 
