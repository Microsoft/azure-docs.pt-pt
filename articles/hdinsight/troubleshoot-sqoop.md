---
title: O comando de importação/exportação sqoop falha para alguns utilizadores em clusters ESP - Azure HDInsight
description: 'O comando de importação/exportação da Apache Sqoop falha com "Import Failed: java.io.IOException: A propriedade no diretório de encenação /utilizador/yourusername/.staging não é o erro esperado" para alguns utilizadores no cluster Azure HDInsight ESP'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387359"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>Cenário: O comando de importação/exportação sqoop falha para nomes de utilizadores superiores a 20 caracteres em clusters Azure HDInsight ESP

Este artigo descreve um problema conhecido e solução alternativa quando utiliza os clusters Azure HDInsight ESP (Enterprise Security Pack) usando a conta de armazenamento ADLS Gen2 (ABFS).

## <a name="issue"></a>Problema

Ao executar o comando de importação/exportação de sqoop, falha com o erro abaixo para alguns utilizadores:

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

No exemplo acima, exibe o nome de `/user/yourlongdomainuserna/.staging` utilizador truncado de 20 caracteres para o nome de utilizador `yourlongdomainusername` .

## <a name="cause"></a>Causa

O comprimento do nome de utilizador excede 20 caracteres de comprimento. 

Consulte [como os objetos e credenciais são sincronizados num domínio gerido por Serviços de Domínio do Diretório Ativo Azure](../active-directory-domain-services/synchronization.md) para mais detalhes.

## <a name="workaround"></a>Solução

Utilize um nome de utilizador inferior ou igual a 20 caracteres.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
