---
title: Proteger a comunicação de cliente de OPC e PLC OPC com a gestão de certificados do Azure IoT OPC UA | Documentos da Microsoft
description: Proteger a comunicação de cliente de OPC e OPC PLC inscrevendo-os certificados utilizando o Cofre de OPC AC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: c437f6db21956d1be5e4f6d3512f325f37ca7308
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759570"
---
# <a name="secure-the-communication-of-opc-client-and-opc-plc"></a>Proteger a comunicação de cliente de OPC e OPC PLC

O Azure IoT OPC UA gestão de certificados, também conhecido como cofre OPC, é um micro serviço que pode configurar, registre-se e gerir o ciclo de vida do certificado para aplicações de servidor e cliente de OPC UA na cloud. Este artigo mostra-lhe como proteger a comunicação de cliente de OPC e OPC PLC inscrevendo-os certificados utilizando o Cofre de OPC AC.

Na configuração seguinte, o cliente de OPC testa a conectividade com a PLC OPC. Por predefinição, a conectividade não é possível porque ambos os componentes não são aprovisionados com os certificados corretos. Se um componente de OPC UA ainda não foi aprovisionado com um certificado, irá gerar um certificado autoassinado na inicialização. No entanto, o certificado pode ser assinado por uma autoridade de certificado (AC) e instalado no componente de OPC UA. Depois que isso é feito para o cliente de OPC e OPC PLC, a conectividade é ativada. O fluxo de trabalho abaixo descreve o processo. Algumas informações gerais sobre a segurança de OPC UA podem ser encontradas na [este documento](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf) white paper. As informações completas podem ser encontradas na especificação OPC UA.

Testbed: O seguinte ambiente está configurado para fins de teste.

Scripts de OPC cofre:
- Proteger a comunicação de cliente de OPC e OPC PLC inscrevendo-os certificados utilizando o Cofre de OPC AC.

