---
author: baanders
description: incluir arquivo para Azure Digital Twins - pré-requisito para configurar um registo de aplicações
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 2ce534972cf6509cdc1ca026f4b29efd3df91afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96303599"
---
Para autenticar todos os recursos utilizados neste artigo, terá de configurar um registo de **aplicações [Azure Ative (Azure AD).](../articles/active-directory/fundamentals/active-directory-whatis.md)** Siga as instruções em [*Como-a: Crie um registo de aplicações*](../articles/digital-twins/how-to-create-app-registration.md) para configurar isto. 

Uma vez que você tem um registro de aplicação, você precisará do ID de **_Aplicação (cliente)_** do registo e **_diretório (inquilino) ID (encontre_** [no portal Azure).](../articles/digital-twins/how-to-create-app-registration.md#collect-client-id-and-tenant-id) Tome nota destes valores para usá-los mais tarde para conceder acesso às APIs das Gémeas Digitais Azure.