---
title: Visão geral da autenticação mútua no Gateway de Aplicações Azure
description: Este artigo é uma visão geral da autenticação mútua no Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231527"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Visão geral da autenticação mútua com Gateway de Aplicação (Pré-visualização)

A autenticação mútua, ou a autenticação do cliente, permite que o Gateway de Aplicação autentica os pedidos de envio do cliente. Normalmente, apenas o cliente autentica o Gateway de Aplicações; a autenticação mútua permite que tanto o cliente como o Gateway de aplicação autentem a autenticação uns dos outros. 

> [!NOTE]
> Recomendamos a utilização de TLS 1.2 com autenticação mútua, uma vez que o TLS 1.2 será mandatado no futuro. 

## <a name="mutual-authentication"></a>Autenticação mútua

A Application Gateway suporta a autenticação mútua baseada em certificados onde pode enviar um certificado(s) de cliente de confiança para o Gateway de Aplicação e o gateway utilizará esse certificado para autenticar o cliente enviando um pedido para o gateway. Com o aumento dos casos de utilização de IoT e o aumento dos requisitos de segurança em todas as indústrias, a autenticação mútua proporciona uma forma de gerir e controlar quais os clientes que podem falar com o seu Gateway de Aplicações. 

Para configurar a autenticação mútua, é necessário carregar um certificado ca de cliente fidedigno para ser carregado como parte da parte de autenticação do cliente de um perfil SSL. O perfil SSL terá então de ser associado a um ouvinte para completar a configuração da autenticação mútua. Deve haver sempre um certificado de CA raiz no certificado de cliente que você carrega. Também pode carregar uma cadeia de certificados, mas a cadeia deve incluir um certificado de CA raiz, além de tantos certificados de CA intermédios como você gostaria. 

Por exemplo, se o seu certificado de cliente contiver um certificado de CA raiz, vários certificados de CA intermédios e um certificado de folha, certifique-se de que o certificado de CA raiz e todos os certificados de CA intermédios são enviados para o Gateway de Aplicação num único ficheiro. Para obter mais informações sobre como extrair um certificado de CA de cliente fidedignos, consulte [como extrair certificados de CA de cliente fidedignos.](./mutual-authentication-certificate-management.md)

Se estiver a carregar uma cadeia de certificados com certificados ca raiz e ca intermédio, a cadeia de certificados deve ser carregada como ficheiro PEM ou CER para o gateway. 

> [!NOTE] 
> A autenticação mútua só está disponível nos Standard_v2 e WAF_v2 SKUs. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certificados suportados para a autenticação mútua

O Application Gateway suporta os seguintes tipos de certificados:

- Certificado CA (Autoridade de Certificados): Um certificado de CA é um certificado digital emitido por uma autoridade de certificados (CA)
- Certificados ca auto-assinados: Os navegadores de clientes não confiam nestes certificados e avisam o utilizador de que o certificado do serviço virtual não faz parte de uma cadeia de fidedignidade. Os certificados de CA auto-assinados são bons para testes ou ambientes onde os administradores controlam os clientes e podem contornar com segurança os alertas de segurança do navegador. As cargas de trabalho de produção nunca devem utilizar certificados de CA auto-assinados.

Para obter mais informações sobre como configurar a autenticação mútua, consulte [configurar a autenticação mútua com o Application Gateway.](./mutual-authentication-portal.md)

> [!IMPORTANT]
> Certifique-se de que envia toda a cadeia de certificados ca do cliente fidedigno para o Gateway de aplicações ao utilizar a autenticação mútua. 

## <a name="additional-client-authentication-validation"></a>Validação adicional de autenticação do cliente

### <a name="verify-client-certificate-dn"></a>Verificar certificado de cliente DN

Tem a opção de verificar o emitente imediato do certificado do cliente e apenas permitir que o Gateway de Aplicação confie nesse emitente. Estas opções estão desligadas por padrão, mas podes habiligar isto através do Portal, PowerShell ou Azure CLI. 

Se optar por ativar o Gateway de Aplicação para verificar o emitente imediato do certificado de cliente, eis como determinar o que o emissor de certificado de cliente DN será extraído dos certificados carregados. 
* **Cenário 1:** A cadeia de certificados inclui: certificado de raiz - certificado intermédio - certificado de folha 
    * *O* nome do certificado intermédio é o que o Application Gateway irá extrair como o emissor de certificado de cliente DN e será verificado contra. 
* **Cenário 2:** A cadeia de certificados inclui: certificado de raiz - certificado intermédio1 - certificado intermédio2 - certificado de folha
    * O nome *do certificado intermédio2* será o que é extraído como o emissor de certificado de cliente DN e será verificado contra. 
* **Cenário 3:** Cadeia de certificados inclui: certificado de raiz - certificado de folha 
    * *O* nome do sujeito do certificado de raiz será extraído e utilizado como emitente de certificado de cliente DN.
* **Cenário 4:** Cadeias de certificados múltiplas do mesmo comprimento no mesmo ficheiro. A cadeia 1 inclui: certificado de raiz - certificado intermédio1 - certificado de folha. A cadeia 2 inclui: certificado de raiz - certificado intermédio2 - certificado de folha. 
    * O nome *do certificado intermédio1* será extraído como emitente de certificado de cliente DN.  
* **Cenário 5:** Cadeias de certificados múltiplas de diferentes comprimentos no mesmo ficheiro. A cadeia 1 inclui: certificado de raiz - certificado intermédio1 - certificado de folha. A cadeia 2 inclui o certificado de raiz - certificado intermédio2 - certificado intermédio3 - certificado de folha. 
    * O nome *do certificado intermédio3* será extraído como emitente de certificado de cliente DN. 

> [!IMPORTANT]
> Recomendamos apenas o upload de uma cadeia de certificados por ficheiro. Isto é especialmente importante se ativar o certificado de cliente DN. Ao carregar várias cadeias de certificados num ficheiro, acabará no cenário quatro ou cinco e poderá encontrar problemas mais tarde quando o certificado de cliente apresentado não corresponder ao emissor de certificados de cliente DN Application Gateway extraído das cadeias. 

Para obter mais informações sobre como extrair cadeias de certificados de ca cliente fidedignos, consulte [como extrair cadeias de certificados de cliente fidedignos](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Variáveis de servidor 

Com a autenticação mútua, existem variáveis adicionais de servidor que pode utilizar para passar informações sobre o certificado de cliente para os servidores backend por trás do Gateway de aplicações. Para obter mais informações sobre quais as variáveis do servidor disponíveis e como usá-las, verifique as [variáveis do servidor](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Passos seguintes

Depois de aprender sobre a autenticação mútua, vá ao [Configure Application Gateway com autenticação mútua no PowerShell](./mutual-authentication-powershell.md) para criar um Gateway de aplicação utilizando a autenticação mútua. 

