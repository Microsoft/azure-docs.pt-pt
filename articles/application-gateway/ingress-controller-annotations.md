---
title: Anotações do Controlador de Ingress ingresso gateway de aplicação
description: Este artigo fornece documentação sobre as anotações específicas do Controlador de Ingress gateway de aplicação.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335813"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Anotações para controlador de entrada de gateway de aplicação 

## <a name="introductions"></a>Apresentações

O recurso Kubernetes Ingress pode ser anotado com pares de chaves/valor arbitrários. A AGIC baseia-se em anotações para programar funcionalidades de Gateway de Aplicação, que não são configuráveis através do Ingress YAML. As anotações de ingresso são aplicadas a todos os cenários HTTP, piscinas de backend e ouvintes derivados de um recurso de ingresso.

## <a name="list-of-supported-annotations"></a>Lista de anotações apoiadas

Para que um recurso Ingress seja observado pela AGIC, `kubernetes.io/ingress.class: azure/application-gateway`deve ser **anotado** com . Só então a AGIC trabalhará com o recurso Ingress em questão.

| Chave de Anotação | Tipo de Valor | Valor Predefinido | Valores Permitidos
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`(segundos) | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`(segundos) | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefixo do caminho de backend

Esta anotação permite que o caminho de backend especificado num recurso de entrada seja reescrito com prefixo especificado nesta anotação. Isto permite que os utilizadores exponham serviços cujos pontos finais são diferentes dos nomes finais usados para expor um serviço num recurso de ingresso.

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
No exemplo acima, definimos um `go-server-ingress-bkprefix` recurso ingresso `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` nomeado com uma anotação. A anotação indica a porta de entrada da aplicação para criar `/hello` uma `/test/`definição HTTP, que terá um prefixo de caminho para o caminho para .

> [!NOTE] 
> No exemplo acima, temos apenas uma regra definida. No entanto, as anotações aplicam-se a todo o recurso ingress, pelo que se um utilizador tivesse definido várias regras, o prefixo do caminho de backend seria configurado para cada um dos caminhos especificados. Assim, se um utilizador quer regras diferentes com prefixos de caminho diferentes (mesmo para o mesmo serviço) teria de definir diferentes recursos de ingresso.

## <a name="tls-redirect"></a>Redirecionamento TLS

O Gateway de aplicações [pode ser configurado](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) para redirecionar automaticamente OS URLs HTTP para as suas congéneres HTTPS. Quando esta anotação estiver presente e o TLS estiver corretamente configurado, o controlador Kubernetes Ingress criará uma regra de [encaminhamento com uma configuração de redirecionamento](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) e aplicará as alterações ao gateway da aplicação. O redirecionamento `301 Moved Permanently`criado será HTTP .

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

## <a name="connection-draining"></a>Drenagem de Ligação

`connection-draining`: Esta anotação permite que os utilizadores especifiquem se permitem a drenagem da ligação.
`connection-draining-timeout`: Esta anotação permite que os utilizadores especifiquem um prazo após o qual o Gateway da Aplicação irá encerrar os pedidos para o ponto final de drenagem.

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

## <a name="cookie-based-affinity"></a>Afinidade Baseada em Cookies

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

Esta anotação permite especificar o prazo de pedido em segundos após o qual o Gateway da Aplicação falhará o pedido se a resposta não for recebida.

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

Esta anotação permite-nos especificar se devemos expor este ponto final em IP privado de Gateway de Aplicação.

> [!NOTE]
> * O Gateway de aplicação não suporta vários IPs na mesma porta (exemplo: 80/443). Ingress com anotação `appgw.ingress.kubernetes.io/use-private-ip: "false"` `appgw.ingress.kubernetes.io/use-private-ip: "true"` e `HTTP` outro com on fará com que a AGIC falhe na atualização do Gateway de Aplicação.
> * Para o Application Gateway que não tem um `appgw.ingress.kubernetes.io/use-private-ip: "true"` IP privado, ingresses com será ignorado. Isto irá refletir-se nos registos do controlador `NoPrivateIP` e nos eventos de ingresso para aqueles ingressos com aviso.


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

## <a name="backend-protocol"></a>Protocolo de backend

Esta anotação permite-nos especificar o protocolo que o Gateway de Aplicação deve utilizar durante a conversa com as Cápsulas. Protocolos apoiados: `http``https`

> [!NOTE]
> * Enquanto os certificados auto-assinados são suportados no `https` Application Gateway, atualmente, a AGIC só suporta quando os Pods estão usando certificado assinado por um conhecido CA.
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