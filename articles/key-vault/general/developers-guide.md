---
title: Guia do Programador do Azure Key Vault
description: Os desenvolvedores podem usar o Azure Key Vault para gerir chaves criptográficas dentro do ambiente Microsoft Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 2b5c28fccc411a8372ebd6015f796c8309146dfa
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85476772"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do Programador do Azure Key Vault

O Key Vault permite-lhe aceder de forma segura a informações sensíveis a partir das suas aplicações:

- As chaves e segredos estão protegidos sem ter de escrever o código por si mesmo e você pode facilmente usá-los a partir das suas aplicações.
- É capaz de ter os seus clientes e gerir as suas próprias chaves para que possa concentrar-se em fornecer as funcionalidades de software principal. Desta forma, as suas aplicações não serão responsáveis pela responsabilidade ou potencial responsabilidade pelas chaves e segredos do seu cliente.
- A sua aplicação pode usar chaves para a assinatura e encriptação, mas mantém a gestão da chave externa da sua aplicação, permitindo que a sua solução seja adequada como uma aplicação geograficamente distribuída.
- Gerir os certificados do Cofre-Chave. Para mais informações, consulte [Certificados](../certificates/about-certificates.md)

Para obter informações mais gerais sobre o Cofre da Chave Azure, consulte [o que é o Cofre de Chaves).](overview.md)

## <a name="public-previews"></a>Pré-visualizações públicas

Periodicamente, lançamos uma pré-visualização pública de um novo recurso key vault. Experimente estes e informe-nos sobre o que pensa através azurekeyvault@microsoft.com do nosso endereço de e-mail de feedback.

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres-chave

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. Identidades geridas para recursos Azure simplificam a resolução deste problema, dando aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. 

