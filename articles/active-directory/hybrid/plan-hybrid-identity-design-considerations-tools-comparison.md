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
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80811092"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação das ferramentas de integração de diretórios de identidade híbrida
Ao longo dos anos, as ferramentas de integração de diretórios cresceram e evoluíram.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) e [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) ainda são suportados e permitem principalmente a sincronização entre sistemas no local.   O [Conector AD FIM Windows Azure](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) é suportado tanto no FIM como no MIM, mas não é recomendado para novas implementações - os clientes com fontes no local, como notas ou SAP HCM, devem utilizar o MIM para povoar serviços de domínio de diretório ativo (AD DS) e, em seguida, também utilizar a sincronização Azure AD Connect ou a azure AD Connect para sincronizar de AD DS a Azure AD.
- [O Azure AD Connect sync](how-to-connect-sync-whatis.md) incorpora os componentes e funcionalidades previamente lançados em DirSync e Azure AD Sync, para sincronização entre as florestas de DS AD e Azure AD.  
- [O Azure AD Connect cloud provisioning](../cloud-provisioning/what-is-cloud-provisioning.md) é um novo agente da Microsoft para sincronizar de AD DS a Azure AD, útil para cenários como fusão e aquisição onde as florestas de AD da empresa adquiridas estão isoladas das florestas de AD da empresa-mãe.

Para saber mais sobre as diferenças entre a sincronização Azure AD Connect e o provisionamento em nuvem Azure AD Connect, consulte o artigo [O que é a azure AD Connect provisioning cloud?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

