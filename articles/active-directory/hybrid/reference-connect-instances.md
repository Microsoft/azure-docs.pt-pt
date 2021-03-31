---
title: 'Azure AD Connect: Sync | Microsoft Docs'
description: Esta página documenta considerações especiais para instâncias Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4d8b0a33763a967550453d8a205258f7583084
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90015267"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerações especiais para casos
O Azure AD Connect é mais utilizado com o exemplo mundial de Azure AD e Microsoft 365. Mas há também outros casos e estes têm requisitos diferentes para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
A [Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) é uma nuvem soberana operada por um administrador de dados alemão.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Listas de Revogação de Certificados |

Ao entrar no seu inquilino AZure AD, deve utilizar uma conta no domínio onmicrosoft.de.

Funcionalidades atualmente não presentes na Microsoft Cloud Germany:

* **A gravação da palavra-passe** está disponível para pré-visualização com a versão 1.1.570.0 e depois do Azure AD Connect.
* Outros serviços Azure AD Premium não estão disponíveis.

## <a name="microsoft-azure-government"></a>Governo microsoft Azure
A [nuvem do Governo do Microsoft Azure](https://azure.microsoft.com/features/gov/) é uma nuvem para o governo dos EUA.

Esta nuvem foi apoiada por lançamentos anteriores de DirSync. A partir da construção 1.1.180 do Azure AD Connect, a próxima geração da nuvem é suportada. Esta geração está a usar pontos finais baseados apenas nos EUA e tem uma lista diferente de URLs para abrir no seu servidor proxy.

| URLs para abrir no servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (Requerida para deteção automática de inquilinos do Governo Azure) |
| \*.gov.us.microsoftonline.com |
| +Listas de Revogação de Certificados |

> [!NOTE]
> A partir da versão 1.1.647.0 do Azure AD Connect, a definição do valor AzureInstance no registo já não é necessária desde que a *.windows.net esteja aberta no servidor(s) do seu proxy. No entanto, para clientes que não permitem a conectividade da Internet a partir dos seus servidores Azure AD Connect, pode ser utilizada a seguinte configuração manual.

### <a name="manual-configuration"></a>Configuração manual

Os seguintes passos de configuração manual são utilizados para garantir que o Azure AD Connect utiliza pontos finais de sincronização do Governo Azure.

1. Inicie a instalação Azure AD Connect.
2. Quando vir a primeira página onde deve aceitar o EULA, não continue, mas deixe o assistente de instalação em funcionamento.
3. Inicie o regedit e altere a chave de registo `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` para o valor `4` .
4. Volte para o assistente de instalação Azure AD Connect, aceite o EULA e continue. Durante a instalação, certifique-se de que utiliza o caminho de instalação de **configuração personalizada** (e não a instalação Do Expresso), e continue a instalação como de costume.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
