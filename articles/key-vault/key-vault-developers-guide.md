---
title: Guia do programador do Cofre de chaves do Azure
description: Os programadores podem utilizar o Azure Key Vault para gerir chaves criptográficas dentro do ambiente do Microsoft Azure.
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 06fd66c3fb9e90e0198d40253f3c554fe8596c3d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57543379"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do programador do Cofre de chaves do Azure

Key Vault permite-lhe aceder de forma segura informações confidenciais em seus aplicativos:

- Chaves e segredos estão protegidos sem ter de escrever o código por conta própria e facilmente pode utilizá-los a partir das suas aplicações.
- É possível ter seus próprios de clientes e gerir as suas próprias chaves para que pode se concentrar em fornecer as principais funcionalidades de software. Dessa forma, as aplicações não possui a responsabilidade ou um potencial encargo para chaves de inquilino dos seus clientes e segredos.
- Seu aplicativo pode usar as chaves para assinatura e encriptação ainda mantém a gestão de chaves externas à sua aplicação, permitindo que a sua solução adequada como uma aplicação distribuída geograficamente.
- No momento do lançamento de Setembro de 2016 do Cofre de chaves, seus aplicativos agora podem gerir certificados do Key Vault. Para obter mais informações, consulte [sobre chaves, segredos e certificados](/rest/api/keyvault/about-keys--secrets-and-certificates).

