---
title: Microsoft Threat Modeling Tool lança 03/22/2020 - Azure
description: Documentando as notas de lançamento para a ferramenta de modelação de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80146865"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Lançamento da atualização da Ferramenta de Modelação de Ameaças 7.3.00316.1 - 03/22/2020

A versão 7.3.00316.1 da Microsoft Threat Modeling Tool (TMT) foi lançada a 22 de março de 2020 e contém as seguintes alterações:

- Melhorias de acessibilidade
- Correções de erros
- Nova funcionalidade DiagramReader

## <a name="notable-bug-fixes"></a>Correções notáveis de bugs

### <a name="exporting-the-threat-list-to-csv"></a>Exportação da lista de ameaças para a CSV

A função de exportação para csv foi selecionar inconsistentemente quais os campos da lista de ameaças que seriam exportados. Agora todos os campos da lista de ameaças serão exportados para o ficheiro CSV. 

### <a name="ux-bugs"></a>Insetos UX

- Os menus de ajuda no fluxo de trabalho primário (criar/abrir/analisar) e a experiência do editor do modelo têm agora opções de menu consistentes.
- A barra de pesquisa no painel de stencils tem agora um cursor padrão e foram adicionadas etiquetas apropriadas.

## <a name="new-features"></a>Novas funcionalidades

### <a name="diagramreader-feature-has-been-added"></a>A função DiagramReader foi adicionada

Uma nova funcionalidade DiagramReader foi adicionada no menu principal enquanto um modelo está aberto. Esta funcionalidade converterá a representação gráfica do modelo numa narrativa baseada em texto. 

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas operativos suportados:
  - [Microsoft Windows 10 Atualização](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) de Aniversário ou mais tarde
- versão .NET necessária:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos adicionais:
  - Uma ligação à Internet para receber atualizações da ferramenta, bem como modelos

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação para a Ferramenta de Modelação de Ameaças está localizada na [docs.microsoft.com,](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre a utilização da ferramenta.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da Ferramenta de [Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
