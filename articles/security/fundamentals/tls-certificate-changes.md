---
title: Alterações no certificado Azure TLS
description: Alterações no certificado Azure TLS
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 9337349914748a38152b97cab50e15afbab3040e
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495877"
---
# <a name="azure-tls-certificate-changes"></a>Alterações no certificado Azure TLS  

A Microsoft está a atualizar os serviços da Azure para utilizar certificados TLS de um conjunto diferente de Autoridades de Certificados De Raiz (CAs). Esta alteração está a ser feita porque os certificados ac atuais não cumprem um dos requisitos de Base do Fórum CA/Browser.

## <a name="when-will-this-change-happen"></a>Quando é que esta mudança vai acontecer?

- Os serviços [do Azure Ative Directory](/azure/active-directory) (Azure AD) iniciaram esta transição a 7 de julho de 2020.
- Todos os pontos finais Azure TLS/SSL recentemente criados contêm certificados atualizados acorrentados aos novos CAs raiz.
- Os pontos finais do Azure existentes vão transitar de forma faseada a partir de 13 de agosto de 2020.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) e [DPS](/azure/iot-dps/) permanecerão na Baltimore CyberTrust Root CA, mas os seus CAs intermédios mudarão. [Clique aqui para mais detalhes.](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456)
- [O Azure Storage](/azure/storage) permanecerá na Baltimore CyberTrust Root CA, mas os seus CAs intermédios vão mudar. [Clique aqui para mais detalhes.](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-changes-are-coming-and-why-you-care/ba-p/1705518)

> [!IMPORTANT]
> Os clientes poderão ter de atualizar as suas aplicações após esta alteração para evitar falhas de conectividade ao tentarem ligar-se aos serviços Azure.

## <a name="what-is-changing"></a>O que está a mudar?

Hoje, a maioria dos certificados TLS utilizados pela cadeia de serviços Azure até à seguinte Root CA:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [Raiz cybertrust de Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a5082c78db2852cae474 |

Os certificados TLS utilizados pelos serviços Azure irão acorrentar até um dos seguintes CAs raiz:

| Nome comum da AC | Impressão digital (SHA1) |
|--|--|
| [Raiz Global DigiCert G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9f666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d6d6d40c6dd2fb19c5436 |
| [Raiz cybertrust de Baltimore](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a5082c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Quando posso retirar a velha impressão digital intermediária?

Os certificados de CA em vigor *só* serão revogados a 15 de fevereiro de 2021. Depois dessa data, pode remover as impressões digitais antigas do seu código.

Se esta data mudar, será notificado da nova data de revogação.

## <a name="will-this-change-affect-me"></a>Esta mudança vai afetar-me? 

Esperamos que **a maioria dos clientes da Azure não** sejam impactados.  No entanto, a sua aplicação pode ser impactada se especificar explicitamente uma lista de AA aceitáveis. Esta prática é conhecida como pinning certificado.

Aqui estão algumas formas de detetar se a sua aplicação é impactada:

- Procure o seu código fonte para obter a impressão digital, nome comum e outras propriedades cert de qualquer um dos CAs do Microsoft IT TLS [encontrados aqui](https://www.microsoft.com/pki/mscorp/cps/default.htm). Se houver uma correspondência, a sua aplicação será impactada. Para resolver este problema, a atualização do código fonte inclui os novos CAs. Como boas práticas, certifique-se de que os CAs podem ser adicionados ou editados em curto prazo. Os regulamentos da indústria exigem que os certificados de CA sejam substituídos no prazo de sete dias e, por conseguinte, os clientes que confiam na fixação precisam de reagir rapidamente.

- Se tiver uma aplicação que se integre com APIs Azure ou outros serviços Azure e não tem a certeza se utiliza a fixação de certificados, consulte o fornecedor de aplicações.

- Diferentes sistemas operativos e tempos de execução linguísticos que comunicam com os serviços da Azure podem exigir medidas adicionais para construir corretamente a cadeia de certificados com estas novas raízes:
    - **Linux**: Muitas distribuições requerem que adicione CAs a /etc/ssl/certs. Para instruções específicas, consulte a documentação da distribuição.
    - **Java**: Certifique-se de que a loja de chaves Java contém os CAs listados acima.
    - **Windows running in disconnected environments**: Os sistemas que estão a funcionar em ambientes desligados terão de ter as novas raízes adicionadas à loja Trusted Root Certification Authorities e os intermediários adicionados à loja Das Autoridades de Certificação Intermédia.
    - **Android**: Verifique a documentação do seu dispositivo e versão do Android.
    - **Outros dispositivos de hardware, especialmente ioT**: Contacte o fabricante do dispositivo.

- Se tiver um ambiente onde as regras de firewall são definidas para permitir chamadas de saída apenas para locais específicos de verificação da Lista de Revogação de Certificados (CRL) e/ou protocolo de verificação do certificado online (OCSP). Você precisará permitir os seguintes URLs CRL e OCSP:

    - http://crl3&#46;d igicert&#46;com
    - http://crl4&#46;d igicert&#46;com
    - http://ocsp&#46;d igicert&#46;com
    - http://www&#46rede de&#46;de ;d-trust
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas adicionais, contacte-nos através [do suporte.](https://azure.microsoft.com/support/options/)
