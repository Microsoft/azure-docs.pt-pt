---
title: Configure TLS 1.2 em clientes windows que acedem ao dispositivo GPU Azure Stack Edge Pro
description: Descreve como configurar o TLS 1.2 em clientes windows que acedem ao dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4a159f7fa384a6899fb3cbb4db3bba9e0ed02d52
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102639076"
---
# <a name="configure-tls-12-on-windows-clients-accessing-azure-stack-edge-pro-device"></a>Configure TLS 1.2 em clientes windows que acedam ao dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Se estiver a utilizar um cliente Windows para aceder ao seu dispositivo Azure Stack Edge Pro, é-lhe exigido que configuure o TLS 1.2 no seu cliente. Este artigo fornece recursos e diretrizes para configurar o TLS 1.2 no seu cliente Windows. 

As diretrizes aqui fornecidas baseiam-se em testes realizados num cliente que executa o Windows Server 2016.

## <a name="configure-tls-12-for-current-powershell-session"></a>Configure TLS 1.2 para a sessão atual da PowerShell

Faça os seguintes passos para configurar o TLS 1.2 no seu cliente.

1. Executar powerShell como administrador.
2. Para definir TLS 1.2 para a sessão atual do PowerShell, escreva:
  
    ```azurepowershell
    $TLS12Protocol = [System.Net.SecurityProtocolType] 'Ssl3 , Tls12'
    [System.Net.ServicePointManager]::SecurityProtocol = $TLS12Protocol
    ```
## <a name="configure-tls-12-on-client"></a>Configure TLS 1.2 no cliente

Se pretender definir O TLS 1.2 em todo o sistema para o seu ambiente, siga as diretrizes destes documentos:

- [Geral- como ativar tLS 1.2](/windows-server/security/tls/tls-registry-settings#tls-12)
- [Como ativar o TLS 1.2 nos clientes](/configmgr/core/plan-design/security/enable-tls-1-2-client)
- [Como ativar o TLS 1.2 nos servidores do site e nos sistemas de site remoto](/configmgr/core/plan-design/security/enable-tls-1-2-server)
- [Protocolos em TLS/SSL (Schannel SSP)](/windows-server/security/tls/manage-tls#configuring-tls-ecc-curve-order)
- [Cipher Suites](/windows-server/security/tls/tls-registry-settings#tls-12): Especificamente [configurar a ordem da suíte cifra TLS](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order) Certifique-se de que lista as suas suites de cifra atuais e prepara quaisquer desaparecidos da seguinte lista:

    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
    - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

    Também pode adicionar estas suítes de cifra editando diretamente as definições de registo.

    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "Functions"  -PropertyType String -Value ("TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384, TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384")
    ```

- Como definir curvas elípticas

    Certifique-se de que lista as suas curvas elípticas atuais e prepara quaisquer desaparecidos da seguinte lista:

    - P-256 
    - P-384

    Também pode adicionar estas curvas elípticas editando diretamente as definições de registo.
    
    ```azurepowershell
    New-ItemProperty -Path "$HklmSoftwarePath\Policies\Microsoft\Cryptography\Configuration\SSL\00010002" -Name "EccCurves" -PropertyType MultiString -Value @("NistP256", "NistP384")
    ```
    
    - [Desaque min tamanho de troca de chaves RSA para 2048](/windows-server/security/tls/tls-registry-settings#keyexchangealgorithm---client-rsa-key-sizes).



## <a name="next-steps"></a>Passos seguintes

[Ligue-se ao Gestor de Recursos Azure](azure-stack-edge-j-series-connect-resource-manager.md)