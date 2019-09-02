---
title: Solucionar problemas de instalação do Azure AD Connect | Microsoft Docs '
description: Este tópico fornece etapas para solucionar problemas com a instalação do Azure AD Connect.
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
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211839"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Solucionar problemas Problemas de instalação do Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**
Verifique qual [tipo de instalação Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) é adequado para você. Se você atender aos critérios da instalação expressa, é altamente recomendável que você vá para a instalação expressa. A instalação expressa fornece as opções mínimas necessárias para concluir a instalação, portanto, há menos probabilidade de qualquer problema. 

No entanto, se você não atender aos critérios de instalação expressa e precisar fazer a instalação personalizada, aqui estão algumas práticas recomendadas que você pode seguir para evitar problemas comuns. Para simplificar, apenas as opções seletivas são mencionadas aqui:

* Verifique se você é um administrador no computador em que está instalando o AAD Connect. Faça logon no computador com as mesmas credenciais de administrador.

* Deixe que todas as opções sejam padrão na página a seguir, com exceção de "usar um SQL Server existente", se você quiser usar SQL Server existentes. Aqui estão [mais detalhes](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) sobre como usar as opções de instalação personalizadas. 

    ![Usar SQL Server existentes](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página a seguir, escolha a opção "criar nova conta do AD" para evitar quaisquer problemas de permissão com a conta existente.

    ![Conta da floresta do AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas Comuns**

* [Problemas de conectividade com Active Directory locais](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemas de conectividade com Azure Active Directory online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemas de permissão com Active Directory locais](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Pré-requisitos do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selecionar o tipo de instalação a utilizar para o Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introdução ao Azure AD Connect com as definições rápidas](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Instalação personalizada do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: Atualizar de uma versão anterior para a mais recente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: O que é um servidor de teste?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [O que é o Módulo ADConnectivityTool do PowerShell?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Passos Seguintes
- [Sincronização de Azure ad Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md)





