---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: cephalin
ms.openlocfilehash: c3fa57dd162fbbfbf0d46f73bffc78f279ef2968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649117"
---
* **Credenciais ao nível do utilizador:** um conjunto de credenciais para toda a conta Azure. Pode ser usado para implementar no Serviço de Aplicações para qualquer app, em qualquer subscrição, que a conta Azure tenha permissão para aceder. É o conjunto padrão que surgiu no portal GUI (como a **Visão Geral** e **propriedades** da página de [recursos](../articles/azure-resource-manager/management/manage-resources-portal.md#manage-resources)da aplicação). Quando um utilizador recebe acesso a aplicações através de Role-Based permissões de Controlo de Acesso (RBAC) ou coadmin, esse utilizador pode usar as suas próprias credenciais de nível de utilizador até que o acesso seja revogado. Não partilhe estas credenciais com outros utilizadores do Azure.

* **Credenciais de nível de aplicação**: um conjunto de credenciais para cada aplicação. Pode ser usado apenas para implementar para essa aplicação. As credenciais para cada aplicação são geradas automaticamente na criação de aplicações. Não podem ser configurados manualmente, mas podem ser reiniciados a qualquer momento. Para que um utilizador tenha acesso a credenciais de nível de aplicação através (RBAC), esse utilizador deve ser contribuinte ou superior na aplicação (incluindo o papel incorporado do Colaborador do Website). Os leitores não estão autorizados a publicar, e não podem aceder a essas credenciais.