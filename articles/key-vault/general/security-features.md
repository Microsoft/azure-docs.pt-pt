---
title: Visão geral da segurança do Cofre de Chaves Azure
description: Uma visão geral das funcionalidades de segurança e das melhores práticas para o Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: fe88933049ad39de57f879789e8c1b86ed7a54f5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819451"
---
# <a name="azure-key-vault-security"></a>Segurança do Azure Key Vault

O Azure Key Vault protege chaves e segredos de encriptação (tais como certificados, cadeias de ligação e palavras-passe) na nuvem. No entanto, ao armazenar dados críticos sensíveis e empresariais, deve tomar medidas para maximizar a segurança dos seus cofres e os dados armazenados nos mesmos.

Este artigo fornece uma visão geral das funcionalidades de segurança e das melhores práticas para o Azure Key Vault.

> [!NOTE]
> Para obter uma lista completa das recomendações de segurança do Azure Key Vault consulte a [linha de base de segurança para o Cofre da Chave Azure](security-baseline.md).

## <a name="network-security"></a>Segurança da rede

Pode reduzir a exposição dos seus cofres especificando quais endereços IP têm acesso aos mesmos. Os pontos finais de serviço de rede virtual para Azure Key Vault permitem-lhe restringir o acesso a uma rede virtual especificada. Os pontos finais também permitem restringir o acesso a uma lista de intervalos de endereços IPv4 (versão 4 do protocolo de internet). Qualquer utilizador que se conecte ao cofre de fora dessas fontes é negado acesso.  Para mais detalhes, consulte [os pontos finais do serviço de rede virtual para Azure Key Vault](overview-vnet-service-endpoints.md)

Após as regras de firewall estarem em vigor, os utilizadores só podem ler dados do Key Vault quando os seus pedidos são originários de redes virtuais permitidas ou de intervalos de endereços IPv4. Isto também se aplica ao acesso ao Cofre de Chaves a partir do portal Azure. Embora os utilizadores possam navegar para um cofre chave a partir do portal Azure, podem não ser capazes de listar chaves, segredos ou certificados se a sua máquina cliente não estiver na lista permitida. Para etapas de implementação, consulte [firewalls Configure Key Vault e redes virtuais](network-security.md)

O Azure Private Link Service permite-lhe aceder ao Azure Key Vault e aos serviços de cliente/parceiro hospedados no Azure durante um Private Endpoint na sua rede virtual. Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.  Para etapas de implementação, consulte [Integrar o Cofre de Chaves com Ligação Privada Azure](private-link-service.md)

## <a name="tls-and-https"></a>TLS e HTTPS

- A extremidade frontal do Key Vault (plano de dados) é um servidor multi-inquilino. Isto significa que os cofres-chave de diferentes clientes podem partilhar o mesmo endereço IP público. Para conseguir o isolamento, cada pedido HTTP é autenticado e autorizado independentemente de outros pedidos.
- Pode identificar versões mais antigas do TLS para reportar vulnerabilidades, mas como o endereço IP público é partilhado, não é possível que a equipa de serviço de cofre-chave desative versões antigas do TLS para cofres-chave individuais a nível de transporte.
- O protocolo HTTPS permite ao cliente participar na negociação da TLS. **Os clientes podem impor a versão mais recente do TLS,** e sempre que um cliente o faça, toda a ligação utilizará a proteção de nível correspondente. O facto de o Key Vault ainda suportar versões TLS mais antigas não prejudicará a segurança das ligações utilizando versões TLS mais recentes.
- Apesar das vulnerabilidades conhecidas no protocolo TLS, não existe um ataque conhecido que permita a um agente malicioso extrair qualquer informação do seu cofre-chave quando o intruso iniciar uma ligação com uma versão TLS que tenha vulnerabilidades. O intruso ainda precisaria de autenticar e autorizar-se, e enquanto os clientes legítimos se ligarem sempre às versões recentes do TLS, não é possível que as credenciais possam ter sido vazadas de vulnerabilidades em versões antigas do TLS.

