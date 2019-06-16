---
title: Proteja as mensagens B2B com certificados - Azure Logic Apps | Documentos da Microsoft
description: Adicionar certificados para proteger mensagens B2B no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167188"
---
# <a name="secure-b2b-messages-with-certificates"></a>Mensagens B2B seguras com certificados

Quando precisar de manter a comunicação de B2B confidencial, pode proteger a comunicação de B2B para a sua empresa integração de aplicações, especificamente a aplicações lógicas, adicionando certificados à sua conta de integração. Os certificados são documentos digitais que verifique as identidades para os participantes da comunicação eletrônicos e ajudar a proteger a comunicação nas seguintes formas:

* Encripte o conteúdo da mensagem.
* Assinar digitalmente mensagens. 

Pode utilizar estes certificados nas suas aplicações de integração do enterprise:

* [Certificados públicos](https://en.wikipedia.org/wiki/Public_key_certificate), que tem de comprar uma Internet pública [autoridade de certificação (AC)](https://en.wikipedia.org/wiki/Certificate_authority) mas não precisam de todas as chaves. 

* Certificados privados ou [ *certificados autoassinados*](https://en.wikipedia.org/wiki/Self-signed_certificate), que criar e emitir por conta própria, mas também exigem chaves privadas. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para utilizar um *certificado público* no logic apps com capacidades B2B, primeiro tem de carregar o certificado para a sua conta de integração. Depois de definir as propriedades a [contratos](logic-apps-enterprise-integration-agreements.md) que cria, o certificado está disponível para ajudar a proteger as mensagens B2B.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, introduza o nome da sua conta de integração e, em seguida, selecione a conta de integração que pretende.

   ![Localize e selecione a sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Sob **componentes**, escolha a **certificados** mosaico.

   ![Escolha "Certificados"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Sob **certificados**, escolha **Add**. Sob **Add Certificate**, forneça estes detalhes para o seu certificado. Quando tiver terminado, escolha **OK**.

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | Nome do seu certificado, o que é "publicCert", neste exemplo | 
   | **Tipo de certificado** | Público | Tipo do seu certificado |
   | **Certificado** | <*certificate-file-name*> | Para localizar e selecionar o ficheiro de certificado que pretende carregar, escolha o ícone de pasta junto a **certificado** caixa. |
   ||||

   ![Escolha "Adicionar", forneça os detalhes do certificado](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Depois do Azure valida sua seleção, o Azure carrega o certificado.

   ![Azure apresenta o novo certificado](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para utilizar um *certificado privado* no logic apps com capacidades B2B, primeiro tem de carregar o certificado para a sua conta de integração. Também tem de ter uma chave privada que primeiro adiciona à [do Azure Key Vault](../key-vault/key-vault-get-started.md). 

Depois de definir as propriedades a [contratos](logic-apps-enterprise-integration-agreements.md) que cria, o certificado está disponível para ajudar a proteger as mensagens B2B.

> [!NOTE]
> Para certificados privados, certifique-se de que adicione um certificado público correspondente que aparece no [contrato de AS2](logic-apps-enterprise-integration-as2.md) **enviar e receber** definições para assinatura e criptografia de mensagens.

1. [Adicionar a sua chave privada ao Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) e forneça um **nome da chave**.
   
2. Autorize o Azure Logic Apps para executar operações no Azure Key Vault. Para conceder acesso ao principal de serviço de aplicações lógicas, utilize o comando do PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), por exemplo:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu principal do Azure, selecione **todos os recursos**. Na caixa de pesquisa, introduza o nome da sua conta de integração e, em seguida, selecione a conta de integração que pretende.

   ![Localize a conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Sob **componentes**, escolha a **certificados** mosaico.  

   ![Escolha o mosaico de certificados](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Sob **certificados**, escolha **Add**. Sob **Add Certificate**, forneça estes detalhes para o seu certificado. Quando tiver terminado, escolha **OK**.

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*certificate-name*> | Nome do seu certificado, o que é "privateCert", neste exemplo | 
   | **Tipo de certificado** | Privado | Tipo do seu certificado |
   | **Certificado** | <*certificate-file-name*> | Para localizar e selecionar o ficheiro de certificado que pretende carregar, escolha o ícone de pasta junto a **certificado** caixa. | 
   | **Grupo de Recursos** | <*integration-account-resource-group*> | Grupo de recursos da sua conta de integração, o que é "MyResourceGroup" neste exemplo | 
   | **Cofre de Chaves** | <*key-vault-name*> | Nome do seu Azure do Cofre de chaves |
   | **Nome da chave** | <*key-name*> | Nome da sua chave |
   ||||

   ![Escolha "Adicionar", forneça os detalhes do certificado](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Depois do Azure valida sua seleção, o Azure carrega o certificado.

   ![Azure apresenta o novo certificado](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Passos Seguintes

* [Criar um contrato de B2B](logic-apps-enterprise-integration-agreements.md)
