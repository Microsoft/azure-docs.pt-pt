---
title: Microsoft Threat Modeling Tool lança 4/9/2019
titleSuffix: Azure
description: Documentando as notas de lançamento para a ferramenta de modelação de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 59d385ba7de5bf7bceae4dc8ddadbca813046094
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269715"
---
# <a name="threat-modeling-tool-update-release-71604081---492019"></a>Lançamento da atualização da Ferramenta de Modelação de Ameaças 7.1.60408.1 - 4/9/2019

A versão 7.1.60408.1 da Microsoft Threat Modeling Tool (TMT) foi lançada a 9 de abril de 2019 e contém as seguintes alterações:

- Novos Stencils para Cofre chave Azure e Gestor de Tráfego Azure
- Número da versão TMT é agora mostrado no ecrã principal
- Os links de suporte foram atualizados
- Correções de erros

## <a name="feature-changes"></a>Alterações de funcionalidades

### <a name="new-stencils-for-azure-key-vault-and-azure-traffic-manager"></a>Novos Stencils para Cofre chave Azure e Gestor de Tráfego Azure

![Stencil do cofre de chaves azure](./media/threat-modeling-tool-releases-71604081/tmt_keyvault_trafficmanager.PNG)

Novos stencils e ameaças para o Azure Key Vault e o Azure Traffic Manager foram adicionados ao conjunto de stencil Azure. Ao abrir modelos baseados no conjunto de stencil Azure, os utilizadores serão solicitados a atualizar o modelo associado ao modelo. Atualizar um modelo baseado no conjunto de stencil Azure também pode ser iniciado manualmente utilizando o comando "Modelo de Aplicação" no menu "File" e reaplicando o mais recente ficheiro Azure Cloud Services.tb7.

### <a name="tmt-version-number-is-now-shown-on-the-home-screen"></a>Número da versão TMT é agora mostrado no ecrã principal

A versão cliente da Ferramenta de Modelação de Ameaças é agora mostrada no ecrã principal da aplicação de facilidade de acesso.

![Stencil do cofre de chaves azure](./media/threat-modeling-tool-releases-71604081/tmt_version.PNG)

### <a name="support-links-have-been-updated"></a>Os links de suporte foram atualizados

Todas as ligações de suporte dentro da ferramenta foram atualizadas para direcionar os utilizadores para [tmtextsupport@microsoft.com](mailto:tmtextsupport@microsoft.com) em vez de um fórum MSDN.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Atualização](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) de Aniversário ou mais tarde
- versão .NET necessária
  - [.Rede 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação para a Ferramenta de Modelação de Ameaças está localizada na [docs.microsoft.com,](threat-modeling-tool.md)e inclui informações [sobre a utilização da ferramenta.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da Ferramenta de [Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
