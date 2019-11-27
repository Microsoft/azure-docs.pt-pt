---
title: Threat Modeling Tool releases-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Documentando as notas de versão da ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2019
ms.author: jegeib
ms.openlocfilehash: ba18989b72f0c3f44099031a6949acc54ce41db0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233834"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Versão de atualização da Threat Modeling Tool 7.1.61015.1 - 16/10/2019

A versão 7.1.61015.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em outubro de 16 2019 e contém as seguintes alterações:

- Aprimoramentos de acessibilidade
- Correções de erros
- Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure

## <a name="notable-bug-fixes"></a>Correções de bugs notáveis

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Compatibilidade com versões anteriores aprimorada com arquivos criados em "Threat Modeling Tool 2016"

Vários bugs relacionados à abertura ou exibição de arquivos de modelo de ameaça criados no "Threat Modeling Tool 2016" foram corrigidos.

## <a name="feature-enhancements"></a>Aprimoramentos de recursos

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure

Novos estênceis para aplicativos lógicos do Azure e Data Explorer do Azure foram adicionados ao estêncil do Azure junto com suas ameaças e mitigações associadas.

![Aplicativos lógicos do Azure e estênceis do Azure Data Explorer](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados a valores de prioridade fora dos intervalos esperados

Alguns clientes relataram o recebimento da seguinte mensagem de erro ao abrir arquivos criados no "Threat Modeling Tool 2016" ou modelos personalizados:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Esse problema está em investigação

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - Uma conexão com a Internet é necessária para receber atualizações para a ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação do Threat Modeling Tool está localizada em [docs.Microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre como usar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passos Seguintes

Baixe a versão mais recente do [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
