---
title: Azure AD Connect no Microsoft Cloud Alemanha
description: O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para as aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD.
keywords: introdução ao Azure AD Connect, descrição geral do Azure AD Connect, o que é o Azure AD Connect, instalar o Active Directory, Alemanha, Floresta Negra
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: e7f2ae5e62c5b310a1491caa362807a561f6f961
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490180"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect no Microsoft Cloud Alemanha - Pré-visualização Pública
## <a name="introduction"></a>Introdução
O Azure AD Connect fornece sincronização entre o Active Directory no local e o Azure Active Directory.
Atualmente, muitos dos cenários no [Microsoft Cloud Alemanha](https://azure.microsoft.com/global-infrastructure/germany/
) têm de ser executados pelo operador. Ao utilizar o Microsoft Cloud Alemanha, deve estar ciente das seguintes informações:

* Os seguintes URLs têm de ser abertos num servidor proxy para que a sincronização seja feita com êxito:
  
  * *.microsoftonline.de
  * \*.windows.net
  * * Listas de Revogação de Certificados
* Quando inicia sessão no seu diretório do Azure AD, tem de utilizar uma conta no domínio onmicrosoft.de.

 
## <a name="download"></a>Transferência
Pode transferir o Azure AD Connect a partir do painel do Azure AD Connect no portal.  Utilize as instruções abaixo para localizar o painel do Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>O Painel do Azure AD Connect
Uma vez que entrar no portal do Azure:

1. Vá para Procurar
2. Selecione Azure Active Directory
3. Em seguida, selecione Azure AD Connect

Verá esses detalhes:

![Painel do Azure AD Connect](./media/reference-connect-germany/germany1.png)

A tabela seguinte descreve as funcionalidades apresentadas no painel.

| Título | Descrição |
| --- | --- |
| ESTADO DE SINCRONIZAÇÃO |Permite-lhe saber se a sincronização está ativada ou desativada. |
| ÚLTIMA SINCRONIZAÇÃO |A última vez que uma sincronização bem-sucedida foi concluída. |
| DOMÍNIOS FEDERADOS |Mostra o número de domínios federados atualmente configurados. |

## <a name="installation"></a>Instalação
Para instalar o Azure AD Connect, pode utilizar a documentação [aqui](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Funcionalidades avançadas e Informações Adicionais
Para obter informações adicionais sobre as definições personalizadas ou configurações avançadas, aceda a [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md). Esta página fornece informações e hiperligações para orientações adicionais.

