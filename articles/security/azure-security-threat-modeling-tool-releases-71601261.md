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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57874577"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Versão de atualização da Threat Modeling Tool 7.1.60126.1 - 29/01/2019

Versão 7.1.60126.1 da ferramenta de modelagem de ameaças da Microsoft foi lançado 2019 de 29 de Janeiro e contém as seguintes alterações:

- A versão mínima necessária do .NET foi aumentada para [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- A versão mínima necessária do Windows foi aumentada para [Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) devido à dependência do .NET.
- Uma funcionalidade de alternância de validação de modelo foi adicionada ao menu de opções da ferramenta.
- Várias ligações nas propriedades de ameaças foram atualizadas.
- UX pequenas alterações ao ecrã principal da ferramenta.
- A ferramenta de modelagem de ameaças agora herda as definições de TLS do sistema operativo anfitrião e é suportada em ambientes que necessitam de TLS 1.2 ou superior.

## <a name="feature-changes"></a>Alterações de funcionalidade

### <a name="model-validation-option"></a>Opção de validação de modelo

Com base nos comentários dos clientes, uma opção foi adicionada à ferramenta para ativar ou desativar a validação de modelo. Anteriormente, se seu modelo usado um fluxo de dados unidirecional único entre dois objetos, poderá ter recebido uma mensagem de erro no quadro de mensagens que diz: ObjectsName requer pelo menos um 'Any'. Desativar a validação de modelo irá impedir que esses avisos de mostrar na exibição.

A opção para ativar a validação de modelo e desativar pode ser encontrada no arquivo -> Definições -> menu de opções. O valor predefinido para esta definição está desativado.

![Opção de validação de modelo](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - Uma ligação à Internet é necessária para receber atualizações para a ferramenta, bem como os modelos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="unsupported-systems"></a>Sistemas sem suporte

#### <a name="issue"></a>Problema

Os utilizadores de sistemas do Windows 10 que sejam não é possível instalar o .NET 4.7.1 ou posterior, como o Windows 10 Enterprise LTSB (versão 1507), será não é possível abrir a ferramenta após a atualização. Estas versões mais antigas do Windows já não são as plataformas suportadas para a ferramenta de modelagem de ameaças e não devem instalar a atualização mais recente.

#### <a name="workaround"></a>Solução

Os utilizadores do Windows 10 Enterprise LTSB (versão 1507) que têm instalado a atualização mais recente podem reverter para a versão anterior da ferramenta de modelagem de ameaças a caixa de diálogo de desinstalação de aplicações e funcionalidades.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- Documentação para a ferramenta de modelagem de ameaças está localizada em [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)e inclui informações [sobre como utilizar a ferramenta](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Passos Seguintes

Baixe a versão mais recente dos [ferramenta de modelagem de ameaças na Microsoft](https://aka.ms/threatmodelingtool).
