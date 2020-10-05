---
title: Guia do Programador do Azure Key Vault
description: Os desenvolvedores podem usar o Azure Key Vault para gerir chaves criptográficas dentro do ambiente Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 23c64f956821dd2a204a15c37bf0fcdde4d09ba8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91716127"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do Programador do Azure Key Vault

O Key Vault permite-lhe aceder de forma segura a informações sensíveis a partir das suas aplicações:

- Chaves, segredos e certificados estão protegidos sem ter de escrever o código por si mesmo e você pode facilmente usá-los a partir das suas aplicações.
- Você é capaz de ter clientes para possuir e gerir suas próprias chaves, segredos e certificados para que você possa se concentrar em fornecer as principais funcionalidades de software. Desta forma, as suas aplicações não serão proprietárias da responsabilidade ou potencial responsabilidade pelas chaves, segredos e certificados dos seus clientes.
- A sua aplicação pode usar chaves para a assinatura e encriptação, mas mantém a gestão da chave externa da sua aplicação. Para mais informações sobre as chaves, consulte [Sobre Chaves](../keys/about-keys.md)
- Você pode gerir credenciais como palavras-passe, chaves de acesso, fichas sas armazenando-as em Key Vault como segredos, ver [Sobre Segredos](../secrets/about-secrets.md)
- Gerir certificados. Para mais informações, consulte [Sobre Certificados](../certificates/about-certificates.md)

Para obter informações mais gerais sobre o Cofre da Chave Azure, consulte [o que é o Cofre de Chaves](overview.md).

## <a name="public-previews"></a>Pré-visualizações públicas

Periodicamente, lançamos uma pré-visualização pública de um novo recurso key vault. Experimente as funcionalidades de pré-visualização do público e informe-nos sobre o que pensa através azurekeyvault@microsoft.com do nosso endereço de e-mail de feedback.

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres-chave

A gestão key Vault, semelhante a outros serviços Azure, é feita através do serviço Azure Resource Manager. O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua conta do Azure. Para mais informações, consulte [o Gestor de Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

O acesso à camada de gestão é controlado pelo [controlo de acesso baseado em funções Azure.](https://docs.microsoft.com/azure/role-based-access-control/overview) No Key Vault, a camada de gestão, também conhecida como avião de gestão ou controlo, permite-lhe criar e gerir cofres-chave e seus atributos, incluindo políticas de acesso, mas não chaves, segredos e certificados, que são geridos em plano de dados. Pode utilizar um papel pré-definido `Key Vault Contributor` para conceder acesso à gestão ao Key Vault.     

**API's e SDKs para gestão de cofres chave:**

| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault)<br>[Início rápido](quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](quick-create-powershell.md)|[Referência](/rest/api/keyvault/)|[Referência](/azure/templates/microsoft.keyvault/vaults)|[Referência](/dotnet/api/microsoft.azure.management.keyvault)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referência](/java/api/com.microsoft.azure.management.keyvault)|[Referência](/javascript/api/@azure/arm-keyvault)|

Consulte [as Bibliotecas do Cliente](client-libraries.md) para obter pacotes de instalação e código fonte.

