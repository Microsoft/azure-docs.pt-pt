---
title: Proteger o OPC UA cliente e a aplicação de servidor OPC UA com o OPC Vault - Azure | Documentos da Microsoft
description: Proteger o cliente de OPC UA e OPC UA a aplicação de servidor com um novo par de chaves e o certificado com o Cofre de OPC.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 5ba2dba02585598b3797dd1b490976ebe34b489e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450669"
---
# <a name="secure-opc-ua-client-and-opc-ua-server-application"></a>Proteger o OPC UA cliente e a aplicação de servidor OPC UA 
O Cofre de OPC é um microsserviço que pode configurar, registe-se e gerenciar o ciclo de vida do certificado para o servidor OPC UA e aplicações de cliente na cloud. Este artigo mostra-lhe como proteger um cliente de OPC UA e um OPC UA, aplicação de servidor com um novo par de chaves e o certificado com o Cofre de OPC.

A configuração seguinte, o cliente OPC é testar a conectividade com a PLC OPC. Por predefinição, a conectividade não é possível porque os dois componentes ainda não tem sido aprovisionados com os certificados corretos. Neste fluxo de trabalho, nós não usamos os certificados autoassinados de componentes de OPC UA e registá-los através do Cofre de OPC. Consulte anterior [testbed](howto-opc-vault-deploy-existing-client-plc-communication.md). Em vez disso, este testbed Aprovisiona os componentes com um novo certificado, assim como com uma nova chave privada que são ambos gerados pelo Cofre de OPC. Algumas informações gerais sobre a segurança de OPC UA podem ser encontradas neste [White Paper](https://opcfoundation.org/wp-content/uploads/2014/05/OPC-UA_Security_Model_for_Administrators_V1.00.pdf). As informações completas podem ser encontradas na especificação OPC UA.

Testbed: O seguinte ambiente está configurado para fins de teste.

Scripts de OPC cofre:
- Proteger o cliente de OPC UA e OPC UA aplicações de servidor com um novo par de chaves e o certificado com o Cofre de OPC.

> [!NOTE]
> Para obter mais informações, consulte o GitHub [repositório](https://github.com/Azure-Samples/iot-edge-industrial-configs#testbeds).

## <a name="generate-a-new-certificate-and-private-key"></a>Gerar um novo certificado e chave privada 
**Preparação**
- Certifique-se de que as variáveis de ambiente `$env:_PLC_OPT` e `$env:_CLIENT_OPT` são indefinido. Por exemplo, `$env:_PLC_OPT=""` no seu PowerShell
- Defina a variável de ambiente `$env:_OPCVAULTID` para uma cadeia de caracteres que permite que encontre novamente os seus dados no Cofre de OPC. É recomendável defini-la como um número de 6 dígitos. No nosso exemplo, "123456" foi utilizado como valor para a variável.
- Certifique-se de que não existe nenhum volume do docker `opcclient` ou `opcplc`. Verifique com `docker volume ls` e removê-los com `docker volume rm <volumename>`. Poderá ter de remover também a contentores com `docker rm <containerid>` se os volumes ainda são utilizados por um contentor.

**Início rápido**
1. Vá para o [Web site do Cofre de OPC](https://opcvault.azurewebsites.net/)

1. Selecione `Register New`

1. Introduza as informações de OPC PLC, conforme mostrado na saída de registo do testbed anterior `CreateSigningRequest information` área nos campos de entrada no `Register New OPC UA Application` página, selecione `Server` como ApplicationType.

1. Selecione `Register`

1. Na página seguinte, `Request New Certificate for OPC UA Application` selecione `Request new KeyPair and Certificate`

1. Na página seguinte, `Generate a new Certificate with a Signing Request` colar a `CSR (base64 encoded)` cadeia de caracteres a partir da saída de registo para o `CreateRequest` campo de entrada. Certifique-se de que copie a cadeia de caracteres completa.

1. Na página seguinte, `Request New Certificate for OPC UA Application` selecione `Request new Certificate with Signing Request`

1. Na página seguinte `Generate a new KeyPair and for an OPC UA Application` introduzir `CN=OpcPlc` como SubjectName, `opcplc-<_OPCVAULTID>` (substitua `<_OPCVAULTID>` com o seu) como nome de domínio, selecione `PEM` como PrivateKeyFormat e introduza uma palavra-passe (nos Referimos mais tarde ao mesmo como `<certpassword-string>`)

1. Selecione `Generate New KeyPair`

1. Está agora a mover para a frente para `View Certificate Request Details`. Nesta página, pode baixar todas as informações necessárias para aprovisionar os arquivos de certificados da `opc-plc`.

1. Nesta página:  
    - Selecione `Certificate` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<applicationcertbase64-string>` mais tarde. Selecione `Back`.
    - Selecione `PrivateKey` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<privatekeybase64-string>` mais tarde. Selecione `Back`.
    - Selecione `Issuer` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<addissuercertbase64-string>` mais tarde. Selecione `Back`.
    - Selecione `Crl` no `Download as Base64` e copie a cadeia de texto apresentada a `EncodedBase64` campo e armazená-lo para utilização posterior. Fazemos referência a ele como `<updatecrlbase64-string>` mais tarde. Selecione `Back`.

1. Agora definir no seu PowerShell uma variável chamada `$env:_PLC_OPT`:

   `$env:_PLC_OPT="--applicationcertbase64 <applicationcertbase64-string> --privatekeybase64 <privatekeybase64-string> --certpassword <certpassword-string> --addtrustedcertbase64 <addissuercertbase64-string> --addissuercertbase64 <addissuercertbase64-string> --updatecrlbase64 <updatecrlbase64-string>"`  

    Substitua as cadeias de caracteres passadas como cadeias de Base64 de valores de opção obtidas a partir do Web site.  

1. Repita o processo completo, começando com `Register New` para o cliente OPC. Existem apenas as seguintes diferenças que precisa estar atento:
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

**Verificação**

Certifique-se de que os dois componentes não tenham tido um certificado de aplicação existente. Verificar a saída de registo. Segue-se a saída de OPC PLC e o cliente OPC tem uma saída de registo semelhantes.

```
opcplc-123456 | [13:40:08 INF] There is no existing application certificate.
```
Se existir um certificado de aplicação, remova os volumes do docker, conforme explicado nos passos de preparação.

Certifique-se no registo de que o certificado de AC OPC cofre foi instalado no emissor arquivo de certificados, bem como no arquivo de certificados de elemento de rede fidedigna. Segue-se a saída de registo de OPC PLC e o cliente de OPC tem uma saída de registo semelhantes. 

```
opcplc-123456 | [13:40:09 INF] Trusted issuer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted issuer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Trusted peer store contains 1 certs
opcplc-123456 | [13:40:09 INF] 01: Subject 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.' (thumbprint: BC78F1DDC3BB5D2D8795F3D4FF0C430AD7D68E83)
opcplc-123456 | [13:40:09 INF] Trusted peer store has 1 CRLs.
opcplc-123456 | [13:40:09 INF] 01: Issuer 'CN=Azure IoT OPC Vault CA, O=Microsoft Corp.', Next update time '10/19/2019 22:06:46'
opcplc-123456 | [13:40:09 INF] Rejected certificate store contains 0 certs
```
O PLC OPC agora confiar em todos os clientes de OPC UA com certificados assinados por cofre OPC.

Certifique-se no registo de que o formato de chave privada é reconhecido como PEM e que o novo certificado de aplicação é instalado. Segue-se a saída de registo de OPC PLC e o cliente OPC tem uma saída de registo semelhantes. 

```
opcplc-123456 | [13:40:09 INF] The private key for the new certificate was passed in using PEM format.
opcplc-123456 | [13:40:09 INF] Remove the existing application certificate.
opcplc-123456 | [13:40:09 INF] The new application certificate 'CN=OpcPlc' and thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C' was added to the application certificate store.
opcplc-123456 | [13:40:09 INF] Activating the new application certificate with thumbprint 'A3CB288FC1D2B7A5C08AACF531CF4A85E44A6C4C'.
```

O certificado de aplicação e a chave privada são agora instalados no arquivo de certificados de aplicativo e usados pelo aplicativo OPC UA.

Certifique-se de que a ligação entre o cliente OPC e OPC PLC pode ser estabelecida com êxito e o cliente OPC com êxito pode ler dados a partir OPC PLC. Deverá ver o resultado seguinte na saída de registo de cliente OPC:
```
opcclient-123456 | [13:40:12 INF] Create secured session for endpoint URI 'opc.tcp://opcplc-123456:50000/' with timeout of 10000 ms.
opcclient-123456 | [13:40:12 INF] Session successfully created with Id ns=3;i=941910499.
opcclient-123456 | [13:40:12 INF] The session to endpoint 'opc.tcp://opcplc-123456:50000/' has 4 entries in its namespace array:
opcclient-123456 | [13:40:12 INF] Namespace index 0: http://opcfoundation.org/UA/
opcclient-123456 | [13:40:12 INF] Namespace index 1: urn:OpcPlc:opcplc-123456
opcclient-123456 | [13:40:12 INF] Namespace index 2: http://microsoft.com/Opc/OpcPlc/
opcclient-123456 | [13:40:12 INF] Namespace index 3: http://opcfoundation.org/UA/Diagnostics
opcclient-123456 | [13:40:12 INF] The server on endpoint 'opc.tcp://opcplc-123456:50000/' supports a minimal sampling interval of 0 ms.
opcclient-123456 | [13:40:12 INF] Execute 'OpcClient.OpcTestAction' action on node 'i=2258' on endpoint 'opc.tcp://opcplc-123456:50000/' with security.
opcclient-123456 | [13:40:12 INF] Action (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258') completed successfully
opcclient-123456 | [13:40:12 INF] Value (ActionId: 000 ActionType: 'OpcTestAction', Endpoint: 'opc.tcp://opcplc-123456:50000/' Node 'i=2258'): 10/21/2018 13:40:12
```
Se vir esta saída, em seguida, o PLC OPC é agora confiar a OPC cliente e vice-versa, uma vez que ambos têm agora os certificados assinados por uma autoridade de certificação e ambos os certificados de confiança que tenham sido assinados por esta AC.

### <a name="a-testbed-for-opc-publisher"></a>Um testbed para o publicador OPC ###

**Início rápido**

Execute o seguinte comando do PowerShell na raiz do repositório:
```
docker-compose -f testbed.yml up
```

**Verificação**
- Certifique-se de que os dados são enviados para o IoTHub configurado através da definição `_HUB_CS` usando [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) ou [iothub-explorer](https://github.com/Azure/iothub-explorer).
- Cliente de teste OPC vai usar chamadas de método direto IoTHub e chamadas de métodos OPC para configurar o publicador OPC a publicar/anular a publicação de nós do servidor de teste de OPC.
- Observe a saída para mensagens de erro.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar o Cofre de OPC para um projeto existente, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Implementar duplo de OPC para um projeto existente](howto-opc-twin-deploy-existing.md)