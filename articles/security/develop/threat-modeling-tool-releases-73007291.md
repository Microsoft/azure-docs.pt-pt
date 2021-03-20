---
title: Microsoft Threat Modeling Tool release 07/29/2020 - Azure
description: Documentando as notas de lançamento para a versão da ferramenta de modelação de ameaças 7.3.00729.1.
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516378"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Lançamento da atualização da ferramenta de modelação de ameaças 7.3.00729.1 - 07/29/2020

A versão 7.3.00729.1 da Ferramenta de Modelação de Ameaças da Microsoft (TMT) foi lançada a 29 de julho de 2020 e contém as seguintes alterações:

- Correções de erros
 
## <a name="known-issues"></a>Problemas conhecidos

### <a name="errors-related-to-tmt7application-file-deserialization"></a>Erros relacionados com a deserialização do ficheiro TMT7.application

#### <a name="issue"></a>Problema

Alguns clientes relataram ter recebido a seguinte mensagem de erro ao descarregar a Ferramenta de Modelação de Ameaças:

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

Este erro ocorre porque alguns navegadores não suportam de forma nativa a instalação do ClickOnce. Nesses casos, o ficheiro de aplicação ClickOnce é descarregado para o disco rígido do utilizador.

#### <a name="workaround"></a>Solução

Este erro continuará a aparecer se a Ferramenta de Modelação de Ameaças for lançada clicando duas vezes no ficheiro TMT7.application. No entanto, depois de contornar o erro, a ferramenta funcionará normalmente. Em vez de lançar a Ferramenta de Modelação de Ameaças clicando duas vezes no ficheiro TMT7.application, os utilizadores devem utilizar atalhos criados no Menu Windows durante a instalação para iniciar a Ferramenta de Modelação de Ameaças.

## <a name="system-requirements"></a>Requisitos de sistema

- Sistemas Operativos Suportados
  - [Atualização de Aniversário do Microsoft Windows 10](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) ou mais tarde
- .VERSÃO NET Necessária
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) ou mais tarde
- Requisitos Adicionais
  - É necessária uma ligação à Internet para receber atualizações da ferramenta, bem como modelos.

## <a name="documentation-and-feedback"></a>Documentação e feedback

- A documentação da Ferramenta de Modelação de Ameaças encontra-se no [docs.microsoft.com](./threat-modeling-tool.md), e inclui informações [sobre a utilização da ferramenta.](./threat-modeling-tool-getting-started.md)

## <a name="next-steps"></a>Passos seguintes

Descarregue a versão mais recente da [Ferramenta de Modelação de Ameaças da Microsoft](https://aka.ms/threatmodelingtool).