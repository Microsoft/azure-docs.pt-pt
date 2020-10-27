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
ms.openlocfilehash: 9c49957eeab921c86b9901461dc5da594fa4188f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546964"
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

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais detalhadas, [reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).