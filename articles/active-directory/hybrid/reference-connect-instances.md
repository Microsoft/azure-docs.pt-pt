---
title: 'Azure AD Connect: Instâncias do serviço de sincronização | Documentos da Microsoft'
description: Esta página documenta considerações especiais sobre instâncias do Azure AD.
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
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298818"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerações especiais sobre instâncias
O Azure AD Connect é geralmente utilizado com a instância de nível mundial do Azure AD e o Office 365. Mas também existem outras instâncias e estes têm diferentes requisitos para URLs e outras considerações especiais.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Alemanha
O [Microsoft Cloud Alemanha](https://www.microsoft.de/cloud-deutschland) é uma cloud soberana, operada por um dados na Alemanha.

| URLs para abrir o servidor proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| \+ Listas de revogação de certificados |

Quando inicia sessão no inquilino do Azure AD, tem de utilizar uma conta no domínio onmicrosoft.de.

Recursos atualmente não estão presentes na Microsoft Cloud Alemanha:

* **Repetição de escrita de palavra-passe** está disponível para pré-visualização do Azure AD Connect versão 1.1.570.0 e depois.
* Outros serviços do Azure AD Premium não estão disponíveis.

## <a name="microsoft-azure-government"></a>Microsoft Azure Governo
O [cloud do Microsoft Azure Government](https://azure.microsoft.com/features/gov/) é uma cloud para administração pública dos EUA.

Esta nuvem tem sido suportada por versões anteriores do DirSync. A partir de compilação 1.1.180 do Azure AD Connect, a próxima geração da cloud é suportada. Esta geração está a utilizar pontos de extremidade com base apenas dos EUA e ter uma lista diferente de URLs para abrir no seu servidor proxy.

| URLs para abrir o servidor proxy |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*. windows.net (necessário para deteção de inquilino do Azure Government automática) |
| \*.gov.us.microsoftonline.com |
| \+ Listas de revogação de certificados |

> [!NOTE]
> A partir do Azure AD Connect versão 1.1.647.0, definindo o valor de AzureInstance no registo já não é necessário fornecido que *. windows.net está aberta no seu servidor ou servidores de proxy. No entanto, para os clientes que não permitirem a conectividade de Internet a partir de seus servidores do Azure AD Connect, a seguinte configuração manual pode ser utilizada.

### <a name="manual-configuration"></a>Configuração manual

Os seguintes passos de configuração manual são utilizados para garantir que o Azure AD Connect utiliza pontos finais de sincronização do Azure Government.

1. Inicie a instalação do Azure AD Connect.
2. Quando vir a primeira página em que precisa para aceitar o EULA, não continue, mas deixe o Assistente de instalação em execução.
3. Inicie o regedit e alterar a chave de registo `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` para o valor `4`.
4. Vá para o Assistente de instalação do Azure AD Connect, aceite o EULA e continuar. Durante a instalação, certifique-se utilizar o **configuração personalizada** instalação caminho (e não com a instalação Express), em seguida, continuar a instalação como de costume.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
