---
title: Microsoft Threat Modeling Tool release 02/11/2020 - Azure
description: Documentando as notas de lançamento para a versão da ferramenta de modelação de ameaças 7.3.00206.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 9f9b162460cd2e7a624c1ad3f992011487d1b795
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516939"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Lançamento da atualização da ferramenta de modelação de ameaças 7.3.00206.1 - 02/11/2020

A versão 7.3.00206.1 da Ferramenta de Modelação de Ameaças da Microsoft (TMT) foi lançada a 11 de fevereiro de 2020 e contém as seguintes alterações:

- Correções de erros

## <a name="notable-bug-fixes"></a>Notáveis correções de bugs

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Erros relacionados com valores prioritários fora das gamas esperadas

Alguns clientes tinham relatado ter recebido a seguinte mensagem de erro ao abrir ficheiros criados na "Ferramenta de Modelação de Ameaças 2016" ou modelos personalizados:

```output
System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()

System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)
```

Esta questão foi resolvida nesta versão.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET Necessária
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](./threat-modeling-tool.md), e inclui informações [sobre a utilização da ferramenta.](./threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).