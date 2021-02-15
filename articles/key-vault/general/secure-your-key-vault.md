---
title: Acesso seguro a um cofre de chaves
description: O modelo de acesso para Azure Key Vault, incluindo a autenticação do Ative Directory e os pontos finais de recursos.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sudbalas
ms.openlocfilehash: f82c7060f703aff6c19f0082454779b8fea1ba76
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526260"
---
# <a name="secure-access-to-a-key-vault"></a>Acesso seguro a um cofre de chaves

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. Como estes dados são sensíveis e críticos do negócio, é necessário garantir o acesso aos cofres das chaves, permitindo apenas aplicações e utilizadores autorizados. Este artigo fornece uma visão geral do modelo de acesso ao Cofre de Chaves. Explica a autenticação e a autorização e descreve como garantir o acesso aos cofres das chaves.

Para obter mais informações sobre o Cofre de Chaves, consulte [Sobre o Cofre da Chave Azure;](overview.md) para obter mais informações sobre o que pode ser armazenado num cofre de chaves, consulte [sobre chaves, segredos e certificados.](about-keys-secrets-certificates.md)

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre-chave é controlado através de duas interfaces: o **plano de gestão** e o **plano de dados.** O avião de gestão é onde geres o Key Vault. As operações neste avião incluem a criação e eliminação de cofres chave, a recuperação de propriedades do Cofre-Chave e a atualização das políticas de acesso. O plano de dados é onde trabalha com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

Ambos os aviões utilizam [o Azure Ative Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) para autenticação. Para autorização, o avião de gestão utiliza o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) e o plano de dados utiliza uma política de acesso ao [Cofre-Chave](./assign-access-policy-portal.md) e [a Azure RBAC para operações de plano de dados key Vault](./rbac-guide.md).

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ter a autenticação e a autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais as operações que o chamador pode executar. A autenticação com a Key Vault funciona em conjunto com [o Azure Ative Directory (Azure AD),](../../active-directory/fundamentals/active-directory-whatis.md)que é responsável pela autenticação da identidade de qualquer **dado diretor de segurança.**

Um principal de segurança é um objeto que representa um utilizador, grupo, serviço ou aplicação que está solicitando acesso aos recursos Azure. O Azure atribui uma **identificação** única a todos os diretores de segurança.

* Um diretor de segurança do **utilizador** identifica um indivíduo que tem um perfil no Diretório Ativo Azure.

* Um diretor de segurança do **grupo** identifica um conjunto de utilizadores criados no Azure Ative Directory. Quaisquer funções ou permissões atribuídas ao grupo são concedidas a todos os utilizadores dentro do grupo.

* Um diretor de **serviço** é um tipo de princípio de segurança que identifica uma aplicação ou serviço, ou seja, um pedaço de código em vez de um utilizador ou grupo. O ID do objeto do principiante de serviço é conhecido como **iD do** seu cliente e age como o seu nome de utilizador. O segredo ou **certificado** do cliente do **cliente** do serviço funciona como a sua senha. A Azure Services apoia a atribuição [de Identidade Gerida](../../active-directory/managed-identities-azure-resources/overview.md) com gestão automatizada de identificação e **certificado** do **cliente.** A identidade gerida é a opção mais segura e recomendada para autenticação dentro do Azure.

Para obter mais informações sobre a autenticação no Cofre de Chaves, consulte [Autenticar para Cofre de Chaves Azure](authentication.md)

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

## <a name="resource-endpoints"></a>Pontos finais de recursos

As aplicações acedem aos aviões através de pontos finais. Os controlos de acesso dos dois aviões funcionam de forma independente. Para conceder a uma aplicação acesso a chaves de utilização num cofre de chaves, concede acesso a um plano de dados utilizando uma política de acesso ao Cofre-Chave ou a Azure RBAC (pré-visualização). Para conceder a um utilizador o acesso às propriedades e etiquetas do Key Vault, mas não ter acesso a dados (chaves, segredos ou certificados), concede acesso de avião de gestão com o Azure RBAC.

A tabela seguinte mostra os pontos finais para os aviões de gestão e dados.

