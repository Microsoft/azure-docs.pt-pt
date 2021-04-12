---
title: Resolução de problemas da autenticação mútua no Gateway de Aplicações Azure
description: Saiba como resolver a autenticação mútua no Gateway de Aplicação
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231508"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Resolução de problemas erros de autenticação mútua no Gateway de Aplicação (Preview)

Saiba como resolver problemas com autenticação mútua ao utilizar o Application Gateway. 

## <a name="overview"></a>Descrição Geral 

Depois de configurar a autenticação mútua num Gateway de aplicações, pode surgir uma série de erros ao tentar utilizar a autenticação mútua. Algumas causas comuns para erros incluem:

* Carregado um certificado ou cadeia de certificados sem um certificado de CA raiz
* Carregou uma cadeia de certificados com certificados ca de raiz múltiplas
* Carregou uma cadeia de certificados que continha apenas um certificado de folha sem certificado de AC 
* Erros de validação devidos ao incompatibilidade do emitente DN  

Vamos passar por diferentes cenários que possas encontrar e como resolver esses cenários. Em seguida, endereçaremos códigos de erro e explicaremos as causas prováveis de certos códigos de erro que poderá estar a ver com autenticação mútua. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Resolução de problemas de cenário - problemas de configuração
Há alguns cenários que poderá estar a enfrentar ao tentar configurar a autenticação mútua. Vamos passar por como resolver problemas em algumas das armadilhas mais comuns. 

### <a name="self-signed-certificate"></a>Certificado autoassinado

#### <a name="problem"></a>Problema 

O certificado de cliente que carregou é um certificado auto-assinado e está a resultar no código de erro ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Solução 

Verifique duas vezes se o certificado auto-assinado que está a usar tem a extensão *BasicConstraintsOid* = "2.5.29.19" que indica que o sujeito pode funcionar como um CA. Isto garantirá que o certificado utilizado seja um certificado de CA. Para obter mais informações sobre como gerar certificados de cliente auto-assinados, consulte [certificados de cliente fidedignos.](./mutual-authentication-certificate-management.md)

## <a name="scenario-troubleshooting---connectivity-problems"></a>Resolução de problemas de cenário - problemas de conectividade

Pode ter sido capaz de configurar a autenticação mútua sem problemas, mas está a ter problemas ao enviar pedidos para o seu Gateway de Aplicações. Abordamos alguns problemas e soluções comuns na secção seguinte. Pode encontrar a propriedade *sslClientVerify* nos registos de acesso do seu Gateway de Aplicações. 

### <a name="sslclientverify-is-none"></a>SslClientVerify é NENHUM

#### <a name="problem"></a>Problema 

A propriedade *sslClientVerify* aparece como "NENHUMA" nos seus registos de acesso. 

#### <a name="solution"></a>Solução 

Isto é visto quando o cliente não envia um certificado de cliente ao enviar um pedido para o Gateway de Aplicação. Isto pode acontecer se o cliente que envia o pedido para o Gateway de aplicações não estiver configurado corretamente para usar certificados de cliente. Uma forma de verificar se a configuração de autenticação do cliente no Application Gateway está a funcionar como esperado é através do seguinte comando OpenSSL:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

A `-cert` bandeira é o certificado de folha, a bandeira é o ficheiro chave privado do `-key` cliente. 

Para obter mais informações sobre como utilizar o comando OpenSSL, `s_client` consulte a página [manual](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)deles.

### <a name="sslclientverify-is-failed"></a>SslClientVerify é FALHADO

#### <a name="problem"></a>Problema

A propriedade *sslClientVerify* está a aparecer como "FAILED" nos seus registos de acesso. 

#### <a name="solution"></a>Solução

