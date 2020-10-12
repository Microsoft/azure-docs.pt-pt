---
title: Lançamento da ferramenta de modelação de ameaças da Microsoft 1/29/2019
titleSuffix: Azure
description: Leia as notas de lançamento da Ferramenta de Modelação de Ameaças da Microsoft lançadas em 1/29/2019. As notas incluem alterações de funcionalidades e questões conhecidas.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/25/2019
ms.openlocfilehash: 17147d412bd888cdd3cd270829ad6d6103867b34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87539055"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Versão de atualização da Threat Modeling Tool 7.1.60126.1 - 29/01/2019

A versão 7.1.60126.1 da Ferramenta de Modelação de Ameaças da Microsoft foi lançada a 29 de janeiro de 2019 e contém as seguintes alterações:

- A versão mínima exigida de .NET foi aumentada para [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262).
- A versão mínima exigida do Windows foi aumentada para a Atualização de Aniversário do [Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) devido à dependência .NET.
- Foi adicionada uma funcionalidade de alternância de validação do modelo ao menu Opções da ferramenta.
- Vários links nas Propriedades de Ameaça foram atualizados.
- Alterações ligeiras de UX no ecrã principal da ferramenta.
- A Ferramenta de Modelação de Ameaças herda agora as definições TLS do sistema operativo anfitrião e é suportada em ambientes que requerem TLS 1.2 ou superior.

## <a name="feature-changes"></a>Alterações de recursos

### <a name="model-validation-option"></a>Opção de validação do modelo

Com base no feedback do cliente, foi adicionada uma opção à ferramenta para ativar ou desativar a validação do modelo. Anteriormente, se o seu modelo usou um único fluxo de dados unidirecional entre dois objetos, pode ter recebido uma mensagem de erro no quadro de mensagens indicando: O Nome de Objetos requer pelo menos um 'Any'. A desativação da validação do modelo impedirá que estas advertências apareçam na vista.

A opção de alternar a validação do modelo pode ser encontrada no menu Opções de >de Definições de >ficheiro. O valor predefinido desta definição é desativado.

![Opção de validação de modelos](./media/threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET Necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="unsupported-systems"></a>Sistemas não suportados

#### <a name="issue"></a>Problema

Os utilizadores de sistemas Windows 10 que não possam instalar .NET 4.7.1 ou posterior, como o Windows 10 Enterprise LTSB (versão 1507), não poderão abrir a ferramenta após a atualização. Estas versões mais antigas do Windows já não são suportadas por plataformas para a Ferramenta de Modelação de Ameaças, e não devem instalar a mais recente atualização.

#### <a name="workaround"></a>Solução

Os utilizadores do Windows 10 Enterprise LTSB (versão 1507) que tenham instalado a mais recente atualização podem reverter para a versão anterior da Ferramenta de Modelação de Ameaças através do diálogo desinstalar em Apps & Funcionalidades.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](threat-modeling-tool.md), e inclui informações [sobre a utilização da ferramenta.](threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).
