---
title: Problema ao criar uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Como resolver problemas de criação de aplicações de Proxy de aplicações no portal de administração do Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7aad73331d06828a4bfe909dbb3b98350b1d0d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60293598"
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
