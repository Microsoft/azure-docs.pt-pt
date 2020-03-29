---
title: Problema na criação de aplicação de procuração de aplicações / Microsoft Docs
description: Como resolver problemas criando aplicações de procuração de aplicações no portal Azure AD Admin
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825806"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema de criação de uma aplicação proxy de aplicação 

Abaixo estão algumas das questões comuns que as pessoas enfrentam na criação de uma nova aplicação de procuração de aplicações.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre a criação de uma aplicação proxy de aplicação através do Portal da Administração, consulte as aplicações da Publicação utilizando o Proxy de [Aplicação AD Azure](application-proxy-add-on-premises-application.md).

Se estiver a seguir os passos desse documento e estiver a ter um erro na criação da aplicação, consulte os detalhes do erro para obter informações e sugestões para a forma de corrigir a aplicação. A maioria das mensagens de erro incluem uma correção sugerida. 

## <a name="specific-things-to-check"></a>Coisas específicas para verificar

Para evitar erros comuns, verifique:

-   É um administrador com permissão para criar um pedido de procuração de aplicação

-   O URL interno é único

-   O URL externo é único

-   Os URLs começam com http ou https, e terminam com um "/"

-   O URL deve ser um nome de domínio e não um endereço IP

A mensagem de erro deve ser exibida no canto superior direito quando criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Pedido de notificação](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Passos seguintes
[Ativar o Proxy da Aplicação no Portal do Azure](application-proxy-add-on-premises-application.md)
