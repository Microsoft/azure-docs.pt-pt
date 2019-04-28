---
title: 'Azure AD Connect: Resolver problemas de âncora de origem durante a instalação | Documentos da Microsoft'
description: Este tópico fornece os passos sobre como resolver problemas com a âncora de origem durante a instalação.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114160"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Resolução de problemas de âncora de origem durante a instalação
Este artigo explica os diferentes problemas que possam ocorrer durante a instalação e ofertas formas de resolver esses problemas relacionados com a âncora de origem.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de origem inválido no Azure Active Directory

### <a name="custom-installation"></a>Instalação Personalizada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existe no Azure Active Directory, Azure AD Connect aplica-se a mesma política, a menos que ele é substituído pelo cliente. O assistente apresenta o atributo que foi lido. Além disso, o assistente avisa se tentar substituir a política de âncora de origem.

Durante esta operação de leitura, é possível que a política de âncora de origem no Azure Active Directory é inesperada. Neste caso, o Azure AD Connect não sabe que a âncora de origem para utilizar e tem de substituição manual.</br>
![unexpected](media/tshoot-connect-source-anchor/source1.png)

Para resolver este problema, é possível substituir manualmente a âncora de origem ao selecionar um atributo específico. Continuar com esta opção se, e apenas se, que tem a certeza de atributo que selecionar. Se não estiver certo, contacte [suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientações. Se alterar a política de âncora de origem, ele pode quebrar a associação entre os utilizadores no local e de recursos do Azure associados.</br>
![unexpected](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação rápida
Durante a instalação do express, o Azure AD Connect lê a política de âncora de origem do Azure Active Directory. Se a política existe no Azure Active Directory, Azure AD Connect aplica-se a mesma política. Não existe nenhuma opção para fazer a substituição manual.

Durante esta operação de leitura, é possível que a política de âncora de origem no Azure Active Directory é inesperada. Neste caso, o Azure AD Connect não sabe o que deve ser a âncora de origem.</br>
![unexpected](media/tshoot-connect-source-anchor/source3.png)

Para resolver este problema, terá de instalar novamente usando o modo personalizado e substituir manualmente a âncora de origem ao selecionar um atributo específico. Continuar com esta opção se, e apenas se, que tem a certeza de atributo que selecionar. Se não estiver certo, contacte [suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientações. Se alterar a política de âncora de origem, ele pode quebrar a associação entre os utilizadores no local e de recursos do Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválido no motor de sincronização
Durante a instalação, é possível do Azure AD Connect tentativas para configurar o motor de sincronização com uma âncora de origem inválido. Esta operação é muito provável que é um problema de produto e a instalação do Azure AD Connect irá falhar. Contacte [suporte da Microsoft](https://support.microsoft.com/contactus/) se depare com este problema.</br>
![unexpected](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).