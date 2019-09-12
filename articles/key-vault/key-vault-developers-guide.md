---
title: Guia do desenvolvedor de Azure Key Vault
description: Os desenvolvedores podem usar Azure Key Vault para gerenciar chaves de criptografia no ambiente de Microsoft Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: ca640b058a1d91d15c5accb9367936368511a3ef
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881603"
---
# <a name="azure-key-vault-developers-guide"></a>Guia do desenvolvedor de Azure Key Vault

Key Vault permite que você acesse com segurança informações confidenciais de dentro de seus aplicativos:

- Chaves e segredos são protegidos sem precisar escrever o código por conta própria e você pode usá-los facilmente de seus aplicativos.
- Você pode ter seus clientes e gerenciar suas próprias chaves para que possa se concentrar em fornecer os principais recursos de software. Dessa forma, seus aplicativos não terão a responsabilidade ou a responsabilidade potencial das chaves de locatário e dos segredos de seus clientes.
- Seu aplicativo pode usar chaves para assinatura e criptografia, mas mantém o gerenciamento de chaves externo do seu aplicativo, permitindo que sua solução seja adequada como um aplicativo distribuído geograficamente.
- A partir da versão de setembro de 2016 do Key Vault, seus aplicativos agora podem gerenciar Key Vault certificados. Para obter mais informações, consulte [sobre chaves, segredos e certificados](/rest/api/keyvault/about-keys--secrets-and-certificates).

