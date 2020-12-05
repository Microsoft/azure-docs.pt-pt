---
title: Criar certificados utilizando a ferramenta Microsoft Azure Stack Hub Readiness Checker Microsoft Docs
description: Descreve como criar pedidos de certificados e, em seguida, obter e instalar certificados no seu dispositivo GPU Azure Stack Edge Pro utilizando a ferramenta Azure Stack Hub Readiness Checker.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: ab9559e1e8265b3adf08b36d1a8097a00297c61a
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96606995"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Crie certificados para o seu Azure Stack Edge Pro utilizando a ferramenta Azure Stack Hub Readiness Checker 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Este artigo descreve como criar certificados para o seu Azure Stack Edge Pro utilizando a ferramenta Azure Stack Hub Readiness Checker. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Usando a ferramenta de verificação de prontidão do hub de pilha de Azure Stack

Utilize a ferramenta Azure Stack Hub Readiness Checker para criar pedidos de assinatura de certificados (CSRs) para uma implementação do dispositivo Azure Stack Edge Pro. Pode criar estes pedidos depois de fazer um pedido para o dispositivo Azure Stack Edge Pro e aguardar a chegada do dispositivo.

> [!NOTE]
> Utilize esta ferramenta apenas para fins de teste ou desenvolvimento e não para dispositivos de produção. 

Pode utilizar a ferramenta Azure Stack Hub Readiness Checker (AzsReadinessChecker) para solicitar os seguintes certificados:

- Certificado de Gestor de Recursos Azure
- Certificado de UI local
- Certificado de nó
- Certificado blob
- Certificado VPN


## <a name="prerequisites"></a>Pré-requisitos

Para criar CSRs para a implementação do dispositivo Azure Stack Edge Pro, certifique-se de que: 

- Tem um cliente a executar o Windows 10 ou o Windows Server 2016 ou mais tarde. 
- Descarregou a ferramenta Microsoft Azure Stack Hub Readiness Checker [da PowerShell Gallery](https://aka.ms/AzsReadinessChecker) neste sistema.
- Tem as seguintes informações para os certificados:
  - Nome do dispositivo
  - Número de série do nó
  - Nome de domínio totalmente qualificado externo (FQDN)

## <a name="generate-certificate-signing-requests"></a>Gerar pedidos de assinatura de certificados

Utilize estes passos para preparar os certificados do dispositivo Azure Stack Edge Pro:

1. Executar PowerShell como administrador (5.1 ou mais tarde).
2. Instale a ferramenta Azure Stack Hub Readiness Checker. Na solicitação powerShell, escreva: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Para obter a versão instalada, escreva:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Crie um diretório para todos os certificados se ainda não tiver um. Escreva: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Para criar um pedido de certificado, forneça as seguintes informações. Se estiver a gerar um certificado VPN, algumas destas entradas não se aplicam.
    
    |Entrada |Description  |
    |---------|---------|
    |`OutputRequestPath`|O caminho do arquivo no seu cliente local onde deseja que os pedidos de certificado sejam criados.        |
    |`DeviceName`|O nome do seu dispositivo na página **dispositivos** na UI web local do seu dispositivo. <br> Este campo não é necessário para um certificado VPN.         |
    |`NodeSerialNumber`|O número de série do nó do dispositivo na página **'Rede'** na UI web local do seu dispositivo. <br> Este campo não é necessário para um certificado VPN.       |
    |`ExternalFQDN`|O valor DNSDomain na página **dispositivos** na UI web local do seu dispositivo.         |
    |`RequestType`|O tipo de pedido pode ser para `MultipleCSR` - certificados diferentes para os vários pontos finais, ou `SingleCSR` - um certificado único para todos os pontos finais. <br> Este campo não é necessário para um certificado VPN.     |

    Para todos os certificados, com exceção do certificado VPN, tipo: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Se estiver a criar um certificado VPN, escreva: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Encontrará os ficheiros de pedido de certificado no diretório especificado no parâmetro OutputRequestPath acima. Ao utilizar o `MultipleCSR` parâmetro, verá os seguintes quatro ficheiros com a `.req` extensão:

    
    |Nomes de ficheiros  |Tipo de pedido de certificado  |
    |---------|---------|
    |Começando com o seu `DeviceName`     |Pedido de certificado de UI web local      |
    |Começando com o seu `NodeSerialNumber`     |Pedido de certificado de nó         |
    |Começando com `login`     |Pedido de certificado endpoint do Gestor de Recursos Azure       |
    |Começando com `wildcard`     |Pedido de certificado de armazenamento blob. Contém um wildcard porque cobre todas as contas de armazenamento que pode criar no dispositivo.          |
    |Começando com `AzureStackEdgeVPNCertificate`     |Pedido de certificado de cliente VPN.         |

    Também verá uma pasta INF. Isto contém um ficheiro de informação <<dispositivo de borda> em texto claro, explicando os detalhes do certificado.  


6. Envie estes ficheiros para a sua autoridade de certificados (interna ou pública). Certifique-se de que o seu CA gera certificados, utilizando o seu pedido gerado, que cumprem os requisitos do certificado Azure Stack Edge Pro para [certificados de nó, certificados](azure-stack-edge-j-series-manage-certificates.md#node-certificates) [de ponto final](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)e [certificados de UI locais.](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates)

## <a name="prepare-certificates-for-deployment"></a>Preparar certificados para implantação

Os ficheiros de certificado que obtém da sua autoridade de certificados (CA) devem ser importados e exportados com propriedades que correspondam aos requisitos de certificado do dispositivo Azure Stack Edge Pro. Complete os seguintes passos no mesmo sistema onde gerou os pedidos de assinatura do certificado.

- Para importar os certificados, siga os passos nos [certificados de importação dos clientes que acedem ao seu dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).

- Para exportar os certificados, siga os passos nos [certificados de exportação do cliente que acede ao dispositivo Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device).


## <a name="validate-certificates"></a>Validar certificados

Em primeiro lugar, irá gerar uma estrutura adequada da pasta e colocar os certificados nas pastas correspondentes. Só assim validará os certificados utilizando a ferramenta.

1. Executar powerShell como administrador.

2. Para gerar a estrutura adequada da pasta, no tipo de solicitação:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Converta a palavra-passe PFX numa cadeia segura. Escreva:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Em seguida, valide os certificados. Escreva:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Passos seguintes

[Implemente o seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
