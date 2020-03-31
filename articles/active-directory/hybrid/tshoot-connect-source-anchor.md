---
title: 'Azure AD Connect: Problemas de origem durante a instalação Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a âncora de origem durante a instalação.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114160"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Problemas de resolução de problemas problemas de âncora durante a instalação
Este artigo explica as diferentes questões relacionadas com a âncora de origem que podem ocorrer durante a instalação e oferece formas de resolver estes problemas.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de Origem Inválida no Diretório Ativo Azure

### <a name="custom-installation"></a>Instalação Personalizada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Diretório Ativo Azure. Se a política existir no Azure Ative Directory, o Azure AD Connect aplica a mesma política a menos que seja ultrapassado pelo cliente. O feiticeiro informa-o de que atributo foi lido. Além disso, o assistente avisa se tentar anular a política de âncora de origem.

Durante esta operação de leitura, é possível que a política de âncora de origem no Diretório Ativo Azure seja inesperada. Neste caso, o Azure AD Connect não sabe qual a âncora de origem a utilizar e necessita de substituição manual.</br>
![inesperado](media/tshoot-connect-source-anchor/source1.png)

Para resolver este problema, pode substituir manualmente a âncora de origem selecionando um atributo específico. Proceda a esta opção se e apenas se tiver a certeza de qual atributo a selecionar. Se não tiver a certeza, contacte o suporte da [Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se alterar a política de âncora de origem, pode quebrar a associação entre os seus utilizadores no local e os seus recursos Azure associados.</br>
![inesperado](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação Express
Durante a instalação expressa, o Azure AD Connect lê a política de âncora de origem do Diretório Ativo Azure. Se a política existir no Diretório Ativo Azure, o Azure AD Connect aplica a mesma política. Não há opção para fazer a sobreposição manual.

Durante esta operação de leitura, é possível que a política de âncora de origem no Diretório Ativo Azure seja inesperada. Neste caso, o Azure AD Connect não sabe qual deve ser a âncora de origem.</br>
![inesperado](media/tshoot-connect-source-anchor/source3.png)

Para resolver este problema, é necessário reinstalar utilizando o modo personalizado e substituir manualmente a âncora de origem selecionando um atributo específico. Proceda a esta opção se e apenas se tiver a certeza de qual atributo a selecionar. Se não tiver a certeza, contacte o suporte da [Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se alterar a política de âncora de origem, pode quebrar a associação entre os seus utilizadores no local e os seus recursos Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválida no motor sync
Durante a instalação, é possível que o Azure AD Connect tente configurar o motor sincronizado utilizando uma âncora de origem inválida. Esta operação é provavelmente um problema de produto e a instalação do Azure AD Connect falhará. Contacte o [suporte da Microsoft](https://support.microsoft.com/contactus/) se se encontrar com este problema.</br>
![inesperado](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).