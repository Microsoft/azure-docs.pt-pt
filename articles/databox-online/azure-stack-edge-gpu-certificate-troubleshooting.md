---
title: Resolução de problemas com Azure Stack Edge Pro com GPU| Microsoft Docs
description: Descreve erros de certificado de resolução de problemas com o dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 67dd2b35229c15ae4df5ec8acb357aa35621d67c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436639"
---
# <a name="troubleshooting-certificate-errors"></a>Erros de certificado de resolução de problemas

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

O artigo fornece erros comuns de certificado ao instalar certificados no seu dispositivo Azure Stack Edge Pro.

## <a name="common-certificate-errors"></a>Erros comuns de certificado

O quadro que se segue apresenta erros comuns de certificado e informações detalhadas sobre estes erros e possíveis soluções:

> [!NOTE]
> Ocorrências de &#8220;{0} , ... ... {1} {n}&#8221; indicam parâmetros posicionais. Os parâmetros posicionais terão valores dependendo dos certificados que estiver a utilizar.

| Código de Erro | Descrição |
|---|---|
| CertificateManagement_UntrustedCertificate | O certificado com nome do sujeito {0} tem a cadeia de certificados quebrada. Faça o upload do certificado da cadeia de assinaturas antes de carregar este certificado.|
| CertificateManagement_DeviceNotRegistered| O seu dispositivo não está ativado. Só pode carregar um certificado de suporte após a ativação.|
| CertificateManagement_ExpiredCertificate | O certificado com o tipo {0} expirou ou expira em breve. Verifique a expiração do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_FormatMismatch | O formato do certificado não é suportado. Verifique o formato do certificado e, se necessário, traga um novo certificado.  {0}Esperado, {1} encontrado. |
| CertificateManagement_GenericError | Não foi possível realizar a operação de gestão de certificados. Recandidutar esta operação em poucos minutos. Se o problema persistir, contacte o Microsoft Support. |
| CertificateManagement_HttpsBindingFailure | O certificado com nome do sujeito {0} não criou um canal de https seguro. Verifique o certificado que carregou e, se necessário, traga um novo certificado. Este erro ocorre com o certificado de nó do dispositivo.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Certificado com nome de {0} sujeito não deve ter assinatura de certificado de utilização chave. Verifique a utilização chave do certificado e, se necessário, traga um novo certificado. Este erro ocorre com o certificado de corrente de assinatura. |
| CertificateManagement_IncorrectKeyNumber | O certificado com nome do sujeito {0} tem um número de chave incorreto {1} . Verifique o número-chave do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_InvalidP7b | O ficheiro de certificado carregado não é válido.  Verifique o formato do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_KeyAlgorithmNotRSA | Este certificado não utiliza o algoritmo de chave RSA. Apenas os certificados RSA são suportados. |
| CertificateManagement_KeySizeNotSufficient | O certificado com nome do sujeito {0} tem um tamanho de chave {1} insuficiente. O tamanho mínimo da chave é 4096.|
| CertificateManagement_MissingClientOid | O certificado com nome sujeito {0} não tem autenticação do cliente OID. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | O certificado com nome sujeito {0} não tem Assinatura Digital na Utilização de Chaves. Verifique as propriedades do certificado e, se necessário, traga um novo certificado. |
| CertificateManagement_MissingKeyCertSignKeyUsage | O certificado com nome sujeito {0} não tem assinatura de certificado em utilização chave. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | O certificado com nome do sujeito {0} não tem Chave Encipherment em Utilização chave. Verifique as propriedades do certificado e, se necessário, traga um novo certificado. |
| CertificateManagement_MissingServerOid | O certificado com nome sujeito {0} não tem autenticação do servidor OID. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_NameMismatch | Incompatibilidade do tipo de certificado. Âmbito esperado: {0} encontrado {1} . Faça upload do certificado apropriado.|
| CertificateManagement_NoPrivateKeyPresent | Certificado com nome do sujeito {0} não tem chave privada presente. Faça o upload de um certificado .pfx com chave privada.|
| CertificateManagement_NoRSACryptoPrivateKey | A chave privada para certificado com nome sujeito {0} não é acessível. Certifique-se de que está a utilizar um certificado suportado. Apenas o Microsoft RSA/Schannel Cryptographic Provider é suportado. |
| CertificateManagement_NotSelfSignedCertificate | O certificado com nome do sujeito {0} não é auto-assinado. Os certificados de raiz devem ser auto-assinados |
| CertificateManagement_NotSupportedOnVirtualAppliance | Esta operação não é suportada no dispositivo virtual. Este erro indica que a assinatura só ocorrerá com o Gateway da Caixa de Dados em funcionamento no Aparelho Cloud Tático. Este erro ocorre durante a gestão do dispositivo através do Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | O certificado com nome do sujeito {0} é auto-assinado. Faça o upload de um certificado que está devidamente assinado.|
| CertificateManagement_SignatureAlgorithmSha1 | O certificado com nome do sujeito {0} tem algoritmo de assinatura não suportado. SHA1-RSA não é suportado. |
| CertificateManagement_SubjectNamesInvalid | O certificado com nome sujeito {0} não tem o nome de sujeito correto ou nomes alternativos sujeitos para {1} certificado. Verifique o certificado que carregou e, se necessário, traga um novo certificado. Também deve verificar o nome DNS para combinar com os nomes SANS.|
| CertificateManagement_UnreadableCertificate | Certificado com tipo {0} não podia ser lido. Este erro ocorre quando o certificado é ilegível ou corrompido. Traga um novo certificado.|
| CertificadoSubjectNotFound | O certificado com o nome do sujeito {0} não foi encontrado. Traga um novo certificado.|
| CertificaRotationGenericFailure | Uma ou mais certidões de rotação falharam. Tentar novamente em alguns minutos. Se o problema persistir, contacte o Microsoft Support.|
| CertificadoImportFailure | O certificado com impressão digital {0} não foi importado no {1} nó. Se o problema persistir, contacte o Microsoft Support. |
| CertificadoApplyFailure | O certificado com impressão digital {0} não foi aplicado no {1} nó. Se o problema persistir, contacte o Microsoft Support.|
| NodeNotReachable | Não foi possível validar o certificado em {0} . Verifique a saúde do hardware e software do sistema.|


## <a name="next-steps"></a>Passos seguintes

[Requisitos de certificado](azure-stack-edge-gpu-certificate-requirements.md)