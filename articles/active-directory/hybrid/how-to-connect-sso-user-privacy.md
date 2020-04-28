---
title: Privacidade do utilizador e entrada única do AD Azure Seamless / Microsoft Docs
description: Este artigo trata do Azure Ative Directory (Azure AD) Seamless SSO e do RGPD.
services: active-directory
keywords: o que é Azure AD Connect, RGPD, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60242110"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacidade do Utilizar e Início de Sessão Simples no Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição geral


O Azure AD Seamless SSO cria o seguinte tipo de registo, que pode conter Dados Pessoais: 

- Ficheiros de registo de rastreio de ligação adligações Azur.

Melhorar a privacidade do utilizador para SSO sem emenda de duas formas:

1.  Mediante solicitação, extrai os dados de uma pessoa e remova os dados dessa pessoa das instalações.
2.  Certifique-se de que nenhum dado é retido para além de 48 horas.

Recomendamos vivamente a segunda opção, uma vez que é mais fácil implementar e manter. Consulte as seguintes instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de rastreio de ligação ad ad azure

Verifique o conteúdo da pasta **%ProgramData\AADConnect** e elimine o conteúdo do registo de rastreios **(ficheiros de\*** rastreio.

>[!IMPORTANT]
>Não elimine o ficheiro **PersistedState.xml** nesta pasta, uma vez que este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de atualização é feita. Este ficheiro nunca conterá quaisquer dados sobre uma pessoa e nunca deve ser eliminado.

Pode rever e eliminar estes ficheiros de registo de rastreio utilizando o Windows Explorer ou pode utilizar o seguinte script PowerShell para executar as ações necessárias:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o guião num ficheiro com ". Extensão PS1". Executa este guião conforme necessário.

Para saber mais sobre os requisitos relacionados do RGPD Azure AD Connect, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Nota sobre registos de controladores de domínio

Se o registo de auditoria estiver ativado, este produto poderá gerar registos de segurança para os seus Controladores de Domínio. Para saber mais sobre a configuração das políticas de auditoria, leia este [artigo.](https://technet.microsoft.com/library/dd277403.aspx)

## <a name="next-steps"></a>Passos seguintes

* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
  - [**Troubleshoot**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.