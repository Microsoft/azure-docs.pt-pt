---
title: Lançamento da ferramenta de modelação de ameaças da Microsoft 4/9/2019
titleSuffix: Azure
description: Documentando as notas de lançamento para a versão da ferramenta de modelação de ameaças 7.1.60408.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 8d24eb868a13e7b2d9ff3d2b625302c1c1fcb908
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318012"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Lançamento da atualização da ferramenta de modelação de ameaças 7.1.60408.1 - 4/9/2019

A versão 7.1.60408.1 da Ferramenta de Modelação de Ameaças da Microsoft (TMT) foi lançada a 9 de abril de 2019 e contém as seguintes alterações:

- Novos Stencils para Azure Key Vault e Azure Traffic Manager
- O número da versão TMT é agora mostrado no ecrã principal
- Os links de suporte foram atualizados
- Correções de erros

## <a name="feature-changes"></a>Alterações de recursos

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Novos Stencils para Azure Key Vault e Azure Traffic Manager

![Stencil de cofre de chave Azure](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Novos stencils e ameaças para Azure Key Vault e Azure Traffic Manager foram adicionados ao conjunto de stencil Azure. Ao abrir os modelos com base no conjunto de stencil Azure, os utilizadores serão solicitados a atualizar o modelo associado ao modelo. A atualização de um modelo baseado no conjunto de stencil Azure também pode ser iniciada manualmente utilizando o comando "Apply Template" no menu "Arquivo" e reaplicando o mais recente ficheiro Azure Cloud Services.tb7.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>O número da versão TMT é agora mostrado no ecrã principal

A versão cliente da Ferramenta de Modelação de Ameaças é agora mostrada no ecrã inicial da aplicação de facilidade de acesso.

![Stencil de cofre de chave Azure](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Os links de suporte foram atualizados

Todos os links de suporte dentro da ferramenta foram atualizados para direcionar os utilizadores para [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) um fórum MSDN.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET Necessária
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](threat-modeling-tool.md), e inclui informações [sobre a utilização da ferramenta.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
