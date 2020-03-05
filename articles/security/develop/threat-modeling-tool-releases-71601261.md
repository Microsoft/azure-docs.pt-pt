---
title: Microsoft Threat Modeling Tool lança 1/29/2019
titleSuffix: Azure
description: Documentando as notas de lançamento para a ferramenta de modelação de ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 7d0be8d7243331264c10a407e3d78370ea798928
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269780"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Versão de atualização da Threat Modeling Tool 7.1.60126.1 - 29/01/2019

A versão 7.1.60126.1 da Microsoft Threat Modeling Tool foi lançada a 29 de janeiro de 2019 e contém as seguintes alterações:

- A versão mínima exigida de .NET foi aumentada para [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- A versão mínima exigida do Windows foi aumentada para a Atualização de [Aniversário do Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) devido à dependência .NET.
- Foi adicionada uma função de alternância de validação de modelos ao menu Opções da ferramenta.
- Várias ligações nas Propriedades ameaçadoras foram atualizadas.
- Pequenas alterações ux no ecrã principal da ferramenta.
- A Ferramenta de Modelação de Ameaças herda agora as definições tLS do sistema operativo hospedeiro e é suportada em ambientes que requerem TLS 1.2 ou superior.

## <a name="feature-changes"></a>Alterações de funcionalidades

### <a name="model-validation-option"></a>Opção de validação de modelos

Com base no feedback do cliente, foi adicionada uma opção à ferramenta para ativar ou desativar a validação do modelo. Anteriormente, se o seu modelo usasse um único fluxo de dados unidirecional entre dois objetos, pode ter recebido uma mensagem de erro no quadro de Mensagens afirmando: O Nome dos Objetos requer pelo menos um 'Qualquer'. A validação do modelo incapacitante impedirá que estes avisos apareçam na vista.

A opção de alternar a validação do modelo dentro e fora pode ser encontrada no menu File&>Settings->Options. O valor predefinido para esta definição é desativado.

![Opção de Validação de Modelos](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Microsoft Windows 10 Atualização](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) de Aniversário ou mais tarde
- versão .NET necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou posterior
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="unsupported-systems"></a>Sistemas não suportados

#### <a name="issue"></a>Problema

Os utilizadores de sistemas Windows 10 que não consigam instalar .NET 4.7.1 ou posteriormente, como o Windows 10 Enterprise LTSB (versão 1507), não poderão abrir a ferramenta após a atualização. Estas versões mais antigas do Windows já não são plataformas suportadas para a Ferramenta de Modelação de Ameaças, e não devem instalar a mais recente atualização.

#### <a name="workaround"></a>Solução

Os utilizadores do Windows 10 Enterprise LTSB (versão 1507) que instalaram a mais recente atualização podem reverter para a versão anterior da Ferramenta de Modelação de Ameaças através do diálogo desinstalado em Apps & Features.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação para a Ferramenta de Modelação de Ameaças está localizada na [docs.microsoft.com,](threat-modeling-tool.md)e inclui informações [sobre a utilização da ferramenta.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da Ferramenta de [Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