## <a name="identity-management"></a>Gestão de identidades

Quando cria um cofre chave numa subscrição do Azure, está automaticamente associado ao inquilino AD AD da subscrição. Qualquer pessoa que tente gerir ou recuperar conteúdo de um cofre deve ser autenticada pela Azure AD. Em ambos os casos, as aplicações podem aceder ao Key Vault de três formas:

- **Apenas para aplicação**: A aplicação representa um principal serviço ou identidade gerida. Esta identidade é o cenário mais comum para aplicações que periodicamente precisam de aceder a certificados, chaves ou segredos do cofre chave. Para que este cenário funcione, `objectId` a aplicação deve ser especificada na política de acesso e `applicationId` _a_ não deve ser especificada ou deve ser `null` .
- **Apenas para o utilizador**: O utilizador acede ao cofre de chaves a partir de qualquer aplicação registada no arrendatário. Exemplos deste tipo de acesso incluem a Azure PowerShell e o portal Azure. Para que este cenário funcione, `objectId` o do utilizador deve ser especificado na política de acesso e o não deve ser especificado ou deve `applicationId` ser  `null` .
- **Aplicação-plus-user** (por vezes designada como _identidade composta):_ O utilizador é obrigado a aceder ao cofre de uma aplicação específica _e_ a aplicação deve utilizar o fluxo de autenticação em nome do utilizador (OBO) para personificar o utilizador. Para que este cenário funcione, ambos `applicationId` devem `objectId` ser especificados na política de acesso. Identifica `applicationId` a aplicação necessária e `objectId` identifica o utilizador. Atualmente, esta opção não está disponível para o plano de dados Azure RBAC.

