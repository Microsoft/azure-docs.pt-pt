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
ms.openlocfilehash: 18e1ab1d01394d585cfb06ced8c5fbac04114177
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431959"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do Programador do Azure Key Vault

O Key Vault permite-lhe aceder de forma segura a informações sensíveis a partir das suas aplicações:

- Chaves e segredos são protegidos sem ter que escrever o código por si mesmo e você é facilmente capaz de usá-los a partir das suas aplicações.
- Você é capaz de ter os seus clientes próprios e gerir as suas próprias chaves para que você possa se concentrar em fornecer as funcionalidades de software principais. Desta forma, as suas aplicações não serão responsáveis pela responsabilidade ou potencial responsabilidade pelas chaves e segredos dos seus clientes.
- A sua aplicação pode utilizar chaves para a assinatura e encriptação, mas mantém a gestão da chave externa da sua aplicação, permitindo que a sua solução seja adequada como uma aplicação distribuída geograficamente.
- Gerencie os certificados do Cofre chave. Para mais informações, consulte [Certificados](../certificates/about-certificates.md)

Para obter informações mais gerais sobre o Cofre de Chaves Azure, consulte [o que é](overview.md)o Cofre chave ).

## <a name="public-previews"></a>Pré-visualizações públicas

Periodicamente, lançamos uma pré-visualização pública de uma nova funcionalidade key vault. Experimente isto e deixe-nos saber azurekeyvault@microsoft.como que pensa através do nosso endereço de e-mail de feedback.

## <a name="creating-and-managing-key-vaults"></a>Criação e gestão de cofres-chave

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. Identidades geridas para recursos Azure torna a resolução deste problema mais simples, conferindo aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. 

