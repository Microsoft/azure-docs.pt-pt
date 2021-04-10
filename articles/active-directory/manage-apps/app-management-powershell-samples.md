---
title: Amostras powerShell para gestão de aplicações de diretório ativo Azure
description: Estas amostras PowerShell são usadas para apps que gere no seu inquilino Azure Ative Directory. Você pode usar estes scripts de amostra para encontrar informações de expiração sobre segredos e certificados.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: f5f7ec8245a43440a400b9ca6b55bf1093eb62cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102636193"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Exemplos de PowerShell de Diretório Ativo Azure para Gestão de Aplicações

A tabela seguinte inclui links para exemplos de scripts PowerShell para Azure AD Application Management. Estas amostras requerem:
- [A AzureAD V2 PowerShell para módulo gráfico](/powershell/azure/active-directory/install-adv2) ou,
- [A versão de pré-visualização do módulo AzureAD V2 para visualização do módulo gráfico,](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)salvo indicação em contrário.

Para obter mais informações sobre os cmdlets utilizados nestas [amostras,](/powershell/module/azuread/#applications)consulte aplicações .

| Ligação | Description |
|---|---|
|**Scripts de Gestão de Aplicações**||
| [Segredos de exportação e certificados (registos de aplicações)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Segredos de exportação e certificados para registos de aplicações no inquilino do Azure Ative Directory. |
| [Segredos de exportação e certs (aplicações empresariais)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Segredos de exportação e certificados para aplicações empresariais no inquilino do Azure Ative Directory. |
| [Exportar segredos e certificados de expiração](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportar registos de aplicativos com segredos e certificados caducados e seus proprietários no inquilino do Azure Ative Directory. |
| [Segredos de exportação e certificados que expiram para além da data exigida](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportar registos de aplicativos com segredos e certificados que expiram para além da data exigida no inquilino do Azure Ative Directory. Isto utiliza o fluxo de Client_Credentials de Óau não interativo. |