| Avião de acesso &nbsp; | Pontos finais de acesso | Operações | Mecanismo &nbsp; de controlo de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Governo Azure US:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e apagar cofres-chave<br><br>Definir políticas de acesso ao cofre de chaves<br><br>Definir tags de cofre de chaves | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Governo Azure US:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: encriptar, desencriptar, embrulharKey, desembrulharKey, assinar, verificar, obter, listar, criar, atualizar, importar, excluir, recuperar, backup, restaurar, purgar<br><br> Certificados: gerenciamentos, getissuers, listissuers, setissuers, deleteissuers, manageissuers, get, list, create, import, update, delete, recovery, backup, restore, purpur<br><br>  Segredos: obter, listar, definir, excluir,recuperar, apoiar, restaurar, purgar | Política de acesso ao Cofre de Chaves ou RBAC Azure (pré-visualização)|

## <a name="management-plane-and-azure-rbac"></a>Avião de gestão e Azure RBAC

No plano de gestão, você usa o [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md) para autorizar as operações que um chamador pode executar. No modelo Azure RBAC, cada subscrição do Azure tem um exemplo de Azure AD. Você concede acesso a utilizadores, grupos e aplicações deste diretório. O acesso é concedido para gerir recursos na subscrição Azure que utilizam o modelo de implementação do Gestor de Recursos Azure.

