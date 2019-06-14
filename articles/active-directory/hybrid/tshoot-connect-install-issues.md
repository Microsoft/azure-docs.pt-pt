---
title: Resolver problemas de instalação do Azure AD Connect | Documentos da Microsoft
description: Este tópico fornece os passos sobre como resolver problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e077127681f8bd7b650ab22f2d036efd7f9733ee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454796"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Resolução de problemas: Problemas de instalação do Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**
Certifique-se de que [tipo de instalação do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) é adequada para. Se cumprir os critérios de instalação rápida, em seguida, é altamente recomendável que passe com a instalação do express. Instalação rápida dá-lhe opções mínimo necessárias para concluir a instalação, portanto, há menos probabilidade de quaisquer problemas. 

No entanto, se não cumpre os critérios de instalação rápida e terá de efetuar a instalação personalizada, em seguida, estas são algumas práticas recomendadas pode seguir para evitar problemas comuns. Para simplificar apenas seletivas opções são mencionadas aqui:

* Certifique-se de que é um administrador no computador no qual está a instalar o AAD Connect. Faça logon máquina com as mesmas credenciais de administrador.

* Permitir que todas as opções para ser predefinido na página seguinte, exceto para "Utilizar um SQL Server existente", se pretender utilizar o SQL Server existente. Seguem-se [mais detalhes](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) sobre como utilizar as opções de instalação personalizada. 

    ![Utilizar SQL Server existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página seguinte, escolha a opção "Criar AD nova conta", para evitar qualquer permissão, problemas com a conta existente.

    ![Conta de floresta do AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas Comuns**

* [Problemas de conectividade do Active Directory no local](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemas de conectividade com online do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemas de permissão com o Active Directory no local](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Pré-requisitos do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selecionar o tipo de instalação a utilizar para o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introdução ao Azure AD Connect com as definições rápidas](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Atualizar de uma versão anterior para a mais recente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: O que é um servidor de teste?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [O que é o Módulo ADConnectivityTool do PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Passos Seguintes
- [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida? ](whatis-hybrid-identity.md).





