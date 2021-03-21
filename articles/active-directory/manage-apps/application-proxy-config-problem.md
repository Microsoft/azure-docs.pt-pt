---
title: Problema na criação de uma aplicação de procuração de aplicação | Microsoft Docs
description: Como resolver problemas criando aplicações de Procuração de Aplicações no portal AD AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0c88b90d0caa2f723921d76b5f9dc036895f14e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258019"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problema na criação de uma aplicação de Procuração de Aplicação 

Abaixo estão algumas das questões comuns que as pessoas enfrentam ao criar uma nova aplicação de procuração de aplicações.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre a criação de uma aplicação Proxy de aplicação através do Portal Admin, consulte [as aplicações publicar usando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Se estiver a seguir os passos nesse documento e estiver a obter um erro ao criar a aplicação, consulte os detalhes de erro para obter informações e sugestões de como corrigir a aplicação. A maioria das mensagens de erro incluem uma correção sugerida. 

## <a name="specific-things-to-check"></a>Coisas específicas para verificar

Para evitar erros comuns, verifique:

-   É um administrador com permissão para criar uma aplicação de Procuração de Aplicação

-   O URL interno é único

-   O URL externo é único

-   Os URLs começam com http ou https, e terminam com um "/"

-   O URL deve ser um nome de domínio e não um endereço IP

A mensagem de erro deve ser exibida no canto superior direito quando criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Pedido de notificação](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Passos seguintes
[Ativar o Proxy da Aplicação no Portal do Azure](application-proxy-add-on-premises-application.md)
