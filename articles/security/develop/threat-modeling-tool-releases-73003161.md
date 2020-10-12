---
title: Microsoft Threat Modeling Tool release 03/22/2020 - Azure
description: Documentando as notas de lançamento para a versão da ferramenta de modelação de ameaças 7.3.00316.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 39963e9dadca0ee0be7bb8ad0ebf824c287bfee0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317910"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Lançamento da atualização da ferramenta de modelação de ameaças 7.3.00316.1 - 03/22/2020

A versão 7.3.00316.1 da Ferramenta de Modelação de Ameaças da Microsoft (TMT) foi lançada a 22 de março de 2020 e contém as seguintes alterações:

- Melhorias de acessibilidade
- Correções de erros
- Nova funcionalidade DiagramReader

## <a name="notable-bug-fixes"></a>Notáveis correções de bugs

### <a name="exporting-the-threat-list-to-csv"></a>Exportação da lista de ameaças para cSV

A exportação para a função CSV selecionava inconsistentemente quais os campos da lista de ameaças que seriam exportados. Agora todos os campos da lista de ameaças serão exportados para o ficheiro CSV. 

### <a name="ux-bugs"></a>Insetos UX

- Os menus de ajuda no fluxo de trabalho primário (criar/abrir/analisar) e a experiência do editor de modelo agora têm opções de menu consistentes.
- A barra de pesquisa no painel de stencils tem agora um cursor padrão e foram adicionadas etiquetas apropriadas.

## <a name="new-features"></a>Novas funcionalidades

### <a name="diagramreader-feature-has-been-added"></a>A funcionalidade DiagramReader foi adicionada

Uma nova funcionalidade DiagramReader foi adicionada no menu principal enquanto um modelo está aberto. Esta funcionalidade converterá a representação gráfica do modelo numa narrativa baseada em texto. 

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operativos suportados:
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET necessária:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos adicionais:
  - Uma ligação à Internet para receber atualizações para a ferramenta, bem como modelos

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), e inclui informações [sobre a utilização da ferramenta.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
