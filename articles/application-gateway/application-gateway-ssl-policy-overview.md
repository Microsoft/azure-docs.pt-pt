---
title: Visão geral da política do TLS para gateway de aplicação Azure
description: Aprenda a configurar a política TLS para o Azure Application Gateway e reduza a encriptação e desencriptação a partir de uma fazenda de servidores de back-end.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631695"
---
# <a name="application-gateway-tls-policy-overview"></a>Visão geral da política do Gateway TLS de aplicação

Pode utilizar o Gateway de Aplicações Azure para centralizar a gestão de certificados TLS/SSL e reduzir a encriptação e desencriptação a partir de uma fazenda de servidores de back-end. Este manuseamento centralizado de TLS também permite especificar uma política central de TLS que se adequa aos seus requisitos de segurança organizacional. Isto ajuda-o a cumprir os requisitos de conformidade, bem como as diretrizes de segurança e as práticas recomendadas.

A política TLS inclui o controlo da versão do protocolo TLS, bem como as suites de cifra e a ordem em que as cifras são usadas durante um aperto de mão TLS. A Application Gateway oferece dois mecanismos para controlar a política TLS. Pode utilizar uma política predefinida ou uma política personalizada.

## <a name="predefined-tls-policy"></a>Política de TLS predefinida

Application Gateway tem três políticas de segurança predefinidas. Pode configurar o seu portal com qualquer uma destas políticas para obter o nível de segurança adequado. Os nomes das políticas são anotados pelo ano e mês em que foram configurados. Cada política oferece diferentes versões de protocolo TLS e suítes de cifra. Recomendamos que utilize as mais recentes políticas TLS para garantir a melhor segurança TLS.

## <a name="known-issue"></a>Problema conhecido
O Application Gateway v2 não suporta as seguintes cifras DHE e estas não serão utilizadas para as ligações TLS com os clientes, mesmo que sejam mencionadas nas políticas predefinidas. Em vez de cifras DHE, recomenda-se a fixação e mais rápidas cifras ECDHE.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propriedade  |Valor  |
|---|---|
|Nome     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Predefinição| Verdade (se não for especificada nenhuma política predefinida) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propriedade  |Valor  |
|   ---      |  ---       |
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Predefinição| Falso |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propriedade  |Valor  |
|---|---|
|Nome     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Predefinição| Falso |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Política de TLS personalizado

Se uma política TLS predefinida precisa de ser configurada para os seus requisitos, deve definir a sua própria política de TLS personalizada. Com uma política TLS personalizada, você tem controlo total sobre a versão mínima do protocolo TLS para suportar, bem como as suítes de cifra suportadas e sua ordem prioritária.

> [!IMPORTANT]
> Se estiver a utilizar uma política SSL personalizada no Application Gateway v1 SKU (Standard ou WAF), certifique-se de que adiciona a cifra obrigatória "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" à lista. Esta cifra é necessária para permitir métricas e registar-se no Gateway de Aplicação v1 SKU.
> Isto não é obrigatório para o Gateway de Aplicação v2 SKU (Standard_v2 ou WAF_v2).
 
### <a name="tlsssl-protocol-versions"></a>Versões de protocolo TLS/SSL

* SSL 2.0 e 3.0 são desativadas por padrão para todos os gateways de aplicações. Estas versões de protocolo não são configuráveis.
* Uma política TLS personalizada dá-lhe a opção de selecionar qualquer um dos três protocolos seguintes como a versão mínima do protocolo TLS para o seu gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se não for definida nenhuma política TLS, os três protocolos (TLSv1_0, TLSv1_1 e TLSv1_2) estão ativados.

### <a name="cipher-suites"></a>Suítes cifra

Application Gateway suporta as seguintes suítes de cifra a partir das quais pode escolher a sua política personalizada. A encomenda das suítes cifra determina a ordem prioritária durante a negociação do TLS.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> As suítes de cifra TLS utilizadas para a ligação baseiam-se também no tipo de certificado utilizado. No cliente para as ligações de gateway de aplicação, as suítes cifra utilizadas baseiam-se no tipo de certificados de servidor no ouvinte do gateway de aplicações. No gateway de aplicações para reto às ligações de piscina, as suítes de cifra utilizadas baseiam-se no tipo de certificados de servidor nos servidores de backend pool.

## <a name="next-steps"></a>Passos seguintes

Se quiser aprender a configurar uma política de TLS, consulte [versões de política configure TLS e suítes de cifra no Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
