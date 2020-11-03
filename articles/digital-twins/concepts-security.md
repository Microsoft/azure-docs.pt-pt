---
title: Segurança para soluções Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Compreenda as melhores práticas de segurança com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 349f57299387b616373bb5fb4d295da8df8ee493
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279889"
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

>[!NOTE]
> Estes papéis foram recentemente renomeados a partir dos seus nomes anteriores na pré-visualização:
> * *Azure Digital Twins Data Owner* foi anteriormente *Azure Digital Twins Owner (Preview)*.
> * *Azure Digital Twins Data Reader* foi anteriormente *Azure Digital Twins Reader (Preview)*.

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

## <a name="service-tags"></a>Etiquetas de serviço

Uma etiqueta de **serviço** representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede. Para obter mais informações sobre etiquetas de serviço, consulte  [*tags de rede Virtual*](../virtual-network/service-tags-overview.md). 

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](../virtual-network/network-security-groups-overview.md#security-rules)de rede   ou [Azure Firewall,](../firewall/service-tags.md)utilizando tags de serviço em vez de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço (neste caso, **AzureDigitalTwins)** no campo de *origem* ou destino adequado   de uma *destination*   regra, pode permitir ou negar o tráfego para o serviço correspondente. 

Abaixo estão os detalhes da etiqueta de serviço **AzureDigitalTwins.**

| Etiqueta | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>Nota: Esta etiqueta ou os endereços IP abrangidos por esta etiqueta podem ser utilizados para restringir o acesso aos pontos finais configurados para [as rotas do evento](concepts-route-events.md). | Entrada | No | Yes |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Utilização de etiquetas de serviço para aceder aos pontos finais da rota do evento 

Aqui estão os passos para aceder aos pontos finais [da rota](concepts-route-events.md) do evento usando tags de serviço com Azure Digital Twins.

1. Em primeiro lugar, descarregue esta referência de ficheiro JSON mostrando gamas IP Azure e tags de serviço: [*Gamas IP Azure e Tags de Serviço*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Procure gamas IP "AzureDigitalTwins" no ficheiro JSON.  

3. Consulte a documentação do recurso externo ligado ao ponto final (por exemplo, a Grade de [Eventos,](../event-grid/overview.md)o Centro de [Eventos,](../event-hubs/event-hubs-about.md)o [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)ou [o Azure Storage](../storage/blobs/storage-blobs-overview.md) para [eventos com letras mortas](concepts-route-events.md#dead-letter-events)) para ver como definir filtros IP para esse recurso.

4. Desaprote os filtros IP nos recursos externos utilizando as gamas IP do *Passo 2*.  

5. Atualize os intervalos de IP periodicamente conforme necessário. As gamas podem mudar ao longo do tempo, por isso é uma boa ideia verificar estas regularmente e refrescá-las quando necessário. A frequência destas atualizações pode variar, mas é uma boa ideia verificá-las uma vez por semana.

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso

A Azure Digital Twins fornece encriptação de dados em repouso e em trânsito, tal como está escrito nos nossos centros de dados, e desencripta-os para si à medida que os acede. Esta encriptação ocorre utilizando uma chave de encriptação gerida pela Microsoft.

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de Recursos Transversais à Origem (CORS)

A Azure Digital Twins não suporta atualmente **a partilha de recursos cross-origin (CORS)**. Como resultado, se estiver a chamar uma API REST a partir de uma aplicação de navegador, uma interface [de Gestão API (APIM)](../api-management/api-management-key-concepts.md) ou um conector [Power Apps,](https://docs.microsoft.com/powerapps/powerapps-overview) poderá ver um erro de política.

Para resolver este erro, pode fazer um dos seguintes:
* Retire o cabeçalho CORS `Access-Control-Allow-Origin` da mensagem. Este cabeçalho indica se a resposta pode ser partilhada. 
* Em alternativa, crie um proxy CORS e faça o pedido de API para as Gémeas Digitais Azure. 

## <a name="next-steps"></a>Passos seguintes

* Consulte estes conceitos em ação em [*Como-a: Configurar um caso e autenticação.*](how-to-set-up-instance-portal.md)

* Veja como interagir com estes conceitos a partir do código de aplicação do cliente em [*Como-a-: Escrever código de autenticação de aplicações*](how-to-authenticate-client.md).

* Leia mais sobre [a Azure RBAC.](../role-based-access-control/overview.md)
