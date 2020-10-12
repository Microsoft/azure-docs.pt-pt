---
title: Rescisão de TLS com certificados Azure Key Vault
description: Saiba como pode integrar o Azure Application Gateway com o Key Vault para certificados de servidor que estão ligados a ouvintes com ativação HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: victorh
ms.openlocfilehash: c1f6cc21c7a45dbc5c7be7e3f3cc46b4ec4e8c39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282352"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Rescisão de TLS com certificados Key Vault

[O Azure Key Vault](../key-vault/general/overview.md) é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados TLS/SSL. O Azure Application Gateway suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com via HTTPS. Este suporte está limitado ao v2 SKU do Gateway de aplicação.

A integração key Vault oferece dois modelos para a rescisão de TLS:

- Pode fornecer explicitamente certificados TLS/SSL anexados ao ouvinte. Este modelo é a forma tradicional de passar certificados TLS/SSL para o Gateway de Aplicação para a rescisão de TLS.
- Pode fornecer opcionalmente uma referência a um certificado de Cofre de Chaves ou segredo existente quando criar um ouvinte com indicação HTTPS.

A integração do Gateway de Aplicação com o Key Vault oferece muitos benefícios, incluindo:

- Segurança mais forte, porque os certificados TLS/SSL não são tratados diretamente pela equipa de desenvolvimento de aplicações. A integração permite que uma equipa de segurança separada:
  * Configurar portais de aplicação.
  * Controle os ciclos de vida do gateway da aplicação de controlo.
  * Conceda permissões a gateways de aplicações selecionados para aceder a certificados que estejam armazenados no cofre da chave.
- Suporte para importar certificados existentes no seu cofre chave. Ou use APIs key vault para criar e gerir novos certificados com qualquer um dos parceiros confiáveis do Key Vault.
- Suporte para renovação automática de certificados que estão armazenados no seu cofre chave.

O Application Gateway suporta atualmente apenas certificados validados por software. Os certificados validados por hardware (HSM) não são suportados. Após a aplicação Gateway ser configurado para usar certificados Key Vault, as suas instâncias recuperam o certificado do Key Vault e instalam-nos localmente para a rescisão de TLS. Os casos também pesquisam Key Vault em intervalos de 24 horas para recuperar uma versão renovada do certificado, se é que existe. Se for encontrado um certificado atualizado, o certificado TLS/SSL atualmente associado ao ouvinte HTTPS é automaticamente rodado.

> [!NOTE]
> O portal Azure só suporta certificados KeyVault, não segredos. O Application Gateway ainda suporta a referência de segredos do KeyVault, mas apenas através de recursos não-Portal como PowerShell, CLI, API, modelos ARM, etc. 

## <a name="how-integration-works"></a>Como funciona a integração

A integração do Gateway de Aplicação com o Key Vault requer um processo de configuração em três etapas:

1. **Criar uma identidade gerida atribuída ao utilizador**

   Cria ou reutiliza uma identidade gerida atribuída pelo utilizador, que o Application Gateway utiliza para obter certificados do Key Vault em seu nome. Para obter mais informações, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Este passo cria uma nova identidade no inquilino do Azure Ative Directory. A identidade é confiável pela subscrição que é usada para criar a identidade.

1. **Configure o seu cofre chave**

   Em seguida, importa um certificado existente ou cria um novo no seu cofre chave. O certificado será utilizado por aplicações que passam pelo gateway de aplicações. Neste passo, também pode usar um segredo de cofre chave que é armazenado como um ficheiro PFX codificado sem palavra-passe, base-64 codificado. Recomendamos a utilização de um tipo de certificado devido à capacidade de auto-mente que está disponível com objetos tipo certificado no cofre da chave. Depois de criar um certificado ou um segredo, define as políticas de acesso no cofre chave para permitir que a identidade *tenha* acesso ao segredo.
   
   > [!NOTE]
   > Se implementar o gateway de aplicação através de um modelo ARM, quer utilizando o Azure CLI ou o PowerShell, quer através de uma aplicação Azure implantada a partir do portal Azure, o certificado SSL é armazenado no cofre de chaves como um ficheiro PFX codificado com base64. Tem de completar os passos no [Cofre da Chave Azure para passar o valor do parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > É particularmente importante definir `enabledForTemplateDeployment` para `true` . O certificado pode não ter palavra-passe ou pode ter uma senha. No caso de um certificado com uma palavra-passe, o exemplo a seguir mostra uma possível configuração para a `sslCertificates` entrada na `properties` configuração do modelo ARM para um gateway de aplicações. Os valores `appGatewaySSLCertificateData` de e `appGatewaySSLCertificatePassword` são olhados para cima a partir do cofre chave como descrito na secção [Segredos de Referência com ID dinâmico](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Siga as referências para trás `parameters('secretName')` para ver como a aparência acontece. Se o certificado não tiver palavra-passe, omita a `password` entrada.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Configurar o gateway de aplicação**

   Depois de completar os dois passos anteriores, pode configurar ou modificar um gateway de aplicações existente para utilizar a identidade gerida atribuída pelo utilizador. Também pode configurar o certificado TLS/SSL do ouvinte HTTP para indicar o URI completo do certificado Key Vault ou iD secreto.

   ![Certificados de cofre chave](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos seguintes

[Configure a rescisão de TLS com certificados Key Vault utilizando a Azure PowerShell](configure-keyvault-ps.md)
