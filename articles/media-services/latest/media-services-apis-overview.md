---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Campos obrigatórios. Veja mais na aka.ms/skyeye/meta.
título: Desenvolver com v3 APIs : Descrição dos Serviços Azure Media: Conheça as regras aplicáveis às entidades e APIs ao desenvolver com os Media Services v3. serviços: media-services documentationcenter: '' autor: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: conceptual ms.date: 10/23/2020 ms.author: inhenkel ms.custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Programar com as APIs dos Serviços de Multimédia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Como desenvolvedor, pode utilizar [a API](/rest/api/media/) dos Serviços de Media OU bibliotecas de clientes que lhe permitem interagir com a API REST para criar, gerir e manter fluxos de trabalho personalizados para os meios de comunicação. O [Serviço de Mídia v3](https://aka.ms/ams-v3-rest-sdk) API baseia-se na especificação OpenAPI (anteriormente conhecida como Swagger).

Este artigo discute regras que se aplicam a entidades e APIs quando se desenvolve com os Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Aceder à API dos Serviços de Multimédia do Azure

Para obter autorização para aceder aos recursos dos Serviços de Multimédia e à API dos Serviços de Multimédia, primeiro tem de se autenticar. Os Serviços de Multimédia suportam a autenticação baseada no [Azure Active Directory (AAD)](../../active-directory/fundamentals/active-directory-whatis.md). As duas opções de autenticação comuns são:
 
* **Autenticação do principal de serviço**: utilizada para autenticar um serviço (por exemplo: aplicações Web, aplicações de funções, aplicações lógicas, API e microsserviços). As aplicações que normalmente utilizam este método de autenticação são aplicações que executam serviços daemon, serviços de camada média ou tarefas agendadas. Por exemplo, para aplicações web deve haver sempre um nível médio que se conecta aos Serviços de Mídia com um Principal de Serviço.
* **Autenticação do utilizador**: utilizada para autenticar uma pessoa que está a utilizar a aplicação para interagir com os recursos dos Serviços de Multimédia. A aplicação interativa deve primeiro pedir ao utilizador as suas credenciais. Um exemplo é uma aplicação da consola de gestão utilizada pelos utilizadores autorizados para monitorizar as tarefas de codificação ou a transmissão em direto.

A API dos Serviços de Multimédia exige que o utilizador ou a aplicação que faz os pedidos da API REST tenha acesso ao recurso da conta dos Serviços de Multimédia e utilize uma função de **Contribuidor** ou **Proprietário**. É possível aceder à API com a função de **Leitor**, mas estarão disponíveis apenas as operações **Obter** ou **Listar**.Para obter mais informações, consulte [o controlo de acesso baseado em funções (Azure RBAC) para contas de Serviços de Comunicação Social](security-rbac-concept.md).

Em vez de criar um principal de serviço, considere utilizar identidades geridas para os recursos do Azure para aceder à API dos Serviços de Multimédia através do Azure Resource Manager. Para saber mais sobre as identidades geridas dos recursos do Azure, veja [O que são identidades geridas dos recursos do Azure?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Diretor de serviço AZure AD

A app Azure AD e o diretor de serviço devem estar no mesmo inquilino. Depois de criar a app, dê à app o acesso da função **Contribuinte** ou **Proprietário** à conta dos Serviços de Media.

Se não tem a certeza se tem permissões para criar uma aplicação AD Azure, consulte [permissões necessárias.](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

No seguinte número, os números representam o fluxo dos pedidos por ordem cronológica:

![Autenticação de aplicativos de nível médio com AAD a partir de uma API web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Uma aplicação de nível médio solicita um token de acesso AZure AD que tem os seguintes parâmetros:  

   * Azure AD inquilino ponto final.
   * Recursos de mídia URI.
   * URI de recurso para rest media Services.
   * Valores de aplicação AD AZure: o ID do cliente e o segredo do cliente.

   Para obter todos os valores necessários, consulte [a Access Azure Media Services API](./access-api-howto.md).

2. O sinal de acesso Azure AD é enviado para o nível médio.
4. O nível médio envia pedido à Azure Media REST API com o token AD Azure.
5. O nível médio recebe de volta os dados dos Serviços de Comunicação Social.

### <a name="samples"></a>Amostras

Veja as seguintes amostras que mostram como se conectar com o principal do serviço Azure AD:

* [Conecte-se com REST](setup-postman-rest-how-to.md)  
* [Connect with Java](configure-connect-java-howto.md) (Ligar com Java)
* [Connect with .NET](configure-connect-dotnet-howto.md) (Ligar com .NET)
* [Connect with Node.js](configure-connect-nodejs-howto.md) (Ligar com Node.js)
* [Connect with Python](configure-connect-python-howto.md) (Ligar com Python)

## <a name="naming-conventions"></a>Convenções de nomenclatura

Os nomes de recursos dos Serviços de Multimédia do Azure v3 (por exemplo, Ativos, Tarefas, Transformações) estão sujeitos às restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes de recursos são sempre exclusivos. Desta forma, pode utilizar todas as cadeias de identificador exclusivo (por exemplo, GUIDs) para os nomes de recursos.

Os nomes dos recursos dos Serviços de Comunicação Social não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', '.', o personagem de citação única, ou quaisquer personagens de controlo. Todos os outros carateres são permitidos. O comprimento máximo de um nome de recurso é superior a 260 carateres.

Para obter mais informações sobre o nome do Azure Resource Manager, consulte [os requisitos de nomeação](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [as convenções de nomeação.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Nomes de ficheiros/bolhas dentro de um ativo

Os nomes de ficheiros/bolhas dentro de um ativo devem seguir tanto os requisitos de [nome blob](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) como os requisitos de [nome NTFS](/windows/win32/fileio/naming-a-file). A razão para estes requisitos é que os ficheiros podem ser copiados do armazenamento de bolhas para um disco NTFS local para processamento.

## <a name="long-running-operations"></a>Operações de longa duração

As operações `x-ms-long-running-operation` marcadas nos [ficheiros](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) swagger da Azure Media Services são operações de longa duração. 

Para mais detalhes sobre como rastrear as operações assíncronas do Azure, consulte [as operações da Async](../../azure-resource-manager/management/async-operations.md).

Os Serviços de Comunicação Social têm as seguintes operações de longo prazo:

* [Criar eventos ao vivo](/rest/api/media/liveevents/create)
* [Atualizar eventos ao vivo](/rest/api/media/liveevents/update)
* [Excluir evento ao vivo](/rest/api/media/liveevents/delete)
* [Início ao evento ao vivo](/rest/api/media/liveevents/start)
* [Parar LiveEvent](/rest/api/media/liveevents/stop)

  Utilize o `removeOutputsOnStop` parâmetro para eliminar todas as saídas ao vivo associadas ao interromper o evento.  
* [Reset LiveEvent](/rest/api/media/liveevents/reset)
* [Criar LiveOutput](/rest/api/media/liveevents/create)
* [Excluir LiveOutput](/rest/api/media/liveevents/delete)
* [Criar StreamingEndpoint](/rest/api/media/streamingendpoints/create)
* [Atualização StreamingEndpoint](/rest/api/media/streamingendpoints/update)
* [Excluir streamingEndpoint](/rest/api/media/streamingendpoints/delete)
* [Iniciar streamingEndpoint](/rest/api/media/streamingendpoints/start)
* [Parar streamingEndpoint](/rest/api/media/streamingendpoints/stop)
* [Escala streamingEndpoint](/rest/api/media/streamingendpoints/scale)

Após a submissão bem sucedida de uma longa operação, recebe um '201 Criado' e deve sondar para conclusão da operação utilizando o ID de operação devolvido.

O artigo [de operações assíncronas da Azure](../../azure-resource-manager/management/async-operations.md) explica em profundidade como acompanhar o estado das operações assíncronas do Azure através de valores devolvidos na resposta.

Apenas uma operação de longa duração é suportada para um determinado Evento Ao Vivo ou qualquer uma das suas saídas ao vivo associadas. Uma vez iniciado, uma operação de longa duração deve ser concluída antes de iniciar uma operação de longo prazo subsequente no mesmo LiveEvent ou em quaisquer Saídas Ao Vivo associadas. Para eventos ao vivo com várias Saídas Ao Vivo, deve aguardar a conclusão de uma longa operação em execução numa Saída Ao Vivo antes de desencadear uma longa operação em execução em outra Saída Ao Vivo.

## <a name="sdks"></a>SDKs

> [!NOTE]
> Os Azure Media Services v3 SDKs não são garantidos como seguros. Ao desenvolver uma aplicação multi-roscada, deve adicionar a sua própria lógica de sincronização de fios para proteger o cliente ou utilizar um novo objeto AzureMediaServicesClient por fio. Deve também ter cuidado com as questões de múltiplos fios introduzidas por objetos opcionais fornecidos pelo seu código ao cliente (como uma instância HttpClient em .NET).

|SDK|Referência|
|---|---|
|[SDK do .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referência a .NET](/dotnet/api/overview/azure/mediaservices/management)|
|[SDK Java](https://aka.ms/ams-v3-java-sdk)|[Referência a Java](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Referência a Python](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Referência a Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[SDK Go](https://aka.ms/ams-v3-go-sdk) |[Referência a Go](https://aka.ms/ams-v3-go-ref)|
|[SDK Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Ver também

- [EventGrid .NET SDK que inclui eventos de Media Service](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definições de eventos de Serviços de Mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorador dos Serviços de Multimédia do Azure

[O Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) é uma ferramenta disponível para clientes windows que querem aprender sobre os Media Services. AMSE é uma aplicação Winforms/C# que faz upload, download, codificação, transmissão de VOD e conteúdo ao vivo com serviços de media. A ferramenta AMSE destina-se a clientes que pretendam testar serviços de mídia sem escrever qualquer código. O código AMSE é fornecido como um recurso para clientes que queiram desenvolver-se com os Media Services.

A AMSE é um projeto Open Source, o apoio é fornecido pela comunidade (as questões podem ser reportadas a https://github.com/Azure/Azure-Media-Services-Explorer/issues) . Este projeto adotou o [Microsoft Open Source Code of Conduct (Código de Conduta do Microsoft Open Source)](https://opensource.microsoft.com/codeofconduct/). Para mais informações, consulte o [Código de Conduta faQ](https://opensource.microsoft.com/codeofconduct/faq/) ou contacte opencode@microsoft.com com quaisquer outras questões ou comentários.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtragem, encomenda, paging de entidades de Serviços de Comunicação Social

Consulte [a filtragem, encomenda, paging de entidades da Azure Media Services](filter-order-page-entitites-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="see-also"></a>Ver também

Para obter todos os valores necessários, consulte [a Access Azure Media Services API](./access-api-howto.md).

## <a name="next-steps"></a>Passos seguintes

* [Ligue-se aos Serviços de Comunicação Social com a Java](configure-connect-java-howto.md)
* [Ligar aos Serviços de Mídia com .NET](configure-connect-dotnet-howto.md)
* [Ligue-se aos Serviços de Comunicação social com Node.js](configure-connect-nodejs-howto.md)
* [Ligue-se aos serviços de mídia com a Python](configure-connect-python-howto.md)