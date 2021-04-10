---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995625"
---
Se pretender que os utilizadores sejam solicitados para um segundo fator de autenticação antes de conceder acesso, pode configurar a autenticação multi-factor Azure AD (MFA). Pode configurar mFA por utilizador, ou pode alavancar o MFA através do [Acesso Condicional.](../articles/active-directory/conditional-access/overview.md)

* O MFA por utilizador pode ser ativado sem custos adicionais. Ao ativar o MFA por utilizador, o utilizador será solicitado para a autenticação do segundo fator contra todas as aplicações ligadas ao inquilino Azure AD. Consulte [a opção 1](#peruser) para ver os passos.
* O Acesso Condicional permite um controlo mais fino sobre a forma como deve ser promovido um segundo fator. Pode permitir a atribuição de MFA apenas a VPN, e excluir outras aplicações ligadas ao inquilino AZURE AD. Consulte [a opção 2](#conditional) para ver os passos.