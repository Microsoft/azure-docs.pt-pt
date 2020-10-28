---
title: Autoridades de certificados do Azure Ative Directory
description: Listagem de certificados de confiança utilizados no Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 887e76fb1fa3dc630b12862c49689b3fa923b656
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795365"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Autoridades de certificados utilizadas pelo Azure Ative Directory

> [!IMPORTANT]
> As informações nesta página são relevantes apenas para entidades que especificam explicitamente uma lista de Autoridades de Certificados aceitáveis (AA). Esta prática, conhecida como fixação de certificados, deve ser evitada a menos que não existam outras opções.

Qualquer entidade que tente aceder aos serviços de identidade do Azure Ative (Azure AD) através dos protocolos TLS/SSL será apresentada com certificados dos CAs listados abaixo. Se a entidade confiar nesses CAs, pode utilizar os certificados para verificar a identidade e a legitimidade dos serviços de identidade e estabelecer ligações seguras.

As autoridades de certificados podem ser classificadas em AA de raiz e CAs intermédios. Tipicamente, os CAs de raiz têm um ou mais CAs intermédios associados. Este artigo lista os CAs de raiz utilizados pelos serviços de identidade Azure AD e os CAs intermédios associados a cada uma dessas raízes. Para cada CA, incluímos identificadores de recursos uniformes (URIs) para descarregar os ficheiros De Acesso à Informação da Autoridade Associada (AIA) e os ficheiros Do Ponto de Distribuição da Lista de Revogação de Certificados (CDP). Quando apropriado, também fornecemos um URI ao ponto final do Protocolo de Estado do Certificado Online (OCSP).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>CAs usados nas nuvens do Governo Azure Public e Azure US

Serviços diferentes podem utilizar diferentes CAs de raiz ou intermédios.

### <a name="digicert-global-root-g2"></a>Raiz Global DigiCert G2


| AC de Raiz| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - |- |-|-|-|-|
| Raiz Global DigiCert G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1 de agosto de 2013 <br>15 de janeiro de 2038| df3c24f9f666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[CDP](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>CAs intermédios associados

| Emissão e Ca Intermédia| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| Microsoft Azure TLS emitindo CA 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 de julho de 2020<br>27 de junho de 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TL%20Issuing%20CA%2001.crl)|
|Microsoft Azure TLS emitindo CA 02| 0c6ae97cced59983 8690a00a9ea53214| 29 de julho de 2020<br>27 de junho de 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure TLS emitindo CA 05| 0d7bede97d8209967a 52631b8bdd18bd18bd| 29 de julho de 2020<br>27 de junho de 2024| 6c3af02e7f269aa73a fd0eff2a8a8a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure TLS emitindo CA 06| 02e79171fb8021e93fe 2d983834c50c0| 29 de julho de 2020<br>27 de junho de 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20zure%20TLS%20Issuing%20CA%2006%20-%20xsign.crt)<br>[CDP](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Raiz cybertrust de Baltimore

| AC de Raiz| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| Raiz cybertrust de Baltimore| 020000b9| 12 de maio de 2000<br>12 de maio de 2025| d4de20d05e66fc53fe 1aa50882c78db2852cae474|<br>[CDP](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>CAs intermédios associados

| Emissão e Ca Intermédia| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaaa 59f98eaf4a206004eb2516a| 21 de julho de 2020<br>8 de outubro de 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](https://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13ddd56cdaa 6ab6e2c04440be4a429c75| 21 de julho de 2020<br>20 de maio de 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[CDP](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](https://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| AC de Raiz| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac9591c74a| 9 de novembro de 2006<br>9 de novembro de 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>CAs intermédios associados

| Emissão e Ca Intermédia| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8 de março de 2013 8 de março de 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[CDP](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |


 

## <a name="cas-used-in-azure-china-21vianet-cloud"></a>CAs usados na nuvem Azure China 21Vianet

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| AC de Raiz| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 9 de novembro de 2006<br>9 de novembro de 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [CDP](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>CA Intermediário Associado

| Emissão e Ca Intermédia| Número de série| Data de Expiração da Data de Emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | - |
| DigiCert Basic RSA CN CA G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 de março de 2020<br>4 de março de 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[CDP](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Passos Seguintes
[Conheça as cadeias de encriptação Microsoft 365](https://docs.microsoft.com/microsoft-365/compliance/encryption-office-365-certificate-chains?view=o365-worldwide)
