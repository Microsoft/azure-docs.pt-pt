---
title: Gerar certificados de infraestrutura de chave pública do Azure Stack para a implementação de sistemas integrados do Azure Stack | Documentos da Microsoft
description: Descreve o processo de implementação de certificado PKI de pilha do Azure para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 9d358c021f795172e7ced0ba2a2f309a0a0dab6e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649732"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Certificados de pilha do Azure, geração de pedido de assinatura

Pode usar a ferramenta de verificação de preparação do Azure Stack para criar pedidos de assinatura de certificado (os CSRs) adequadas para uma implementação do Azure Stack. Certificados devem ser solicitados, gerados e validados com tempo suficiente para testar antes da implantação. Pode obter a ferramenta [da galeria do PowerShell](https://aka.ms/AzsReadinessChecker).

Pode utilizar a ferramenta do Verificador de preparação do Azure Stack (AzsReadinessChecker) para pedir os seguintes certificados:

- **Padrão de pedidos de certificado** acordo com a [gerar certificados de PKI para a implementação do Azure Stack](azure-stack-get-pki-certs.md).
- **Plataforma-como-serviço**: Pode pedir nomes de (PaaS) de plataforma-como-serviço para os certificados conforme especificado nas [requisitos de certificados de infraestrutura de chave pública do Azure Stack - certificados de PaaS opcional](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Pré-requisitos

O sistema deve cumprir os seguintes pré-requisitos antes de gerar CSR(s) para certificados PKI para uma implementação do Azure Stack:

- Verificador de preparação do Microsoft Azure Stack
- Atributos de certificado:
  - Nome da região
  - Nome de domínio completamente qualificado (FQDN) externo
  - Requerente
- Windows 10 ou Windows Server 2016

  > [!NOTE]  
  > Quando receber os certificados de fazer uma cópia da sua autoridade de certificação os passos em [certificados preparar o Azure Stack PKI](azure-stack-prepare-pki-certs.md) terá de ser concluídos no mesmo sistema!

## <a name="generate-certificate-signing-requests"></a>Gerar o pedido (s) de assinatura de certificado

Utilize estes passos para preparar e validar os certificados PKI de pilha do Azure:

1. Instale AzsReadinessChecker num prompt do PowerShell (5.1 ou acima), ao executar o seguinte cmdlet:

    ```PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Declarar o **assunto** como um dicionário ordenado. Por exemplo:

    ```PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Se for fornecido um nome comum (CN) isso será substituído pelo nome do DNS do primeiro o pedido de certificado.

3. Declare um diretório de saída que já existe. Por exemplo:

    ```PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Declarar o sistema de identidade

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ```

    Serviços de Federação do Active Directory

    ```PowerShell
    $IdentitySystem = "ADFS"
    ```

5. Declarar **nome da região** e uma **FQDN externo** se destina a implementação do Azure Stack.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` compõe a base no qual todos os nomes DNS externos no Azure Stack são criados, neste exemplo, o portal seria `portal.east.azurestack.contoso.com`.  

6. Para gerar solicitações para cada nome DNS de assinatura de certificado:

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para incluir serviços PaaS, especifique a opção ```-IncludePaaS```

7. Em alternativa, para ambientes de Dev/Test, para gerar um pedido de certificado único com vários nomes alternativos do requerente adicione **- RequestType SingleCSR** parâmetro e o valor (**não** recomendado para ambientes de produção):

    ```PowerShell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para incluir serviços PaaS, especifique a opção ```-IncludePaaS```

8. Reveja o resultado:

    ```PowerShell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Submeter o **. REQ** ficheiro gerado para a sua autoridade de certificação (interna ou pública).  O diretório de saída de **New-AzsCertificateSigningRequest** contém CSR(s) necessário para enviar para uma autoridade de certificação.  O diretório também contém, para sua referência, um diretório filho que contém os ficheiros INF utilizados durante a geração de pedido de certificado. Certifique-se de que a sua autoridade de certificação gera certificados ao utilizar o seu pedido gerado que cumprem os [requisitos de PKI do Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Passos Seguintes

[Preparar os certificados PKI de pilha do Azure](azure-stack-prepare-pki-certs.md)
