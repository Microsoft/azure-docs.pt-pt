---
title: Terminação de SSL com certificados do Azure Key Vault
description: Saiba como integrar o Gateway de aplicação do Azure com o Key Vault para certificados de servidor que estão anexados a serviços de escuta de HTTPS ativado.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827633"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminação de SSL com certificados do Key Vault

[O Azure Key Vault](../key-vault/key-vault-whatis.md) é armazenar de um segredo de plataforma gerido que pode utilizar para salvaguardar segredos, chaves e certificados SSL. O Gateway de aplicação do Azure suporta a integração com o Key Vault (em pré-visualização pública) para certificados de servidor que estão anexados a serviços de escuta de HTTPS ativado. Esse suporte é limitado ao v2 SKU de Gateway de aplicação.

> [!IMPORTANT]
> Integração do Gateway de aplicação com o Key Vault está atualmente em pré-visualização pública. Esta pré-visualização é fornecida sem um contrato de nível de serviço (SLA) e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Esta pré-visualização pública oferece dois modelos para a terminação de SSL:

- Pode fornecer explicitamente os certificados SSL ligados ao serviço de escuta. Este modelo é a maneira tradicional de transmitir os certificados SSL no Gateway de aplicação para a terminação de SSL.
- Opcionalmente, pode fornecer uma referência a um certificado do Key Vault existente ou um segredo ao criar um serviço de escuta de HTTPS ativado.

Integração do Gateway de aplicação com o Key Vault oferece muitas vantagens adicionais, incluindo:

- Segurança mais forte, como certificados SSL não são tratados diretamente pela equipe de desenvolvimento de aplicativo. Integração permite que uma equipe de segurança separados para:
  * Configure gateways de aplicação.
  * Controlar os ciclos de vida de gateway de aplicação.
  * Conceder permissões aos gateways de aplicação selecionados para aceder a certificados que estão armazenados no seu Cofre de chaves.
- Suporte para a importação de certificados existentes no seu Cofre de chaves. Ou utilize APIs de Cofre de chave para criar e gerir novos certificados com qualquer um dos parceiros de Key Vault confiáveis.
- Suporte para renovação automática de certificados que estão armazenados no seu Cofre de chaves.

Gateway de aplicação suporta atualmente certificados validados de software. O módulo de segurança de hardware (HSM)-validados certificados não são suportados. Depois do Gateway de aplicação está configurado para utilizar certificados do Key Vault, suas instâncias obter o certificado do Key Vault e instalação-los localmente para a terminação de SSL. As instâncias também consultar o Cofre de chaves em intervalos de 24 horas para recuperar uma versão renovada do certificado, se existir. Se não for encontrado um certificado atualizado, o certificado SSL atualmente associado com o serviço de escuta HTTPS é rodado automaticamente.

## <a name="how-integration-works"></a>Como funciona a integração

Integração do Gateway de aplicação com o Key Vault requer um processo de configuração de três etapas:

1. **Criar uma identidade gerida atribuído ao utilizador**

   Criar ou reutilizar uma atribuído ao utilizador identidade gerida existente, que utiliza o Gateway de aplicação para obter certificados do Key Vault em seu nome. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md). Este passo cria uma nova identidade no inquilino do Azure Active Directory. A identidade é considerado fidedigno pela subscrição que é utilizada para criar a identidade.

1. **Configurar o seu Cofre de chaves**

   , Em seguida, ou importar um certificado existente ou crie uma nova no seu Cofre de chaves. O certificado será utilizado por aplicações que são executados através do gateway de aplicação. Neste passo, também pode usar um segredo do Cofre de chaves que é armazenado como um sem palavra-passe, a base 64 codificada ficheiro PFX. Recomendamos que utilize um tipo de certificado devido a capacidade de renovação automática está disponível com objetos de tipo de certificado no Cofre de chaves. Depois de criar um certificado ou um segredo, é possível definir políticas de acesso no Cofre de chaves para permitir que a identidade ser concedida *obter* acesso para o segredo.

1. **Configurar o gateway de aplicação**

   Depois de concluir dois passos anteriores, pode configurar ou modificar um gateway de aplicação existente para utilizar a identidade gerida atribuído ao utilizador. Também pode configurar o certificado SSL do serviço de escuta HTTP para apontar para o URI completo do certificado do Key Vault ou ID secreta.

   ![Certificados de Cofre de chaves](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos Seguintes

[Configurar a terminação de SSL com certificados do Key Vault com o Azure PowerShell](configure-keyvault-ps.md)
