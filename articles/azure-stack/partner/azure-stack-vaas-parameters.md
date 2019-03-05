---
title: Parâmetros comuns do fluxo de trabalho na validação de pilha do Azure como um serviço | Documentos da Microsoft
description: Parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/19/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 9802184ebef764e9e493b8477fcca72e308987d0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337231"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Parâmetros comuns do fluxo de trabalho para a validação de pilha do Azure como um serviço

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Parâmetros comuns incluem valores como variáveis de ambiente e o utilizador as credenciais necessárias por todos os testes de validação como um serviço (VaaS). Estes valores são definidos ao nível do fluxo de trabalho ao criar ou alterar um fluxo de trabalho. Durante o agendamento de testes, estes valores são transmitidos como parâmetros para cada teste sob o fluxo de trabalho.

> [!NOTE]
> Cada teste define seu próprio conjunto de parâmetros. No momento de agenda, um teste poderá exigir que introduza um valor independentemente dos parâmetros comuns de, ou poderá permitir-lhe substituir o valor do parâmetro comum.

## <a name="environment-parameters"></a>Parâmetros do ambiente

Parâmetros do ambiente descrevem o ambiente do Azure Stack em teste. Estes valores devem ser fornecidos ao gerar e carregar um ficheiro de informações de carimbo de data / Azure Stack para a instância específica que está a testar.

> [!NOTE]
> Em fluxos de trabalho de validação oficial, os parâmetros do ambiente não podem ser modificados após a criação de fluxo de trabalho.

### <a name="generate-the-stamp-information-file"></a>Gerar o ficheiro de informações de carimbo de data /

1. Inicie sessão do DVM ou de qualquer computador que tenha acesso ao ambiente do Azure Stack.
2. Execute os seguintes comandos numa janela elevada do PowerShell:
    ```PowerShell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Localizar valores no ficheiro de configuração de ECE

Valores de parâmetro de ambiente também podem estar localizados manualmente no **o ficheiro de configuração de ECE** localizado em `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` do DVM.

## <a name="test-parameters"></a>Parâmetros de teste

Parâmetros comuns de teste incluem informações confidenciais que não podem ser armazenadas nos arquivos de configuração. Estas devem ser fornecidas manualmente.

Parâmetro    | Descrição
-------------|-----------------
Utilizador de administrador inquilino                            | Administrador do Azure Active Directory inquilino que foi provisionado pelo administrador do serviço no diretório do AAD. Este utilizador executa ações de nível de inquilino, como a implantação de modelos para configurar recursos (VMs, contas de armazenamento, etc.) e executar cargas de trabalho. Para obter detalhes sobre a conta de inquilino de aprovisionamento, consulte [adicionar um novo inquilino do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Utilizador de administrador de serviços             | Administrador do diretório de Active Directory do Azure do inquilino do AAD especificada durante a implementação do Azure Stack. Procure `AADTenant` na configuração ECE de ficheiro e selecione o valor a `UniqueName` elemento.
Utilizador de administrador da nuvem               | Conta de administrador de domínio do Azure Stack (por exemplo, `contoso\cloudadmin`). Procure `User Role="CloudAdmin"` na configuração ECE de ficheiro e selecione o valor a `UserName` elemento.
Cadeia de ligação de diagnóstico          | Um URL de SAS para uma conta de armazenamento do Azure para que o diagnóstico registos serão copiados durante a execução de teste. Para obter instruções sobre a gerar o URL de SAS, consulte [gerar a cadeia de ligação de diagnóstico](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> O **cadeia de ligação de diagnóstico** tem de ser válido antes de continuar.

### <a name="generate-the-diagnostics-connection-string"></a>Gerar a cadeia de ligação de diagnóstico

A cadeia de ligação de diagnóstico é necessária para armazenar os registos de diagnóstico durante a execução de teste. Utilizar a conta de armazenamento do Azure criadas durante a configuração (consulte [configurar a sua validação como recursos de serviço](azure-stack-vaas-set-up-resources.md)) para criar um URL de assinatura (SAS) de acesso partilhado para conceder acesso de VaaS carregar registos para a sua conta de armazenamento.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Selecione **Blob** partir **opções de serviços permitidos**. Anular seleção de quaisquer opções restantes.

1. Selecione **serviço**, **contentor**, e **objeto** de **tipos de recurso permitidos**.

1. Selecione **leitura**, **escrever**, **lista**, **adicionar**, **criar** de **permitido permissões**. Anular seleção de quaisquer opções restantes.

1. Definir **hora de início** para a hora atual, e **hora de fim** para três meses a partir da hora atual.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> O URL de SAS expira ao fim na hora de fim especificada quando o URL foi gerado.  
Durante o agendamento de testes, certifique-se de que o URL é válido durante, pelo menos, 30 dias mais o tempo necessário para a execução de teste (os três meses é sugerida).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [validação como um conceito de principais de serviço](azure-stack-vaas-key-concepts.md)