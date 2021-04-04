---
title: Microsoft Threat Modeling Tool release 10/16/2019 - Azure
description: Documentando as notas de lançamento para a versão da ferramenta de modelação de ameaças 7.1.61015.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 761ad45496e799a555b60480ff575b9d8f30e984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94516990"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Versão de atualização da Threat Modeling Tool 7.1.61015.1 - 16/10/2019

A versão 7.1.61015.1 da Ferramenta de Modelação de Ameaças da Microsoft (TMT) foi lançada a 16 de outubro de 2019 e contém as seguintes alterações:

- Melhorias de acessibilidade
- Correções de erros
- Novos stencils para apps Azure Logic Apps e Azure Data Explorer

## <a name="notable-bug-fixes"></a>Notáveis correções de bugs

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Melhor compatibilidade para trás com ficheiros criados em "Ferramenta de Modelação de Ameaças 2016"

Vários bugs relacionados com a abertura ou exibição de ficheiros de modelos de ameaça criados em "Threat Modeling Tool 2016" foram corrigidos.

## <a name="feature-enhancements"></a>Melhorias de recursos

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Novos stencils para apps Azure Logic Apps e Azure Data Explorer

Novos stencils para Azure Logic Apps e Azure Data Explorer foram adicionados ao Azure Stencil juntamente com as suas ameaças e mitigações associadas.

![Azure Logic Apps e Azure Data Explorer Stencils](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados com valores prioritários fora das gamas esperadas

Alguns clientes relataram ter recebido a seguinte mensagem de erro ao abrir ficheiros criados na "Ferramenta de Modelação de Ameaças 2016" ou modelos personalizados:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Esta questão está sob investigação.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET Necessária
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](./threat-modeling-tool.md), e inclui informações [sobre a utilização da ferramenta.](./threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).