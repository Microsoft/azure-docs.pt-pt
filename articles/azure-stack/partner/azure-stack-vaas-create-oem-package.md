---
title: Validação de pilha melhores práticas do Azure | Documentos da Microsoft
description: Este artigo descreve as melhores práticas para a utilização de validação como um serviço.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2018
ms.author: mabrigg
ms.reviewer: John.Haskin
ms.openlocfilehash: 8fd551156d424adbe4d6ce7eb38a6a6a00aa8f83
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651506"
---
# <a name="create-an-oem-package"></a>Criar um pacote do OEM

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

O pacote de extensão do Azure Stack OEM é o mecanismo pelo qual OEM conteúdo específico é adicionado à infraestrutura do Azure Stack, para utilização na implementação, bem como processos operacionais, como atualização, expansão e substituição de campo.

## <a name="creating-the-package"></a>Criação do pacote

Depois de criado e validado, o pacote de extensão do OEM pode ser utilizado no VaaS.  Antes de continuar, certifique-se de que concluiu os passos para [criando um pacote do OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). O pacote, em seguida, é enviado à Microsoft, juntamente com os resultados do teste VaaS por inscrever-se no fluxo de trabalho de validação do pacote. Os passos seguintes detalham como agrupar os arquivos gerados num ficheiro zip único que VaaS pode consumir.

1. Identificar o seguinte conteúdo para o pacote:
    - Um executável com o nome `<Publisher>-<Model>-<Version>.exe`
    - Um ou mais ficheiros com o nome de ficheiros binários `<Publisher><Model>-<Version>-#.bin`, onde # é um número seqüencial, começando do 1. O número de ficheiros binários é depende do tamanho total do conteúdo do pacote.
    - Um arquivo de manifesto chamado `oemMetadata.xml`, que deve ser idêntico no conteúdo para o arquivo METADATA XML na raiz do conteúdo do pacote.

2. Selecione os ficheiros de conteúdo e criar um ficheiro zip a partir do conteúdo:

    ![Conteúdo do ficheiro de Zip](media/vaas-create-oem-package-1.png) ![comprimir conteúdo do item](media/vaas-create-oem-package-2.png)

3. Renomeie o arquivo resultante para que seja descritiva o bastante para que possa identificá-lo.

## <a name="verifying-the-contents"></a>Verificar o conteúdo

Para validar a estrutura do seu ficheiro zip, Inspecione-lo e verificar que existem sem subpastas. O TLD tem o conteúdo compactado. Uma estrutura de pacote válido é mostrada abaixo.
> [!IMPORTANT]
> Compactar a pasta principal em vez do conteúdo fará com que a falha de assinatura do pacote.

![Conteúdo do pacote zipado corretamente](media/vaas-create-oem-package-3.png)

O ficheiro zip agora pode ser carregado para VaaS e assinado pela Microsoft no fluxo de trabalho de validação do pacote.

## <a name="next-steps"></a>Passos Seguintes

- [Validar um pacote de OEM](azure-stack-vaas-validate-oem-package.md)
