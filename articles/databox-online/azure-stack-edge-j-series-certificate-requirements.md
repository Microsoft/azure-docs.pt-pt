---
title: Requisitos de certificados e resolução de problemas com a Azure Stack Edge Pro Microsoft Docs
description: Descreve os requisitos dos certificados e erros de certificado de resolução de problemas com o dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: de41bd030ea73ac68bfac5fbfbd03ae14cf7980f
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874241"
---
# <a name="certificate-requirements"></a>Requisitos de certificados

Este artigo descreve os requisitos de certificado que devem ser cumpridos antes de os certificados poderem ser instalados no seu dispositivo Azure Stack Edge Pro. Os requisitos estão relacionados com certificados PFX, autoridade emissora, nome do sujeito de certificado e nome alternativo sujeito, e algoritmos de certificado suportados.

## <a name="certificate-issuing-authority"></a>Autoridade emissora de certificados

Os requisitos de emissão de certificados são os seguintes:

* Os certificados devem ser emitidos a partir de uma autoridade de certificados internos ou de uma autoridade de certificados públicos.

* A utilização de certificados auto-assinados não é suportada.

* O certificado *emitido para:* o campo não deve ser o mesmo que o *emitido por:* campo, com exceção dos certificados Root CA.


## <a name="certificate-algorithms"></a>Algoritmos de certificado

Os algoritmos de certificado devem ter os seguintes requisitos:

* Os certificados devem usar o algoritmo de chave RSA.

* Apenas são suportados certificados RSA com Microsoft RSA/Schannel Cryptographic Provider.

* O algoritmo de assinatura de certificado não pode ser SHA1.

* O tamanho mínimo da chave é 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Nome do sujeito do certificado e nome alternativo do sujeito

Os certificados devem ter o seguinte nome de sujeito e requisitos de nome alternativo:

* Pode utilizar um único certificado que cubra todos os espaços de nome nos campos de Nome Alternativo Sujeito (SAN) do certificado. Em alternativa, pode utilizar certificados individuais para cada um dos espaços com nomes. Ambas as abordagens requerem a utilização de cartões selvagens para pontos finais, como objetos grandes binários (Blob).

* Certifique-se de que os nomes do sujeito (nome comum no nome do sujeito) fazem parte de nomes alternativos sujeitos na extensão do nome alternativo do sujeito.

* Você pode usar um único certificado wildcard cobrindo todos os espaços de nome nos campos SAN do certificado.

* Utilize a seguinte tabela ao criar um certificado de ponto final:

    |Tipo |Nome do sujeito (SN)  |Nome alternativo sujeito (SAN)  |Exemplo do nome do sujeito |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Armazenamento de blobs|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Local UI| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Certificado único multi-SAN para ambos os pontos finais|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Nó|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate é codificado.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>Certificado PFX

Os certificados PFX instalados no seu dispositivo Azure Stack Edge Pro devem satisfazer os seguintes requisitos:

* Quando receber os seus certificados da autoridade SSL, certifique-se de que obtém a cadeia de assinaturas completa dos certificados.

* Ao exportar um certificado PFX, certifique-se de que selecionou **todos os certificados da cadeia, se possível.**

* Utilize um certificado PFX para ponto final, UI local, nó, VPN e Wi-Fi, uma vez que as chaves públicas e privadas são necessárias para o Azure Stack Edge Pro. A chave privada deve ter o conjunto de atributos da chave da máquina local.

* A encriptação PFX do certificado deve ser 3DES. Esta é a encriptação padrão utilizada na exportação de um cliente Windows 10 ou loja de certificados Windows Server 2016. Para obter mais informações relacionadas com o 3DES, consulte [O DeS Triplo.](https://en.wikipedia.org/wiki/Triple_DES)

* Os ficheiros PFX de certificado devem ter valores válidos *de Assinatura Digital* e *KeyEncipherment* no campo *'Utilização chave'.*

* Os ficheiros PFX do certificado devem ter os valores *autenticação do servidor (1.3.6.1.5.5.7.3.1)* e *Autenticação do Cliente (1.3.6.1.5.5.7.3.2)* no campo *de Utilização da Chave Melhorada.*

* As palavras-passe de todos os ficheiros PFX de certificado devem ser as mesmas no momento da sua implantação se estiver a utilizar a Ferramenta de Verificação de Prontidão de Azure Stack. Para obter mais informações, consulte [Criar certificados para o seu Azure Stack Edge Pro utilizando a ferramenta Azure Stack Hub Readiness Checker](azure-stack-edge-j-series-create-certificates-tool.md).

* A palavra-passe do certificado PFX deve ser uma senha complexa. Tome nota desta palavra-passe porque é usada como parâmetro de implantação.

* Utilize apenas certificados RSA com o fornecedor criptográfico Microsoft RSA/Schannel.

Para obter mais informações, consulte [os certificados Export PFX com chave privada.](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

## <a name="next-steps"></a>Passos seguintes

[Use certificados com Azure Stack Edge Pro](azure-stack-edge-j-series-manage-certificates.md)

[Crie certificados para o seu Azure Stack Edge Pro utilizando a ferramenta Azure Stack Hub Readiness Checker](azure-stack-edge-j-series-create-certificates-tool.md)

[Certificados PFX de exportação com chave privada](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Erros de certificado de resolução de problemas](azure-stack-edge-j-series-certificate-troubleshooting.md)