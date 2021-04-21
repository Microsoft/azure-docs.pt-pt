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
ms.openlocfilehash: 08ac1ae09741b63648aec2b51b6a774a46b9af7c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818445"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do Programador do Azure Key Vault

O Key Vault permite-lhe aceder de forma segura a informações sensíveis a partir das suas aplicações:

- Chaves, segredos e certificados estão protegidos sem ter de escrever o código por si mesmo e você pode facilmente usá-los a partir das suas aplicações.
- Permite que os clientes possuam e gerem as suas próprias chaves, segredos e certificados para que possa concentrar-se em fornecer as principais funcionalidades de software. Desta forma, as suas aplicações não serão proprietárias da responsabilidade ou potencial responsabilidade pelas chaves, segredos e certificados dos seus clientes.
- A sua aplicação pode usar chaves para a assinatura e encriptação, mas mantém a gestão da chave externa da sua aplicação. Para mais informações sobre as chaves, consulte [Sobre Chaves](../keys/about-keys.md)
- Você pode gerir credenciais como palavras-passe, chaves de acesso e fichas sas armazenando-as em Key Vault como segredos, ver [Sobre Segredos](../secrets/about-secrets.md)
- Gerir certificados. Para mais informações, consulte [Sobre Certificados](../certificates/about-certificates.md)

Para obter informações mais gerais sobre o Cofre da Chave Azure, consulte [o que é o Cofre de Chaves](overview.md).

## <a name="public-previews"></a>Pré-visualizações públicas

Periodicamente, lançamos uma pré-visualização pública de um novo recurso key vault. Experimente as funcionalidades de pré-visualização do público e informe-nos sobre o que pensa através azurekeyvault@microsoft.com do nosso endereço de e-mail de feedback.

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres-chave

A gestão key Vault, semelhante a outros serviços Azure, é feita através do serviço Azure Resource Manager. O Azure Resource Manager é o serviço de implementação e gestão do Azure. Fornece uma camada de gestão que lhe permite criar, atualizar e eliminar recursos na sua conta do Azure. Para mais informações, consulte [o Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md)

O acesso à camada de gestão é controlado pelo [controlo de acesso baseado em funções Azure.](../../role-based-access-control/overview.md) No Key Vault, a camada de gestão, também conhecida como avião de gestão ou controlo, permite-lhe criar e gerir cofres-chave e seus atributos, incluindo políticas de acesso, mas não chaves, segredos e certificados, que são geridos em plano de dados. Pode utilizar um papel pré-definido `Key Vault Contributor` para conceder acesso à gestão ao Key Vault.     

**API's e SDKs para gestão de cofres chave:**

| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault)<br>[Início rápido](quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](quick-create-powershell.md)|[Referência](/rest/api/keyvault/)|[Referência](/azure/templates/microsoft.keyvault/vaults)<br>[Início rápido](./vault-create-template.md)|[Referência](/dotnet/api/microsoft.azure.management.keyvault)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referência](/java/api/com.microsoft.azure.management.keyvault)|[Referência](/javascript/api/@azure/arm-keyvault)|

Consulte [as Bibliotecas do Cliente](client-libraries.md) para obter pacotes de instalação e código fonte.

Para obter mais informações sobre o plano de gestão key vault, consulte [as funcionalidades de segurança do Azure Key Vault](security-features.md)

## <a name="authenticate-to-key-vault-in-code"></a>Autenticar para cofre de chave em código

O Key Vault está a usar a autenticação AD AZure que requer o diretor de segurança AZure AD para conceder acesso. Um diretor de segurança Azure AD pode ser um utilizador, um diretor de serviço de aplicação, uma [identidade gerida para os recursos Azure,](../../active-directory/managed-identities-azure-resources/overview.md)ou um grupo de qualquer tipo de princípios de segurança.

### <a name="authentication-best-practices"></a>Melhores práticas de autenticação

Recomenda-se a utilização de identidade gerida para aplicações implantadas no Azure. Se utilizar os serviços Azure, que não suportam a identidade gerida ou se os pedidos forem implementados no local, [o principal de serviço com um certificado](../../active-directory/develop/howto-create-service-principal-portal.md) é uma alternativa possível. Nesse cenário, o certificado deve ser armazenado no Cofre de Chaves e rodado frequentemente. Recomenda-se o principal do serviço com segredo para ambientes de desenvolvimento e teste, e localmente ou em Cloud Shell usando o principal utilizador é recomendado.

