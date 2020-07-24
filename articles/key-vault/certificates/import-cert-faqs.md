---
title: Perguntas frequentes - Importação de certificado azure Key Vault
description: Perguntas frequentes - Importação de certificado azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098271"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Perguntas frequentes - Importação de Certificado de Cofre chave Azure

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Como posso importar um certificado no Azure Key Vault?

Certificado de importação – Para operação de importação, o cofre-chave Azure aceita dois formatos de certificado PEM e PFX. Embora existam ficheiros PEM apenas com a parte pública, o cofre da chave Azure requer e apenas aceita um PEM ou PFX na pasta de ficheiros e com uma chave privada. Siga [o tutorial para o certificado de importação](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Depois de importar o certificado protegido de senha para o cofre chave e depois descarregá-lo, não posso ver a senha associada ao certificado?
    
O certificado protegido carregado após o armazenamento no cofre da chave não pouparia a palavra-passe associada. Só é preciso uma vez durante a operação de importação. Embora este seja um conceito de design por by-design, você pode sempre obter o certificado como um segredo e converter de Base64 para PFX adicionando a senha anterior através [do Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Como posso resolver "Erro de parâmetro grave"? Quais são os formatos de certificado suportados para importação no Key Vault?

Quando está a importar o certificado, tem de se certificar de que a chave está incluída no próprio ficheiro. Se tiver a chave privada separadamente num formato diferente, terá de combinar a chave com o certificado. Algumas autoridades de certificados fornecem certificados em diferentes formatos, por conseguinte, antes de importar o certificado, certifique-se de que estão em formato .pem ou .pfx e que a chave utilizada é RSA ou ECC. Consulte-os para rever [os requisitos dos certificados](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) e [os requisitos-chave dos certificados.](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Erro “Ocorreu um erro” ao importar o certificado através do Portal. Como posso investigar mais?
    
Para visualizar um erro mais descritivo, importe o ficheiro do certificado através do [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) ou [da PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Como posso resolver 'Error type: Access denied or user is unauthorized to import certificate'?
    
Esta operação requer a permissão certificados/importar. Depois de navegar até ao local onde se encontra o Key Vault, terá de conceder ao utilizador as permissões adequadas em políticas de acesso. Navegue para as políticas de acesso> cofre > adicionar > Obter permissões de certificado (ou como quiser as permissões) > principal > procurar e, em seguida, adicionar o e-mail do utilizador. [Leia mais sobre as políticas de acesso relacionadas com certificados](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Como posso resolver 'Error type: Conflict when creating a certificate'?
    
O nome do certificado deve ser único. O certificado com o mesmo nome pode estar em estado de eliminação suave, também, de acordo com a [composição de um certificado](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) no cofre de chaves Azure, se houver outra Chave ou Segredo no Cofre-Chave com o mesmo nome que está a tentar especificar para o seu certificado, ele falhará e terá de remover essa chave ou segredo ou usar um nome diferente para o seu certificado. [ver certificado eliminado](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Porque é que estou a receber o tipo de erro: o comprimento do char é demasiado longo?
    
* O comprimento do nome do sujeito do certificado tem um limite de caracteres de 200 char
* O comprimento da senha do certificado tem um limite de caracteres de 200 char

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Posso importar um certificado caducado no cofre da Chave Azure?
    
Não, os certificados PFX expirados não serão importados para o Cofre da Chave Azure.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Como posso converter o meu certificado em formato adequado?

Pode solicitar à sua Autoridade de Certificados que forneça o certificado no formato necessário, existindo também ferramentas de terceiros que podem ajudá-lo a converter-se no formato adequado, no entanto, a Microsoft não poderá aconselhar mais sobre como obter o certificado no formato pretendido.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Posso importar certificados de CAs não sócios?
Sim, pode importar certificados de qualquer AC, mas o cofre-chave não poderá renovar automaticamente esses certificados. Poderá definir notificações por e-mail para ser notificado sobre o termo do certificado.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Se eu importar um certificado de um parceiro CA, a função de renovação automática ainda funcionará?
Sim, tem de se certificar de que uma vez carregado especifique a autorotação na política de emissão do certificado. Além disso, as alterações serão refletidas até à versão do próximo ciclo ou certificado.


## <a name="next-steps"></a>Passos seguintes

- [Certificados de cofre de chaves Azure](/azure/key-vault/certificates/about-certificates)
