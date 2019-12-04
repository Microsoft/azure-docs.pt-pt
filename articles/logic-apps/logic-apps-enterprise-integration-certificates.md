---
title: Proteger mensagens B2B com certificados
description: Adicionar certificados para proteger mensagens B2B em aplicativos lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 6c5de6eba000c9052c7eb7b31d75804b9f454607
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790685"
---
# <a name="secure-b2b-messages-with-certificates"></a>Proteger mensagens B2B com certificados

Quando você precisar manter a comunicação B2B confidencial, poderá proteger a comunicação B2B para seus aplicativos de integração corporativa, especificamente os aplicativos lógicos, adicionando certificados à sua conta de integração. Os certificados são documentos digitais que verificam as identidades dos participantes em comunicações eletrônicas e ajudam a proteger a comunicação das seguintes maneiras:

* Criptografar o conteúdo da mensagem.
* Assinar mensagens digitalmente. 

Você pode usar esses certificados em seus aplicativos de integração corporativa:

* [Certificados públicos](https://en.wikipedia.org/wiki/Public_key_certificate), que devem ser comprados de uma [AC (autoridade de certificação)](https://en.wikipedia.org/wiki/Certificate_authority) pública da Internet, mas não exigem nenhuma chave. 

* Certificados privados ou [*certificados autoassinados*](https://en.wikipedia.org/wiki/Self-signed_certificate), que você cria e emite por conta própria, mas também requer chaves privadas. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para usar um *certificado público* em aplicativos lógicos que têm recursos B2B, você deve primeiro carregar o certificado para sua conta de integração. Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) que você cria, o certificado estará disponível para ajudá-lo a proteger suas mensagens B2B.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, insira o nome da conta de integração e, em seguida, selecione a conta de integração desejada.

   ![Localizar e selecionar sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Em **componentes**, escolha o bloco **certificados** .

   ![Escolha "certificados"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Em **certificados**, escolha **Adicionar**. Em **Adicionar certificado**, forneça estes detalhes para seu certificado. Quando terminar, escolha **OK**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*nome do certificado*> | O nome do seu certificado, que é "publicCert" neste exemplo | 
   | **Tipo de certificado** | Público | O tipo do certificado |
   | **Certificado** | <> de *nome de arquivo de certificado* | Para localizar e selecionar o arquivo de certificado que você deseja carregar, escolha o ícone de pasta ao lado da caixa **certificado** . |
   ||||

   ![Escolha "Adicionar", forneça os detalhes do certificado](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Depois que o Azure valida sua seleção, o Azure carrega seu certificado.

   ![O Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para usar um *certificado privado* em aplicativos lógicos que têm recursos B2B, você deve primeiro carregar o certificado para sua conta de integração. Você também precisa ter uma chave privada que você primeiro adiciona ao [Azure Key Vault](../key-vault/key-vault-get-started.md). 

Depois de definir as propriedades nos [contratos](logic-apps-enterprise-integration-agreements.md) que você cria, o certificado estará disponível para ajudá-lo a proteger suas mensagens B2B.

> [!NOTE]
> Para certificados privados, certifique-se de adicionar um certificado público correspondente que aparece nas configurações de **envio e recebimento** [do contrato AS2](logic-apps-enterprise-integration-as2.md) para assinar e criptografar mensagens.

1. [Adicione sua chave privada a Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) e forneça um **nome de chave**.
   
2. Autorize o aplicativo lógico do Azure a executar operações em Azure Key Vault. Para conceder acesso à entidade de serviço de aplicativos lógicos, use o comando do PowerShell, [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), por exemplo:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, insira o nome da conta de integração e, em seguida, selecione a conta de integração desejada.

   ![Localizar sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Em **componentes**, escolha o bloco **certificados** .  

   ![Escolha o bloco certificados](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Em **certificados**, escolha **Adicionar**. Em **Adicionar certificado**, forneça estes detalhes para seu certificado. Quando terminar, escolha **OK**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*nome do certificado*> | O nome do seu certificado, que é "privateCert" neste exemplo | 
   | **Tipo de certificado** | Privado | O tipo do certificado |
   | **Certificado** | <> de *nome de arquivo de certificado* | Para localizar e selecionar o arquivo de certificado que você deseja carregar, escolha o ícone de pasta ao lado da caixa **certificado** . Ao usar um cofre de chaves para a chave privada, o arquivo carregado será o certificado público. | 
   | **Grupo de Recursos** | *integração <-Account-Resource-group*> | O grupo de recursos da sua conta de integração, que é "MyResource Group" neste exemplo | 
   | **Cofre de Chaves** | <*nome do cofre de chaves*> | O nome do cofre de chaves do Azure |
   | **Nome da chave** | <*nome da chave*> | O nome da chave |
   ||||

   ![Escolha "Adicionar", forneça os detalhes do certificado](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Depois que o Azure valida sua seleção, o Azure carrega seu certificado.

   ![O Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Passos seguintes

* [Criar um contrato B2B](logic-apps-enterprise-integration-agreements.md)
