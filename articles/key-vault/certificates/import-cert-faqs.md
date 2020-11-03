---
title: Perguntas frequentes - Importação de certificado azure Key Vault
description: Obtenha respostas para perguntas frequentes sobre a importação de certificados Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: d7d34b61e584b63c517b6c0f8af4cb4adcc7fefe
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289509"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Certificação de Azure Key Vault FAQ

Este artigo responde frequentemente a perguntas sobre a importação de certificados Azure Key Vault.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Como posso importar um certificado no Azure Key Vault?

Para uma operação de importação de certificados, a Azure Key Vault aceita dois formatos de ficheiros de certificado: PEM e PFX. Embora existam ficheiros PEM apenas com a parte pública, o Key Vault requer e aceita apenas um ficheiro PEM ou PFX com uma chave privada. Para mais informações, consulte [Importar um certificado para o Key Vault.](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Depois de importar um certificado protegido por palavra-passe para o Key Vault e depois descarregá-lo, por que não consigo ver a senha que está associada?
    
Depois de um certificado ser importado e protegido em Key Vault, a sua senha associada não é guardada. A palavra-passe só é necessária uma vez durante a operação de importação. Isto é por design, mas pode sempre obter o certificado como segredo e convertê-lo de Base64 para PFX adicionando a palavra-passe através do [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Como posso resolver um erro de "mau parâmetro"? Quais são os formatos de certificado suportados para importar para o Key Vault?

Quando importa um certificado, tem de se certificar de que a chave está incluída no ficheiro. Se tiver uma chave privada armazenada separadamente num formato diferente, tem de combinar a chave com o certificado. Algumas autoridades de certificados (AA) fornecem certificados noutros formatos. Portanto, antes de importar o certificado, certifique-se de que está em formato de ficheiro PEM ou PFX e que a chave utiliza a encriptação Rivest-Shamir-Adleman (RSA) ou a criptografia de curva elíptica (ECC). 

Para mais informações, consulte [os requisitos dos certificados](./certificate-scenarios.md#formats-of-import-we-support) e [os requisitos-chave do certificado.](../keys/about-keys.md)

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Posso importar um certificado usando um modelo ARM?

Não, não é possível realizar operações de certificado utilizando um modelo Azure Resource Manager (ARM). Uma solução alternativa recomendada seria utilizar os métodos de importação de certificados na AZure API, no Azure CLI ou no PowerShell. Se tiver um certificado existente, pode importá-lo como um segredo.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Quando importo um certificado através do portal Azure, tenho um erro de "Algo correu mal". Como posso investigar mais?
    
Para visualizar um erro mais descritivo, importe o ficheiro do certificado utilizando [o Azure CLI](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) ou [o PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Como posso resolver "Error type: Access denied or user is unauthorized to import certificate"?
    
A operação de importação requer que conceda ao utilizador permissões para importar o certificado ao abrigo das políticas de acesso. Para tal, vá ao cofre de chaves, selecione **Políticas de Acesso** Adicionar Política de  >  **Acesso**  >  **Selecionar Permissões de Certificado**  >  **Principal,** procurar o utilizador e, em seguida, adicionar o endereço de e-mail do utilizador. 

Para obter mais informações sobre as políticas de acesso relacionadas com [certificados, consulte os certificados sobre o Cofre chave de Azure](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Como posso resolver "Error type: Conflict when creating a certificate"?
    
Cada nome de certificado deve ser único. Um certificado com o mesmo nome pode estar num estado de apagação suave. Além disso, de acordo com a [composição de um certificado](./about-certificates.md#composition-of-a-certificate), quando o novo certificado é criado, cria um segredo endereçado com o mesmo nome, por isso, se houver outra chave ou segredo no cofre com o mesmo nome que o que está a tentar especificar para o seu certificado, a criação do certificado falhará e terá de remover essa chave ou segredo ou usar um nome diferente para o seu certificado. 

Para mais informações, consulte [a operação 'Obter CertificadoS' (Certificado Desaubita).](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Porque estou a receber "Error type: char length is too long"?
Este erro pode ser causado por uma de duas razões:    
* O nome do sujeito do certificado está limitado a 200 caracteres.
* A senha do certificado está limitada a 200 caracteres.

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Posso importar um certificado caducado para Azure Key Vault?
    
Não, os certificados PFX expirados não podem ser importados para o Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Como posso converter o meu certificado no formato adequado?

Pode solicitar à sua AC que forneça o certificado no formato requerido. Existem também ferramentas de terceiros que podem ajudá-lo a converter o certificado no formato adequado.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Posso importar certificados de CAs não sócios?
Sim, pode importar certificados de qualquer AC, mas o seu cofre-chave não poderá renová-los automaticamente. Pode definir lembretes para ser notificado sobre a expiração do certificado.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Se eu importar um certificado de um parceiro CA, a função de autorenewal ainda funcionará?
Sim. Depois de ter carregado o certificado, não se esqueça de especificar a autorotação na política de emissão do certificado. As suas definições permanecerão em vigor até que a versão do próximo ciclo ou certificado seja lançada.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Por que não posso ver o certificado do Serviço de Aplicações que importei para o Key Vault? 
Se importou o certificado com sucesso, deverá poder confirmá-lo indo ao painel **Secrets.**


## <a name="next-steps"></a>Passos seguintes

- [Certificados Azure Key Vault](./about-certificates.md)