> [!NOTE]
> Para obter mais informações, consulte o GitHub [repositório](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-self-signed-certificate-on-startup"></a>Gerar um certificado autoassinado na inicialização

**Preparação**

- Certifique-se de que as variáveis de ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` não estiverem, por exemplo, `$env:_PLC_OPT=""` no seu PowerShell.

- Defina a variável de ambiente `$env:_OPCVAULTID` para uma cadeia de caracteres que permite que encontre novamente os seus dados no Cofre de OPC. Apenas carateres alfanuméricos são permitidos. No nosso exemplo, "123456" foi utilizado como o valor desta variável.

- Certifique-se de que existem não existem volumes do docker `opcclient` ou `opcplc`. Verifique com `docker volume ls` e removê-los com `docker volume rm <volumename>`. Poderá ter de remover também a contentores com `docker rm <containerid>` se os volumes ainda são utilizados por um contentor.

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:

```
docker-compose -f connecttest.yml up
```

**Verificação**

Certifique-se no registo de que não há nenhum certificado instalado no primeiro arranque. Aqui, a saída de registo do OPC PLC (mostra semelhante para o cliente de OPC):....
```
opcplc-123456 | [20:51:32 INF] Trusted issuer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted issuer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Trusted peer store contains 0 certs
opcplc-123456 | [20:51:32 INF] Trusted peer store has 0 CRLs.
opcplc-123456 | [20:51:32 INF] Rejected certificate store contains 0 certs
```
Se vir certificados comunicados, siga os passos de preparação acima e eliminar os volumes de docker.

Certifique-se de que a ligação para o PLC OPC falhou. Deverá ver o resultado seguinte no cliente OPC saída de registo:

```
opcclient-123456 | [20:51:35 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:51:36 ERR] Session creation to endpoint 'opc.tcp://opcplc-123456:50000/' failed 1 time(s). Please verify if server is up and OpcClient configuration is correct.
opcclient-123456 | Opc.Ua.ServiceResultException: Certificate is not trusted.
```
O motivo da falha é que o certificado não é fidedigno. Isso significa que `opc-client` tentar estabelecer ligação ao `opc-plc` e recebeu uma resposta de volta, indicando que `opc-plc` não confia `opc-client`, o resultado de `opc-plc` não é possível validar o certificado que `opc-client` forneceu. Este é um certificado autoassinado sem qualquer configuração adicional do certificado no `opc-plc`, e, portanto, a ligação falhou.

## <a name="sign-and-install-certificates-in-opc-ua-components"></a>Inicie sessão e instalar certificados nos componentes de OPC UA

**Preparação**
1. Observe a saída de registo do passo 1 e obter "CreateSigningRequest informações" para o PLC OPC e o cliente de OPC. Aqui o resultado é mostrado apenas para OPC PLC:

    ```
    opcplc-123456 | [20:51:32 INF] ----------------------- CreateSigningRequest information ------------------
    opcplc-123456 | [20:51:32 INF] ApplicationUri: urn:OpcPlc:opcplc-123456
    opcplc-123456 | [20:51:32 INF] ApplicationName: OpcPlc
    opcplc-123456 | [20:51:32 INF] ApplicationType: Server
    opcplc-123456 | [20:51:32 INF] ProductUri: https://github.com/azure-samples/iot-edge-opc-plc
    opcplc-123456 | [20:51:32 INF] DiscoveryUrl[0]: opc.tcp://opcplc-123456:50000
    opcplc-123456 | [20:51:32 INF] ServerCapabilities: DA
    opcplc-123456 | [20:51:32 INF] CSR (base64 encoded):
    opcplc-123456 | MIICmzCCAYMCAQAwETEPMA0GA1UEAwwGT3BjUGxjMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAwTvlbinAPWPxR9Lw1ndGsrLGy8GiqVOxyGaDpPUcMchX0k0/ncg28h7xrB2H1PThdiZxUJuUwsNM74HrVgt
    ofmXhA4dLM1cTxZHyJVFjl2L3vK5M58NNf6UNdKcB0x3LyuoT6mAIMXmCioqymFCk1TMzIMzbAe7JVAdUaSRBP1vuqQ1rV/cfNAe35dKQW4aPYgl7pR5f1hqprcDu/oca67X8L4kTl4oN0/bCYTk+Ibcd9cG462oAN+bSwbHn8a2jNky8rGsofA
    o9DOT+0ALPhk6CApCYIP2yxoI/kT188eqUUxzAFF9nyU79AyCkpGPuY8DGMyf56pDofgtGpfY3wQIDAQABoEUwQwYJKoZIhvcNAQkOMTYwNDAyBgNVHREEKzAphhh1cm46T3BjUGxjOm9wY3BsYy0xMjM0NTaCDW9wY3BsYy0xMjM0NTYwDQYJK
    oZIhvcNAQELBQADggEBAAsZLoOLzS2VhDcQRu0QhRbG7CGAxX19l7fDCG2WjU7lTFnCvYVTWTYyaY61ljmrWc7IbCaQdMJM8GRnAnvAzUh/PBDxkOX7NqI2+8F1yQOHgs/AfKuppOd6DIP8EzFAHnc0H85jay6zFdmIDWoWwpy0ACqOVooOTKST
    7uty0mT87bj8Cdy1yf4mvBNQx+nsuTbKgxWCBxGYAyg9dIL2uKL0aeB/ROW5Gkelz5sCEzQ1fFDokUA4oC5QiATQBN3cY7EmvRbPgdToY7CpRN3iiO7J+7bC7BP9YKfuE34E8xOFpskHPHAPf3r002/L0S67HyuVSXLUj1+Jc0LeAAF9Bw0=
    opcplc-123456 | [20:51:32 INF] ---------------------------------------------------------------------------
    ```
    
1. Vá para o [Web site do Cofre de OPC](https://opcvault.azurewebsites.net/).

1. Selecione `Register New`

1. Introduza as informações de OPC PLC entre as saídas de registo `CreateSigningRequest information` área nos campos de entrada no `Register New OPC UA Application` página, selecione `Server` como ApplicationType.

1. Selecione `Register`.

1. Na página seguinte, `Request New Certificate for OPC UA Application` selecione `Request new Certificate with Signing Request`.

1. Na página seguinte, `Generate a new Certificate with a Signing Request` colar a `CSR (base64 encoded)` cadeia de caracteres a partir da saída de registo para o `CreateRequest` campo de entrada. Certifique-se de que copie a cadeia de caracteres completa.

1. Selecione `Generate New Certificate`.

1. Está agora a mover para a frente para `View Certificate Request Details`. Nesta página, pode baixar todas as informações necessárias para aprovisionar os arquivos de certificados da `opc-plc`.

1. Nesta página:  
    - Selecione `Certificate` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<applicationcertbase64-string>` mais tarde. Selecione `Back`.

    - Selecione `Issuer` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<addissuercertbase64-string>` mais tarde. Selecione `Back`.

    - Selecione `Crl` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<updatecrlbase64-string>` mais tarde. Selecione `Back`.

1. Agora definir no seu PowerShell uma variável chamada `$env:_PLC_OPT`:

    ```
    `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  
    ```
    > [!NOTE] 
    > Substitua as cadeias de caracteres passadas como cadeias de Base64 de valores de opção obtidas a partir do Web site.

Repita o processo completo, começando com `Register New` (passo 3 acima) para o cliente de OPC. Existem apenas as seguintes diferenças que precisa estar atento:

- Utilizar a saída de registo do `opcclient`.
- Selecione `Client` como ApplicationType durante o registo.
- Utilize `$env:_CLIENT_OPT` como nome da variável do PowerShell.

> [!NOTE]
> Ao trabalhar com este cenário, pode ter reconheceu que o `<addissuercertbase64-string>` e `<updatecrlbase64-string>` valores são idênticos para `opcplc` e `opcclient`. Isso é verdadeiro para nosso caso de utilização e pode economizar tempo ao realizar os passos.

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:

```
docker-compose -f connecttest.yml up
```

**Verificação** Certifique-se de que os dois componentes inscreveu agora certificados de aplicação. Verificar a saída de registo para a seguinte saída:

```
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Starting to add certificate(s) to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Certificate 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' and thumbprint 'BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Starting to update the current CRL.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted peer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted peer store.
opcplc-123456 | [20:54:38 INF] Remove the current CRL from the trusted issuer store.
opcplc-123456 | [20:54:38 INF] The new CRL issued by 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' was added to the trusted issuer store.
opcplc-123456 | [20:54:38 INF] Start updating the current application certificate.
opcplc-123456 | [20:54:38 INF] The current application certificate has SubjectName 'CN=OpcPlc' and thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] Remove the existing application certificate with thumbprint '8FD43F66479398BDA3AAF5B193199A6657632B49'.
opcplc-123456 | [20:54:39 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' was added to the application certificate store.
opcplc-123456 | [20:54:39 INF] Activating the new application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586'.
opcplc-123456 | [20:54:39 INF] Application certificate with thumbprint 'DA6B8B2FB533FBC188F7017BAA8A36FDB77E2586' found in the application certificate store.
opcplc-123456 | [20:54:39 INF] Application certificate is for ApplicationUri 'urn:OpcPlc:opcplc-123456', ApplicationName 'OpcPlc' and Subject is 'OpcPlc'
 ```
O certificado de aplicação está lá e assinado por uma AC.

Certifique-se no registo de que agora existem certificados instalados. Segue-se a saída de registo de OPC PLC e o cliente de OPC tem um resultado semelhante.
```
opcplc-123456 | [20:54:39 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Trusted peer store contains 1 certs
opcplc-123456 | [20:54:39 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [20:54:39 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [20:54:39 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [20:54:39 INF] Rejected certificate store contains 0 certs
```
O emissor do certificado de aplicação é a AC `CN=Azure IoT OPC Vault CA, O=Microsoft Corp.` e o PLC OPC confiar também, todos os certificados assinados por esta AC.


Certifique-se de que a ligação para o PLC OPC tiver sido criada com êxito e o cliente de OPC pode ler dados a partir OPC PLC. Deverá ver o resultado seguinte no cliente OPC saída de registo:
```
opcclient-123456 | [20:54:42 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [20:54:42 INF] Session successfully created with Id ns=3;i=1085867946.
opcclient-123456 | [20:54:42 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [20:54:42 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [20:54:42 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [20:54:42 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [20:54:42 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [20:54:42 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [20:54:42 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [20:54:42 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [20:54:42 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/20/2018 20:54:42
```
Se vir esta saída, em seguida, o PLC OPC é agora confiar cliente OPC e vice-versa, uma vez que ambos têm agora certificados assinados por uma AC e confiança de ambos os certificados que onde assinado por esta AC.

> [!NOTE] 
> Embora os primeiros passos de verificação de dois que mostramos apenas para OPC PLC, aqueles precisam ser verificados também para o cliente de OPC.


## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar o Cofre de OPC para um projeto existente, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Implementar duplo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)