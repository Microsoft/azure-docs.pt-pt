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
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 6992ac58ebf8976714e5a478dd7b886efab2a4b7
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934790"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Versão de atualização do Threat Modeling Tool 7.1.60408.1-4/9/2019

A versão 7.1.60408.1 do Microsoft Threat Modeling Tool (TMT) foi lançada em abril de 9 2019 e contém as seguintes alterações:

- Novos estênceis para Azure Key Vault e o Gerenciador de tráfego do Azure
- O número de versão do TMT agora é mostrado na tela inicial
- Links de suporte foram atualizados
- Correções de erros

## <a name="feature-changes"></a>Alterações de recurso

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Novos estênceis para Azure Key Vault e o Gerenciador de tráfego do Azure

![Azure Key Vault estêncil](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Novos estênceis e ameaças para Azure Key Vault e o Gerenciador de tráfego do Azure foram adicionados ao conjunto de estênceis do Azure. Ao abrir modelos com base no conjunto do estêncil do Azure, os usuários serão solicitados a atualizar o modelo associado ao modelo. A atualização de um modelo baseado no conjunto de estênceis do Azure também pode ser iniciada manualmente usando o comando "aplicar modelo" no menu "arquivo" e reaplicando o arquivo. tb7 mais recente dos serviços de nuvem do Azure.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>O número de versão do TMT agora é mostrado na tela inicial

A versão do cliente do Threat Modeling Tool agora é mostrada na tela inicial do aplicativo do para facilitar o acesso.

![Azure Key Vault estêncil](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Links de suporte foram atualizados

Todos os links de suporte dentro da ferramenta foram atualizados para direcionar [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) os usuários para o em vez de um fórum do MSDN.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - Uma conexão com a Internet é necessária para receber atualizações para a ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação do Threat Modeling Tool está localizada em [docs.Microsoft.com](threat-modeling-tool.md)e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passos seguintes

Baixe a versão mais recente do [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
