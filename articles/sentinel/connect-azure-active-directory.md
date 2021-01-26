---
title: Ligue os dados do Azure Ative Directory ao Azure Sentinel | Microsoft Docs
description: Saiba como recolher dados do Azure Ative Directory e transmitir registos de login azure AD e registos de auditoria no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802259"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Ligar dados do Azure Ative Directory (Azure AD)

Pode utilizar o conector incorporado do Azure Sentinel para recolher dados do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-lo para o Azure Sentinel. O conector permite-lhe transmitir [registos de login](../active-directory/reports-monitoring/concept-sign-ins.md) e [registos](../active-directory/reports-monitoring/concept-audit-logs.md)de auditoria .

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter uma assinatura [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) para ingerir logins de entrada em Azure Sentinel. Podem ser aplicadas taxas adicionais por gigabyte para o Azure Monitor (Log Analytics) e para o Azure Sentinel.

- O seu utilizador deve ser atribuído ao contributo do Azure Sentinel no espaço de trabalho.

- O seu utilizador deve ser atribuído às funções de Administrador Global ou Administrador de Segurança no inquilino a partir do quais pretende transmitir os registos.

- O seu utilizador deve ter lido e escrito permissões para as definições de diagnóstico AZure AD para poder ver o estado da ligação. 

## <a name="connect-to-azure-active-directory"></a>Ligue-se ao Diretório Ativo Azure

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **Azure Ative Directory** e, em seguida, selecione **Abrir a página do conector**.

1. Marque as caixas de verificação ao lado dos tipos de registo que pretende transmitir para Azure Sentinel e clique em **Connect**. Estes são os tipos de registo que pode escolher:

    - **Registos de início de sessão**: Informações sobre a utilização de aplicações geridas e atividades de login do utilizador.
    - **Registos de auditoria**: Informações de atividade do sistema sobre gestão de utilizadores e grupos, aplicações geridas e atividades de diretório.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **De Gestão** de Registos, nas seguintes tabelas:

- `SigninLogs`
- `AuditLogs`

Para consultar os registos AZure AD, insira o nome de mesa relevante no topo da janela de consulta.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar o Azure Ative Directory ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