Para obter mais informações sobre o avião de gestão key vault, consulte [o Key Vault Management Plane](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Autenticar para cofre de chave em código

O Key Vault está a usar a autenticação AD AZure que requer o diretor de segurança AZure AD para conceder acesso. Um diretor de segurança Azure AD pode ser um utilizador, um diretor de serviço de aplicação, uma [identidade gerida para os recursos Azure,](../../active-directory/managed-identities-azure-resources/overview.md)ou um grupo de qualquer tipo de princípios de segurança.

Para aplicações seguras, recomenda-se a utilização de identidade gerida para aplicações implantadas no Azure. Se os serviços da Azure, que não suportam a identidade gerida ou os pedidos implementados no local, [o principal de serviço com um certificado](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) é uma alternativa possível. O certificado deve ser armazenado no Cofre de Chaves e rodado frequentemente. 

O principal do serviço com segredo pode ser usado para ambientes de desenvolvimento e teste, e localmente ou no principal utilizador da Cloud Shell é recomendado.

Para o desenvolvimento de aplicações, pode utilizar o Azure Identity SDK em diferentes ambientes e plataformas sem alterar código. A Azure Identity está integrada com Azure CLI, Visual Studio, Visual Studio Code, entre outros. 

Para obter mais informações, consulte: 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Identidade Azure SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Identidade SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Identidade Azure SDK Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Identidade Azure SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>Gerir chaves, certificados e segredos

O acesso a chaves, segredos e certificados é controlado por um plano de dados. O controlo de acesso a planos de dados pode ser feito usando políticas locais de acesso ao cofre ou RBAC (pré-visualização).

**APIs de chaves e SDKs**


| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/key)<br>[Início rápido](../keys/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../keys/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#key-operations)|N/D|[Referência](/dotnet/api/azure.security.keyvault.keys)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Início rápido](../keys/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[Referência](/javascript/api/@azure/keyvault-keys/)|

**Certificados APIs e SDKs**


| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/certificate)<br>[Início rápido](../certificates/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](../certificates/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#certificate-operations)|N/D|[Referência](/dotnet/api/azure.security.keyvault.certificates)|[Referência](/python/api/overview/azure/keyvault-certificates-readme)<br>[Início rápido](../certificates/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[Referência](/javascript/api/@azure/keyvault-certificates/)|

**Segredos APIs e SDKs**


| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/secret)<br>[Início rápido](../secrets/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../secrets/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#secret-operations)|[Referência](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Início rápido](../secrets/quick-create-template.md)|[Referência](/dotnet/api/azure.security.keyvault.secrets)<br>[Início rápido](../secrets/quick-create-net.md)|[Referência](/python/api/overview/azure/keyvault-secrets-readme)<br>[Início rápido](../secrets/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Início rápido](../secrets/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-secrets/)<br>[Início rápido](../secrets/quick-create-node.md)|

Consulte [as Bibliotecas do Cliente](client-libraries.md) para obter pacotes de instalação e código fonte.

Para obter mais informações sobre a segurança do plano de dados do Key Vault, consulte [o Key Vault Data Plane e as políticas](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) de acesso e Key Vault Data Plane e [RBAC (pré-visualização)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos utilizando o Key Vault com as suas aplicações, consulte:

- [Amostras de código Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) - Amostras de código para cofre de chave Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários fornecem orientações específicas para a tarefa para trabalhar com o Cofre da Chave Azure:

- [Aceder ao Key Vault atrás da firewall](access-behind-firewall.md) - Para aceder a um cofre chave, a aplicação do cliente do cofre principal precisa de ser capaz de aceder a vários pontos finais para várias funcionalidades.
- Como implementar Certificados para VMs do Key Vault - [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) - Uma aplicação em nuvem em execução num VM em Azure precisa de um certificado. Como é que se consegue este certificado neste VM hoje?
- [Implementação do Certificado de Aplicação Web Azure através do Cofre de Chaves](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- Atribuir uma política de acesso[(Portal CLI](assign-access-policy-cli.md)  |  [PowerShell).](assign-access-policy-powershell.md)  |  [Portal](assign-access-policy-portal.md) 
- [Como utilizar o Key Vault soft-delete com O CLI guia-o](soft-delete-cli.md) através da utilização e ciclo de vida de um cofre de chaves e vários objetos chave do cofre com eliminação suave ativado.
- [Como passar valores seguros (como palavras-passe) durante](../../azure-resource-manager/templates/key-vault-parameter.md) a implementação - Quando precisa de passar um valor seguro (como uma palavra-passe) como parâmetro durante a implementação, pode armazenar esse valor como segredo num Cofre de Chave Azure e fazer referência ao valor em outros modelos do Gestor de Recursos.

## <a name="integrated-with-key-vault"></a>Integrado com Cofre-Chave

Estes artigos são sobre outros cenários e serviços que usam ou integram com o Key Vault.

- [Encriptação em REST com Cofre de Chaves](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [A Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) permite-lhe gerente da sua própria chave de inquilino. Por exemplo, em vez de a Microsoft gerir a chave do seu inquilino (o padrão), pode gerir a sua própria chave de inquilino para cumprir os regulamentos específicos que se aplicam à sua organização. A gestão da sua própria chave de inquilino também é referida como Bring Your Own Key (Traga a Sua Própria Chave) ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Principais visões e conceitos do Cofre

- [O comportamento de eliminação suave do Key Vault](soft-delete-overview.md) descreve uma funcionalidade que permite a recuperação de objetos eliminados, quer a eliminação tenha sido acidental ou intencional.
- [O cliente Key Vault o](overview-throttling.md) orienta para os conceitos básicos de estrangulamento e oferece uma abordagem para a sua aplicação.
- [Os principais mundos de segurança do Cofre](overview-security-worlds.md) descrevem as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Key Vault Blog](https://aka.ms/kvblog)
- [Fórum do Cofre-Chave](https://aka.ms/kvforum)