Há uma série de potenciais causas para falhas nos registos de acesso. Abaixo está uma lista de causas comuns para o insucesso:
* **Não é possível obter o certificado de emitente:** O certificado de emitente do certificado de cliente não foi encontrado. Isto normalmente significa que a cadeia de certificados ca cliente fidedigna não está completa no Gateway de aplicação. Validar que a cadeia de certificados ca do cliente fidedigno carregada no Gateway de Aplicação está completa.  
* **Não é possível obter o certificado de emitente local:** Semelhante ao não conseguir obter o certificado de emitente, o certificado de emitente do certificado de cliente não foi encontrado. Isto normalmente significa que a cadeia de certificados ca cliente fidedigna não está completa no Gateway de aplicação. Validar que a cadeia de certificados ca do cliente fidedigno carregada no Gateway de Aplicação está completa.
* **Não é possível verificar o primeiro certificado:** Não é possível verificar o certificado do cliente. Este erro ocorre especificamente quando o cliente apresenta apenas o certificado de folha, cujo emitente não é confiável. Validar que a cadeia de certificados ca do cliente fidedigno carregada no Gateway de Aplicação está completa.
* **Não é possível verificar o emitente de certificado de cliente:** Este erro ocorre quando a configuração *Verificar o NciientCertIssuerDN* é definida como verdadeira. Isto acontece normalmente quando o Emitente DN do certificado de cliente não corresponde ao *ClientCertificateIssuerDN* extraído da cadeia de certificados ca cliente fidedigna carregada pelo cliente. Para obter mais informações sobre como o Gateway de Aplicação extrai o *ClientCertificateIssuerDN,* consulte [o emitente de extração de Gateway de Aplicação DN](./mutual-authentication-overview.md#verify-client-certificate-dn). Como melhor prática, certifique-se de que está a enviar uma cadeia de certificados por ficheiro para o Application Gateway. 

Para obter mais informações sobre como extrair toda a cadeia de certificados ca cliente fidedigna para carregar para o Application Gateway, consulte [como extrair cadeias de certificados ca cliente fidedignos](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Resolução de problemas de código de erro
Se estiver a ver algum dos seguintes códigos de erro, temos algumas soluções recomendadas para ajudar a resolver o problema que pode estar a enfrentar. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Código de erro: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Causa

Há dados de certificado que faltam. O certificado enviado pode ter sido um ficheiro vazio sem qualquer dado de certificado. 

#### <a name="solution"></a>Solução

Validar que o ficheiro de certificado enviado não tem dados em falta. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Código de erro: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Causa

Há uma chave privada na cadeia de certificados. Não deveria haver uma chave privada na cadeia de certificados. 

#### <a name="solution"></a>Solução

Verifique duas vezes a cadeia de certificados que foi carregada e remova a chave privada que fazia parte da cadeia. Recarregar a corrente sem a chave privada. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Código de erro: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Causa

Há duas causas potenciais por trás deste código de erro.
1. A análise falhou devido ao facto de a cadeia não ter sido apresentada no formato certo. A Application Gateway espera que uma cadeia de certificados esteja em formato PEM e também espera que os dados individuais dos certificados sejam delimitados. 
2. O parser não encontrou nada para analisar. O ficheiro carregado poderia potencialmente apenas ter os delimiters, mas nenhum dado de certificado. 

#### <a name="solution"></a>Solução

Dependendo da causa deste erro, existem duas soluções potenciais. 
* Validar que a cadeia de certificados carregada estava no formato certo (PEM) e que os dados do certificado foram devidamente delimitados. 
* Verifique se o ficheiro de certificado carregado continha os dados do certificado para além dos delimiters. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Código de erro: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Causa

O certificado carregado continha apenas um certificado de folha sem certificado de ADMISS. O upload de uma cadeia de certificados com certificados de CA e um certificado de folha é aceitável, uma vez que o certificado de folha seria simplesmente ignorado, mas um certificado deve ter um CA. 

#### <a name="solution"></a>Solução 

Verifique duas vezes a cadeia de certificados que foi carregada continha mais do que apenas o certificado de folha. A extensão *BasicConstraintsOid* = extensão "2.5.29.19" deve estar presente e indicar que o sujeito pode funcionar como um CA.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Código de erro: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Causa

A cadeia de certificados continha vários certificados de CA de raiz *ou* continha certificados de CA de raiz zero. 

#### <a name="solution"></a>Solução

Os certificados carregados devem conter exatamente um certificado de CA de raiz (e quantos certificados intermédios de CA necessários).


