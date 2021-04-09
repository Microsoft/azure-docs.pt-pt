---
title: Rescisão de TLS com certificados Azure Key Vault
description: Saiba como pode integrar o Azure Application Gateway com o Key Vault para certificados de servidor que estão ligados a ouvintes com ativação HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 8a64956deb7849568e70e94c9b58170df60db1e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775748"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminação TLS com certificados do Key Vault

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

O Application Gateway suporta atualmente apenas certificados validados por software. Os certificados validados por hardware (HSM) não são suportados. Após a aplicação Gateway ser configurado para usar certificados Key Vault, as suas instâncias recuperam o certificado do Key Vault e instalam-nos localmente para a rescisão de TLS. Os casos também pesquisam Key Vault em intervalos de 4 horas para recuperar uma versão renovada do certificado, se é que existe. Se for encontrado um certificado atualizado, o certificado TLS/SSL atualmente associado ao ouvinte HTTPS é automaticamente rodado.

> [!NOTE]
> O portal Azure só suporta certificados KeyVault, não segredos. O Application Gateway ainda suporta a referência de segredos do KeyVault, mas apenas através de recursos não-Portal como PowerShell, CLI, API, modelos ARM, etc. 

## <a name="how-integration-works"></a>Como funciona a integração

A integração do Gateway de Aplicação com o Key Vault requer um processo de configuração em três etapas:

1. **Criar uma identidade gerida atribuída ao utilizador**

   Cria ou reutiliza uma identidade gerida atribuída pelo utilizador, que o Application Gateway utiliza para obter certificados do Key Vault em seu nome. Para obter mais informações, consulte [Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Este passo cria uma nova identidade no inquilino do Azure Ative Directory. A identidade é confiável pela subscrição que é usada para criar a identidade.

1. **Configure o seu cofre chave**

   Em seguida, importa um certificado existente ou cria um novo no seu cofre chave. O certificado será utilizado por aplicações que passam pelo gateway de aplicações. Neste passo, também pode utilizar um Key Vault Secret que também permite armazenar um ficheiro PFX codificado sem palavra-passe, base-64. Recomendamos a utilização de um tipo "Certificado" devido à capacidade de auto-revisão que está disponível com este tipo de objetos no Cofre de Chaves. Depois de ter criado um Certificado ou um Segredo, tem de definir as Políticas de Acesso no Cofre-Chave para permitir que a identidade tenha acesso ao segredo.
   
   > [!IMPORTANT]
   > A partir de 15 de março de 2021, o Key Vault reconhece o Azure Application Gateway como um dos Serviços Fidedignos, permitindo-lhe assim construir uma fronteira de rede segura em Azure. Isto dá-lhe a capacidade de negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) para o Key Vault, mas ainda assim torná-lo acessível para o recurso Application Gateway sob a sua subscrição. 

   > Pode configurar o seu Gateway de Aplicação numa rede restrita de Key Vault da seguinte forma. <br />
   > a) Sob a lâmina de networking do Key Vault <br />
   > b) escolha o ponto final privado e as redes selecionadas no separador "Firewall e Redes Virtuais" <br/>
   > c) então utilizando redes virtuais, adicione a rede virtual e a sub-rede do Seu Gateway de Aplicação. Durante o processo, também configurar o ponto de serviço 'Microsoft.KeyVault', selecionando a sua caixa de verificação. <br/>
   > d) Por fim, selecione "Sim" para permitir que os Serviços Fidedignos contornem a firewall do Key Vault. <br/>
   > 
   > ![Firewall de cofre de chaves](media/key-vault-certs/key-vault-firewall.png)


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

   Depois de completar os dois passos anteriores, pode configurar ou modificar um gateway de aplicações existente para utilizar a identidade gerida atribuída pelo utilizador. Para mais informações consulte, [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   Também pode configurar o certificado TLS/SSL do ouvinte HTTP para indicar o URI completo do certificado Key Vault ou iD secreto.

   ![Certificados de cofre chave](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos seguintes

[Configure a rescisão de TLS com certificados Key Vault utilizando a Azure PowerShell](configure-keyvault-ps.md)
