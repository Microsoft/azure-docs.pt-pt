---
author: baanders
description: incluir arquivo para Azure Digital Twins - pré-requisito para configurar um registo de aplicações
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 0dd97e109ee907e8eec2d31c7d469e8fb1e70e7b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020313"
---
Para autenticar todos os recursos utilizados neste artigo, terá de configurar um registo de **aplicações** [Azure Ative (Azure AD).](../articles/active-directory/fundamentals/active-directory-whatis.md) Siga as instruções em [*Como-a: Crie um registo de aplicações*](../articles/digital-twins/how-to-create-app-registration.md) para configurar isto. 

Uma vez que você tem um registro de aplicação, você precisará do ID de **_Aplicação (cliente)_** do registo e **_diretório (inquilino) ID (encontre_** [no portal Azure).](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id) Tome nota destes valores para usá-los mais tarde para conceder acesso às APIs das Gémeas Digitais Azure.