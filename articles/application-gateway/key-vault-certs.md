---
title: Terminação de SSL com certificados do Key Vault
description: Saiba como integrar o gateway de aplicação do Azure com o Key Vault para certificados de servidor que estão anexados a serviços de escuta HTTPS ativado.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 37707d56caabf0ae8b0020eb8714245a27501ea6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696500"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Terminação de SSL com certificados do Key Vault

[O Azure Key Vault](../key-vault/key-vault-whatis.md) é um arquivo secreto de plataforma gerido que pode utilizar para salvaguardar segredos, chaves e certificados SSL. Gateway de aplicação suporta a integração com o Key Vault (em pré-visualização pública) para certificados de servidor que estão anexados a serviços de escuta HTTPS ativado. Esse suporte é limitado ao v2 SKU de Gateway de aplicação.

> [!IMPORTANT]
> A integração do Cofre de chaves do Gateway de aplicação está atualmente em pré-visualização pública. Esta pré-visualização é disponibilizada sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Existem dois modelos para a terminação de SSL com esta pré-visualização pública:

- Pode fornecer explicitamente os certificados SSL ligados ao serviço de escuta. Esse é o modelo tradicional de passar os certificados SSL no Gateway de aplicação para a terminação de SSL.
- Opcionalmente, pode fornecer uma referência a um certificado do Key Vault existente ou segredo durante HTTPS ativado a criação do serviço de escuta.

Há muitos benefícios com a integração do Cofre de chaves, incluindo:

- Uma vez que os certificados SSL não são processados diretamente pela equipe de desenvolvimento de aplicativos de segurança mais forte. Integração com o Key Vault permite que uma equipe de segurança separados para aprovisionar, controlar o ciclo de vida e dar permissão adequada para selecionar os Gateways de aplicação para certificados de acesso armazenados no Key Vault.
- Suporte para importar certificados existentes para o Cofre de chaves ou utilizar as APIs do Cofre de chave para criar e gerir novos certificados com qualquer um dos parceiros de Key Vault confiáveis.
- Suporte para certificados armazenados no Cofre de chaves para renovar automaticamente.

Atualmente, o Gateway de aplicação suporta apenas os certificados de software validada. Certificados de módulo (HSM) validado de segurança de hardware não são suportados. Assim que o Gateway de aplicação está configurado para utilizar certificados do Key Vault, suas instâncias obter o certificado do Key Vault e instalação-los localmente para a terminação de SSL. As instâncias de consultar também periodicamente o Key Vault a num intervalo de 24 horas para recuperar uma versão renovada do certificado, se existir. Se não for encontrado um certificado atualizado, o certificado SSL atualmente associado com o serviço de escuta de HTTPS é rodado automaticamente.

## <a name="how-it-works"></a>Como funciona

Integração com o Key Vault requer um processo de configuração de três etapas:

1. **Criar utilizador atribuído a identidade gerida**

   Tem de criar ou reutilizar um atribuído a identidade gerida que utiliza o Gateway de aplicação para obter certificados do Key Vault em seu nome de utilizador existente. Para obter mais informações, consulte [o que há de identidades geridas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md) Este passo cria uma nova identidade no inquilino do Azure AD, que é considerado fidedigno pela subscrição utilizada para criar a identidade.
1. **Configurar o Cofre de chaves**

   Em seguida, tem a importar ou criar um novo certificado no Cofre de chaves utilizados pelas aplicações em execução por meio do Gateway de aplicação. Um segredo do Key Vault armazenado como palavra-passe sem base 64 codificada ficheiro PFX também pode ser usadas neste passo. A utilização de um tipo de certificado é preferido devido à capacidades de renovação automática disponíveis com objetos de tipo de certificado no Cofre de chaves. Assim que for criado um certificado ou um segredo, as políticas de acesso tem de ser definidas no Key Vault, para permitir que a identidade ser concedida *obter* acesso ao obter o segredo.

1. **Configurar o Gateway de aplicação**

   Depois de concluir os dois passos anteriores, pode aprovisionar ou modificar um Gateway de aplicação existente para utilizar o identidade gerida atribuída ao utilizador. Também de configurar o certificado SSL do serviço de escuta HTTP para apontar para do completa URI do Cofre de chaves certificado ou ID secreta.

![Certificados do Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passos Seguintes

[Configurar a terminação de SSL com certificados do Key Vault com o Azure PowerShell](configure-keyvault-ps.md).