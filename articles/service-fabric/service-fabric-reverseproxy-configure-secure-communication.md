---
title: Comunicação segura do proxy reverso do Azure Service Fabric | Microsoft Docs
description: Configure o proxy reverso para habilitar a comunicação segura de ponta a ponta.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: e915e689f09ba7f5c92958ebf8531aa67eef4493
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933943"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Conectar-se a um serviço seguro com o proxy reverso

Este artigo explica como estabelecer uma conexão segura entre o proxy reverso e os serviços, permitindo assim um canal seguro de ponta a ponta. Para saber mais sobre o proxy reverso, consulte [proxy reverso no Azure Service Fabric](service-fabric-reverseproxy.md)

Só há suporte para a conexão com serviços seguros quando o proxy reverso está configurado para escutar em HTTPS. Este artigo pressupõe que esse é o caso.
Consulte Configurar [proxy reverso no Azure Service Fabric](service-fabric-reverseproxy-setup.md) para configurar o proxy reverso no Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de conexão segura entre o proxy reverso e os serviços 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticação de proxy reverso para serviços:
O proxy reverso se identifica aos serviços usando seu certificado. Para clusters do Azure, o certificado é especificado com a propriedade ***reverseProxyCertificate*** na [seção tipo de recurso](../azure-resource-manager/resource-group-authoring-templates.md) [**Microsoft. perfabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) do modelo do Resource Manager. Para clusters autônomos, o certificado é especificado com a propriedade ***ReverseProxyCertificate*** ou ***ReverseProxyCertificateCommonNames*** na seção de **segurança** de ClusterConfig. JSON. Para saber mais, confira [habilitar o proxy reverso em clusters autônomos](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Os serviços podem implementar a lógica para verificar o certificado apresentado pelo proxy reverso. Os serviços podem especificar os detalhes do certificado de cliente aceito como definições de configuração no pacote de configuração. Isso pode ser lido em tempo de execução e usado para validar o certificado apresentado pelo proxy reverso. Consulte [gerenciar parâmetros de aplicativo](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Proxy reverso verificando a identidade do serviço por meio do certificado apresentado pelo serviço:
O proxy reverso dá suporte às seguintes políticas para executar a validação de certificado do servidor dos certificados apresentados pelos serviços: nenhum, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar a política para uso de proxy reverso, especifique o **ApplicationCertificateValidationPolicy** na seção **ApplicationGateway/http** em [fabricSettings](service-fabric-cluster-fabric-settings.md).

A próxima seção mostra os detalhes de configuração para cada uma dessas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação de certificado de serviço 

- **Nenhum**: o proxy reverso ignora a verificação do certificado de serviço com proxy e estabelece a conexão segura. Esse é o comportamento padrão.
Especifique o **ApplicationCertificateValidationPolicy** com o valor **None** na seção [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: o proxy reverso verifica o certificado apresentado pelo serviço com base no nome comum do certificado e na impressão digital do emissor imediato: especifique o **ApplicationCertificateValidationPolicy** com o valor  **ServiceCommonNameAndIssuer** na seção [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Para especificar a lista de nome comum do serviço e as impressões digitais do emissor, adicione uma seção [**ApplicationGateway/http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) em **fabricSettings**, conforme mostrado abaixo. Vários nomes comuns de certificado e pares de impressão digital do emissor podem ser adicionados na matriz de **parâmetros** . 

   Se o proxy reverso do ponto de extremidade estiver se conectando para apresentar um certificado que é o nome comum e a impressão digital do emissor corresponde a qualquer um dos valores especificados aqui, o canal SSL é estabelecido. 
   Após a falha de correspondência dos detalhes do certificado, o proxy reverso falha a solicitação do cliente com um código de status 502 (gateway inadequado). A linha de status HTTP também conterá a frase "certificado SSL inválido". 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: o proxy reverso verificará o certificado de serviço com proxy com base em sua impressão digital. Você pode optar por ir para essa rota quando os serviços são configurados com certificados autoassinados: especifique o **ApplicationCertificateValidationPolicy** com o valor **ServiceCertificateThumbprints** no [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) Section.

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Especifique também as impressões digitais com uma entrada **ServiceCertificateThumbprints** na seção **ApplicationGateway/http** . Várias impressões digitais podem ser especificadas como uma lista separada por vírgulas no campo valor, conforme mostrado abaixo:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Se a impressão digital do certificado do servidor estiver listada nessa entrada de configuração, o proxy reverso terá sucesso na conexão SSL. Caso contrário, ele encerra a conexão e falha a solicitação do cliente com um 502 (gateway inadequado). A linha de status HTTP também conterá a frase "certificado SSL inválido".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção de ponto de extremidade quando os serviços expõem pontos de extremidades seguros e não seguros
O Service Fabric dá suporte à configuração de vários pontos de extremidade para um serviço. Para obter mais informações, consulte [especificar recursos em um manifesto do serviço](service-fabric-service-manifest-resources.md).

Proxy reverso seleciona um dos pontos de extremidade para encaminhar a solicitação com base no parâmetro de consulta **listenername** no [URI do serviço](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se o parâmetro **listenername** não for especificado, o proxy reverso poderá escolher qualquer ponto de extremidade da lista de pontos de extremidade. Dependendo dos pontos de extremidade configurados para o serviço, ele selecionado pode ser um ponto de extremidade HTTP ou HTTPS. Pode haver cenários ou requisitos em que você deseja que o proxy reverso opere em um "modo somente de segurança"; ou seja, você não quer que o proxy reverso seguro encaminhe solicitações a pontos de extremidade não seguros. Para definir o proxy reverso para o modo somente segurança, especifique a entrada de configuração **SecureOnlyMode** com o valor **true** na seção [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Ao operar em **SecureOnlyMode**, se um cliente tiver especificado um **listenername** correspondente a um ponto de extremidade http (não seguro), o proxy reverso falhará na solicitação com um código de status http 404 (não encontrado).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurando a autenticação de certificado do cliente por meio do proxy reverso
A terminação SSL ocorre no proxy reverso e todos os dados do certificado do cliente são perdidos. Para os serviços executarem a autenticação de certificado de cliente, especifique a configuração **ForwardClientCertificate** na seção [**ApplicationGateway/http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) .

1. Quando **ForwardClientCertificate** for definido como **false**, o proxy reverso não solicitará o certificado do cliente durante seu handshake de SSL com o cliente.
Esse é o comportamento padrão.

2. Quando **ForwardClientCertificate** é definido como **true**, o proxy reverso solicita o certificado do cliente durante seu handshake de SSL com o cliente.
Em seguida, ele encaminhará os dados do certificado do cliente em um cabeçalho HTTP personalizado chamado **X-Client-Certificate**. O valor do cabeçalho é a cadeia de caracteres de formato PEM codificado em base64 do certificado do cliente. O serviço pode ter êxito/falha na solicitação com o código de status apropriado depois de inspecionar os dados do certificado.
Se o cliente não apresentar um certificado, o proxy reverso encaminha um cabeçalho vazio e permite que o serviço manipule o caso.

> [!NOTE]
> O proxy reverso é um mero encaminhador. Ele não executará nenhuma validação do certificado do cliente.


## <a name="next-steps"></a>Passos seguintes
* [Configure e configure o proxy reverso em um cluster](service-fabric-reverseproxy-setup.md).
* Consulte [Configurar o proxy reverso para se conectar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Consulte um exemplo de comunicação HTTP entre serviços em um [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com Reliable Services comunicação remota](service-fabric-reliable-services-communication-remoting.md)
* [API Web que usa OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
