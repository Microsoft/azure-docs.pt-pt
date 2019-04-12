---
title: Ferramenta de modelagem de ameaças libera - ferramenta de modelagem de ameaças Microsoft - Azure | Documentos da Microsoft
description: Documentar as notas de versão para a ferramenta de modelagem de ameaças
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
ms.date: 04/03/2019
ms.author: jegeib
ms.openlocfilehash: 502c1e8a422eb9e74586e5a6820d5b12ec4ae2a4
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501907"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Versão de atualização do ferramenta de modelagem de ameaças 7.1.60408.1 - 4/9/2019

Versão 7.1.60408.1 da Microsoft Threat Modeling ferramenta (TMT) foi lançado em 9 de Abril de 2019 e contém as seguintes alterações:

- Estênceis novos para o Cofre de chaves do Azure e o Gestor de tráfego do Azure
- Número da versão TMT agora é mostrado no ecrã inicial
- Links para suporte foram atualizadas
- Correções de erros

## <a name="feature-changes"></a>Alterações de funcionalidade

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Estênceis novos para o Cofre de chaves do Azure e o Gestor de tráfego do Azure

![Estêncil do Cofre de chaves do Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Foram adicionadas estênceis novos e de ameaças para o Azure Key Vault e o Gestor de tráfego do Azure para o conjunto de estêncil do Azure. Ao abrir os modelos com base no conjunto de estêncil do Azure, serão pedido aos utilizadores atualizar o modelo associado ao modelo. Atualizar um modelo com base no conjunto de estêncil do Azure pode também ser iniciado manualmente utilizando o comando "Aplicar modelo" no menu "Arquivo" e reaplicando o ficheiro de Services.tb7 de Cloud do Azure mais recente.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Número da versão TMT agora é mostrado no ecrã inicial

A versão de cliente da ferramenta de modelagem de ameaças agora é mostrada no ecrã inicial do aplicativo de para facilidade de acesso.

![Estêncil do Cofre de chaves do Azure](./media/azure-security-threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Links para suporte foram atualizadas

Todas as ligações de suporte dentro da ferramenta foram atualizadas para direcionar os utilizadores para [ tmtextsupport@microsoft.com ](mailto:tmtextsupport@microsoft.com) em vez de um fórum MSDN.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária
  - [.NET 4.7.1](http://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - Uma ligação à Internet é necessária para receber atualizações para a ferramenta, bem como os modelos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- Documentação para a ferramenta de modelagem de ameaças está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre como utilizar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passos Seguintes

Baixe a versão mais recente dos [ferramenta de modelagem de ameaças na Microsoft](https://aka.ms/threatmodelingtool).