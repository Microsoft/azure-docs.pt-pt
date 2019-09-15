---
title: Terminação SSL com certificados Azure Key Vault
description: Saiba como você pode integrar Aplicativo Azure gateway com Key Vault para certificados de servidor que são anexados a ouvintes habilitados para HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 725a9d67e6a6412fc48a4278b5a8a163272e5133
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000990"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminação SSL com certificados Key Vault

[Azure Key Vault](../key-vault/key-vault-overview.md) é um repositório de segredos gerenciado por plataforma que você pode usar para proteger segredos, chaves e certificados SSL. Aplicativo Azure gateway dá suporte à integração com o Key Vault (em visualização pública) para certificados de servidor que são anexados a ouvintes habilitados para HTTPS. Esse suporte é limitado à SKU v2 do gateway de aplicativo.

> [!IMPORTANT]
> A integração do gateway de aplicativo com o Key Vault está atualmente em visualização pública. Essa visualização é fornecida sem um SLA (contrato de nível de serviço) e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Essa visualização pública oferece dois modelos para terminação SSL:

- Você pode fornecer explicitamente certificados SSL anexados ao ouvinte. Esse modelo é a maneira tradicional de passar certificados SSL para o gateway de aplicativo para terminação SSL.
- Opcionalmente, você pode fornecer uma referência a um certificado ou segredo de Key Vault existente ao criar um ouvinte habilitado para HTTPS.

A integração do gateway de aplicativo com o Key Vault oferece muitos benefícios, incluindo:

- Segurança mais forte, pois os certificados SSL não são tratados diretamente pela equipe de desenvolvimento de aplicativos. A integração permite que uma equipe de segurança separada:
  * Configurar gateways de aplicativo.
  * Controlar ciclos de vida do gateway de aplicativo.
  * Conceda permissões a gateways de aplicativo selecionados para acessar certificados armazenados em seu cofre de chaves.
- Suporte para importar certificados existentes para o cofre de chaves. Ou use Key Vault APIs para criar e gerenciar novos certificados com qualquer um dos parceiros de Key Vault confiáveis.
- Suporte para renovação automática de certificados armazenados em seu cofre de chaves.

O gateway de aplicativo atualmente dá suporte apenas a certificados validados por software. Não há suporte para certificados validados do HSM (módulo de segurança de hardware). Depois que o gateway de aplicativo é configurado para usar certificados de Key Vault, suas instâncias recuperam o certificado do Key Vault e os instalam localmente para terminação SSL. As instâncias também sondam Key Vault em intervalos de 24 horas para recuperar uma versão renovada do certificado, se existir. Se um certificado atualizado for encontrado, o certificado SSL atualmente associado ao ouvinte HTTPS será automaticamente girado.

## <a name="how-integration-works"></a>Como funciona a integração

A integração do gateway de aplicativo com o Key Vault requer um processo de configuração de três etapas:

1. **Criar uma identidade gerenciada atribuída pelo usuário**

   Você cria ou reutiliza uma identidade gerenciada atribuída pelo usuário existente, que o gateway de aplicativo usa para recuperar certificados de Key Vault em seu nome. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md). Esta etapa cria uma nova identidade no locatário Azure Active Directory. A identidade é confiável para a assinatura que é usada para criar a identidade.

1. **Configurar o cofre de chaves**

   Em seguida, importe um certificado existente ou crie um novo no cofre de chaves. O certificado será usado por aplicativos que são executados por meio do gateway de aplicativo. Nesta etapa, você também pode usar um segredo do Key Vault que é armazenado como um arquivo PFX codificado por senha, com base em 64. É recomendável usar um tipo de certificado devido ao recurso de renovação automática disponível com objetos de tipo de certificado no cofre de chaves. Depois de criar um certificado ou um segredo, defina as políticas de acesso no cofre de chaves para permitir que a identidade *receba acesso ao* segredo.

1. **Configurar o gateway de aplicativo**

   Depois de concluir as duas etapas anteriores, você pode configurar ou modificar um gateway de aplicativo existente para usar a identidade gerenciada atribuída pelo usuário. Você também pode configurar o certificado SSL do ouvinte HTTP para apontar para o URI completo do certificado de Key Vault ou ID secreta.

   ![Certificados do Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos seguintes

[Configurar a terminação SSL com certificados de Key Vault usando Azure PowerShell](configure-keyvault-ps.md)
