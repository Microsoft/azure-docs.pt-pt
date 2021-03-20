---
title: Ligue os dados do Azure Ative Directory ao Azure Sentinel | Microsoft Docs
description: Saiba como recolher dados do Azure Ative Directory e transmitir o Azure AD in, auditoria e provisionamento de registos no Azure Sentinel.
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
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99251986"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Ligue os dados do Azure Ative Directory (Azure AD) ao Azure Sentinel

Pode utilizar o conector incorporado do Azure Sentinel para recolher dados do [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) e transmiti-lo para o Azure Sentinel. O conector permite-lhe transmitir os seguintes tipos de registo:

- [**Registos de login**](../active-directory/reports-monitoring/concept-all-sign-ins.md), que contêm informações sobre [logins interativos do utilizador](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) onde um utilizador fornece um fator de autenticação.

    O conector AZURE AD inclui agora as seguintes três categorias adicionais de registos de login, todas atualmente em **PREVIEW**:
    
    - [**Registos de inscrição não interativos do utilizador,**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)que contêm informações sobre logins realizados por um cliente em nome de um utilizador sem qualquer interação ou fator de autenticação por parte do utilizador.
    
    - [**Registos de inscrição principais de**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)serviço, que contêm informações sobre logins por apps e diretores de serviço que não envolvem nenhum utilizador. Nestes insusentuques, a app ou serviço fornece uma credencial em seu próprio nome para autenticar ou aceder a recursos.
    
    - [**Registos de registo de identidade geridos,**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)que contêm informações sobre logins por recursos Azure que têm segredos geridos pela Azure. Para mais informações, veja [quais são as identidades geridas para os recursos da Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Registos de auditoria**](../active-directory/reports-monitoring/concept-audit-logs.md), que contêm informações sobre a atividade do sistema relacionada com a gestão de utilizadores e grupos, aplicações geridas e atividades de diretório.

- [**Provisionamento de registos**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (também em **PREVIEW),** que contêm informações sobre a atividade do sistema sobre utilizadores, grupos e funções prestadas pelo serviço de fornecimento de Azure AD. 

> [!IMPORTANT]
> Como indicado acima, alguns dos tipos de registo disponíveis estão atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.
## <a name="prerequisites"></a>Pré-requisitos

- Qualquer licença AD Azure (Grátis/O365/P1/P2) é suficiente para ingerir registos de entrada no Azure Sentinel. Podem ser aplicadas taxas adicionais por gigabyte para o Azure Monitor (Log Analytics) e para o Azure Sentinel.

- O seu utilizador deve ser atribuído ao contributo do Azure Sentinel no espaço de trabalho.

- O seu utilizador deve ser atribuído às funções de Administrador Global ou Administrador de Segurança no inquilino a partir do quais pretende transmitir os registos.

- O seu utilizador deve ter lido e escrito permissões para as definições de diagnóstico AZure AD para poder ver o estado da ligação. 

## <a name="connect-to-azure-active-directory"></a>Ligue-se ao Diretório Ativo Azure

1. Em Azure Sentinel, selecione **Conectores** de dados do menu de navegação.

1. Na galeria de conectores de dados, selecione **Azure Ative Directory** e, em seguida, selecione **Abrir a página do conector**.

1. Marque as caixas de verificação ao lado dos tipos de registo que pretende transmitir para Azure Sentinel (ver acima) e clique em **Connect**.

## <a name="find-your-data"></a>Encontre os seus dados

Após a criação de uma ligação bem sucedida, os dados aparecem em **Logs,** sob a secção **De Gestão** de Registos, nas seguintes tabelas:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Para consultar os registos AZure AD, insira o nome de mesa relevante no topo da janela de consulta.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar o Azure Ative Directory ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