Princípios de segurança recomendados por ambiente:
- **Ambiente de produção:**
  - Com certificado de identidade ou serviço gerido
- **Ambientes de ensaio e desenvolvimento:**
  - Identidade gerida, principal de serviço com certificado ou principal de serviço com segredo
- **Desenvolvimento local:**
  - Diretor de utilizadores ou diretor de serviço com segredo

Acima, os cenários de autenticação são suportados pela **biblioteca de clientes da Azure Identity** e integrados com Key Vault SDKs. A biblioteca de identidade Azure pode ser usada em diferentes ambientes e plataformas sem alterar o seu código. A Azure Identity também recuperaria automaticamente o token de autenticação de iniciar sessão no utilizador Azure com Azure CLI, Visual Studio, Visual Studio Code, entre outros. 

Para obter mais informações sobre o libarário do cliente da Identidade Azure, consulte:

**Bibliotecas de clientes de identidade Azure**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Identidade Azure SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identidade SDK Python](/python/api/overview/azure/identity-readme)|[Identidade Azure SDK Java](/java/api/overview/azure/identity-readme)|[Identidade Azure SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> [Biblioteca de autenticação de aplicativos](/dotnet/api/overview/azure/service-to-service-authentication) que foi recomendada para Key Vault .NET SDK versão 3, que está atualmente despracada . Siga [a AppAuthentication para Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration) para migrar para Key Vault .NET SDK Versão 4.

Para tutoriais sobre como autenticar o Key Vault em aplicações, consulte:
- [Autenticar para Key Vault na aplicação hospedada em VM em .NET](./tutorial-net-virtual-machine.md)
- [Autenticar para Key Vault na aplicação hospedada em VM em Python](./tutorial-python-virtual-machine.md)
- [Autenticar para cofre de chaves com serviço de aplicações](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>Gerir chaves, certificados e segredos

O acesso a chaves, segredos e certificados é controlado por um plano de dados. O controlo de acesso a planos de dados pode ser feito usando políticas locais de acesso ao cofre ou Azure RBAC.

**Chaves API's e SDKs**

| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/key)<br>[Início rápido](../keys/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../keys/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#key-operations)|[Referência](/azure/templates/microsoft.keyvault/vaults/keys)<br>[Início rápido](../keys/quick-create-template.md)|[Referência](/dotnet/api/azure.security.keyvault.keys)<br>[Início rápido](../keys/quick-create-net.md)|[Referência](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Início rápido](../keys/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[Início rápido](../keys/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-keys/)<br>[Início rápido](../keys/quick-create-node.md)|

**Certificados API's e SDKs**

| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/certificate)<br>[Início rápido](../certificates/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault)<br>[Início rápido](../certificates/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#certificate-operations)|N/D|[Referência](/dotnet/api/azure.security.keyvault.certificates)<br>[Início rápido](../certificates/quick-create-net.md)|[Referência](/python/api/overview/azure/keyvault-certificates-readme)<br>[Início rápido](../certificates/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[Início rápido](../certificates/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-certificates/)<br>[Início rápido](../certificates/quick-create-node.md)|

**Segredos API's e SDKs**

| CLI do Azure | PowerShell | API REST | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referência](/cli/azure/keyvault/secret)<br>[Início rápido](../secrets/quick-create-cli.md)|[Referência](/powershell/module/az.keyvault/)<br>[Início rápido](../secrets/quick-create-powershell.md)|[Referência](/rest/api/keyvault/#secret-operations)|[Referência](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Início rápido](../secrets/quick-create-template.md)|[Referência](/dotnet/api/azure.security.keyvault.secrets)<br>[Início rápido](../secrets/quick-create-net.md)|[Referência](/python/api/overview/azure/keyvault-secrets-readme)<br>[Início rápido](../secrets/quick-create-python.md)|[Referência](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Início rápido](../secrets/quick-create-java.md)|[Referência](/javascript/api/@azure/keyvault-secrets/)<br>[Início rápido](../secrets/quick-create-node.md)|

Consulte [as Bibliotecas do Cliente](client-libraries.md) para obter pacotes de instalação e código fonte.

Para obter mais informações sobre a segurança do plano de dados key Vault, consulte [as funcionalidades de segurança do Cofre da Chave Azure](security-features.md).

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos utilizando o Key Vault com as suas aplicações, consulte:

- [Amostras de código Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) - Amostras de código para cofre de chave Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários fornecem orientações específicas para a tarefa para trabalhar com o Cofre da Chave Azure:

- [Aceder ao Key Vault atrás da firewall](access-behind-firewall.md) - Para aceder a um cofre chave, a aplicação do cliente do cofre principal precisa de ser capaz de aceder a vários pontos finais para várias funcionalidades.
- Como implementar Certificados para VMs do Key Vault - [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) - Uma aplicação em nuvem em execução num VM em Azure precisa de um certificado. Como é que se consegue este certificado neste VM hoje?
- [Implementação do Certificado de Aplicação Web Azure através do Cofre de Chaves](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- Atribuir uma política de acesso[(Portal CLI](assign-access-policy-cli.md)  |  [PowerShell).](assign-access-policy-powershell.md)  |  [](assign-access-policy-portal.md) 
- [Como utilizar o Key Vault soft-delete com O CLI guia-o](./key-vault-recovery.md) através da utilização e ciclo de vida de um cofre de chaves e vários objetos chave do cofre com eliminação suave ativado.
- [Como passar valores seguros (como palavras-passe) durante](../../azure-resource-manager/templates/key-vault-parameter.md) a implementação - Quando precisa de passar um valor seguro (como uma palavra-passe) como parâmetro durante a implementação, pode armazenar esse valor como segredo num Cofre de Chave Azure e fazer referência ao valor em outros modelos do Gestor de Recursos.

## <a name="integrated-with-key-vault"></a>Integrado com Cofre-Chave

Estes artigos são sobre outros cenários e serviços que usam ou integram com o Key Vault.

- [A encriptação em repouso](../../security/fundamentals/encryption-atrest.md) permite a codificação (encriptação) dos dados quando estes são persistidos. As chaves de encriptação de dados são frequentemente encriptadas com uma chave de encriptação chave no Cofre da Chave Azure para limitar ainda mais o acesso.
- [A Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) permite-lhe gerente da sua própria chave de inquilino. Por exemplo, em vez de a Microsoft gerir a chave do seu inquilino (o padrão), pode gerir a sua própria chave de inquilino para cumprir os regulamentos específicos que se aplicam à sua organização. A gestão da sua própria chave de inquilino também é referida como Bring Your Own Key (Traga a Sua Própria Chave) ou BYOK.
- [O Azure Private Link Service](private-link-service.md) permite-lhe aceder aos Serviços Azure (por exemplo, Azure Key Vault, Azure Storage e Azure Cosmos DB) e a Azure acolheu serviços de cliente/parceiro sobre um Ponto Final Privado na sua rede virtual.
- A integração do Key Vault com [a Grade de Eventos](../../event-grid/event-schema-key-vault.md)  permite que os utilizadores sejam notificados quando o estado de um segredo armazenado no cofre da chave tiver mudado. Pode distribuir nova versão de segredos para aplicações ou rodar perto de segredos de validade para evitar interrupções.
- Pode proteger os seus segredos [do Azure Devops](/azure/devops/pipelines/release/azure-key-vault) de acesso indesejado no Key Vault.
- [Use segredo armazenado no Cofre de Chaves em DataBricks para ligar ao Azure Storage](./integrate-databricks-blob-storage.md)
- Configure e executar o fornecedor Azure Key Vault para o [motorista CSI Secrets Store](./key-vault-integrate-kubernetes.md) em Kubernetes

## <a name="key-vault-overviews-and-concepts"></a>Principais visões e conceitos do Cofre

- [O comportamento de eliminação suave do Key Vault](soft-delete-overview.md) descreve uma funcionalidade que permite a recuperação de objetos eliminados, quer a eliminação tenha sido acidental ou intencional.
- [O cliente Key Vault o](overview-throttling.md) orienta para os conceitos básicos de estrangulamento e oferece uma abordagem para a sua aplicação.
- [Os principais mundos de segurança do Cofre](overview-security-worlds.md) descrevem as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Key Vault Blog](/archive/blogs/kv/)
- [Fórum do Cofre-Chave](https://aka.ms/kvforum)