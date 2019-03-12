---
title: Relatório de validação para o Azure Stack | Documentos da Microsoft
description: Utilize o relatório do Verificador de preparação do Azure Stack para rever os resultados da validação.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: b51f88aae20506e70c3a7bd1c199d032d98fd6fa
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774766"
---
# <a name="azure-stack-validation-report"></a>Relatório de validação do Azure Stack
Utilize a ferramenta de verificação de preparação do Azure Stack para executar validações que suportam a implementação e manutenção de um ambiente do Azure Stack. A ferramenta grava os resultados para um ficheiro de relatório. JSON. O relatório apresenta dados detalhados e resumidos sobre o estado dos pré-requisitos de implementação do Azure Stack. O relatório também apresenta informações sobre a rotação de segredos para Implantações de pilha do Azure existente.  

## <a name="where-to-find-the-report"></a>Onde encontrar o relatório
Quando a ferramenta é executada, regista os resultados para **AzsReadinessCheckerReport.json**. A ferramenta também cria um registo com o nome **AzsReadinessChecker.log**. A localização dos ficheiros é apresentada com os resultados de validação no PowerShell.

![validação de execução](./media/azure-stack-validation-report/validation.png)

Ambos os arquivos de mantêm os resultados de verificações de validação subsequentes quando são executados no mesmo computador.  Por exemplo, a ferramenta pode ser executada para validar certificados, execute novamente para validar a identidade do Azure e, em seguida, uma terceira vez para validar o registo. Os resultados de todas as três validações estão disponíveis no relatório resultante. JSON.  

Por predefinição, ambos os ficheiros são escritos *C:\Users\<nome de utilizador > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Utilize o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comandos de execução para especificar uma localização diferente do relatório.   
- Utilize o **- CleanReport** parâmetro no final do comando de execução para limpar as informações de *AzsReadinessCheckerReport.json*. sobre execuções anteriores da ferramenta.

## <a name="view-the-report"></a>Ver o relatório
Para ver o relatório no PowerShell, fornecer o caminho para o relatório como um valor para **- ReportPath**. Este comando apresenta o conteúdo do relatório e identifica as validações que ainda não tem resultados.

Por exemplo, para ver o relatório a partir da linha de comandos do PowerShell que está aberta para a localização onde o relatório se encontra, execute: 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

O resultado é semelhante ao seguinte:

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
```

## <a name="view-the-report-summary"></a>Ver o relatório de resumo
Para ver um resumo do relatório, pode adicionar os **-resumo** mudar para o final da linha de comando do PowerShell. Por exemplo: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

O resumo mostra as validações que não têm os resultados e indica a aprovação ou reprovação para validações forem concluídas. O resultado é semelhante ao seguinte:

```PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
```


## <a name="view-a-filtered-report"></a>Ver um relatório filtrado
Para ver um relatório que é filtrado num único tipo de validação, utilize o **- ReportSections** parâmetro com um dos seguintes valores:
- Certificado
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Tarefas   
- Tudo  

Por exemplo, para ver o relatório Resumo de certificados só, utilizar a seguinte linha de comandos do PowerShell: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Ver também
