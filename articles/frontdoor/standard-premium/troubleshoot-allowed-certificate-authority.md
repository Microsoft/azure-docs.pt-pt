---
title: Autoridades de certificados permitidas para Azure Front Door Standard/Premium (Pré-visualização)
description: Este artigo lista todas as autoridades de certificados permitidas quando cria o seu próprio certificado.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100238"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autoridades de certificados permitidas para Azure Front Door Standard/Premium (Pré-visualização)

Quando ativa a função HTTPS utilizando o seu próprio certificado para um domínio personalizado Azure Front Door Standard/Premium. Precisa de uma autoridade de certificados permitida (CA) para criar o seu certificado TLS/SSL. Caso contrário, se utilizar um CA não permitido ou um certificado auto-assinado, o seu pedido será rejeitado.

Os seguintes CAs são permitidos quando cria o seu próprio certificado:

- Raiz externa de CA AddTrust
- Raiz AlfaSSL CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Raiz de piloto automático CA
- Raiz cybertrust de Baltimore
- Autoridade de Certificação Primária Pública de Classe 3
- Autoridade de Certificação COMODO RSA
- COMODO RSA Validação de Domínio Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- Raiz Global DigiCert G2
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 Servidor de Validação Estendida CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- Raiz DST CA X3
- D-trust Root Class 3 CA 2 2009
- Encriptação em todos os lugares DV TLS CA
- Autoridade de Certificação de Raiz de Confiança
- Autoridade de Certificação de Raiz - G2
- Autoridade de Certificação Entrust.net (2048)
- GeoTrust Global CA
- Autoridade de Certificação Primária da GeoTrust
- Autoridade de Certificação Primária GeoTrust - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Validação Estendida CA - SHA256 - G2
- Validação da Organização GlobalSign CA - G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority - G2
- Go Daddy Secure Certificate Authority - G2
- Vamos encriptar a Autoridade X3
- Microsec e-Szigno Root CA 2009
- Raiz QuoVadis CA2 G3
- Rapidssl RSA CA 2018
- Raiz de Comunicação de Segurança RootCA1
- Raiz de Comunicação de Segurança RootCA2
- Raiz de Comunicação de Segurança RootCA3
- Symantec Classe 3 EV SSL CA - G3
- Symantec Classe 3 Servidor Seguro CA - G4
- Symantec Enterprise Mobile Root para a Microsoft
- Descongelar a raiz primária ca
- Descongelar raiz primária CA - G2
- Descongelar a raiz primária CA - G3
- Descongelar RSA CA 2018
- Descongelamento timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Classe 3 Validação Alargada SSL CA
- VeriSign Classe 3 Validação Alargada SSL SGC CA
- VeriSign Classe 3 Autoridade de Certificação Primária Pública - G5
- VeriSign International Server CA - Classe 3
- Raiz de serviço de marcação de tempo veriSign
- Autoridade de Certificação Universal de Raízes veriSign
