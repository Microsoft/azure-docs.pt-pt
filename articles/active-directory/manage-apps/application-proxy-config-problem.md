---
title: Problema ao criar uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Como resolver problemas de criação de aplicações de Proxy de aplicações no portal de administração do Azure AD
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825806"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema ao criar uma aplicação de Proxy de aplicações 

Seguem-se alguns dos problemas comuns face de pessoas ao criar uma nova aplicação de proxy de aplicação.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre como criar uma aplicação de Proxy de aplicações através do Portal de administração, veja [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md).

Se estiver a seguir os passos esse documento e estiver a receber um erro ao criar a aplicação, consulte os detalhes do erro para obter informações e sugestões para saber como corrigir o aplicativo. A maioria das mensagens de erro incluem uma correção sugerida. 

## <a name="specific-things-to-check"></a>Aspetos específicos a verificar

Para evitar erros comuns, certifique-se:

-   For um administrador com permissão para criar uma aplicação de Proxy de aplicações

-   O URL interno é exclusivo

-   O URL externo é exclusivo

-   Os URLs de começar com http ou https e terminar com uma "/"

-   O URL deve ser um nome de domínio e não um endereço IP

A mensagem de erro deve exibir no canto superior direito, ao criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Linha de notificação](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Passos Seguintes
[Ativar o Proxy de aplicações no portal do Azure](application-proxy-add-on-premises-application.md)
