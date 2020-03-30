---
title: Azure Service Fabric reverter proxy secure communication
description: Configure o proxy inverso para permitir uma comunicação segura de ponta a ponta numa aplicação Azure Service Fabric.
author: kavyako
ms.topic: conceptual
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: 4cfeaf34a39231ffa91ea970a61f66632bae40c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282253"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ligar a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma ligação segura entre o proxy e os serviços invertidos, permitindo assim o fim do canal seguro. Para saber mais sobre procuração inversa, consulte [Procuração Reversa no Tecido de Serviço Azure](service-fabric-reverseproxy.md)

A ligação a serviços seguros só é suportada quando o proxy inverso estiver configurado para ouvir em HTTPS. Este artigo assume que este é o caso.
Consulte a [Configuração de procuração inversa no Tecido de Serviço Azure](service-fabric-reverseproxy-setup.md) para configurar o proxy inverso no Tecido de Serviço.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de ligação seguro entre o proxy e os serviços invertidos 

### <a name="reverse-proxy-authenticating-to-services"></a>Proxy inversa autenticando serviços:
O representante inverso identifica-se aos serviços que utilizam o seu certificado. Para os clusters Azure, o certificado é especificado com a propriedade ***proxyCertificate inversa*** na secção de [recursos](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.ServiceFabric/clusters**](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) Do modelo de Gestor de Recursos. Para clusters autónomos, o certificado é especificado com o ***ReverseProxyCertificate*** ou com a propriedade ***ReverseProxyCertificateCommonNames*** na secção **de Segurança** do ClusterConfig.json. Para saber mais, consulte Permitir a [procuração inversa em clusters autónomos](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Os serviços podem implementar a lógica para verificar o certificado apresentado pelo representante inverso. Os serviços podem especificar os dados do certificado de cliente aceites como configurações de configuração no pacote de configuração. Isto pode ser lido no tempo de execução e utilizado para validar o certificado apresentado pelo representante inverso. Consulte os [parâmetros de aplicação Para](service-fabric-manage-multiple-environment-app-configuration.md) adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Procuração inversa verificando a identidade do serviço através do certificado apresentado pelo serviço:
O proxy inverso suporta as seguintes políticas para executar a validação do certificado de servidor dos certificados apresentados pelos serviços: Nenhum, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar a política de procuração inversa para usar, especifique a Política de **Validação** de Certificados de Aplicação na secção **ApplicationGateway/Http** em [definições](service-fabric-cluster-fabric-settings.md)de tecido .

A secção seguinte mostra detalhes de configuração para cada uma destas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação de certificadode serviço 

- **Nenhum**: Procuração inversa ignora a verificação do certificado de serviço à procura e estabelece a ligação segura. Este é o comportamento padrão.
Especifique a Política de **Validação** de Certificados de Aplicação com valor **Nenhum** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

- **ServiçoCommonNameAndIssuer**: Proxy invertido verifica o certificado apresentado pelo serviço com base no nome comum do certificado e na impressão digital do emitente imediato: Especifique a Política de Validação de Certificados de **Aplicação** com o serviço de **valorCommonNameAndIssuer** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Para especificar a lista de impressões digitais de nome comum do serviço e emitente, adicione uma secção [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) em **definições**de tecido, como mostrado abaixo. Os pares de impressão de polegar estampa de impressão de vários certificados podem ser adicionados na matriz de **parâmetros.** 

   Se o proxy inverso do ponto final estiver ligado a apresentar um certificado que o nome comum e a impressão digital emitentes correspondem a qualquer um dos valores aqui especificados, o canal SSL é estabelecido. 
   Ao não corresponder aos dados do certificado, o proxy inverso falha o pedido do cliente com um código de estado 502 (Bad Gateway). A linha de estado HTTP também conterá a frase "Certificado SSL inválido". 

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

- **ServiceCertificateThumbprints**: O proxy inverso verificará o certificado de serviço com base na sua impressão digital. Pode optar por seguir esta rota quando os serviços estiverem configurados com certificados auto-assinados: Especifique a Política de Validação de Certificados de **Aplicação** com fichas de valor **ServiceCertificateThumbprints** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Especifique também as impressões digitais com uma entrada **serviceCertificateThumbprints** na secção **ApplicationGateway/Http.** As impressões digitais múltiplas podem ser especificadas como uma lista separada da víramida no campo de valor, como mostrado abaixo:

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

   Se a impressão digital do certificado de servidor estiver listada nesta entrada de config, o proxy inverso sucede à ligação SSL. Caso contrário, encerra a ligação e falha o pedido do cliente com um 502 (Bad Gateway). A linha de estado HTTP também conterá a frase "Certificado SSL inválido".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção de pontos finais quando os serviços expõem pontos finais seguros e não seguros
O tecido de serviço suporta configurar vários pontos finais para um serviço. Para mais informações, consulte [Especificar recursos num manifesto](service-fabric-service-manifest-resources.md)de serviço .

O proxy inverso seleciona um dos pontos finais para encaminhar o pedido com base no parâmetro de consulta **ListenerName** no [serviço URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se o parâmetro **ListenerName** não for especificado, o proxy inverso pode escolher qualquer ponto final da lista de pontos finais. Dependendo dos pontos finais configurados para o serviço, o ponto final selecionado pode ser um ponto final HTTP ou HTTPS. Pode haver cenários ou requisitos em que pretenda que o representante inverso funcione num "modo de segurança"; isto é, não quer que o representante inverso seguro reencaminha os pedidos para pontos finais não seguros. Para definir o proxy inverso para o modo de segurança, especifique a entrada de configuração **SecureOnlyMode** com valor **verdadeiro** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)   

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
> Ao operar em **SecureOnlyMode**, se um cliente tiver especificado um **ListenerName** correspondente a um ponto final HTTP (não garantido), o proxy inverso falha o pedido com um código de estado HTTP 404 (não encontrado).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configuração da autenticação do certificado de cliente através do proxy inverso
A rescisão do SSL ocorre no proxy inverso e todos os dados do certificado de cliente são perdidos. Para que os serviços realizem a autenticação do certificado de cliente, especifique a definição **de ForwardClientCertificate** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

1. Quando o **ForwardClientCertificate** estiver definido como **falso,** o proxy inverso não solicitará o certificado de cliente durante o seu aperto de mão SSL com o cliente.
Este é o comportamento padrão.

2. Quando o **ForwardClientCertificate** estiver definido como **verdadeiro,** o proxy inverso solicita o certificado do cliente durante o seu aperto de mão SSL com o cliente.
Em seguida, encaminhará os dados do certificado de cliente num cabeçalho HTTP personalizado chamado **X-Cliente-Certificado**. O valor do cabeçalho é a cadeia de formato PEM codificada base64 do certificado do cliente. O serviço pode suceder/falhar o pedido com o código de estado adequado após a inspeção dos dados do certificado.
Se o cliente não apresentar um certificado, o proxy inverso encaminha um cabeçalho vazio e deixe o serviço tratar da caixa.

> [!NOTE]
> Procuração inversa é um mero avançado. Não realizará qualquer validação do certificado do cliente.


## <a name="next-steps"></a>Passos seguintes
* [Configurar e configurar procuração inversa num cluster](service-fabric-reverseproxy-setup.md).
* Consulte o [Proxy inverso da Configuração para ligar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Veja um exemplo de comunicação http entre serviços num [projeto de amostra no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com serviços fiáveis remoting](service-fabric-reliable-services-communication-remoting.md)
* [Web API que usa OWIN em Serviços Fiáveis](service-fabric-reliable-services-communication-webapi.md)
* [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
