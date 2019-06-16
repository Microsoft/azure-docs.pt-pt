---
title: O Azure Service Fabric inverter uma comunicação segura proxy | Documentos da Microsoft
description: Configure o proxy inverso para permitir a comunicação de ponto-a-ponto segura.
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
ms.openlocfilehash: d8a11a3289037602535d1b5727d041e376012bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837849"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ligar a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma ligação segura entre o proxy inverso e serviços, assim, ativar um canal de segurança de ponta a ponta. Para saber mais sobre o proxy inverso, consulte [inverter o proxy no Azure Service Fabric](service-fabric-reverseproxy.md)

Ligar a serviços seguros é suportado apenas quando o proxy inverso está configurado para escutar em HTTPS. Este artigo pressupõe que esse é o caso.
Consulte a [proxy inverso do programa de configuração no Azure Service Fabric](service-fabric-reverseproxy-setup.md) para configurar o proxy inverso no Service Fabric.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de ligação segura entre o proxy inverso e serviços 

### <a name="reverse-proxy-authenticating-to-services"></a>Proxy inverso autenticar-se aos serviços:
O proxy inverso identifica-se aos serviços com o seu certificado. Para o certificado de clusters do Azure é especificado com ***reverseProxyCertificate*** propriedade na [ **Microsoft.ServiceFabric/clusters** ](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [recursos Escreva a seção](../azure-resource-manager/resource-group-authoring-templates.md) do modelo do Resource Manager. Para clusters autónomos, o certificado é especificado com o os ***ReverseProxyCertificate*** ou o ***ReverseProxyCertificateCommonNames*** propriedade no **segurança**secção de ClusterConfig.json. Para obter mais informações, consulte [ativar o proxy inverso no clusters autónomos](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Serviços podem implementar a lógica para verificar o certificado apresentado pelo proxy inverso. Os serviços podem especificar os detalhes do certificado de cliente aceita como definições de configuração no pacote de configuração. Isso pode ser lido em tempo de execução e utilizado para validar o certificado apresentado pelo proxy inverso. Consulte a [gerir parâmetros da aplicação](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Verificar a identidade do serviço por meio do certificado apresentado pelo serviço de um proxy inverso:
Proxy inverso suporta as seguintes políticas para executar a validação de certificado de servidor de certificados apresentada por serviços: ' None ', ' ServiceCommonNameAndIssuer e ServiceCertificateThumbprints '.
Para selecionar a política de proxy inverso utilizar, especifique a **ApplicationCertificateValidationPolicy** no **gateway de aplicação/Http** secção em [fabricSettings](service-fabric-cluster-fabric-settings.md).

A secção seguinte mostra os detalhes de configuração para cada uma destas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação do certificado de serviço 

- **Nenhum**: Ignora a verificação do certificado de proxy do serviço de proxy inverso e estabelece a ligação segura. Este é o comportamento padrão.
Especifique a **ApplicationCertificateValidationPolicy** com o valor **None** no [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção.

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

- **ServiceCommonNameAndIssuer**: Proxy inverso verifica o certificado apresentado pelo serviço com base no nome comum do certificado e o thumbprint do emissor imediato: Especifique a **ApplicationCertificateValidationPolicy** com o valor **ServiceCommonNameAndIssuer** no [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção.

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

   Para especificar a lista de nome comum do serviço e os thumbprints do emissor, adicione uma [ **gateway de aplicação/Http/ServiceCommonNameAndIssuer** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) secção sob **fabricSettings**, conforme mostrado abaixo. Vários certificados comuns emissor thumbprint pares de nome e podem ser adicionados no **parâmetros** matriz. 

   Se o proxy inverso do ponto final está se conectando a apresenta um certificado que é comum thumbprint de nome e o emissor corresponde a qualquer um dos valores especificados aqui, o canal SSL é estabelecida. 
   Após a falha de acordo com os detalhes do certificado, o proxy inverso falha a solicitação do cliente com um código de estado (Gateway inválido) 502. A linha de estado HTTP também conterá a frase "Certificado SSL inválido." 

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

- **ServiceCertificateThumbprints**: Proxy inverso irá verificar o certificado de serviço transmitidas por proxy com base na sua impressão digital. Pode optar por aceder esta rota quando os serviços estão configurados com auto-assinado certificados: Especifique a **ApplicationCertificateValidationPolicy** com o valor **ServiceCertificateThumbprints** no [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção.

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

   Também especificar os thumbprints com um **ServiceCertificateThumbprints** entrada no **gateway de aplicação/Http** secção. Thumbprints vários podem ser especificados como uma lista separada por vírgulas no campo valor, conforme mostrado abaixo:

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

   Se o thumbprint do certificado de servidor estiver listado nesta entrada de configuração, o proxy inverso é bem-sucedida com a ligação SSL. Caso contrário, ele encerra a conexão e falha a solicitação do cliente com um 502 (Gateway inválido). A linha de estado HTTP também conterá a frase "Certificado SSL inválido."

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção do ponto de extremidade quando os serviços expõem pontos de extremidade seguros, bem como não seguros
O Service fabric suporta a configuração de vários pontos de extremidade para um serviço. Para obter mais informações, consulte [especificar recursos num manifesto do serviço](service-fabric-service-manifest-resources.md).

Proxy inverso seleciona um dos pontos finais para encaminhar a solicitação com base na **ListenerName** parâmetro na consulta a [URI do serviço](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se o **ListenerName** parâmetro não for especificado, proxy reverso pode escolher qualquer ponto final da lista de pontos de extremidade. Consoante os pontos finais configurados para o serviço, o ponto de extremidade selecionado pode ser um ponto de extremidade HTTP ou HTTPS. Poderá haver requisitos ou cenários onde pretende que o proxy inverso para operar num "secure modo apenas de"; ou seja, não quer o proxy inverso seguro para reencaminhar pedidos para os pontos finais não seguros. Para definir o proxy inverso para o modo só de proteger, especifique a **SecureOnlyMode** entrada de configuração com o valor **true** no [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção.   

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
> Quando funciona na **SecureOnlyMode**, se um cliente tiver especificado um **ListenerName** correspondente a um ponto de extremidade HTTP(unsecured), o proxy inverso falha o pedido com um código de estado 404 (não encontrado) HTTP.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurar a autenticação de certificado de cliente através do proxy inverso
Terminação de SSL ocorre ao proxy inverso e todos os dados de certificado de cliente é perdido. Para os serviços efetuar a autenticação de certificado de cliente, especifique a **ForwardClientCertificate** definição [ **gateway de aplicação/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) secção.

1. Quando **ForwardClientCertificate** está definida como **falso**, inverta proxy não irá pedir o certificado de cliente durante o handshake SSL com o cliente.
Este é o comportamento padrão.

2. Quando **ForwardClientCertificate** está definida como **verdadeiro**, inverta proxy pedidos de certificado do cliente durante o handshake SSL com o cliente.
Em seguida, reencaminhará o cliente de dados de certificado num cabeçalho HTTP personalizado denominado **certificado de cliente X**. O valor do cabeçalho é a cadeia de formato com codificação base64 PEM do certificado do cliente. O serviço pode ter êxito/falha o pedido com o código de estado apropriado depois inspecionar os dados do certificado.
Se o cliente não apresentar um certificado, o proxy inverso reencaminha um cabeçalho vazio e permitir que o serviço de administrar o caso.

> [!NOTE]
> Proxy inverso é um mero reencaminhador. Ele não executará nenhuma validação de certificado do cliente.


## <a name="next-steps"></a>Passos Seguintes
* [Definir e configurar o proxy inverso num cluster](service-fabric-reverseproxy-setup.md).
* Consulte a [configurar o proxy inverso para ligar a serviços seguros](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) para o Azure Resource Manager exemplos de modelos para configurar o secure proxy inverso, com o certificado de serviço diferentes opções de validação.
* Ver um exemplo de comunicação HTTP entre os serviços numa [projeto de exemplo no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com a comunicação remota do Reliable Services](service-fabric-reliable-services-communication-remoting.md)
* [API Web que utiliza o OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
