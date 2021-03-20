---
title: Lista de compatibilidades de federação do Azure AD
description: Esta página tem fornecedores de identidade não-Microsoft que podem ser usados para implementar um único s-on.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89661830"
---
# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidades de federação do Azure AD
O Azure Ative Directory fornece uma única segurança de acesso a aplicações para o Microsoft 365 e outros serviços Microsoft Online para implementações híbridas e apenas na nuvem sem necessitar de qualquer solução de terceiros. O Microsoft 365, tal como a maioria dos serviços online da Microsoft, está integrado com o Azure Ative Directory para serviços de diretório, autenticação e autorização. O Azure Ative Directory também fornece um único sign-on a milhares de aplicações SaaS e aplicações web no local. Consulte a galeria de [aplicações](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) do Azure Ative Directory para aplicações saaS suportadas. 

## <a name="idp-validation"></a>Validação do IDP
Se a sua organização utilizar uma solução de federação de terceiros, pode configurar um único sinal para os seus utilizadores do Ative Directory com serviços Microsoft Online, como o Microsoft 365, desde que a solução de federação de terceiros seja compatível com o Azure Ative Directory.  Para questões de compatibilidade, contacte o seu fornecedor de identidade.  Se quiser ver uma lista de fornecedores de identidade que já foram testados para compatibilidade com a Azure AD, pela Microsoft, consulte [os docs de compatibilidade do fornecedor de identidade Azure AD](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>A Microsoft já não fornece testes de validação a fornecedores independentes de identidade para compatibilidade com o Azure Ative Directory. Se pretender testar o seu produto para interoperabilidade, consulte estas [diretrizes.](https://www.microsoft.com/download/details.aspx?id=56843) 

## <a name="next-steps"></a>Passos Seguintes

- [Integrar os diretórios no local com o Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect e federação](how-to-connect-fed-whatis.md)