Para obter mais informações sobre identidades geridas para recursos Azure, consulte [a visão geral das identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre o trabalho com a Azure AD, consulte [integrar aplicações com o Azure Ative Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados no seu cofre chave, irá criar e gerir o seu cofre-chave através de CLI, PowerShell, Resource Manager Templates ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerir cofres-chave com CLI](../secrets/quick-create-cli.md)
- [Criar e gerir cofres-chave com PowerShell](../secrets/quick-create-powershell.md)
- [Criar e gerir cofres-chave com a porta Azure](../secrets/quick-create-portal.md)
- [Criar e gerir cofres-chave com Python](../secrets/quick-create-python.md)
- [Criar e gerir cofres-chave com Java](../secrets/quick-create-java.md)
- [Criar e gerir cofres-chave com Node.js](../secrets/quick-create-node.md)
- [Criar e gerir cofres-chave com .NET (v4 SDK)](../secrets/quick-create-net.md)
- [Crie um cofre chave e adicione um segredo através de um modelo de Gestor de Recursos Azure](../secrets/quick-create-template.md)
- [Criar e gerir cofres-chave com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificação com cofre de chaves

O sistema de gestão do Cofre-Chave para programadores é composto por várias interfaces. Esta secção contém ligações a todas as línguas, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Linguagens de programação e scripts apoiadas

#### <a name="rest"></a>REST

Todos os seus recursos Key Vault estão acessíveis através da interface REST; cofres, chaves, segredos, etc. 

[Referência API do Cofre do Cofre de Chaves](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET API referência para Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Para obter mais informações sobre a versão 2.x do .NET SDK, consulte as [notas de lançamento](dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK para cofre de chaves](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Em Node.js, a API de gestão do Cofre-Chave e a API do key Vault estão separadas. O seguinte artigo de visão geral dá-lhe acesso a ambos. 

[Módulos Azure Key Vault para Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Bibliotecas Azure Key Vault para Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>CLI do Azure

[Azure CLI para Cofre de Chaves](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para cofre de chaves](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos utilizando o Key Vault com as suas aplicações, consulte:

- [Amostras de código Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) - Amostras de código para cofre de chave Azure. 
- [Use o Cofre de Chaves Azure a partir de uma aplicação web](../secrets/quick-create-net.md) - tutorial para ajudá-lo a aprender a usar o Azure Key Vault a partir de uma aplicação web em Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários fornecem orientações específicas para a tarefa para trabalhar com o Cofre da Chave Azure:

- [Alterar o ID](subscription-move-fix.md) do inquilino do cofre chave após a mudança de subscrição - Quando você mudar a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes são inacessíveis pelos principais (utilizadores e aplicações) no inquilino B. Corrija-o usando este guia.
- [Aceder ao Key Vault atrás da firewall](access-behind-firewall.md) - Para aceder a um cofre chave, a aplicação do cliente do cofre principal precisa de ser capaz de aceder a vários pontos finais para várias funcionalidades.
- [Como gerar e transferir chaves protegidas pelo HSM para o cofre da chave Azure](../keys/hsm-protected-keys.md) - Isto irá ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas pelo HSM para usar com o Cofre da Chave Azure.
- [Como passar valores seguros (como palavras-passe) durante](../../azure-resource-manager/templates/key-vault-parameter.md) a implementação - Quando precisa de passar um valor seguro (como uma palavra-passe) como parâmetro durante a implementação, pode armazenar esse valor como segredo num Cofre de Chave Azure e fazer referência ao valor em outros modelos do Gestor de Recursos.
- [Como utilizar o Key Vault para uma gestão extensível de chaves com o SQL](https://msdn.microsoft.com/library/dn198405.aspx) Server - O Conector do Servidor SQL para o Azure Key Vault permite que o SQL-in-a-VM aproveite o serviço Azure Key Vault como fornecedor de Gestão de Chaves Extensível (EKM) para proteger as suas chaves de encriptação para ligação de aplicações; Encriptação de dados transparente, encriptação de backup e encriptação do nível da coluna.
- [Como implementar certificados para VMs do Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Uma aplicação em nuvem em execução num VM em Azure precisa de um certificado. Como é que se consegue este certificado neste VM hoje?
- [A implementação do Certificado de Aplicação Web Azure através do Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fornece instruções passo a passo para a implementação de certificados armazenados no Cofre-Chave como parte da oferta de Certificado de Serviço de [Aplicações.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Conceder permissão a muitas aplicações para aceder a um cofre chave](group-permissions-for-apps.md) A política de controlo de acesso do Key Vault suporta até 1024 entradas. No entanto, pode criar um grupo de segurança Azure Ative Directory. Adicione todos os principais de serviço associados a este grupo de segurança e, em seguida, conceda acesso a este grupo de segurança ao Key Vault.
- Para obter mais orientações específicas para a integração e utilização de Key Vaults com Azure, consulte [exemplos de modelos do Azure Resource Manager de Ryan Jones para Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como utilizar o Key Vault soft-delete com O CLI guia-o](soft-delete-cli.md) através da utilização e ciclo de vida de um cofre de chaves e vários objetos chave do cofre com eliminação suave ativado.
- [Como utilizar o Key Vault soft-delete com o PowerShell guia-o](soft-delete-powershell.md) através da utilização e ciclo de vida de um cofre de chaves e vários objectos-chave com aabasta ativada.

## <a name="integrated-with-key-vault"></a>Integrado com Cofre-Chave

Estes artigos são sobre outros cenários e serviços que usam ou integram com o Key Vault.

- [A Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) aproveita a funcionalidade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o SISTEMA e os discos de dados. A solução está integrada com o Azure Key Vault para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre principal, garantindo ao mesmo tempo que todos os dados nos discos de máquinas virtuais são encriptados em repouso no seu armazenamento Azure.
- [A Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) oferece a opção de encriptação de dados que são armazenados na conta. Para uma gestão chave, a Data Lake Store fornece dois modos para gerir as suas chaves de encriptação principal (MEKs), que são necessárias para desencriptar quaisquer dados que sejam armazenados na Data Lake Store. Pode permitir que a Data Lake Store gere os MEKs para si, ou opte por manter a propriedade dos MEKs utilizando a sua conta Azure Key Vault. Especifica o modo de gestão de chaves ao criar uma conta Data Lake Store.
- [A Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) permite-lhe gerente da sua própria chave de inquilino. Por exemplo, em vez de a Microsoft gerir a chave do seu inquilino (o padrão), pode gerir a sua própria chave de inquilino para cumprir os regulamentos específicos que se aplicam à sua organização. A gestão da sua própria chave de inquilino também é referida como Bring Your Own Key (Traga a Sua Própria Chave) ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Principais visões e conceitos do Cofre

- [Key Vault soft-delete comportamento](overview-soft-delete.md)) descreve uma característica que permite a recuperação de objetos apagados, quer a eliminação tenha sido acidental ou intencional.
- [O cliente Key Vault o](overview-throttling.md) orienta para os conceitos básicos de estrangulamento e oferece uma abordagem para a sua aplicação.
- [Visão geral das chaves da conta de armazenamento do cofre chave](../secrets/overview-storage-keys.md)) descreve as chaves de integração do Cofre chave Azure Storage Accounts.
- [Os principais mundos de segurança do Cofre](overview-security-worlds.md) descrevem as relações entre regiões e áreas de segurança.

## <a name="social"></a>Redes sociais

- [Key Vault Blog](https://aka.ms/kvblog)
- [Fórum do Cofre-Chave](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de Apoio

- [A Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece interfaces **IKey** e **IKeyResolver** para localizar chaves de identificadores e realizar operações com teclas.
- [As extensões do cofre da chave do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem capacidades alargadas para o Cofre da Chave Azure.
