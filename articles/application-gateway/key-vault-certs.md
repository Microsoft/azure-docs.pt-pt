---
title: TLS terminacom certificados azure key vault
description: Saiba como pode integrar o Portal de Aplicações Azure com o Key Vault para certificados de servidor que estão ligados a ouvintes com acesso a HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: be558c3e3a68ce6c194dcf98d8f5ff92c4c14edb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457829"
---
# <a name="tls-termination-with-key-vault-certificates"></a>TLS terminação com certificados key vault

[O Azure Key Vault](../key-vault/general/overview.md) é uma loja secreta gerida pela plataforma que pode usar para salvaguardar segredos, chaves e certificados TLS/SSL. O Portal de Aplicações Azure suporta a integração com o Key Vault para certificados de servidor que estão ligados a ouvintes com SUPORTE HTTPS. Este suporte está limitado ao v2 SKU do Application Gateway.

A integração do Key Vault oferece dois modelos para a rescisão de TLS:

- Pode fornecer explicitamente certificados TLS/SSL anexados ao ouvinte. Este modelo é a forma tradicional de passar certificados TLS/SSL para o Gateway de Aplicação para a rescisão de TLS.
- Pode fornecer opcionalmente uma referência a um certificado ou segredo do Cofre chave existente quando criar um ouvinte ativado por HTTPS.

A integração do Gateway de aplicações com o Key Vault oferece muitos benefícios, incluindo:

- Segurança mais forte, porque os certificados TLS/SSL não são tratados diretamente pela equipa de desenvolvimento de aplicações. A integração permite que uma equipa de segurança separada:
  * Configurar portais de aplicação.
  * Controlar os ciclos de vida da aplicação de porta de entrada.
  * Conceda permissões a gateways de aplicação selecionados para aceder a certificados que estão armazenados no seu cofre chave.
- Apoio à importação de certificados existentes no seu cofre chave. Ou use APIs key vault para criar e gerir novos certificados com qualquer um dos parceiros de cofre confiável.
- Suporte para renovação automática de certificados que estão armazenados no seu cofre chave.

O Application Gateway suporta atualmente apenas certificados validados por software. Os módulos de segurança de hardware (HSM) validados não são suportados. Depois de configurar o Gateway da Aplicação para utilizar certificados Key Vault, as suas instâncias recuperam o certificado do Cofre chave e instalam-nos localmente para a rescisão de TLS. Os casos também pesquisam Key Vault em intervalos de 24 horas para recuperar uma versão renovada do certificado, se existir. Se for encontrado um certificado atualizado, o certificado TLS/SSL atualmente associado ao ouvinte HTTPS é automaticamente rodado.

> [!NOTE]
> O portal Azure só suporta certificados KeyVault, não segredos. Application Gateway ainda suporta a referenciação de segredos da KeyVault, mas apenas através de recursos não-Portal como PowerShell, CLI, API, modelos ARM, etc. 

## <a name="how-integration-works"></a>Como funciona a integração

A integração do Gateway de aplicação com o Key Vault requer um processo de configuração em três etapas:

1. **Criar uma identidade gerida atribuída ao utilizador**

   Cria ou reutiliza uma identidade gerida atribuída ao utilizador existente, que o Application Gateway utiliza para recuperar certificados da Key Vault em seu nome. Para mais informações, veja [o que é gerida identidades para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md) Este passo cria uma nova identidade no inquilino do Azure Ative Directory. A identidade é confiada pela subscrição que é usada para criar a identidade.

1. **Configure o seu cofre chave**

   Em seguida, importa um certificado existente ou cria um novo no seu cofre chave. O certificado será utilizado por aplicações que percorrem o gateway da aplicação. Neste passo, você também pode usar um segredo de cofre chave que é armazenado como um ficheiro PFX sem palavra-passe, base 64 codificado. Recomendamos a utilização de um tipo de certificado devido à capacidade de renovação automática que está disponível com objetos do tipo certificado no cofre da chave. Depois de criar um certificado ou um segredo, define as políticas de acesso no cofre chave para permitir que a identidade seja concedida *tenha* acesso ao segredo.

1. **Configurar o gateway de aplicação**

   Depois de completar os dois passos anteriores, pode configurar ou modificar um portal de aplicação existente para utilizar a identidade gerida atribuída pelo utilizador. Também pode configurar o certificado TLS/SSL do ouvinte HTTP para apontar para o URI completo do certificado Key Vault ou ID secreto.

   ![Certificados de cofre chave](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos seguintes

[Configure a rescisão de TLS com certificados key vault utilizando o Azure PowerShell](configure-keyvault-ps.md)
