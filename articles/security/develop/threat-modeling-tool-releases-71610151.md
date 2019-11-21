---
title: Threat Modeling Tool Releases - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Documenting the release notes for the threat modeling tool
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
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool update release 7.1.61015.1 - 10/16/2019

Version 7.1.61015.1 of the Microsoft Threat Modeling Tool (TMT) was released on October 16 2019 and contains the following changes:

- Accessibility improvements
- Correções de erros
- New stencils for Azure Logic Apps and Azure Data Explorer

## <a name="notable-bug-fixes"></a>Notable bug fixes

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Improved backward compatibility with files created in "Threat Modeling Tool 2016"

Several bugs related to the opening or display of threat model files created in "Threat Modeling Tool 2016" have been fixed.

## <a name="feature-enhancements"></a>Feature enhancements

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>New stencils for Azure Logic Apps and Azure Data Explorer

New stencils for Azure Logic Apps and Azure Data Explorer were added to the Azure Stencil along with their associated threats and mitigations.

![Azure Logic Apps and Azure Data Explorer Stencils](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Errors related to priority values outside of the expected ranges

Some customers have reported receiving the following error message when opening files created in the "Threat Modeling Tool 2016" or custom templates:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

This issue is under investigation

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) or later
- .NET Version Required
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) or later
- Requisitos Adicionais
  - An Internet connection is required to receive updates to the tool as well as templates.

## <a name="documentation-and-feedback"></a>Documentation and feedback

- Documentation for the Threat Modeling Tool is located on [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), and includes information [about using the tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passos seguintes

Download the latest version of the [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