Para obter mais informações sobre identidades geridas para os recursos Do Azure, consulte [a visão geral das identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md) Para obter mais informações sobre o trabalho com a Azure AD, consulte [AIntegração de aplicações com o Diretório Ativo Azure](../../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados no seu cofre chave, irá criar e gerir o seu cofre chave através de CLI, PowerShell, Resource Manager Templates ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerir cofres chave com CLI](../secrets/quick-create-cli.md)
- [Criar e gerir cofres chave com PowerShell](../secrets/quick-create-powershell.md)
- [Crie e gerencie cofres chave com o porto Azure](../secrets/quick-create-portal.md)
- [Crie e gerencie cofres chave com Python](../secrets/quick-create-python.md)
- [Crie e gerencie cofres chave com Java](../secrets/quick-create-java.md)
- [Crie e gerencie cofres chave com Node.js](../secrets/quick-create-node.md)
- [Criar e gerir cofres chave com .NET (v4 SDK)](../secrets/quick-create-net.md)
- [Crie um cofre chave e adicione um segredo através de um modelo de Gestor de Recursos Azure](../secrets/quick-create-template.md)
- [Criar e gerir cofres chave com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificação com cofre de chaves

O sistema de gestão key vault para programadores é composto por várias interfaces. Esta secção contém ligações a todas as línguas, bem como a alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Línguas de programação e escrita suportadas

#### <a name="rest"></a>REST

Todos os seus recursos Key Vault são acessíveis através da interface REST; cofres, chaves, segredos, etc. 

[Referência aabobadato chave REST API](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência API .NET para Cofre chave](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Para obter mais informações sobre a versão 2.x do .NET SDK, consulte as notas de [Lançamento](dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK para cofre de chaves](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Em Node.js, a API de gestão do Cofre chave e o objeto chave vault API estão separados. O seguinte artigo de visão geral dá-lhe acesso a ambos. 

[Módulos Azure Key Vault para Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Bibliotecas azure key vault para Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>CLI do Azure

[Azure CLI para cofre chave](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para cofre de chaves](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Exemplos de código

Para exemplos completos utilizando o Key Vault com as suas aplicações, consulte:

- [Amostras](https://azure.microsoft.com/resources/samples/?service=key-vault) de código do cofre de chaves Azure - Amostras de código para cofre de chaves Azure. 
- [Use o Cofre chave Azure a partir de uma aplicação web](../secrets/quick-create-net.md) - tutorial para ajudá-lo a aprender a usar o Cofre chave Azure a partir de uma aplicação web em Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários fornecem orientações específicas para tarefas para trabalhar com o Cofre chave Azure:

- [Altere](subscription-move-fix.md) o ID do inquilino do cofre chave após movimento de subscrição - Quando transferir a sua assinatura Azure do inquilino A para o inquilino B, os seus cofres-chave existentes são inacessíveis pelos principais (utilizadores e aplicações) no inquilino B. Corrija isto usando este guia.
- [Aceder ao Key Vault atrás da firewall](access-behind-firewall.md) - Para aceder a um cofre chave, a aplicação do cliente do cofre chave precisa de ter acesso a vários pontos finais para várias funcionalidades.
- [Como gerar e transferir chaves protegidas por HSM para](../keys/hsm-protected-keys.md) o Cofre de Chaves Azure - isto irá ajudá-lo a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para usar com o Cofre de Chaves Azure.
- [Como passar valores seguros (como palavras-passe) durante](../../azure-resource-manager/templates/key-vault-parameter.md) a implementação - Quando precisa passar um valor seguro (como uma palavra-passe) como parâmetro durante a implementação, pode armazenar esse valor como um segredo num Cofre de Chaves Azure e fazer referência ao valor em outros modelos do Gestor de Recursos.
- Como utilizar o Key Vault para uma gestão de chaves extensíveis com o [SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - O Conector de servidor SQL para o Cofre de Chaves Azure permite ao SQL Server e ao SQL-in-a-VM alavancar o serviço Azure Key Vault como fornecedor extensível de gestão de chaves (EKM) para proteger as suas chaves de encriptação para o link de aplicações; Encriptação transparente de dados, encriptação de backup e encriptação do nível da coluna.
- [Como implementar Certificados para VMs a partir do Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Uma aplicação em nuvem em execução num VM no Azure precisa de um certificado. Como é que se consegue este certificado neste VM hoje?
- [Como configurar o Key Vault com](../secrets/key-rotation-log-monitoring.md) a rotação e auditoria da chave de ponta a ponta - Isto passa por como configurar a rotação da chave e a auditoria com o Azure Key Vault.
- A implementação do Certificado de [Aplicação Web Azure através]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) do Key Vault fornece instruções passo a passo para a implementação de certificados armazenados no Key Vault como parte da oferta de Certificado de Serviço de [Aplicações.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Conceda permissão a muitas aplicações para aceder a um cofre chave](group-permissions-for-apps.md) A política de controlo de acesso ao Cofre Chave suporta até 1024 entradas. No entanto, pode criar um grupo de segurança azure Ative Diretório. Adicione todos os principais de serviço associados a este grupo de segurança e, em seguida, conceda acesso a este grupo de segurança ao Key Vault.
- Para obter mais orientações específicas sobre a integração e utilização de Cofres Chave com Azure, consulte os [exemplos de modelo](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)seleções de Key Vault de Ryan Jones para key vault .
- [Como utilizar o Soft-delete key Vault com cli](soft-delete-cli.md) guia-o através da utilização e ciclo de vida de um cofre chave e vários objetos de cofre chave com eliminação suave ativada.
- [Como utilizar o Soft-delete Key Vault com](soft-delete-powershell.md) o PowerShell guia-o através do uso e ciclo de vida de um cofre chave e vários objetos de cofre chave com eliminação suave ativada.

## <a name="integrated-with-key-vault"></a>Integrado com cofre chave

Estes artigos são sobre outros cenários e serviços que usam ou integram com o Key Vault.

- [A encriptação](../../security/fundamentals/encryption-overview.md) do disco Azure aproveita a funcionalidade [bitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume para o OS e os discos de dados. A solução está integrada com o Azure Key Vault para o ajudar a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do cofre de chaves, garantindo ao mesmo tempo que todos os dados nos discos de máquinas virtuais estão encriptados em repouso no seu armazenamento Azure.
- [A Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) oferece opção de encriptação de dados armazenados na conta. Para a gestão de chaves, a Data Lake Store fornece dois modos para gerir as suas chaves de encriptação master (MEKs), que são necessárias para desencriptar quaisquer dados que estejam armazenados na Data Lake Store. Pode deixar a Data Lake Store gerir os MEKs por si, ou optar por manter a propriedade dos MEKs utilizando a sua conta Azure Key Vault. Especifica o modo de gestão da chave enquanto cria uma conta data lake store.
- [A Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) permite-lhe gerente da sua própria chave de inquilino. Por exemplo, em vez de a Microsoft gerir a chave do seu inquilino (o padrão), pode gerir a sua própria chave de inquilino para cumprir os regulamentos específicos aplicáveis à sua organização. A gestão da sua própria chave de inquilino também é referida como Bring Your Own Key (Traga a Sua Própria Chave) ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Vistas e conceitos do cofre chave

- [Porta-chaves do cofre suave comportamento](overview-soft-delete.md)) descreve uma característica que permite a recuperação de objetos apagados, quer a eliminação tenha sido acidental ou intencional.
- [O cliente key Vault que estrangula](overview-throttling.md) orienta-o para os conceitos básicos de estrangulamento e oferece uma abordagem para a sua aplicação.
- Visão geral da conta de [armazenamento do cofre chave)](../secrets/overview-storage-keys.md) descreve as chaves de integração do Cofre Chave Azure Storage Accounts.
- [Os mundos](overview-security-worlds.md) de segurança da Key Vault descrevem as relações entre regiões e áreas de segurança.

## <a name="social"></a>Redes sociais

- [Key Vault Blog](https://aka.ms/kvblog)
- [Fórum do Cofre chave](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de Apoio

- A [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece interfaces **IKey** e **IKeyResolver** para localizar chaves de identificadores e realizar operações com teclas.
- As extensões do cofre da [chave Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem capacidades alargadas para o Cofre de Chaves Azure.
