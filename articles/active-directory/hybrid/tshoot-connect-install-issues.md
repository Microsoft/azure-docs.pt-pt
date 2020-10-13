---
title: Problemas de instalação do Azure AD Connect Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a instalação do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25314b4a306678dc877a95194907b3d73979e4f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89275866"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Resolução de problemas: Problemas de instalação Azure AD Connect

## <a name="recommended-steps"></a>**Passos Recomendados**
Verifique se o [tipo de instalação Azure AD Connect](./how-to-connect-install-select-installation.md) é adequado para si. Se cumprir os critérios de instalação expressa, recomendamos vivamente que vá com a instalação expressa. A instalação expressa dá-lhe as opções mínimas necessárias para terminar a instalação, pelo que há menos probabilidades de quaisquer problemas. 

No entanto, se não cumprir os critérios de instalação expresso e tiver de fazer a instalação personalizada, então aqui estão algumas boas práticas que pode seguir para evitar problemas comuns. Por uma questão de simplicidade, apenas são mencionadas opções seletivas:

* Certifique-se de que é um administrador na máquina em que está a instalar o AAD Connect. Inicie sessão na máquina com as mesmas credenciais de administrador.

* Deixe todas as opções predefinidos na página seguinte, com exceção de "Use um Servidor SQL existente", se pretender utilizar o Servidor SQL existente. Aqui estão [mais detalhes](./how-to-connect-install-custom.md) sobre como usar opções de instalação personalizadas. 

    ![Utilizar o servidor SQL existente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Na página seguinte, escolha a opção "Criar uma nova conta AD", para evitar quaisquer problemas de permissão com a conta existente.

    ![Conta de Floresta AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemas Comuns**

* [Problemas de conectividade com o Ative Directory no local.](./reference-connect-adconnectivitytools.md)

* [Problemas de conectividade com o Azure Ative Directory online](./tshoot-connect-connectivity.md).

* [Problemas de permissão com o Ative Directory no local.](./how-to-connect-configure-ad-ds-connector-account.md)

## <a name="recommended-documents"></a>**Documentos Recomendados**
* [Pré-requisitos do Azure AD Connect](./how-to-connect-install-prerequisites.md)
* [Selecionar o tipo de instalação a utilizar para o Azure AD Connect](./how-to-connect-install-select-installation.md)
* [Introdução ao Azure AD Connect com as definições rápidas](./how-to-connect-install-express.md)
* [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)
* [Azure AD Connect: atualizar de uma versão anterior para a mais recente](./how-to-upgrade-previous-version.md)
* [Azure AD Connect: O que é o servidor de encenação?](./plan-connect-topologies.md#staging-server)
* [O que é o Módulo ADConnectivityTool do PowerShell?](./how-to-connect-adconnectivitytools.md)

## <a name="next-steps"></a>Passos seguintes
- [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md).
- [O que é a identidade híbrida?](whatis-hybrid-identity.md)