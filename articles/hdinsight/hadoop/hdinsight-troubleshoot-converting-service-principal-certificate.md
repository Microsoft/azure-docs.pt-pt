---
title: Conversão de conteúdo de certificado para base-64 - Azure HDInsight
description: Conversão do conteúdo principal do certificado de serviço para o formato de cadeia codificado base-64 em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 88faf878d9528b314e59c81dad7a97d4700b608f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289128"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversão do conteúdo do certificado principal do serviço para o formato de cadeia codificado base-64 em HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Recebe uma mensagem de erro indicando que a entrada não é uma cadeia base-64 válida, uma vez que contém um caracteres não-base 64, mais de dois caracteres de enchimento ou um personagem de espaço não-branco entre os caracteres de enchimento.

## <a name="cause"></a>Causa

Ao utilizar a implementação do modelo PowerShell ou Azure para criar clusters com o Data Lake como armazenamento primário ou adicional, o conteúdo principal do certificado de serviço fornecido para aceder à conta de armazenamento do Data Lake está no formato base-64. A conversão inadequada do conteúdo do certificado PFX para a cadeia codificada base-64 pode levar a este erro.

## <a name="resolution"></a>Resolução

Assim que tiver o certificado principal de serviço em formato pfx (consulte [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) para os passos principais de criação do serviço de amostra), utilize o seguinte comando PowerShell ou corte C# para converter o conteúdo do certificado no formato base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]