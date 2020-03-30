---
title: Microsoft Threat Modeling Tool lança 10/16/2019 - Azure
description: Documentando as notas de lançamento para a ferramenta de modelação de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552054"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Versão de atualização da Threat Modeling Tool 7.1.61015.1 - 16/10/2019

A versão 7.1.61015.1 da Microsoft Threat Modeling Tool (TMT) foi lançada a 16 de outubro de 2019 e contém as seguintes alterações:

- Melhorias de acessibilidade
- Correções de erros
- Novos stencils para Apps Azure Logic e Azure Data Explorer

## <a name="notable-bug-fixes"></a>Correções notáveis de bugs

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Melhora da compatibilidade com ficheiros criados em "Threat Modeling Tool 2016"

Foram corrigidos vários bugs relacionados com a abertura ou exibição de ficheiros de modelos de ameaça criados em "Threat Modeling Tool 2016".

## <a name="feature-enhancements"></a>Melhorias de recursos

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Novos stencils para Apps Azure Logic e Azure Data Explorer

Foram adicionados novos stencils para as Aplicações lógicas azure e o Azure Data Explorer ao Azure Stencil, juntamente com as suas ameaças e mitigações associadas.

![Aplicativos Azure Logic e Stencils do Explorador de Dados Azure](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados com valores prioritários fora das faixas previstas

Alguns clientes relataram ter recebido a seguinte mensagem de erro ao abrir ficheiros criados na "Ferramenta de Modelação de Ameaças 2016" ou modelos personalizados:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Esta questão está sob investigação.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Atualização](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) de Aniversário ou mais tarde
- versão .NET necessária
  - [.Rede 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação para a Ferramenta de Modelação de Ameaças está localizada na [docs.microsoft.com,](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre a utilização da ferramenta.](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da Ferramenta de [Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
