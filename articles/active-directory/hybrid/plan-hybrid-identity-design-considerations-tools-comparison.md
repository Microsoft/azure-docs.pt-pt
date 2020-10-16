---
title: 'Identidade híbrida: comparação das ferramentas de integração de diretórios | Microsoft Docs'
description: Nesta página, encontra uma tabela abrangente que compara as várias ferramentas de integração de diretórios que podem ser utilizadas para a integração de diretórios.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9c8f5a75fea6ddd85026407fe4784c8a4589e33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278314"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação das ferramentas de integração de diretórios de identidade híbrida
Ao longo dos anos, as ferramentas de integração de diretórios cresceram e evoluíram.  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) e [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) ainda são suportados e permitem principalmente a sincronização entre sistemas no local.   O [Conector AD FIM Windows Azure](/previous-versions/mim/dn511001(v=ws.10)) é suportado tanto no FIM como no MIM, mas não é recomendado para novas implementações - os clientes com fontes no local, como notas ou SAP HCM, devem utilizar o MIM para povoar serviços de domínio de diretório ativo (AD DS) e, em seguida, também utilizar a sincronização Azure AD Connect ou a azure AD Connect para sincronizar de AD DS a Azure AD.
- [O Azure AD Connect sync](how-to-connect-sync-whatis.md) incorpora os componentes e funcionalidades previamente lançados em DirSync e Azure AD Sync, para sincronização entre as florestas de DS AD e Azure AD.  
- [O Azure AD Connect cloud provisioning](../cloud-provisioning/what-is-cloud-provisioning.md) é um novo agente da Microsoft para sincronizar de AD DS a Azure AD, útil para cenários como fusão e aquisição onde as florestas de AD da empresa adquiridas estão isoladas das florestas de AD da empresa-mãe.

Para saber mais sobre as diferenças entre a sincronização Azure AD Connect e o provisionamento em nuvem Azure AD Connect, consulte o artigo [O que é a azure AD Connect provisioning cloud?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).