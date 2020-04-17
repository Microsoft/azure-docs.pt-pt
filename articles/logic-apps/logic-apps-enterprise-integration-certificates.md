---
title: Mensagens B2B seguras com certificados
description: Adicione certificados para ajudar a proteger mensagens B2B em Aplicações Lógicas Azure com o Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 19a1883685193e80da5f1365ec2a30db0b8754f6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450145"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>Melhorar a segurança das mensagens B2B utilizando certificados

Quando precisa de manter a comunicação B2B confidencial, pode aumentar a segurança para a comunicação B2B nas suas aplicações de integração empresarial, especificamente aplicações lógicas, adicionando certificados à sua conta de integração. Os certificados são documentos digitais que verificam as identidades dos participantes nas comunicações electrónicas e ajudam a garantir a comunicação desta forma:

* Encriptar o conteúdo da mensagem.
* Assine digitalmente mensagens.

Pode utilizar estes certificados nas suas aplicações de integração empresarial:

* [Certificados públicos](https://en.wikipedia.org/wiki/Public_key_certificate), que deve adquirir a uma autoridade pública de certificados de internet [(CA),](https://en.wikipedia.org/wiki/Certificate_authority) mas não requerem chaves. 

* Certificados privados ou [*certificados auto-assinados,*](https://en.wikipedia.org/wiki/Self-signed_certificate)que cria e emite a si mesmo, mas também requer chaves privadas. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Faça upload de um certificado público

Para utilizar um *certificado público* em aplicações lógicas que possuam capacidades B2B, tem primeiro de fazer o upload do certificado para a sua conta de integração. Depois de definir as propriedades nos [acordos](logic-apps-enterprise-integration-agreements.md) que cria, o certificado está disponível para o ajudar a garantir as suas mensagens B2B.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu Azure principal, selecione **Todos os recursos.** Na caixa de pesquisa, introduza o nome da sua conta de integração e, em seguida, selecione a conta de integração que deseja.

   ![Encontre e selecione a sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. Em **Componentes,** escolha o azulejo **certificado.**

   ![Escolha "Certificados"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. Em **Certificados,** escolha **Adicionar**. Em **Certificado de Adição,** forneça estes dados para o seu certificado. Quando tiver terminado, selecione **OK**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*nome do certificado*> | O nome do seu certificado, que é "publicCert" neste exemplo | 
   | **Tipo de Certificado** | Público | O tipo do seu certificado |
   | **Certificado** | <*nome de ficheiro de certificado*> | Para encontrar e selecionar o ficheiro de certificado que pretende carregar, escolha o ícone da pasta ao lado da caixa **de Certificado.** |
   ||||

   ![Escolha "Adicionar", fornecer detalhes do certificado](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Depois de o Azure validar a sua seleção, o Azure faz o upload do seu certificado.

   ![Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Faça upload de um certificado privado

Para utilizar um *certificado privado* em aplicações lógicas que possuam capacidades B2B, tem primeiro de fazer o upload do certificado para a sua conta de integração. Você também precisa ter uma chave privada que você primeiro adiciona ao [Cofre chave Azure](../key-vault/key-vault-get-started.md). 

Depois de definir as propriedades nos [acordos](logic-apps-enterprise-integration-agreements.md) que cria, o certificado está disponível para o ajudar a garantir as suas mensagens B2B.

> [!NOTE]
> Para certificados privados, certifique-se de que adiciona um certificado público correspondente que aparece nas definições de **Envio e Receção** [do acordo AS2](logic-apps-enterprise-integration-as2.md) para a assinatura e encriptação de mensagens.

1. [Adicione a sua chave privada ao Cofre de Chaves Azure](../key-vault/certificates/certificate-scenarios.md#import-a-certificate) e forneça um **nome chave**.
   
2. Autorize as Aplicações Lógicas Azure a realizar operações no Cofre chave Azure. Para garantir o acesso ao diretor de serviço de Aplicações Lógicas, utilize o comando PowerShell, [Set-AzKeyVaultAccessPolicy,](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)por exemplo:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Inicie sessão no [portal do Azure](https://portal.azure.com). No menu Azure principal, selecione **Todos os recursos.** Na caixa de pesquisa, introduza o nome da sua conta de integração e, em seguida, selecione a conta de integração que deseja.

   ![Encontre a sua conta de integração](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. Em **Componentes,** escolha o azulejo **certificado.**  

   ![Escolha o azulejo certificado](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. Em **Certificados,** escolha **Adicionar**. Em **Certificado de Adição,** forneça estes dados para o seu certificado. Quando tiver terminado, selecione **OK**.

   | Propriedade | Valor | Descrição | 
   |----------|-------|-------------|
   | **Nome** | <*nome do certificado*> | O nome do seu certificado, que é "privateCert" neste exemplo | 
   | **Tipo de Certificado** | Privado | O tipo do seu certificado |
   | **Certificado** | <*nome de ficheiro de certificado*> | Para encontrar e selecionar o ficheiro de certificado que pretende carregar, escolha o ícone da pasta ao lado da caixa **de Certificado.** Ao utilizar um cofre chave para a chave privada, o ficheiro enviado será o certificado público. | 
   | **Grupo de Recursos** | <*integração-conta-grupo de recursos*> | O grupo de recursos da sua conta de integração, que é o "MyResourceGroup" neste exemplo | 
   | **Cofre de Chaves** | <*nome-cofre-chave*> | O nome do seu cofre de chaves Azure |
   | **Nome da chave** | <*nome-chave*> | O nome da sua chave |
   ||||

   ![Escolha "Adicionar", fornecer detalhes do certificado](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Depois de o Azure validar a sua seleção, o Azure faz o upload do seu certificado.

   ![Azure exibe novo certificado](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Passos seguintes

* [Criar um acordo B2B](logic-apps-enterprise-integration-agreements.md)
