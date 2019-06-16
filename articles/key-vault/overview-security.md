---
title: Segurança do Cofre de chaves do Azure | Documentos da Microsoft
description: Gerir permissões de acesso para o Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização para o Cofre de chaves e como proteger o seu Cofre de chaves.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 5b32e4897e718e0e411caf9ba76b036f1352bde0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715263"
---
# <a name="azure-key-vault-security"></a>Segurança do Cofre de chaves do Azure

É necessário proteger chaves de encriptação e segredos, como certificados, cadeias de ligação e palavras-passe na cloud, para que está a utilizar o Azure Key Vault. Uma vez que estiver armazenando confidenciais e dados críticos da empresa, terá de tomar medidas para maximizar a segurança dos seus cofres e os dados armazenados nas mesmas. Este artigo, falarei sobre alguns dos conceitos que deve considerar ao projetar a segurança do Azure Key Vault.

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

Quando cria um cofre de chaves numa subscrição do Azure, é automaticamente associado com o inquilino do Azure AD da subscrição. Qualquer pessoa tentar gerir ou obter conteúdo a partir de um cofre tem de ser autenticada pelo Azure AD.

- A autenticação estabelece a identidade do chamador.
- Autorização determina as operações que o chamador pode realizar. Autorização no Key Vault utiliza uma combinação de [controlo de acesso baseado em funções](../role-based-access-control/overview.md) (RBAC) e políticas de acesso do Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

Acesso aos cofres ocorre por meio de duas interfaces ou planos. Estes planos são o plano de gestão e o plano de dados.

- O *plano de gestão* é onde gere Key Vault em si e é a interface usada para criar e eliminar cofres. Também pode ler as propriedades do Cofre de chaves e gerir políticas de acesso.
- O *plano de dados* permite-lhe trabalhar com os dados armazenados num cofre de chaves. Pode adicionar, eliminar e modificar as chaves, segredos e certificados.

Para aceder a um cofre de chaves num plano, todos os chamadores (utilizadores ou aplicações) têm sejam autenticados e autorizados. Ambos os planos utilizam o Azure Active Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso do Cofre de chaves.

O modelo de um mecanismo único para a autenticação para ambos os planos tem várias vantagens:

- As organizações podem controlar o acesso centralmente a todos os cofres de chaves na sua organização.
- Se um utilizador sai, perde imediatamente o acesso a todos os cofres da organização.
- As organizações podem personalizar a autenticação utilizando as opções no Azure AD, tal como para ativar a autenticação multifator para aumentar a segurança

### <a name="managing-administrative-access-to-key-vault"></a>Gerir o acesso administrativo para o Key Vault

Quando cria um cofre de chaves num grupo de recursos, gerir o acesso ao utilizar o Azure AD. Conceder aos utilizadores ou grupos a capacidade de gerir cofres de chaves num grupo de recursos. Pode conceder acesso a um nível de âmbito específico através da atribuição de funções RBAC adequadas. Para conceder acesso a um utilizador para gerir cofres de chaves, atribui um predefinidos `key vault Contributor` função ao utilizador com um âmbito específico. Os seguintes níveis de âmbitos podem ser atribuídos a uma função RBAC:

- **Subscrição**: Uma função RBAC atribuída ao nível da subscrição se aplica a todos os grupos de recursos e recursos nessa subscrição.
- **Grupo de recursos**: Uma função RBAC atribuída ao nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: Uma função RBAC atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre de chaves específico.

Existem várias funções predefinidas. Se uma função predefinida não atender a suas necessidades, pode definir sua própria função. Para obter mais informações, consulte [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um plano de gestão do Cofre de chaves, o utilizador pode conceder próprios acesso para o plano de dados ao definir uma política de acesso do Cofre de chaves. Deve controlar rigorosamente quem tem `Contributor` acesso à função para seus cofres de chaves. Certifique-se de que só pessoas autorizadas podem aceder e gerir cofres de chaves, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlar o acesso a dados do Key Vault

Políticas de acesso do Cofre de chaves concedem permissões em separado para chaves, segredos ou certificados. Pode conceder um acesso de utilizadores apenas a chaves e não a segredos. Permissões de acesso para chaves, segredos e certificados são geridas ao nível do cofre.

> [!IMPORTANT]
> Políticas de acesso do Cofre de chaves não suportam permissões granulares, ao nível do objeto, como uma chave específica, o segredo ou o certificado. Quando um utilizador tem permissão para criar e eliminar chaves, eles podem executar essas operações em todas as chaves no Cofre de chaves.

Para definir políticas de acesso para um cofre de chaves, utilize o [portal do Azure](https://portal.azure.com/), o [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), ou o [APIs de REST de gestão do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Pode restringir o acesso do plano de dados usando [pontos finais de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Pode configurar [firewalls e regras de rede virtual](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Pode reduzir a exposição dos seus cofres ao especificar que endereços IP tem acesso aos mesmos. Os pontos de extremidade de serviço de rede virtual para o Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos de extremidade também permitem-lhe restringir o acesso a uma lista de intervalos de endereços IPv4 (protocolo IP versão 4). Acesso é negado por qualquer utilizador ligar ao seu Cofre de chaves de fora essas origens.

Depois de firewall regras estão em vigor, os utilizadores apenas podem ler dados do Key Vault quando os pedidos provêm de redes virtuais permitidas ou intervalos de endereços IPv4. Isso também se aplica a aceder ao Cofre de chaves do portal do Azure. Embora os usuários podem navegar para um cofre de chaves do portal do Azure, eles poderão não conseguir listar chaves, segredos ou certificados se os respetivos computadores cliente não está na lista de permitidos. Isso também afeta o Seletor de Cofre de chave por outros serviços do Azure. Os utilizadores poderão ver a lista de cofres de chaves, mas não listar as chaves, se as regras de firewall impedem os respetivos computadores cliente.

Para obter mais informações sobre o Azure Key Vault a revisão de endereço de rede [pontos finais de serviço de rede Virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorização

Registo do Cofre de chave guarda informações sobre as atividades executadas no seu cofre. Registos do Cofre de chaves:

- Autenticado todos os pedidos de REST API, incluindo pedidos falhados
  - Operações na chave de cofre em si. Estas operações incluem a criação, eliminação, políticas de acesso de definição e atualizar atributos do Cofre de chaves, como etiquetas.
  - Operações sobre chaves e segredos no Cofre de chaves, incluindo:
    - Criar, modificar ou eliminação dessas chaves ou segredos.
    - Assinatura, verificar, encriptar, desencriptar, encapsulamento e chaves de abertura, obter segredos, listagem de chaves e segredos (e suas versões).
- Pedidos não autenticados que resultam numa resposta 401. Os exemplos são os pedidos que não tenham um token de portador incorretamente formulados ou expirados, ou que tenham um token inválido.

Informações de registo podem ser acedidas em 10 minutos após a operação de Cofre de chaves. Cabe-lhe para que possa gerir os seus registos na sua conta de armazenamento. 

- Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
- Elimine os registos que já não pretende manter na sua conta de armazenamento.

De recomendação no gerenciamento seguro de armazenamento de contas de rever o [guia de segurança do armazenamento do Azure](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Próximos Passos

- [Pontos finais de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md)
- [Pontos finais de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