Para obter mais informações gerais sobre Azure Key Vault, consulte [o que é Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Visualizações públicas

Periodicamente, lançamos uma visualização pública de um novo recurso Key Vault. Experimente-os e diga-nos o que você imagina azurekeyvault@microsoft.compor meio de nosso endereço de email de comentários.

### <a name="storage-account-keys---july-10-2017"></a>Chaves de conta de armazenamento-10 de julho de 2017

>[!NOTE]
>Para esta atualização de Azure Key Vault somente o recurso de **chaves de conta de armazenamento** está em versão prévia.

Essa visualização inclui nosso novo recurso de chaves de conta de armazenamento, disponível por meio dessas interfaces; [.NET/C#](/dotnet/api/microsoft.azure.keyvault/), [REST](/rest/api/keyvault/) e [PowerShell](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault). 

Para obter mais informações sobre o novo recurso de chaves de conta de armazenamento, consulte [Azure Key Vault visão geral das chaves de conta de armazenamento](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Os meus vídeos

Este vídeo mostra como criar seu próprio cofre de chaves e como usá-lo no aplicativo de exemplo ' Olá Key Vault '.

- [Key Vault Developer – guia de início rápido](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Recursos mencionados no vídeo acima:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault código de exemplo](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Criando e gerenciando cofres de chaves

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. Identidades gerenciadas para recursos do Azure facilitam a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código. 

Para obter mais informações sobre identidades gerenciadas para recursos do Azure, consulte [a visão geral de identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md). Para obter mais informações sobre como trabalhar com o AAD, consulte [integrando aplicativos com Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Antes de trabalhar com chaves, segredos ou certificados no cofre de chaves, você criará e gerenciará seu cofre de chaves por meio da CLI, do PowerShell, dos modelos do Resource Manager ou do REST, conforme descrito nos seguintes artigos:

- [Criar e gerenciar cofres de chaves com a CLI](key-vault-manage-with-cli2.md)
- [Criar e gerenciar cofres de chaves com o PowerShell](key-vault-overview.md)
- [Criar um cofre de chaves e adicionar um segredo por meio de um modelo de Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Criar e gerenciar cofres de chaves com REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificando com Key Vault

O sistema de gerenciamento de Key Vault para programadores consiste em várias interfaces. Esta seção contém links para todos os idiomas, bem como alguns exemplos de código. 

### <a name="supported-programming-and-scripting-languages"></a>Linguagens de script e programação com suporte

#### <a name="rest"></a>REST

Todos os seus recursos de Key Vault podem ser acessados por meio da interface REST; cofres, chaves, segredos, etc. 

[Key Vault referência da API REST](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[Referência da API do .net para Key Vault](/dotnet/api/microsoft.azure.keyvault).

Para obter mais informações sobre a versão 2. x do SDK do .NET, consulte as [notas de versão](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[SDK do Java para Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No node. js, a API de gerenciamento de Key Vault e a API de objeto de Key Vault são separadas. O seguinte artigo de visão geral fornece acesso a ambos. 

[Módulos Azure Key Vault para node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotecas de Azure Key Vault para Python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>CLI do Azure 2

[CLI do Azure para Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>Guias de introdução rápida

- [Criar Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introdução ao Key Vault no node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos completos usando Key Vault com seus aplicativos, consulte:

- [Exemplos de código de Azure Key Vault](https://azure.microsoft.com/resources/samples/?service=key-vault) -exemplos de código para Azure Key Vault. 
- [Use Azure Key Vault de um aplicativo Web](quick-create-net.md) -tutorial para ajudá-lo a aprender a usar Azure Key Vault de um aplicativo Web no Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários fornecem diretrizes específicas para tarefas para trabalhar com Azure Key Vault:

- [Alterar a ID de locatário do cofre de chaves após a movimentação da assinatura](key-vault-subscription-move-fix.md) -quando você move sua assinatura do Azure do locatário a para o locatário b, seus cofres de chaves existentes são inacessíveis pelas entidades (usuários e aplicativos) no locatário b. corrija isso usando este guia.
- [Acessando Key Vault por trás do firewall](key-vault-access-behind-firewall.md) -para acessar um cofre de chaves, seu aplicativo cliente do cofre de chaves precisa ser capaz de acessar vários pontos de extremidade para várias funcionalidades.
- [Como gerar e transferir chaves protegidas por HSM para Azure Key Vault](key-vault-hsm-protected-keys.md) -isso o ajudará a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com Azure Key Vault.
- [Como passar valores seguros (como senhas) durante a implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md) -quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, você pode armazenar esse valor como um segredo em um Azure Key Vault e fazer referência ao valor em outro gerenciador de recursos modelo.
- [Como usar Key Vault para o gerenciamento extensível de chaves com SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -a Conector do SQL Server para Azure Key Vault permite que o SQL Server e o SQL-in-a-VM aproveitem o serviço Azure Key Vault como um provedor EKM (gerenciamento extensível de chaves) para proteger seu link de chaves de criptografia para aplicativos; Transparent Data Encryption, criptografia de backup e criptografia em nível de coluna.
- [Como implantar certificados em VMs de Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) -um aplicativo de nuvem em execução em uma VM no Azure precisa de um certificado. Como você obtém esse certificado nesta VM hoje mesmo?
- [Como configurar Key Vault com a rotação de chaves e auditoria de ponta a ponta](key-vault-key-rotation-log-monitoring.md) – explica como configurar a rotação de chaves e a auditoria com Azure Key Vault.
- [A implantação de um certificado de aplicativo Web do Azure por meio de Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fornece instruções passo a passo para implantar certificados armazenados no Key Vault como parte da oferta de [certificado do serviço de aplicativo](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) .
- [Conceder permissão a muitos aplicativos para acessar um cofre de chaves](key-vault-group-permissions-for-apps.md) Key Vault política de controle de acesso dá suporte a até 1024 entradas. No entanto, você pode criar um grupo de segurança Azure Active Directory. Adicione todas as entidades de serviço associadas a esse grupo de segurança e, em seguida, conceda acesso a esse grupo de segurança para Key Vault.
- Para obter mais diretrizes específicas de tarefas sobre como integrar e usar cofres de chaves com o Azure, consulte [Ryan Jones ' Azure Resource Manager exemplos de modelo para Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como usar Key Vault exclusão reversível com a CLI](key-vault-soft-delete-cli.md) orienta o uso e o ciclo de vida de um cofre de chaves e de vários objetos do cofre de chaves com a exclusão reversível habilitada.
- [Como usar Key Vault exclusão reversível com o PowerShell](key-vault-soft-delete-powershell.md) orienta o uso e o ciclo de vida de um cofre de chaves e de vários objetos do cofre de chaves com a exclusão reversível habilitada.

## <a name="integrated-with-key-vault"></a>Integrado com o Key Vault

Estes artigos são sobre outros cenários e serviços que usam ou integram com Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) aproveita o recurso de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer criptografia de volume para o sistema operacional e os discos de dados. A solução é integrada com Azure Key Vault para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e os segredos em sua assinatura do cofre de chaves, garantindo que todos os dados nos discos da máquina virtual sejam criptografados em repouso no armazenamento do Azure.
- [Azure data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) fornece a opção para a criptografia de dados armazenados na conta. Para o gerenciamento de chaves, Data Lake Store fornece dois modos para gerenciar suas chaves de criptografia mestras (MEKs), que são necessárias para descriptografar todos os dados armazenados no Data Lake Store. Você pode permitir que Data Lake Store gerenciar o MEKs para você ou optar por manter a propriedade do MEKs usando sua conta de Azure Key Vault. Você especifica o modo de gerenciamento de chaves ao criar uma conta de Data Lake Store.
- A [proteção de informações do Azure](/azure/information-protection/plan-implement-tenant-key) permite que você Manager sua própria chave de locatário. Por exemplo, em vez de Microsoft gerenciar sua chave de locatário (o padrão), você pode gerenciar sua própria chave de locatário para obedecer a regulamentos específicos que se aplicam à sua organização. Gerenciar sua própria chave de locatário também é conhecido como trazer sua própria chave, ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Visões gerais e conceitos do Key Vault

- [Key Vault comportamento de exclusão reversível](key-vault-ovw-soft-delete.md) descreve um recurso que permite a recuperação de objetos excluídos, independentemente de a exclusão ser acidental ou intencional.
- [Key Vault limitação de cliente](key-vault-ovw-throttling.md) orienta você aos conceitos básicos de limitação e oferece uma abordagem para seu aplicativo.
- [Key Vault visão geral das chaves de conta de armazenamento](key-vault-ovw-storage-keys.md) descreve as chaves de contas de armazenamento do Azure de integração do Key Vault.
- [Key Vault mundos de segurança](key-vault-ovw-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Social

- [Blog de Key Vault](https://aka.ms/kvblog)
- [Fórum de Key Vault](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de suporte

- [Microsoft Azure biblioteca de Key Vault Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece interfaces **iKey** e **IKeyResolver** para localizar chaves de identificadores e executar operações com chaves.
- [Microsoft Azure extensões de Key Vault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornecem recursos estendidos para Azure Key Vault.
