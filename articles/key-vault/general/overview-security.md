---
title: Segurança do Azure Key Vault
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Cobre o modelo de autenticação e autorização do Key Vault e como proteger o cofre da chave.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c3dd4e5138741a3c035507358830f3572cf92751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739695"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

Você usa o Cofre de Chaves Azure para proteger chaves de encriptação e segredos como certificados, cadeias de conexão e senhas na nuvem. Ao armazenar dados críticos sensíveis e empresariais, é necessário tomar medidas para maximizar a segurança dos seus cofres e os dados armazenados nos mesmos.

## <a name="identity-and-access-management"></a>Gestão de identidades e acessos

Quando cria um cofre chave numa subscrição do Azure, está automaticamente associado ao inquilino AD AD da subscrição. Qualquer pessoa que tente gerir ou recuperar conteúdo de um cofre deve ser autenticada pela Azure AD.

- A autenticação estabelece a identidade do chamador.
- A autorização determina quais as operações que o chamador pode efetuar. A autorização em Key Vault utiliza uma combinação de políticas de [acesso baseadas em fun](../../role-based-access-control/overview.md) (RBAC) e Azure Key Vault.

### <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso aos cofres ocorre através de duas interfaces ou aviões. Estes aviões são o avião de gestão e o avião de dados.

- O *avião de gestão* é onde gere o Key Vault em si e é a interface usada para criar e apagar cofres. Também pode ler as principais propriedades do cofre e gerir as políticas de acesso.
- O *plano de dados* permite-lhe trabalhar com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ser autenticados e autorizados. Ambos os aviões utilizam o Azure Ative Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso ao Cofre-Chave.

O modelo de um único mecanismo de autenticação para ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perdem instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em Azure AD, tais como para permitir a autenticação de vários fatores para uma segurança adicional.

### <a name="managing-administrative-access-to-key-vault"></a>Gerir o acesso administrativo ao Cofre-Chave

Quando cria um cofre chave num grupo de recursos, gere o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Pode conceder acesso a um nível de âmbito específico, atribuindo as funções Azure apropriadas. Para conceder acesso a um utilizador para gerir cofres-chave, atribui uma função predefinida `key vault Contributor` ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [o RBAC: Funções incorporadas.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar bem quem tem `Contributor` acesso aos seus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Controlo do acesso aos dados do Cofre-Chave

As políticas de acesso ao Cofre chave concedem permissões separadamente a chaves, segredos ou certificados. Pode conceder acesso a um utilizador apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são geridas ao nível do cofre.

> [!IMPORTANT]
> As políticas de acesso ao Cofre não suportam permissões granulares ao nível de objetos como uma chave específica, segredo ou certificado. Quando um utilizador tem permissão para criar e eliminar chaves, pode efetuar essas operações em todas as teclas do cofre.

Pode definir políticas de acesso para um cofre de chaves utilizar o [portal Azure,](assign-access-policy-portal.md)o [Azure CLI,](assign-access-policy-cli.md) [a Azure PowerShell](assign-access-policy-powershell.md)ou as [APIs de Gestão de Cofres de Chave](/rest/api/keyvault/).

Pode restringir o acesso a um plano de dados utilizando [pontos finais de serviço de rede virtual para a Azure Key Vault](overview-vnet-service-endpoints.md)). Pode configurar [firewalls e regras de rede virtuais](network-security.md) para uma camada adicional de segurança.

## <a name="network-access"></a>Acesso à rede

Pode reduzir a exposição dos seus cofres especificando quais endereços IP têm acesso aos mesmos. Os pontos finais de serviço de rede virtual para Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que se conecte ao cofre de fora dessas fontes é negado acesso.

Após as regras de firewall estarem em vigor, os utilizadores só podem ler dados do Key Vault quando os seus pedidos são originários de redes virtuais permitidas ou de intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Isto também afeta o Key Vault Picker por outros serviços Azure. Os utilizadores podem ser capazes de ver a lista de cofres chave, mas não listar chaves, se as regras de firewall impedirem a sua máquina de clientes.

Para mais informações sobre o endereço da rede Azure Key Vault, reveja [os pontos finais do serviço de rede virtual para Azure Key Vault ( Azure Key Vault)](overview-vnet-service-endpoints.md)

## <a name="tls-and-https"></a>TLS e HTTPS

*   A extremidade frontal do Key Vault (plano de dados) é um servidor multi-inquilino. Isto significa que os cofres-chave de diferentes clientes podem partilhar o mesmo endereço IP público. Para conseguir o isolamento, cada pedido HTTP é autenticado e autorizado independentemente de outros pedidos.
*   Pode identificar versões mais antigas do TLS para reportar vulnerabilidades, mas como o endereço IP público é partilhado, não é possível que a equipa de serviço de cofre-chave desative versões antigas do TLS para cofres-chave individuais a nível de transporte.
*   O protocolo HTTPS permite ao cliente participar na negociação da TLS. **Os clientes podem impor a versão mais recente do TLS,** e sempre que um cliente o faça, toda a ligação utilizará a proteção de nível correspondente. O facto de o Key Vault ainda suportar versões TLS mais antigas não prejudicará a segurança das ligações utilizando versões TLS mais recentes.
*   Apesar das vulnerabilidades conhecidas no protocolo TLS, não existe um ataque conhecido que permita a um agente malicioso extrair qualquer informação do seu cofre-chave quando o intruso iniciar uma ligação com uma versão TLS que tenha vulnerabilidades. O intruso ainda precisaria de autenticar e autorizar-se, e enquanto os clientes legítimos se ligarem sempre às versões recentes do TLS, não é possível que as credenciais possam ter sido vazadas de vulnerabilidades em versões antigas do TLS.

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

O registo do Key Vault guarda informações sobre as atividades realizadas no seu cofre. Para obter todos os detalhes, consulte [a sessão de registo do Key Vault](logging.md).

Para recomendação sobre a gestão segura das contas de armazenamento, reveja o [guia de segurança do Azure Storage](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>Passos Seguintes

- [Pontos finais de serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md)
- [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md)