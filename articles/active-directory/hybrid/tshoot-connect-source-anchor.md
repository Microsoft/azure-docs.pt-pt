---
title: 'Azure AD Connect: Problemas de âncora de resolução de problemas durante a instalação Microsoft Docs'
description: Este tópico fornece passos para como resolver problemas com a âncora de origem durante a instalação.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb82eb2d8e23daec0ddb8856b713c1aa051f25c5
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355952"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Resolução de problemas Problemas de âncora de origem durante a instalação
Este artigo explica as diferentes questões relacionadas com âncora de origem que podem ocorrer durante a instalação e oferece formas de resolver estes problemas.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Âncora de origem inválida no Diretório Ativo Azure

### <a name="custom-installation"></a>Instalação personalizada

Durante a instalação personalizada, o Azure AD Connect lê a política de âncora de origem do Azure Ative Directory. Se a apólice existir no Azure Ative Directory, o Azure AD Connect aplica a mesma política a menos que seja ultrapassada pelo cliente. O assistente informa-o sobre qual atributo foi lido. Além disso, o assistente avisa se tentar anular a política de ancoragem de origem.

Durante esta operação de leitura, é possível que a política de âncora de origem no Diretório Ativo Azure seja inesperada. Neste caso, o Azure AD Connect não sabe qual a âncora de origem a utilizar e precisa de sobreposição manual.</br>
![inesperado](media/tshoot-connect-source-anchor/source1.png)

Para resolver este problema, pode substituir manualmente a âncora de origem selecionando um atributo específico. Proceda com esta opção se e somente se tiver a certeza de qual atributo a selecionar. Se não tiver a certeza, contacte o [suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se alterar a política de âncora de origem, pode quebrar a associação entre os seus utilizadores no local e os seus recursos Azure associados.</br>
![inesperado](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Instalação Express
Durante a instalação expressa, o Azure AD Connect lê a política de âncora de origem do Azure Ative Directory. Se a política existir no Azure Ative Directory, o Azure AD Connect aplica a mesma política. Não há opção para fazer sobreposição manual.

Durante esta operação de leitura, é possível que a política de âncora de origem no Diretório Ativo Azure seja inesperada. Neste caso, o Azure AD Connect não sabe qual deve ser a âncora de origem.</br>
![inesperado](media/tshoot-connect-source-anchor/source3.png)

Para resolver este problema, é necessário reinstalar utilizando o modo personalizado e substituir manualmente a âncora de origem selecionando um atributo específico. Proceda com esta opção se e somente se tiver a certeza de qual atributo a selecionar. Se não tiver a certeza, contacte o [suporte da Microsoft](https://support.microsoft.com/contactus/) para obter orientação. Se alterar a política de âncora de origem, pode quebrar a associação entre os seus utilizadores no local e os seus recursos Azure associados.

### <a name="invalid-source-anchor-in-sync-engine"></a>Âncora de origem inválida no motor de sincronização
Durante a instalação, é possível que o Azure AD Connect tente configurar o motor de sincronização utilizando uma âncora de origem inválida. Esta operação é provavelmente um problema de produto e a instalação do Azure AD Connect falhará. Contacte [o suporte da Microsoft](https://support.microsoft.com/contactus/) se tiver este problema.</br>
![inesperado](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
