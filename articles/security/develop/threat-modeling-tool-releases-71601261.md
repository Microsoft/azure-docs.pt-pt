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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: d9c91752a599debcd8fda6c8396141e6a92e0f06
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68928090"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Versão de atualização da Threat Modeling Tool 7.1.60126.1 - 29/01/2019

A versão 7.1.60126.1 do Microsoft Threat Modeling Tool foi lançada em janeiro de 29 2019 e contém as seguintes alterações:

- A versão mínima necessária do .NET foi aumentada para o [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- A versão mínima necessária do Windows foi aumentada para a [atualização de aniversário do Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) devido à dependência do .net.
- Um recurso de alternância de validação de modelo foi adicionado ao menu de opções da ferramenta.
- Vários links nas propriedades de ameaça foram atualizados.
- Alterações de UX secundárias na tela inicial da ferramenta.
- O Threat Modeling Tool agora herda as configurações de TLS do sistema operacional do host e tem suporte em ambientes que exigem o TLS 1,2 ou superior.

## <a name="feature-changes"></a>Alterações de recurso

### <a name="model-validation-option"></a>Opção de validação de modelo

Com base nos comentários do cliente, uma opção foi adicionada à ferramenta para habilitar ou desabilitar a validação do modelo. Anteriormente, se o modelo usava um único fluxo de dados unidirecional entre dois objetos, você pode ter recebido uma mensagem de erro no quadro mensagens informando: ObjectSid requer pelo menos um ' any '. Desabilitar a validação do modelo impedirá que esses avisos sejam mostrados na exibição.

A opção para ativar e desativar a validação do modelo pode ser encontrada no menu > opções configurações de > de arquivo. O valor padrão dessa configuração é desabilitado.

![Opção de validação de modelo](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou posterior
- Versão do .NET necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - Uma conexão com a Internet é necessária para receber atualizações para a ferramenta, bem como modelos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="unsupported-systems"></a>Sistemas sem suporte

#### <a name="issue"></a>Problema

Os usuários de sistemas Windows 10 que não puderem instalar o .NET 4.7.1 ou posterior, como o Windows 10 Enterprise LTSB (versão 1507), não poderão abrir a ferramenta após a atualização. Essas versões mais antigas do Windows não têm mais suporte para as plataformas do Threat Modeling Tool e não devem instalar a atualização mais recente.

#### <a name="workaround"></a>Solução

Os usuários do Windows 10 Enterprise LTSB (versão 1507) que instalaram a atualização mais recente podem reverter para a versão anterior do Threat Modeling Tool por meio da caixa de diálogo desinstalar em aplicativos & recursos.

## <a name="documentation-and-feedback"></a>Documentação e comentários

- A documentação do Threat Modeling Tool está localizada em [docs.Microsoft.com](threat-modeling-tool.md)e inclui informações [sobre como usar a ferramenta](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Passos seguintes

Baixe a versão mais recente do [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
