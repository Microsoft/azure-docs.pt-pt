---
title: O que há de novo em Azure Cache para Redis
description: Atualizações recentes para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91492539"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>O que há de novo em Azure Cache para Redis

## <a name="azure-tls-certificate-change"></a>Alteração do certificado Azure TLS

A Microsoft está a atualizar os serviços do Azure para utilizar certificados de servidor TLS de um conjunto diferente de Autoridades de Certificados (CAs). Esta alteração é lançada por fases de 13 de agosto de 2020 a 26 de outubro de 2020 (estimado). O Azure está a fazer esta alteração porque [os certificados ac atuais não cumprem um dos requisitos de base do Ca/Browser Forum](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). O problema foi reportado a 1 de julho de 2020 e aplica-se a vários fornecedores populares de Infraestruturas De Chaves Públicas (PKI) em todo o mundo. A maioria dos certificados TLS usados pelos serviços Azure hoje vêm do *Baltimore CyberTrust Root* PKI. O serviço Azure Cache for Redis continuará a ser acorrentado à Raiz CyberTrust de Baltimore. Os seus certificados de servidor TLS, no entanto, serão emitidos pelas novas Autoridades de Certificados Intermédios (ICAs) a partir de 12 de outubro de 2020.

> [!NOTE]
> Esta alteração limita-se aos serviços nas regiões públicas [de Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) Exclui as nuvens soberanas (por exemplo, china) ou governamentais.
>
>

### <a name="does-this-change-affect-me"></a>Esta mudança afeta-me?

Esperamos que a maioria dos clientes da Azure Cache para os clientes Redis não sejam afetados pela mudança. A sua aplicação pode ser impactada se especificar explicitamente uma lista de certificados aceitáveis, uma prática conhecida como "pinning de certificado". Se estiver fixado a um certificado intermédio ou de folha em vez da Raiz CyberTrust de Baltimore, deve **tomar medidas imediatas** para alterar a configuração do certificado.

A tabela seguinte fornece informações sobre os certificados que estão a ser enrolados. Dependendo do certificado que a sua aplicação utiliza, poderá necessitar de o atualizar para evitar a perda de conectividade com o seu Azure Cache para a instância Redis.

| Tipo CA | Atual | Post Rolling (12 out, 2020) | Ação |
| ----- | ----- | ----- | ----- |
| Raiz | Impressão digital: d4de20d05e66fc53fe1a5082c78db2852cae474<br><br> Expiração: segunda-feira, 12 de maio de 2025, 16:59:00<br><br> Nome do sujeito:<br> CN = Baltimore CyberTrust Root<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Não mudar | Nenhum |
| Intermediários | Impressões digitais:<br> CN = Microsoft IT TLS CA 1<br> Impressão digital: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Impressão digital: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Impressão digital: 8a38755d099682fe8fa3116a277ce446eac4e999<br><br> CN = Microsoft IT TLS CA 5<br> Impressão digital: Ad898ac73df3333eb60ac1f5fc6c4b2219db79b7<br><br> Expiration: ‎Friday, ‎May ‎20, ‎2024 5:52:38 AM<br><br> Nome do sujeito:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = EUA<br> | Impressões digitais:<br> CN = Microsoft RSA TLS CA 01<br> Impressão digital: 703d7a8f0ebf55aaaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Impressão digital: b0c2d2d13cddd56cdaa6ab6e2c04440be4a429c75<br><br> Expiration: ‎Tuesday, ‎October ‎8, ‎2024 12:00:00 AM;<br><br> Nome do sujeito:<br> O = Microsoft Corporation<br> C = EUA<br> | Necessário |

### <a name="what-actions-should-i-take"></a>Que ações devo tomar?

Se a sua aplicação utilizar a loja de certificados do sistema operativo ou fixar a raiz de Baltimore entre outras, não é necessária qualquer ação. Por outro lado, se a sua aplicação fixar algum certificado TLS intermédio ou de folha, recomendamos que coloque as seguintes raízes:

| Certificado | Thumbprint |
| ----- | ----- |
| [Baltimore Root CA](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a5082c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [Raiz Global Digicert G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9f666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Espera-se que os certificados intermédios e folhas mudem frequentemente. Recomendamos que não se dependência deles. Em vez disso, fixar a sua aplicação num certificado de raiz, uma vez que rola com menos frequência.
>
>

Para continuar a fixar certificados intermédios, adicione o seguinte à lista de certificados intermédios fixados, que inclui alguns outros adicionais para minimizar as futuras alterações:

| Nome comum da AC | Thumbprint |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cddd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS emitindo CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS emitindo CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS emitindo CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS emitindo CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Se a sua aplicação validar o certificado em código, terá de modificá-lo para reconhecer as propriedades (por exemplo, Emitentes, Impressão digital) dos certificados recentemente fixados. Esta verificação extra deve abranger todos os certificados fixados para serem mais à prova de futuro.

## <a name="next-steps"></a>Passos seguintes

Se tiver dúvidas adicionais, contacte-nos através [do suporte.](https://azure.microsoft.com/support/options/)  
