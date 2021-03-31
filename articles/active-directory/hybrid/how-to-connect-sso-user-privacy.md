---
title: Privacidade do utilizador e Azure AD Single Sign-On | Microsoft Docs
description: Este artigo trata do cumprimento do Azure Ative Directory (Azure AD) Seamless SSO e do RGPD.
services: active-directory
keywords: o que é Azure AD Connect, RGPD, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278603"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Privacidade do Utilizar e Início de Sessão Simples no Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Descrição Geral


A Azure AD Seamless SSO cria o seguinte tipo de registo, que pode conter Dados Pessoais: 

- Azure AD Connect ficheiros de registo de vestígios.

Melhorar a privacidade do utilizador para o SSO sem emenda de duas formas:

1.  A pedido, extrair dados para uma pessoa e remover dados dessa pessoa das instalações.
2.  Certifique-se de que nenhum dado é retido para além de 48 horas.

Recomendamos vivamente a segunda opção, uma vez que é mais fácil de implementar e manter. Consulte as seguintes instruções para cada tipo de registo:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminar ficheiros de registo de registo de rastreio Azure AD Connect

Verifique o conteúdo da pasta **%ProgramData%\AADConnect** e elimine o conteúdo do registo de vestígios **(ficheiros \* trace-.log)** desta pasta no prazo de 48 horas após a instalação ou atualização do Azure AD Connect ou modificar a configuração SSO sem emenda, uma vez que esta ação pode criar dados cobertos pelo RGPD.

>[!IMPORTANT]
>Não elimine o ficheiro **PersistedState.xml** nesta pasta, uma vez que este ficheiro é utilizado para manter o estado da instalação anterior do Azure AD Connect e é utilizado quando uma instalação de upgrade é feita. Este ficheiro nunca conterá quaisquer dados sobre uma pessoa e nunca deverá ser eliminado.

Pode rever e eliminar estes ficheiros de registo de vestígios utilizando o Windows Explorer ou pode utilizar o seguinte script PowerShell para executar as ações necessárias:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Guarde o guião num ficheiro com o ". Extensão PS1. Executar este guião conforme necessário.

Para saber mais sobre os requisitos relacionados com o RGPD Azure AD, consulte [este artigo](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Nota sobre registos de controlador de domínio

Se o registo de auditoria estiver ativado, este produto poderá gerar registos de segurança para os seus Controladores de Domínio. Para saber mais sobre a configuração das políticas de auditoria, leia este [artigo.](/previous-versions/tn-archive/dd277403(v=technet.10))

## <a name="next-steps"></a>Passos seguintes

* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
  - [**Resolução de problemas**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.