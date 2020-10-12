---
title: Azure Service Fabric reverse proxy comunicação segura
description: Configure o proxy inverso para permitir uma comunicação segura de ponta a ponta numa aplicação de Tecido de Serviço Azure.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86256140"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Ligar a um serviço seguro com o proxy inverso

Este artigo explica como estabelecer uma ligação segura entre o proxy e os serviços invertidos, permitindo assim o fim do canal seguro. Para saber mais sobre o proxy invertido, consulte [o Reverse Proxy in Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> A ligação a serviços seguros só é suportada quando o proxy invertido estiver configurado para ouvir em HTTPS. Este artigo pressupõe que este seja o caso. Consulte o [proxy inverso de configuração no Tecido de Serviço Azure](service-fabric-reverseproxy-setup.md) para configurar o proxy inverso no Tecido de Serviço.

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Estabelecimento de ligação segura entre o representante e os serviços invertidos 

### <a name="reverse-proxy-authenticating-to-services"></a>Procuração inversa autenticando serviços:
O representante inverso identifica-se a serviços utilizando o seu certificado. Para os clusters Azure, o certificado é especificado com propriedade ***reverseProxyCertificate*** na secção de [tipo de recurso](../azure-resource-manager/templates/template-syntax.md) [**Microsoft.ServiceFabric/clusters**](/azure/templates/microsoft.servicefabric/clusters) do modelo de Gestor de Recursos. Para agrupamentos autónomos, o certificado é especificado quer com o ***ReverseProxyCertificate,*** quer com a propriedade ***ReverseProxyCertificateCommonNames*** na secção de **Segurança** de ClusterConfig.js. Para saber mais, consulte [Ativar o proxy invertido em agrupamentos autónomos](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

Os serviços podem implementar a lógica para verificar o certificado apresentado pelo representante inverso. Os serviços podem especificar os dados do certificado de cliente aceite como definições de configuração no pacote de configuração. Isto pode ser lido em tempo de execução e usado para validar o certificado apresentado pelo representante inverso. Consulte os [parâmetros de aplicação](service-fabric-manage-multiple-environment-app-configuration.md) para adicionar as definições de configuração. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Procuração inversa verificando a identidade do serviço através do certificado apresentado pelo serviço:
A proxy reversa suporta as seguintes políticas para realizar a validação do certificado de servidor dos certificados apresentados pelos serviços: Nenhum, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Para selecionar a política de procuração inversa a utilizar, especifique a **Política de Arquivamento de Aplicação certificativa** na secção **ApplicationGateway/Http** em [fabricSettings](service-fabric-cluster-fabric-settings.md).

A secção seguinte mostra detalhes de configuração para cada uma destas opções.

### <a name="service-certificate-validation-options"></a>Opções de validação de certificados de serviço 

- **Nenhum**: Procuração inversa ignora a verificação do certificado de serviço proxied e estabelece a ligação segura. Este é o comportamento padrão.
Especificar a **AplicaçãoCertificateValidationPolicy** com o valor **Nenhum** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

- **ServiceCommonNameAndIssuer**: Procuração inversa verifica o certificado apresentado pelo serviço com base no nome comum do certificado e na impressão digital do emitente imediato: Especifique a **AplicaçãoCertificateValidationPolicy** com valor **ServiceCommonNameAndIssuer** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Para especificar a lista de impressões digitais de nome comum e emitente de serviço, adicione uma secção [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) sob **o fabricSettings**, como mostrado abaixo. Vários certificados nomes comuns e pares de impressão digital emitente podem ser adicionados na matriz de **parâmetros.** 

   Se o representante de reversão do ponto final estiver ligado a apresentar um certificado que o nome comum e a impressão digital do emitente correspondam a qualquer um dos valores especificados aqui, é estabelecido um canal TLS.
   Ao não corresponder aos dados do certificado, o representante inverso falha o pedido do cliente com um código de estado 502 (Bad Gateway). A linha de estado HTTP também conterá a frase "Certificado SSL Inválido". 

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

- **ServiceCertificateThumbprints**: O representante inverso verificará o certificado de serviço proxied com base na sua impressão digital. Pode optar por seguir este percurso quando os serviços estiverem configurados com certificados auto-assinados: Especifique a **AplicaçãoCertificateValidationPolicy** com valor **ServiceCertificateThumbprints** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

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

   Especificar também as impressões digitais com uma entrada **ServiceCertificateThumbprints** na secção **ApplicationGateway/Http.** Várias impressões digitais podem ser especificadas como uma lista separada por vírgula no campo de valor, como mostrado abaixo:

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

   Se a impressão digital do certificado do servidor estiver listada nesta entrada config, o representante inverso sucede à ligação TLS. Caso contrário, termina a ligação e falha o pedido do cliente com um 502 (Bad Gateway). A linha de estado HTTP também conterá a frase "Certificado SSL Inválido".

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Lógica de seleção de pontos finais quando os serviços expõem pontos finais seguros e não seguros
O tecido de serviço suporta configurar vários pontos finais para um serviço. Para obter mais informações, consulte [Especificar os recursos num manifesto de serviço.](service-fabric-service-manifest-resources.md)

O proxy reverso seleciona um dos pontos finais para encaminhar o pedido com base no parâmetro de consulta **ListenerName** no [serviço URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se o parâmetro **Menu Desíprocorse** não for especificado, o representante inverso pode escolher qualquer ponto final da lista de pontos finais. Dependendo dos pontos finais configurados para o serviço, o ponto final selecionado pode ser um ponto final HTTP ou HTTPS. Pode haver cenários ou requisitos em que pretende que o representante inverso funcione num "modo seguro"; isto é, você não quer que o representante de marcha atrás seguro encaminhe pedidos para pontos finais não seguros. Para definir o proxy inverso para o modo apenas seguro, especifique a entrada de configuração **SecureOnlyMode** com valor **verdadeiro** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)   

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
> Ao operar em **SecureOnlyMode,** se um cliente tiver especificado um **Nome de Escuta** correspondente a um ponto final HTTP (não seguro), o representante inverso falha o pedido com um código de estado HTTP (Não Encontrado) 404.

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Criação da autenticação de certificado de cliente através do representante inverso
A rescisão de TLS ocorre no proxy reverso e todos os dados do certificado do cliente são perdidos. Para que os serviços realizem a autenticação do certificado de cliente, especifique a definição **forwardClientCertificate** na secção [**ApplicationGateway/Http.**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)

1. Quando **o ForwardClientCertificate** estiver definido como **falso,** o representante inverso não solicitará o certificado de cliente durante o seu aperto de mão TLS com o cliente.
Este é o comportamento padrão.

2. Quando **o ForwardClientCertificate** está definido como **verdadeiro,** o representante inverso solicita o certificado do cliente durante o seu aperto de mão TLS com o cliente.
Em seguida, reencaminha os dados do certificado do cliente num cabeçalho HTTP personalizado denominado **Certificado X-Cliente.** O valor do cabeçalho é a sequência de formato PEM codificada base64 do certificado do cliente. O serviço pode ter sucesso/falhar o pedido com o código de estado adequado após a inspeção dos dados do certificado.
Se o cliente não apresentar um certificado, o representante invertido encaminha um cabeçalho vazio e permite que o serviço trate do caso.

> [!NOTE]
> O representante inverso funciona apenas como um serviço de reencaminhamento. Não efetuará qualquer validação do certificado do cliente.


## <a name="next-steps"></a>Passos seguintes
* [Configurar e configurar](service-fabric-reverseproxy-setup.md)o proxy inverso num cluster .
* Consulte o [Configure proxy reverso para ligar a serviços seguros](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Veja um exemplo de comunicação HTTP entre serviços num [projeto de amostra no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chamadas de procedimento remoto com serviços fiáveis de remoing](service-fabric-reliable-services-communication-remoting.md)
* [API web que usa OWIN em Serviços Fiáveis](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Gerir certificados de cluster](service-fabric-cluster-security-update-certs-azure.md)
