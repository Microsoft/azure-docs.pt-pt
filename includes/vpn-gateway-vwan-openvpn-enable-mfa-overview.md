---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471560"
---
Se quiser que os utilizadores sejam solicitados para um segundo fator de autenticação antes de conceder acesso, pode configurar a autenticação multi-factor Azure (MFA). Pode configurar mFA por utilizador, ou pode alavancar o MFA através do [Acesso Condicional.](../articles/active-directory/conditional-access/overview.md)

* O MFA por utilizador pode ser ativado sem custos adicionais. Ao ativar o MFA por utilizador, o utilizador será solicitado para a autenticação do segundo fator contra todas as aplicações ligadas ao inquilino Azure AD. Consulte [a opção 1](#peruser) para ver os passos.
* O Acesso Condicional permite um controlo mais fino sobre a forma como deve ser promovido um segundo fator. Pode permitir a atribuição de MFA apenas a VPN, e excluir outras aplicações ligadas ao inquilino AZURE AD. Consulte [a opção 2](#conditional) para ver os passos.