---
title: Integrar o Key Vault com a Autoridade de Certificação DigiCert
description: Como integrar o Cofre-Chave com a DigiCert Certificate Authority
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 50f2515cee92ead8018ffaaf4b4574905f8007d5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844510"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrar o Key Vault com a Autoridade de Certificação DigiCert

O Azure Key Vault permite-lhe facilmente provisões, gestão e implementação de certificados digitais para a sua rede e para permitir comunicações seguras para aplicações. Um certificado digital é uma credencial electrónica para estabelecer provas de identidade numa transação electrónica. 

Os utilizadores de cofres chave Azure podem gerar certificados DigiCert diretamente do seu Cofre chave. A Key Vault asseguraria a gestão do ciclo de vida do certificado de ponta a ponta para os certificados emitidos pela DigiCert através da parceria de confiança da Key Vault com a DigiCert Certificate Authority.

Para obter informações mais gerais sobre certificados, consulte [os certificados de cofre chave Azure.](./about-certificates.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, você deve ter os seguintes recursos.
* Um cofre. Você pode usar um cofre de chaves existente, ou criar um novo seguindo os passos em um destes arranques rápidos:
   - [Crie um cofre-chave com o Azure CLI](../general/quick-create-cli.md)
   - [Crie um cofre com Azure PowerShell](../general/quick-create-powershell.md)
   - [Crie um cofre-chave com o portal Azure](../general/quick-create-portal.md).
*   Tem de ativar a conta DigiCert CertCentral. [Inscreva-se na](https://www.digicert.com/account/signup/) sua conta CertCentral.
*   Permissões de nível de administrador nas suas contas.


### <a name="before-you-begin"></a>Before you begin

Certifique-se de que tem as seguintes informações úteis na sua conta DigiCert Central:
-   ID de conta certcentral
-   ID da organização
-   Chave API

## <a name="adding-certificate-authority-in-key-vault"></a>Adicionar autoridade de certificados no cofre chave 
Depois de recolher informações acima da conta DigiCert CertCentral, pode agora adicionar a lista digiCert à Certificate Authority no cofre-chave.

### <a name="azure-portal"></a>Portal do Azure

1.  Para adicionar autoridade de certificado DigiCert, navegue para o cofre-chave que pretende adicionar DigiCert. 
2.  Nas páginas das propriedades do Cofre-Chave, selecione **Certificados**.
3.  **Selecione certificado As autoridades** de certificação. ![ selecionar autoridades de certificados](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Selecione A opção **Adicionar.**
 ![adicionar autoridades de certificados](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  No ecrã **da Create a Certificate A Autoridade** escolhe os seguintes valores:
    -   **Nome**: Adicione um nome emitente identificável. Exemplo DigicertCA
    -   **Provedor**: Selecione DigiCert do menu.
    -   **ID de conta**: Insira o seu ID de ContaCentral DigiCert Cert
    -   **Senha de conta**: Introduza a chave API gerada na sua Conta Centro de Cert Cert DigiCert
    -   **ID da organização**: Enter OrgID recolhido a partir da ContaCentral DigiCert 
    -   Clique em **Criar**.
   
6.  Verá que a DigicertCA foi agora adicionada na lista das Autoridades de Certificados.


### <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell é utilizado para criar e gerir recursos Azure usando comandos ou scripts. O Azure acolhe o Azure Cloud Shell, um ambiente de conchas interativas que pode utilizar através do seu portal Azure no próprio navegador.

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer a versão 1.0.0 ou posterior do módulo Azure PowerShell. Escreva `$PSVersionTable.PSVersion` para encontrar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Login-AzAccount` para criar uma ligação com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Criar um **grupo de recursos**

Criar um grupo de recursos Azure com [o New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Criar um **cofre de chaves**

Deve usar um nome único para o seu cofre. Aqui "Contoso-Vaultname" é o nome para Key Vault em todo este guia.

- **Nome do cofre** Nome Contoso-Vaultname.
- **Nome do grupo de recursos**: ContosoResourceGroup.
- **Localização** O Eastus.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Defina variáveis para informação recolhida na conta DigiCert CertCentral.

- Definir **variável de ID de conta**
- Definir variável **org iD**
- Definir variável **chave API**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Definir **emitente**. Isto irá adicionar Digicert como autoridade de certificado no cofre chave. Para saber mais sobre os parâmetros, [leia aqui](https://docs.microsoft.com/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Definição de política para o certificado e certificado de emissão** da DigiCert diretamente dentro do Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

O certificado foi agora emitido com sucesso pela Digicert CA no interior especificado Key Vault através desta integração.

## <a name="troubleshoot"></a>Resolução de problemas

Se o certificado emitido estiver em estado de 'desactivado' no portal Azure, proceda à visualização da **Operação Certificado** para rever a mensagem de erro do DigiCert para esse certificado.

 ![Operação de certificado](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Para obter mais informações, consulte as operações do [Certificado na referência API do Cofre-Chave](/rest/api/keyvault). Para obter informações sobre o estabelecimento de permissões, consulte [Cofres - Criar ou Atualizar](/rest/api/keyvault/vaults/createorupdate) e [Abóbadas - Atualizar a Política de Acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

- Posso gerar um certificado de wildcard digicert através do KeyVault? 
   Sim. Depende de como configuraste a tua conta de digicert.
- Como posso criar **certificado OV-SSL ou EV-SSL** com DigiCert?? 
   O cofre-chave suporta a criação de certificados OV e EV SSL. Ao criar um certificado, clique na Configuração de Política Avançada e, em seguida, especifique o tipo de Certificado. Os valores suportados são: OV-SSL, EV-SSL
   
   Poderá criar este tipo de certificado no cofre-chave se a sua conta Digicert o permitir. Para este tipo de certificado, a validação é realizada pela DigiCert e a sua equipa de suporte poderá ajudá-lo melhor com a solução, caso a validação falhe. Pode adicionar informações adicionais ao criar um certificado definindo-as no nome do sujeito.

Exemplo
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Existe um atraso de tempo na criação de certificado digicert através da integração vs aquisição de certificado através do digicert diretamente?
   Não. Ao criar um certificado, é o processo de verificação que pode demorar e que a verificação depende do processo que a DigiCert segue.


## <a name="next-steps"></a>Passos seguintes

- [Autenticação, pedidos e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Programador do Key Vault](../general/developers-guide.md)