Para obter mais informações sobre o Azure Key Vault, consulte [o que é o Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Pré-visualizações públicas

Periodicamente, Lançamos uma pré-visualização pública de um novo recurso do Key Vault. Experimente estas e informe-no que pensa azurekeyvault@microsoft.com, nosso endereço de e-mail de comentários.

### <a name="storage-account-keys---july-10-2017"></a>Chaves de conta de armazenamento - 10 de Julho de 2017

>[!NOTE]
>Para esta atualização do Cofre de chaves do Azure apenas a **chaves de conta de armazenamento** funcionalidade está em pré-visualização.

Esta pré-visualização inclui a nossa funcionalidade de chaves de conta de armazenamento nova, disponível através dessas interfaces; [.NET / c#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) e [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault). 

Para obter mais informações sobre o novo recurso de chaves de conta de armazenamento, consulte [descrição geral do chaves de conta de armazenamento do Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Vídeos

Este vídeo mostra como criar seu próprio Cofre de chaves e como usá-lo da aplicação de exemplo "Hello Key Vault".

- [Programador do Key Vault - guia de início rápido](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Recursos mencionados acima vídeo:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de exemplo do Cofre de chaves do Azure](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres de chaves

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. Identidades geridas para recursos do Azure torna a resolver esse problema mais simples, fornecendo serviços do Azure uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. 

Para obter mais informações sobre identidades geridas para recursos do Azure, consulte [a visão geral de identidades geridas](../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como trabalhar com o AAD, consulte [integrar aplicações com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados no seu Cofre de chaves, terá de criar e gerir o seu Cofre de chaves através da CLI, PowerShell, modelos do Resource Manager ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerir cofres de chave com a CLI](key-vault-manage-with-cli2.md)
- [Criar e gerir cofres de chave com o PowerShell](key-vault-overview.md)
- [Criar um cofre de chaves e adicionar um segredo por meio de um modelo Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Criar e gerir cofres de chave com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Criando códigos com o Cofre de chaves

O sistema de gestão do Cofre de chaves para os programadores consiste em várias interfaces. Esta secção contém links para todos os idiomas, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Suporte de programação e linguagens de script

#### <a name="rest"></a>REST

Todos os seus recursos do Cofre de chaves estão acessíveis através da interface REST; os cofres, chaves, segredos, etc. 

[Referência da API REST do Cofre de chaves](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência da .NET API para o Key Vault](/dotnet/api/microsoft.azure.keyvault).

Para obter mais informações sobre a versão 2.x do SDK do .NET, consulte a [notas de versão](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK para o Cofre de chaves](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No node. js, a API de gestão do Cofre de chaves e a API de objeto do Cofre de chaves estão separados. O seguinte artigo de descrição geral fornece acesso a ambos. 

[Módulos de Cofre de chaves do Azure para node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotecas de Cofre de chaves do Azure para Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>CLI 2 do Azure

[CLI do Azure para o Cofre de chaves](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[O Azure PowerShell para o Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>Guias de introdução rápida

- [Criar Cofre de chaves](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introdução ao Key Vault em node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos de conclusão com o Key Vault com as suas aplicações, consulte:

- [Exemplos de código do Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para o Azure Key Vault. 
- [Utilizar o Azure Key Vault a partir de uma aplicação Web](quick-create-net.md) -tutorial para ajudá-lo utilizar o Azure Key Vault a partir de uma aplicação web no Azure. 

## <a name="how-tos"></a>Procedimentos

Os artigos e os cenários que se seguem fornecem diretrizes específicas de tarefas para trabalhar com o Azure Key Vault:

- [ID de inquilino do Cofre de chaves de alteração depois de subscrição mover](key-vault-subscription-move-fix.md) - quando mover a sua subscrição do Azure do inquilino A para o inquilino B, seus cofres de chaves existentes estão inacessíveis pelos principais (utilizadores e aplicações) no inquilino correção de B. utilizar este guia.
- [Aceder ao Cofre de chaves protegido por firewall](key-vault-access-behind-firewall.md) - para aceder a um cofre de chaves, a aplicação de cliente do Cofre de chaves tem de ser capaz de aceder a vários pontos finais para várias funcionalidades.
- [Como gerar e Transfer HSM-Protected chaves para o Azure Key Vault](key-vault-hsm-protected-keys.md) -Isto ajudará a planejar, gerar e, em seguida, transferir suas próprias chaves protegidas por HSM para utilizar com o Azure Key Vault.
- [Como transmitir valores seguros (por exemplo, palavras-passe) durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md) - quando precisa passar um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode armazenar esse valor como um segredo num Azure Key Vault e o valor em outro recurso de referência Modelos do gestor.
- [Como utilizar o Key Vault para gestão de chaves extensível com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -permite que o conector do SQL Server para o Cofre de chaves do Azure SQL Server e SQL-em-um-VM para tirar partido do serviço Azure Key Vault como um fornecedor de gestão de chaves extensível (EKM) para proteger seus chaves de encriptação para ligação de aplicações; Encriptação de dados transparente, a encriptação de cópia de segurança e encriptação de nível de coluna.
- [Como implementar certificados em VMs do Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – uma aplicação na cloud em execução numa VM nas necessidades do Azure um certificado. Como obtém este certificado para esta VM hoje em dia?
- [Como configurar o Cofre de chaves com auditoria e rotação de chaves de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – esta mostra-lhe como configurar a rotação de chaves e auditoria com o Azure Key Vault.
- [Implementar o certificado da aplicação Web do Azure através do Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fornece instruções passo a passo para implementar certificados armazenados no Cofre de chaves, como parte da [certificado do serviço de aplicações](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) oferta.
- [Conceder permissão para muitos aplicativos para aceder a um cofre de chaves](key-vault-group-permissions-for-apps.md) política de controlo de acesso do Key Vault suporta até 1024 entradas. No entanto pode criar um grupo de segurança do Azure Active Directory. Adicionar todos os principais de serviço associado a este grupo de segurança e, em seguida, conceder acesso a este grupo de segurança para o Key Vault.
- Para obter mais orientações de tarefa específica sobre como integrar e utilizar cofres de chave com o Azure, consulte [exemplos de modelo do Azure Resource Manager Ryan Jones para o Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como utilizar a eliminação de forma recuperável do Key Vault com a CLI](key-vault-soft-delete-cli.md) orienta-o através da utilização e o ciclo de vida de um cofre de chaves e vários objetos de Cofre de chaves com eliminação de forma recuperável ativada.
- [Como utilizar a eliminação de forma recuperável do Key Vault com o PowerShell](key-vault-soft-delete-powershell.md) orienta-o através da utilização e o ciclo de vida de um cofre de chaves e vários objetos de Cofre de chaves com eliminação de forma recuperável ativada.

## <a name="integrated-with-key-vault"></a>Integrado com o Cofre de chaves

Os artigos sejam sobre outros cenários e serviços que usam ou integram com o Key Vault.

- [O Azure Disk Encryption](../security/azure-security-disk-encryption.md) tira partido da norma da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) recurso do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer encriptação de volume para o sistema operacional e os discos de dados. A solução está integrada no Azure Key Vault para ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, garantindo que todos os dados nos discos da máquina virtual são encriptados em inatividade no armazenamento do Azure.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção para a encriptação de dados que são armazenados na conta. Gestão de chaves, o Data Lake Store disponibiliza dois modos para gerir as chaves de encriptação mestra (MEKs), que são necessárias para desencriptar todos os dados que são armazenados na Store de Lake dados. Pode optar por permitir que os Data Lake Store faça a gestão de MEKs para, ou optar por manter a propriedade de MEKs com a sua conta do Azure Key Vault. Especifique o modo de gestão de chaves ao criar uma conta do Data Lake Store.
- [O Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) permite-lhe para o Gestor de sua própria chave de inquilino. Por exemplo, em vez da Microsoft gerir a sua chave de inquilino (predefinição), pode gerenciar sua própria chave de inquilino para cumprir regulamentos específicos que se aplicam à sua organização. Gerir a sua própria chave de inquilino também é conhecido como traga a sua própria chave ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais do Key Vault e conceitos

- [Comportamento de eliminação de forma recuperável do Key Vault](key-vault-ovw-soft-delete.md) descreve uma funcionalidade que permite a recuperação de objetos eliminados, se a eliminação foi acidental ou intencional.
- [Cliente do Cofre de chaves limitação](key-vault-ovw-throttling.md) descreve os conceitos básicos de limitação e oferece uma abordagem para a sua aplicação.
- [Descrição geral do chaves de conta de armazenamento do Key Vault](key-vault-ovw-storage-keys.md) descreve as chaves de contas de armazenamento do Azure de integração do Cofre de chaves.
- [Mundos de segurança do Cofre de chaves](key-vault-ovw-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Blog do Cofre de chaves](https://aka.ms/kvblog)
- [Fórum do Cofre de chaves](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de suporte

- [Biblioteca do Microsoft Azure Key Vault Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece **IKey** e **IKeyResolver** interfaces para a localização de chaves a partir dos identificadores e realizar operações com chaves.
- [Extensões do Microsoft Azure Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornece recursos expandidos para o Azure Key Vault.