Cria-se um cofre chave num grupo de recursos e gere o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Você concede o acesso a um nível de âmbito específico, atribuindo as funções apropriadas de Azure. Para conceder acesso a um utilizador para gerir cofres-chave, atribui um papel predefinido [do Key Vault Contributor](../../role-based-access-control/built-in-roles.md#key-vault-contributor) ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para obter mais informações, veja [Funções incorporadas do Azure](../../role-based-access-control/built-in-roles.md). 

Precisa de ter `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, como [Administrador de Acesso ao Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) ou [Proprietário](../../role-based-access-control/built-in-roles.md#owner)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar bem quem tem `Contributor` acesso aos seus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Plano de dados e políticas de acesso

Pode conceder acesso a um plano de dados definindo as políticas de acesso do Key Vault para um cofre de chaves. Para definir estas políticas de acesso, um utilizador, grupo ou aplicação deve ter `Key Vault Contributor` permissões para o plano de gestão para o cofre de chaves.

Você concede a um utilizador, grupo ou acesso de aplicação para executar operações específicas para chaves ou segredos em um cofre chave. O Key Vault suporta até 1.024 entradas na política de acesso para um cofre de chaves. Para conceder acesso de avião de dados a vários utilizadores, crie um grupo de segurança Azure AD e adicione utilizadores a esse grupo.

Pode ver a lista completa de operações secretas e cofres aqui: [Key Vault Operation Reference](/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a> As políticas de acesso ao Cofre chave concedem permissões separadamente a chaves, segredos e certificados.  As permissões de acesso para chaves, segredos e certificados estão ao nível do cofre. 

Para obter mais informações sobre a utilização de políticas de acesso ao cofre chave, consulte [atribuir uma política de acesso ao Cofre de Chaves](assign-access-policy-portal.md)

> [!IMPORTANT]
> As principais políticas de acesso ao cofre aplicam-se ao nível do cofre. Quando um utilizador tem permissão para criar e eliminar chaves, pode efetuar essas operações em todas as teclas do cofre.
As políticas de acesso ao Cofre não suportam permissões granulares ao nível de objetos como uma chave específica, segredo ou certificado. 
>

## <a name="data-plane-and-azure-rbac-preview"></a>Plano de dados e Azure RBAC (pré-visualização)

O controlo de acesso baseado em funções Azure é um modelo de permissão alternativo para controlar o acesso ao plano de dados Azure Key Vault, que pode ser ativado em cofres-chave individuais. O modelo de permissão Azure RBAC é exclusivo e uma vez definido, as políticas de acesso ao cofre tornaram-se inativas. O Azure Key Vault define um conjunto de funções incorporadas do Azure que englobam conjuntos comuns de permissões usadas para aceder a chaves, segredos ou certificados.

Quando um papel de Azure é atribuído a um diretor de segurança da AZure, a Azure concede acesso a esses recursos para esse diretor de segurança. O acesso pode ser examinado ao nível da subscrição, do grupo de recursos, do cofre-chave, ou de uma chave individual, secreta ou certificado. Um diretor de segurança Azure AD pode ser um utilizador, um grupo, um diretor de serviço de aplicação ou uma [identidade gerida para os recursos da Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

Os principais benefícios da utilização da permissão do Azure RBAC sobre as políticas de acesso ao cofre são a gestão centralizada do controlo de acessos e a sua integração com [a Gestão de Identidade Privilegiada (PIM)](../../active-directory/privileged-identity-management/pim-configure.md). A Gestão de Identidade Privilegiada fornece ativação de funções baseadas no tempo e na aprovação para mitigar os riscos de permissões de acesso excessivas, desnecessárias ou mal utilizadas em recursos que lhe interessam.

Para obter mais informações sobre o plano de dados key Vault com a Azure RBAC, consulte [chaves, certificados e segredos do Key Vault com um controlo de acesso baseado em funções Azure](rbac-guide.md)

## <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais

Para uma camada adicional de segurança, pode configurar firewalls e regras de rede virtuais. Pode configurar firewalls key Vault e redes virtuais para negar o acesso ao tráfego de todas as redes (incluindo o tráfego de internet) por padrão. Pode conceder acesso ao tráfego a partir de redes virtuais específicas do [Azure](../../virtual-network/virtual-networks-overview.md) e de intervalos de endereços IP de internet pública, permitindo-lhe construir um limite de rede seguro para as suas aplicações.

Aqui estão alguns exemplos de como pode usar pontos finais de serviço:

* Está a usar o Key Vault para armazenar chaves de encriptação, segredos de aplicação e certificados, e pretende bloquear o acesso ao cofre da internet pública.
* Pretende bloquear o acesso ao cofre de chaves para que apenas a sua aplicação, ou uma pequena lista de anfitriões designados, possa ligar-se ao seu cofre de chaves.
* Tem uma aplicação em execução na sua rede virtual Azure, e esta rede virtual está bloqueada para todo o tráfego de entrada e saída. A sua aplicação ainda precisa de se ligar ao Key Vault para obter segredos ou certificados ou usar chaves criptográficas.

Para obter mais informações sobre firewall key Vault e redes virtuais, consulte [firewalls Configure Key Vault e redes virtuais](network-security.md)

> [!NOTE]
> As firewalls key Vault e as regras de rede virtuais aplicam-se apenas ao plano de dados do Key Vault. As operações do avião de controlo key Vault (tais como criar, excluir e modificar operações, definir políticas de acesso, definir firewalls e regras de rede virtuais) não são afetadas por firewalls e regras de rede virtuais.

## <a name="private-endpoint-connection"></a>Conexão de ponto final privado

Em caso de necessidade de bloquear completamente a exposição do Key Vault ao público, pode ser utilizado um [Azure Private Endpoint.](../../private-link/private-endpoint-overview.md) Um Azure Private Endpoint é uma interface de rede que o liga de forma privada e segura a um serviço alimentado pela Azure Private Link. O ponto final privado utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço para o seu VNet. Todo o tráfego para o serviço pode ser encaminhado através do ponto final privado, pelo que não são necessários gateways, dispositivos NAT, ligações ExpressRoute ou VPN, ou endereços IP públicos. O tráfego entre a rede virtual e o serviço percorre a rede de backbone da Microsoft, eliminando a exposição da Internet pública. Pode ligar-se a um recurso Azure, dando-lhe o mais alto nível de granularidade no controlo de acessos.

Cenários comuns para a utilização de Link Privado para serviços Azure:

- **Serviços de acesso privados na plataforma Azure**: Ligue a sua rede virtual a serviços em Azure sem endereço IP público na fonte ou destino. Os prestadores de serviços podem prestar os seus serviços na sua própria rede virtual e os consumidores podem aceder a esses serviços na sua rede virtual local. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure. 
 
- **No local e redes vigiadas**: Serviços de acesso em funcionamento em Azure a partir de instalações sobre o espreocupamento privado ExpressRoute, túneis VPN e redes virtuais espreitadas utilizando pontos finais privados. Não há necessidade de configurar o olhar público ou atravessar a internet para chegar ao serviço. O Private Link fornece uma forma segura de migrar cargas de trabalho para Azure.
 
- **Proteção contra fugas de dados**: Um ponto final privado é mapeado para uma instância de um recurso PaaS em vez de todo o serviço. Os consumidores só podem ligar-se ao recurso específico. O acesso a qualquer outro recurso no serviço está bloqueado. Este mecanismo fornece proteção contra riscos de fuga de dados. 
 
- **Alcance global**: Conecte-se privadamente aos serviços que correm noutras regiões. A rede virtual do consumidor pode estar na região A e pode ligar-se a serviços por trás do Private Link na região B.  
 
- **Estender aos seus próprios serviços**: Ative a mesma experiência e funcionalidade para prestar o seu serviço de forma privada aos consumidores em Azure. Ao colocar o seu serviço atrás de um Balanceador de Carga Azure padrão, pode ative-lo para Private Link. O consumidor pode então ligar-se diretamente ao seu serviço utilizando um ponto final privado na sua própria rede virtual. Pode gerir os pedidos de ligação utilizando um fluxo de chamada de aprovação. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Azure Ative Directory. 

Para obter mais informações sobre pontos finais privados, consulte [Key Vault com Azure Private Link](./private-link-service.md)

## <a name="example"></a>Exemplo

Neste exemplo, estamos a desenvolver uma aplicação que utiliza um certificado para TLS/SSL, Azure Storage para armazenar dados, e uma chave RSA de 2.048 bits para encriptar dados no Azure Storage. A nossa aplicação funciona numa máquina virtual Azure (VM) (ou num conjunto de escala de máquina virtual). Podemos usar um cofre para guardar os segredos da aplicação. Podemos armazenar o certificado de bootstrap que é usado pela aplicação para autenticar com Azure AD.

Precisamos de acesso às seguintes chaves e segredos armazenados:
- **Certificado TLS/SSL**: Utilizado para TLS/SSL.
- **Chave de armazenamento**: Utilizado para aceder à conta de Armazenamento.
- **Tecla RSA 2.048 bits**: Utilizada para embrulhar/desembrulhar a chave de encriptação de dados pela Azure Storage.
- **Identidade gerida de aplicação**: Usado para autenticar com Azure AD. Após o acesso ao Key Vault, a aplicação pode obter a chave de armazenamento e o certificado.

Precisamos definir as seguintes funções para especificar quem pode gerir, implementar e auditar a nossa aplicação:
- **Equipa de segurança**: Pessoal de TI do gabinete do CSO (Chief Security Officer) ou colaboradores semelhantes. A equipa de segurança é responsável pela proteção adequada dos segredos. Os segredos podem incluir certificados TLS/SSL, chaves RSA para encriptação, cadeias de conexão e chaves de conta de armazenamento.
- **Desenvolvedores e operadores**: O pessoal que desenvolve a aplicação e a implementa em Azure. Os membros desta equipa não fazem parte da equipa de segurança. Não devem ter acesso a dados sensíveis como certificados TLS/SSL e chaves RSA. Apenas a aplicação que implementam deve ter acesso a dados sensíveis.
- **Auditores**: Este papel é para os contribuintes que não são membros do pessoal de desenvolvimento ou de TI geral. Revejam o uso e manutenção de certificados, chaves e segredos para garantir o cumprimento das normas de segurança.

Há outro papel que está fora do âmbito da nossa aplicação: o administrador de subscrição (ou grupo de recursos). O administrador de subscrição estabelece permissões de acesso iniciais para a equipa de segurança. Concedem acesso à equipa de segurança utilizando um grupo de recursos que tem os recursos exigidos pela aplicação.

Precisamos de autorizar as seguintes operações para as nossas funções:

**Equipa de segurança**
- Crie cofres chave.
- Ligue o registo do Cofre de Chaves.
- Adicione chaves e segredos.
- Crie cópias de segurança das chaves para a recuperação de desastres.
- Desa estade as políticas de acesso do Cofre de Chaves e atribua funções para conceder permissões aos utilizadores e aplicações para operações específicas.
- Rolar as chaves e os segredos periodicamente.

**Programadores e operadores**
- Obtenha referências da equipa de segurança para os certificados de bootstrap e TLS/SSL (impressões digitais), chave de armazenamento (URI secreto) e chave RSA (chave URI) para embrulho/desembrulhar.
- Desenvolver e implementar a aplicação para aceder a certificados e segredos programáticamente.

**Auditores**
- Reveja os registos do Cofre-Chave para confirmar o uso adequado de chaves e segredos e o cumprimento das normas de segurança de dados.

A tabela seguinte resume as permissões de acesso para as nossas funções e aplicação.

| Função | Permissões do plano de gestão | Permissões de plano de dados - políticas de acesso a cofres | Permissões de plano de dados -Azure RBAC (pré-visualização)  |
| --- | --- | --- | --- |
| Equipa de segurança | [Contribuidor do Cofre Chave](../../role-based-access-control/built-in-roles.md#key-vault-contributor) | Certificados: todas as operações <br> Chaves: todas as operações <br> Segredos: todas as operações | [Administrador do cofre chave (pré-visualização)](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview) |
| Desenvolvedores e &nbsp; operadores | Permissão de implantação do Cofre de Chaves<br><br> **Nota:** Esta permissão permite que os VM implantados tragam segredos de um cofre de chaves. | Nenhum | Nenhum |
| Auditores | Nenhum | Certificados: lista <br> Chaves: listar<br>Segredos: listar<br><br> **Nota:** Esta permissão permite que os auditores inspecionem atributos (tags, datas de ativação, datas de validade) para obter chaves e segredos não emitidos nos registos. | [Key Vault Reader (pré-visualização)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview) |
| Conta de Armazenamento do Azure | Nenhum | Chaves: obter, listar, embrulharKey, desembrulhar aKey <br> | [Utilizador de encriptação de serviço crypto de cofre chave](../../role-based-access-control/built-in-roles.md#key-vault-crypto-service-encryption-user-preview) |
| Aplicação | Nenhum | Segredos: obter, lista <br> Certificados: obter, lista | [Key Vault Reader (pré-visualização)](../../role-based-access-control/built-in-roles.md#key-vault-reader-preview), [Key Vault Secret User (pré-visualização)](../../role-based-access-control/built-in-roles.md#key-vault-secrets-user-preview) |

As três funções da equipa precisam de acesso a outros recursos, juntamente com permissões do Key Vault. Para implementar VMs (ou a funcionalidade de Aplicações Web do Azure App Service), os desenvolvedores e operadores precisam de implementar o acesso. Os auditores precisam de ler o acesso à conta de armazenamento onde os registos do Cofre de Chaves estão armazenados.

O nosso exemplo descreve um cenário simples. Cenários da vida real podem ser mais complexos. Pode ajustar permissões ao seu cofre com base nas suas necessidades. Assumimos que a equipa de segurança fornece as referências chave e secretas (URIs e impressões digitais), que são usadas pelo pessoal da DevOps nas suas aplicações. Os desenvolvedores e operadores não requerem acesso a um avião de dados. Concentrámo-nos em como proteger o cofre das chaves.

> [!NOTE]
> Este exemplo mostra como o acesso ao Cofre chave é bloqueado na produção. Os desenvolvedores devem ter a sua própria subscrição ou grupo de recursos com permissões completas para gerir os seus cofres, VMs e a conta de armazenamento onde desenvolvem a aplicação.

## <a name="resources"></a>Recursos

- [Acerca do Azure Key Vault](overview.md)
- [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
- [RBAC do Azure](../../role-based-access-control/overview.md)
- [Ligação Privada](../../private-link/private-link-overview.md)

## <a name="next-steps"></a>Passos seguintes

[Autenticar para o Azure Key Vault](authentication.md)

[Atribuir uma política de acesso ao Cofre de Chaves](assign-access-policy-portal.md)

[Atribuir papel de Azure ao acesso a chaves, segredos e certificados](rbac-guide.md)

[Configurar firewalls e redes virtuais do Key Vault](network-security.md)

[Estabelecer uma ligação privada com o Cofre de Chaves](private-link-service.md)