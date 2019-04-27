---
title: Atualizar o código para a plataforma mais recente | Documentos da Microsoft
description: Este tópico explica como atualizar sua de Dynamics 365 da Microsoft para a versão de plataforma de operações para a versão mais recente da plataforma
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776816"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Atualizar o código para a plataforma mais recente

Este artigo explica como atualizar sua de Dynamics 365 da Microsoft para a versão de plataforma de operações para a versão mais recente da plataforma.

## <a name="overview"></a>Descrição geral

De Dynamics 365 Microsoft para a plataforma de operações é composta pelos seguintes componentes:

Dynamics 365 para binários de plataforma de operações, tais como o servidor de objeto de aplicativo (AOS), a estrutura de gerenciamento de dados, relatórios e framework do business intelligence (BI), ferramentas de desenvolvimento e os serviços de análise. Os seguintes pacotes de árvore de objeto de aplicativo (AOT):

1. Plataforma de Aplicações
2. Base do aplicativo
3. Testar o Essentials

**Importante**: Para mover para o Dynamics 365 mais recente para a plataforma de operações, o Dynamics 365 para implementação de operações não pode ter quaisquer personalizações (overlayering) de qualquer um dos pacotes AOT que pertencem à plataforma. Essa restrição foi introduzida na atualização da plataforma 3, para que as atualizações contínuas totalmente integradas podem ser feitas para a plataforma. Se estiver executando numa plataforma que é mais antiga do que plataforma atualização 3, consulte a atualização para a plataforma update 3 a partir de uma seção de compilação anterior no final deste artigo.

Para obter mais informações sobre a atualização do código, consulte [aqui](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).