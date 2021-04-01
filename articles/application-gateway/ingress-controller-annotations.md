---
title: Anotações do controlador de entrada de gateway de aplicação
description: Este artigo fornece documentação sobre as anotações específicas do Controlador de Entradas de Gateway de Aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397455"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anotações para controlador de entrada de gateway de aplicação 

## <a name="introductions"></a>Apresentações

O recurso Kubernetes Ingress pode ser anotado com pares arbitrários de chave/valor. A AGIC baseia-se em anotações para programar funcionalidades de Gateway de Aplicação, que não são configuráveis através do INgress YAML. As anotações ingres são aplicadas a todas as definições HTTP, piscinas de backend e ouvintes derivados de um recurso de entrada.

## <a name="list-of-supported-annotations"></a>Lista de anotações apoiadas

Para que um recurso Ingress seja observado pela AGIC, **deve ser anotado** com `kubernetes.io/ingress.class: azure/application-gateway` . Só então a AGIC trabalhará com o recurso Ingress em questão.

| Chave de anotação | Tipo de Valor | Valor Predefinido | Valores Permitidos
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (segundos) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (segundos) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefixo do caminho de backend

Esta anotação permite que o caminho de backend especificado num recurso ingresso seja reescrito com prefixo especificado nesta anotação. Isto permite que os utilizadores exponham serviços cujos pontos finais são diferentes dos nomes de pontos finais usados para expor um serviço num recurso de entrada.

### <a name="usage"></a>Utilização

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
No exemplo acima, definimos um recurso ingresso nomeado `go-server-ingress-bkprefix` com uma `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` anotação. A anotação indica a porta de entrada da aplicação para criar uma definição HTTP, que terá um prefixo de caminho para o caminho `/hello` para `/test/` .

> [!NOTE] 
> No exemplo acima, temos apenas uma regra definida. No entanto, as anotações são aplicáveis a todo o recurso ingresso, por isso, se um utilizador tivesse definido várias regras, o prefixo do caminho de backend seria configurado para cada um dos caminhos especificados. Assim, se um utilizador quiser regras diferentes com prefixos de caminhos diferentes (mesmo para o mesmo serviço) teria de definir diferentes recursos de entrada.

## <a name="tls-redirect"></a>Redirecionamento de TLS

O Gateway de Aplicações [pode ser configurado](./redirect-overview.md) para redirecionar automaticamente os URLs HTTP para as suas congéneres HTTPS. Quando esta anotação estiver presente e o TLS estiver devidamente configurado, o controlador Kubernetes Ingress criará uma [regra de encaminhamento com uma configuração de redireccionamento](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) e aplicará as alterações no seu Gateway de Aplicação. O redirecionamento criado será HTTP `301 Moved Permanently` .

### <a name="usage"></a>Utilização

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Drenagem de ligação

`connection-draining`: Esta anotação permite que os utilizadores especifiquem se permitem a drenagem da ligação.
`connection-draining-timeout`: Esta anotação permite que os utilizadores especifiquem um intervalo após o qual o Application Gateway terminará os pedidos para o ponto final de retenção de drenagem.

### <a name="usage"></a>Utilização

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Afinidade baseada em cookies

Esta anotação permite especificar se permite a afinidade baseada em cookies.

### <a name="usage"></a>Utilização

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Tempo Limite do Pedido

Esta anotação permite especificar o tempo limite de pedido em segundos após o qual o Application Gateway falhará o pedido se a resposta não for recebida.

### <a name="usage"></a>Utilização

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Exemplo

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Utilizar IP privado

Esta anotação permite-nos especificar se devemos expor este ponto final no Private IP do Application Gateway.

> [!NOTE]
> * O Application Gateway não suporta vários IPs na mesma porta (exemplo: 80/443). A entrada com anotação `appgw.ingress.kubernetes.io/use-private-ip: "false"` e outra com on fará com que a `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` AGIC falhe na atualização do Gateway de Aplicação.
> * Para o Application Gateway que não tenha um IP privado, Ingresses com `appgw.ingress.kubernetes.io/use-private-ip: "true"` será ignorado. Isto refletir-se-á nos registos do controlador e nos eventos de entrada para aqueles que se ingressam com `NoPrivateIP` aviso.


### <a name="usage"></a>Utilização
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protocolo de Backend

Esta anotação permite-nos especificar o protocolo que o Application Gateway deve utilizar enquanto fala com os Pods. Protocolos apoiados: `http` , `https`

> [!NOTE]
> * Embora os certificados auto-assinados sejam suportados no Application Gateway, atualmente, a AGIC só suporta `https` quando os Pods estão a usar o certificado assinado por um conhecido CA.
> * Certifique-se de que não utiliza a porta 80 com HTTPS e a porta 443 com HTTP nas Cápsulas.

### <a name="usage"></a>Utilização
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Exemplo
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```