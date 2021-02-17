---
title: Segurança para soluções Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Compreenda as melhores práticas de segurança com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b589f98ee78d0709b2a74ba4e364cec0e486e968
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547167"
---
# <a name="secure-azure-digital-twins"></a>Secure Azure Digital Twins

Para a segurança, a Azure Digital Twins permite um controlo preciso de acesso sobre dados, recursos e ações específicas na sua implementação. Fá-lo através de uma estratégia de gestão de funções granulares e de permissão chamada **Azure role-based access control (Azure RBAC)**. Pode ler-se [aqui](../role-based-access-control/overview.md)sobre os princípios gerais do Azure RBAC.

A Azure Digital Twins também suporta a encriptação de dados em repouso.

## <a name="roles-and-permissions-with-azure-rbac"></a>Funções e permissões com a Azure RBAC

O Azure RBAC é fornecido à Azure Digital Twins através da integração com [o Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Pode utilizar o Azure RBAC para conceder permissões a um *diretor de segurança,* que pode ser um utilizador, um grupo ou um diretor de serviço de aplicações. O principal de segurança é autenticado pela Azure AD, e recebe um token OAuth 2.0 em troca. Este token pode ser usado para autorizar um pedido de acesso a uma instância Azure Digital Twins.

### <a name="authentication-and-authorization"></a>Autenticação e autorização

Com a Azure AD, o acesso é um processo em duas etapas. Quando um principal de segurança (um utilizador, grupo ou aplicação) tentar aceder a Azure Digital Twins, o pedido deve ser *autenticado* e *autorizado*. 

1. Primeiro, a identidade do diretor de segurança é *autenticada,* e um token OAuth 2.0 é devolvido.
2. Em seguida, o token é passado como parte de um pedido ao serviço Azure Digital Twins, para *autorizar* o acesso ao recurso especificado.

A etapa de autenticação requer qualquer pedido de pedido para conter um token de acesso OAuth 2.0 no tempo de execução. Se uma aplicação estiver a ser executada dentro de uma entidade Azure, como uma aplicação [Azure Functions,](../azure-functions/functions-overview.md) pode utilizar uma **identidade gerida** para aceder aos recursos. Leia mais sobre identidades geridas na secção seguinte.

O passo de autorização requer que um papel de Azure seja atribuído ao diretor de segurança. As funções atribuídas a um diretor de segurança determinam as permissões que o diretor terá. A Azure Digital Twins fornece funções Azure que englobam conjuntos de permissões para recursos da Azure Digital Twins. Estes papéis são descritos mais tarde neste artigo.

Para saber mais sobre papéis e atribuições de papéis suportados no Azure, consulte [*Compreender os diferentes papéis*](../role-based-access-control/rbac-and-directory-admin-roles.md) na documentação do Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Autenticação com identidades geridas

[As identidades geridas para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) são uma funcionalidade cross-Azure que lhe permite criar uma identidade segura associada à implementação onde o seu código de aplicação funciona. Em seguida, pode associar essa identidade a funções de controlo de acesso, para conceder permissões personalizadas para aceder a recursos específicos do Azure de que a sua aplicação necessita.

Com identidades geridas, a plataforma Azure gere esta identidade de tempo de execução. Não precisa de armazenar e proteger as chaves de acesso no seu código de aplicação ou configuração, quer para a própria identidade, quer para os recursos a que necessita. Uma aplicação de clientes Azure Digital Twins que funciona dentro de uma aplicação do Azure App Service não precisa de lidar com as regras e chaves da SAS, nem quaisquer outros tokens de acesso. A aplicação do cliente só precisa do endereço final do espaço de nomes Azure Digital Twins. Quando a aplicação se conecta, a Azure Digital Twins liga o contexto da entidade gerida ao cliente. Uma vez associado a uma identidade gerida, o seu cliente Azure Digital Twins pode fazer todas as operações autorizadas. A autorização será então concedida associando uma entidade gerida com um papel Azure Digital Twins Azure (descrito abaixo).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorização: Funções Azure para Azure Digital Twins

A Azure fornece **duas funções incorporadas para** autorizar o acesso ao plano de dados Azure Digital Twins [APIs](how-to-use-apis-sdks.md#overview-data-plane-apis). Pode consultar as funções pelo nome ou por ID:

| Papel incorporado | Description | ID | 
| --- | --- | --- |
| Proprietário de dados Azure Digital Twins | Dá acesso total sobre os recursos da Azure Digital Twins | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Leitor de dados de gémeos digitais Azure | Dá acesso apenas à leitura dos recursos da Azure Digital Twins | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Pode atribuir funções de duas formas:
* através do painel de controlo de acesso (IAM) para Azure Digital Twins no portal Azure (ver [*Adicionar ou remover atribuições de funções Azure utilizando o portal Azure)*](../role-based-access-control/role-assignments-portal.md)
* através de comandos CLI para adicionar ou remover uma função

Para passos mais detalhados sobre como fazê-lo, experimente-o no Tutorial de Gémeos Digitais Azure: [*Conecte uma solução de ponta a ponta*](tutorial-end-to-end.md).

Para obter mais informações sobre como as funções incorporadas são [*definidas,*](../role-based-access-control/role-definitions.md) consulte definições de funções na documentação do Azure RBAC. Para obter informações sobre a criação de funções personalizadas [*Azure, consulte os papéis personalizados da Azure*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatização de papéis

Quando se refere a papéis em cenários automatizados, recomenda-se referir-se a eles pelos seus IDs em vez dos seus **nomes.** Os nomes podem mudar entre lançamentos, mas os IDs não, tornando-os uma referência mais estável na automação.

> [!TIP]
> Se estiver a assigar papéis com um cmdlet, como `New-AzRoleAssignment` ([referência),](/powershell/module/az.resources/new-azroleassignment)pode usar o `-RoleDefinitionId` parâmetro em vez de passar um `-RoleDefinitionName` ID em vez de um nome para o papel.

### <a name="permission-scopes"></a>Âmbitos de permissão

Antes de atribuir um papel de Azure a um diretor de segurança, determine o âmbito de acesso que o diretor de segurança deve ter. As melhores práticas ditam que o melhor é conceder apenas o âmbito mais estreito possível.

A lista que se segue descreve os níveis em que pode aceder aos recursos da Azure Digital Twins.
* Modelos: As ações para este recurso ditam o controlo sobre [os modelos carregados](concepts-models.md) em Azure Digital Twins.
* Consulta Digital Twins Graph: As ações deste recurso determinam a capacidade de executar [operações de consulta](concepts-query-language.md) em gémeos digitais dentro do gráfico Azure Digital Twins.
* Digital Twin: As ações deste recurso fornecem controlo sobre as operações crud em [gémeos digitais](concepts-twins-graph.md) no gráfico duplo.
* Relação Digital Twin: As ações para este recurso definem o controlo sobre as operações crud nas [relações](concepts-twins-graph.md) entre gémeos digitais no gráfico gémeo.
* Rota do evento: As ações deste recurso determinam permissões para [encaminhar eventos](concepts-route-events.md) da Azure Digital Twins para um serviço de ponto final como [Event Hub,](../event-hubs/event-hubs-about.md) [Event Grid](../event-grid/overview.md)ou [Service Bus.](../service-bus-messaging/service-bus-messaging-overview.md)

### <a name="troubleshooting-permissions"></a>Permissões de resolução de problemas

Se um utilizador tentar executar uma ação não permitida pela sua função, poderá receber um erro da leitura do pedido de serviço `403 (Forbidden)` . Para obter mais informações e etapas de resolução de problemas, consulte [*Resolução de Problemas: Pedido de Gémeos Digitais Azure falhou com o Estado: 403 (Proibido)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Identidade gerida para aceder a outros recursos (pré-visualização)

A criação de uma **identidade gerida** pelo [Azure Ative (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) para uma instância Azure Digital Twins pode permitir que o caso aceda facilmente a outros recursos protegidos pela AZure, como [o Azure Key Vault](../key-vault/general/overview.md). A identidade é gerida pela plataforma Azure, e não requer que forneça ou rode quaisquer segredos. Para obter mais sobre identidades geridas em Azure AD, consulte  [*identidades geridas para recursos Azure*](../active-directory/managed-identities-azure-resources/overview.md). 

O Azure suporta dois tipos de identidades geridas: atribuídas pelo sistema e atribuídas pelo utilizador. Atualmente, a Azure Digital Twins suporta apenas **identidades atribuídas ao sistema.** 

Pode utilizar uma identidade gerida atribuída ao sistema para a sua Instância Digital Azure para autenticar num [ponto final definido sob medida.](concepts-route-events.md#create-an-endpoint) A Azure Digital Twins suporta a autenticação baseada no sistema para pontos finais para destinos [de Event Hub](../event-hubs/event-hubs-about.md) e Service [Bus](../service-bus-messaging/service-bus-messaging-overview.md)e para um ponto final   do [Azure Storage Container](../storage/blobs/storage-blobs-introduction.md)para   [eventos com letras mortas.](concepts-route-events.md#dead-letter-events) [Grelha de eventos](../event-grid/overview.md)   os pontos finais não são atualmente suportados para identidades geridas.

Para obter instruções sobre como ativar uma identidade gerida pelo sistema para as Gémeas Digitais Azure e usá-la para encaminhar eventos, consulte [*Como-a: Habilitar uma identidade gerida para eventos de encaminhamento (pré-visualização)*](how-to-enable-managed-identities.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Acesso à rede privada com Link Privado Azure (pré-visualização)

[O Azure Private Link](../private-link/private-link-overview.md) é um serviço que lhe permite aceder aos recursos da Azure (como [a Azure Event Hubs,](../event-hubs/event-hubs-about.md) [Azure Storage](../storage/common/storage-introduction.md)e [Azure Cosmos DB](../cosmos-db/introduction.md)) e serviços de clientes e parceiros hospedados pela Azure sobre um ponto final privado na sua [Rede Virtual Azure (VNet).](../virtual-network/virtual-networks-overview.md) 

Da mesma forma, pode utilizar pontos finais privados para a sua instância Azure Digital Twin para permitir que os clientes localizados na sua rede virtual acedam de forma segura à ocorrência sobre o Private Link. 

O ponto final privado utiliza um endereço IP a partir do seu espaço de endereço Azure VNet. O tráfego de rede entre um cliente na sua rede privada e a instância Azure Digital Twins atravessa o VNet e uma Ligação Privada na rede de espinha dorsal da Microsoft, eliminando a exposição à internet pública. Aqui está uma representação visual deste sistema:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Um diagrama que mostra uma rede para uma empresa PowerGrid que é um VNET protegido sem acesso à internet/nuvem pública, conectando-se através de Private Link a uma instância Azure Digital Twins chamada CityOfTwins.":::

Configurar um ponto final privado para a sua instância Azure Digital Twins permite-lhe proteger a sua instância Azure Digital Twins e eliminar a exposição pública, bem como evitar a exfiltração de dados a partir do seu VNet.

Para obter instruções sobre como configurar o Link Privado para Gémeos Digitais Azure, consulte [*Como-a-fazer: Ative o acesso privado com o Link Privado (pré-visualização)*](how-to-enable-private-link.md).

### <a name="design-considerations"></a>Considerações de conceção 

Ao trabalhar com o Private Link for Azure Digital Twins, aqui estão alguns fatores que você deve considerar:
* **Preços**: Para obter detalhes sobre os preços, consulte  [os preços do Azure Private Link](https://azure.microsoft.com/pricing/details/private-link). 
* **Disponibilidade regional**: Para a Azure Digital Twins, esta funcionalidade está disponível em todas as regiões do Azure Digital Twins. 
* **Número máximo de pontos finais privados por exemplo das Gémeas Digitais Azure**: 10

Para obter informações sobre os limites do Link Privado, consulte a documentação do [Link Privado Azure: Limitações](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de **serviço** representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre etiquetas de serviço, consulte  [*tags de rede Virtual*](../virtual-network/service-tags-overview.md). 

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules)de rede   ou [Azure Firewall,](../firewall/service-tags.md)utilizando tags de serviço em vez de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (neste caso, **AzureDigitalTwins)** no campo de *origem* ou destino adequado   de uma **   regra, pode permitir ou negar o tráfego para o serviço correspondente. 

Abaixo estão os detalhes da etiqueta de serviço **AzureDigitalTwins.**

| Etiqueta | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Nota: Esta etiqueta ou os endereços IP abrangidos por esta etiqueta podem ser utilizados para restringir o acesso aos pontos finais configurados para [as rotas do evento](concepts-route-events.md). | Entrada | Não | Sim |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Utilização de etiquetas de serviço para aceder aos pontos finais da rota do evento 

Aqui estão os passos para aceder aos pontos finais [da rota](concepts-route-events.md) do evento usando tags de serviço com Azure Digital Twins.

1. Em primeiro lugar, descarregue esta referência de ficheiro JSON mostrando gamas IP Azure e tags de serviço: [*Gamas IP Azure e Tags de Serviço*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Procure gamas IP "AzureDigitalTwins" no ficheiro JSON.  

3. Consulte a documentação do recurso externo ligado ao ponto final (por exemplo, a Grade de [Eventos,](../event-grid/overview.md)o Centro de [Eventos,](../event-hubs/event-hubs-about.md)o [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)ou [o Azure Storage](../storage/blobs/storage-blobs-overview.md) para [eventos com letras mortas](concepts-route-events.md#dead-letter-events)) para ver como definir filtros IP para esse recurso.

4. Desaprote os filtros IP nos recursos externos utilizando as gamas IP do *Passo 2*.  

5. Atualize os intervalos de IP periodicamente conforme necessário. As gamas podem mudar ao longo do tempo, por isso é uma boa ideia verificá-las regularmente e refrescá-las quando necessário. A frequência destas atualizações pode variar, mas é uma boa ideia verificá-las uma vez por semana.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

A Azure Digital Twins fornece encriptação de dados em repouso e em trânsito, tal como está escrito nos nossos centros de dados, e desencripta-os para si à medida que os acede. Esta encriptação ocorre utilizando uma chave de encriptação gerida pela Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de Recursos Transversais à Origem (CORS)

A Azure Digital Twins não suporta atualmente **a partilha de recursos cross-origin (CORS)**. Como resultado, se estiver a chamar uma API REST a partir de uma aplicação de navegador, uma interface [de Gestão API (APIM)](../api-management/api-management-key-concepts.md) ou um conector [Power Apps,](/powerapps/powerapps-overview) poderá ver um erro de política.

Para resolver este erro, pode fazer uma das seguintes ações:
* Retire o cabeçalho CORS `Access-Control-Allow-Origin` da mensagem. Este cabeçalho indica se a resposta pode ser partilhada. 
* Em alternativa, crie um proxy CORS e faça o pedido de API para as Gémeas Digitais Azure. 

## <a name="next-steps"></a>Passos seguintes

* Consulte estes conceitos em ação em [*Como-a: Configurar um caso e autenticação.*](how-to-set-up-instance-portal.md)

* Veja como interagir com estes conceitos a partir do código de aplicação do cliente em [*Como-a-: Escrever código de autenticação de aplicações*](how-to-authenticate-client.md).

* Leia mais sobre [a Azure RBAC.](../role-based-access-control/overview.md)