Em todos os tipos de acesso, a aplicação autentica-se com Azure AD. A aplicação utiliza qualquer [método de autenticação suportado](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicação. A aplicação adquire um símbolo para um recurso no avião para conceder acesso. O recurso é um ponto final na gestão ou plano de dados, baseado no ambiente Azure. A aplicação utiliza o token e envia um pedido de API REST para o Key Vault. Para saber mais, reveja todo o [fluxo de autenticação.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Para mais detalhes, consulte [os fundamentos de autenticação do cofre chave](authentication-fundamentals.md)

## <a name="key-vault-authentication-options"></a>Opções de autenticação do Cofre-Chave

Quando cria um cofre chave numa subscrição do Azure, está automaticamente associado ao inquilino AD AD da subscrição. Todos os chamadores em ambos os aviões devem registar-se neste inquilino e autenticar para aceder ao cofre da chave. Em ambos os casos, as aplicações podem aceder ao Key Vault de três formas:

- **Apenas para aplicação**: A aplicação representa um principal serviço ou identidade gerida. Esta identidade é o cenário mais comum para aplicações que periodicamente precisam de aceder a certificados, chaves ou segredos do cofre chave. Para que este cenário funcione, `objectId` a aplicação deve ser especificada na política de acesso e `applicationId` _a_ não deve ser especificada ou deve ser `null` .
- **Apenas para o utilizador**: O utilizador acede ao cofre de chaves a partir de qualquer aplicação registada no arrendatário. Exemplos deste tipo de acesso incluem a Azure PowerShell e o portal Azure. Para que este cenário funcione, `objectId` o do utilizador deve ser especificado na política de acesso e o não deve ser especificado ou deve `applicationId` ser  `null` .
- **Aplicação-plus-user** (por vezes designada como _identidade composta):_ O utilizador é obrigado a aceder ao cofre de uma aplicação específica _e_ a aplicação deve utilizar o fluxo de autenticação em nome do utilizador (OBO) para personificar o utilizador. Para que este cenário funcione, ambos `applicationId` devem `objectId` ser especificados na política de acesso. Identifica `applicationId` a aplicação necessária e `objectId` identifica o utilizador. Atualmente, esta opção não está disponível para o plano de dados Azure RBAC (pré-visualização).

Em todos os tipos de acesso, a aplicação autentica-se com Azure AD. A aplicação utiliza qualquer [método de autenticação suportado](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicação. A aplicação adquire um símbolo para um recurso no avião para conceder acesso. O recurso é um ponto final na gestão ou plano de dados, baseado no ambiente Azure. A aplicação utiliza o token e envia um pedido de API REST para o Key Vault. Para saber mais, reveja todo o [fluxo de autenticação.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

O modelo de um único mecanismo de autenticação para ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perdem instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em Azure AD, tais como para permitir a autenticação de vários fatores para uma segurança adicional.

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre-chave é controlado através de duas interfaces: o **plano de gestão** e o **plano de dados.** O avião de gestão é onde geres o Key Vault. As operações neste avião incluem a criação e eliminação de cofres chave, a recuperação de propriedades do Cofre-Chave e a atualização das políticas de acesso. O plano de dados é onde trabalha com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

Ambos os aviões utilizam [o Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) para autenticação. Para autorização, o avião de gestão utiliza o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) e o plano de dados utiliza uma política de acesso ao [Cofre-Chave](./assign-access-policy-portal.md) e [a Azure RBAC para operações de plano de dados key Vault](./rbac-guide.md).

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ter a autenticação e a autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais as operações que o chamador pode executar. A autenticação com a Key Vault funciona em conjunto com [o Azure Ative Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)que é responsável pela autenticação da identidade de qualquer **dado diretor de segurança.**

Um principal de segurança é um objeto que representa um utilizador, grupo, serviço ou aplicação que está solicitando acesso aos recursos Azure. O Azure atribui uma **identificação** única a todos os diretores de segurança.

- Um diretor de segurança do **utilizador** identifica um indivíduo que tem um perfil no Diretório Ativo Azure.
- Um diretor de segurança do **grupo** identifica um conjunto de utilizadores criados no Azure Ative Directory. Quaisquer funções ou permissões atribuídas ao grupo são concedidas a todos os utilizadores dentro do grupo.
- Um diretor de **serviço** é um tipo de princípio de segurança que identifica uma aplicação ou serviço, ou seja, um pedaço de código em vez de um utilizador ou grupo. O ID do objeto do principiante de serviço é conhecido como **iD do** seu cliente e age como o seu nome de utilizador. O segredo ou **certificado** do cliente do **cliente** do serviço funciona como a sua senha. A Azure Services apoia a atribuição [de Identidade Gerida](../../active-directory/managed-identities-azure-resources/overview.md) com gestão automatizada de identificação e **certificado** do **cliente.** A identidade gerida é a opção mais segura e recomendada para autenticação dentro do Azure.

Para obter mais informações sobre a autenticação no Cofre de Chaves, consulte [Autenticar para Cofre de Chaves Azure](authentication.md)

## <a name="privileged-access"></a>Acesso privilegiado

A autorização determina quais as operações que o chamador pode efetuar. A autorização em Key Vault utiliza uma combinação de [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) e Azure Key Vault.

O acesso aos cofres ocorre através de duas interfaces ou aviões. Estes aviões são o avião de gestão e o avião de dados.

- O *avião de gestão* é onde gere o Key Vault em si e é a interface usada para criar e apagar cofres. Também pode ler as principais propriedades do cofre e gerir as políticas de acesso.
- O *plano de dados* permite-lhe trabalhar com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

As aplicações acedem aos aviões através de pontos finais. Os controlos de acesso dos dois aviões funcionam de forma independente. Para conceder a uma aplicação acesso a chaves de utilização num cofre de chaves, você concede acesso a um plano de dados usando uma política de acesso ao Cofre chave ou Azure RBAC. Para conceder a um utilizador o acesso às propriedades e etiquetas do Key Vault, mas não ter acesso a dados (chaves, segredos ou certificados), concede acesso de avião de gestão com o Azure RBAC.

A tabela seguinte mostra os pontos finais para os aviões de gestão e dados.

| Avião de acesso &nbsp; | Pontos finais de acesso | Operações | Mecanismo &nbsp; de controlo de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Governo Azure US:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e apagar cofres-chave<br><br>Definir políticas de acesso ao cofre de chaves<br><br>Definir tags de cofre de chaves | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Governo Azure US:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: encriptar, desencriptar, embrulharKey, desembrulharKey, assinar, verificar, obter, listar, criar, atualizar, importar, excluir, recuperar, backup, restaurar, purgar<br><br> Certificados: gerenciamentos, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recovery, backup, restore, purpur<br><br>  Segredos: obter, listar, definir, excluir,recuperar, apoiar, restaurar, purgar | Política de acesso ao Cofre chave ou RBAC Azure |

### <a name="managing-administrative-access-to-key-vault"></a>Gerir o acesso administrativo ao Cofre-Chave

Quando cria um cofre chave num grupo de recursos, gere o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Pode conceder acesso a um nível de âmbito específico, atribuindo as funções Azure apropriadas. Para conceder acesso a um utilizador para gerir cofres-chave, atribui uma função predefinida `key vault Contributor` ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [Azure RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar bem quem tem `Contributor` acesso aos seus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.

### <a name="controlling-access-to-key-vault-data"></a>Controlo do acesso aos dados do Cofre-Chave

As políticas de acesso ao Cofre chave concedem permissões separadamente a chaves, segredos ou certificados. Pode conceder acesso a um utilizador apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados são geridas ao nível do cofre.

> [!IMPORTANT]
> As políticas de acesso ao Cofre não suportam permissões granulares ao nível de objetos como uma chave específica, segredo ou certificado. Quando um utilizador tem permissão para criar e eliminar chaves, pode efetuar essas operações em todas as teclas do cofre.

Pode definir políticas de acesso para um cofre de chaves utilizar o [portal Azure,](assign-access-policy-portal.md)o [Azure CLI,](assign-access-policy-cli.md) [a Azure PowerShell](assign-access-policy-powershell.md)ou as [APIs de Gestão de Cofres de Chave](/rest/api/keyvault/).

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

O registo do Key Vault guarda informações sobre as atividades realizadas no seu cofre. Para obter todos os detalhes, consulte [a sessão de registo do Key Vault](logging.md).

Pode integrar o Key Vault com a Grade de Eventos para ser notificado quando o estado de uma chave, certificado ou segredo armazenado no cofre da chave tiver mudado. Para mais detalhes, consulte [monitoring Key Vault com Azure Event Grid](event-grid-overview.md)

Também é importante monitorizar a saúde do seu cofre chave, para garantir que o seu serviço funciona como pretendido. Para aprender a fazê-lo, consulte [monitorar e alertar para o Cofre da Chave Azure](alert.md).

## <a name="backup-and-recovery"></a>Cópia de segurança e recuperação

A proteção para eliminar e limpar o cofre da Azure Key permite-lhe recuperar cofres apagados e objetos de abóbada. Para obter todos os detalhes, consulte [a visão geral do Azure Key Vault.](soft-delete-overview.md)

Também deve fazer back ups regulares do seu cofre em atualização/exclusão/criação de objetos dentro de um Cofre.  

## <a name="next-steps"></a>Passos Seguintes

- [Linha de segurança Azure Key Vault](security-baseline.md)
- [As melhores práticas do Azure Key Vault](security-baseline.md)
- [Pontos finais de serviço de rede virtual para o Azure Key Vault](overview-vnet-service-endpoints.md)
- [Azure RBAC: Papéis incorporados](../../role-based-access-control/built-in-roles.md)