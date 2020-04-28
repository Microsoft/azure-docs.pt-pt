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
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471560"
---
Se pretender que os utilizadores sejam solicitados para um segundo fator de autenticação antes de conceder acesso, pode configurar a Autenticação Multi-Factor Azure (MFA). Pode configurar o MFA por utilizador, ou pode alavancar o MFA através do [Acesso Condicional](../articles/active-directory/conditional-access/overview.md).

* O MFA por utilizador pode ser ativado sem custos adicionais. Ao ativar o MFA por utilizador, o utilizador será solicitado para autenticação de segundo fator contra todas as aplicações ligadas ao inquilino Da D. Azure. Consulte [a opção 1](#peruser) para os passos.
* O Acesso Condicional permite um controlo mais fino sobre a forma como deve ser promovido um segundo fator. Pode permitir a atribuição de MFA apenas vpN, e excluir outras aplicações ligadas ao inquilino da AD Azure. Consulte [a Opção 2](#conditional) para os passos.