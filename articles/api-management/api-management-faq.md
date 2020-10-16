---
title: Azure API Management FAQ / Microsoft Docs
description: Aprenda as respostas a perguntas frequentes (FAQ), padrões e boas práticas na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 351503db52e4c62414cd5dcbae1f750032a37eb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542279"
---
# <a name="azure-api-management-faqs"></a>FaQs de gestão da Azure API
Obtenha as respostas para perguntas, padrões e boas práticas comuns para a Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* [O que significa quando uma funcionalidade está em pré-visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como posso proteger a ligação entre o gateway de Gestão de API e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como copio a minha instância de serviço de Gestão de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerir o meu caso de Gestão de API programáticamente?](#can-i-manage-my-api-management-instance-programmatically)
* [Como posso adicionar um utilizador ao grupo de Administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Porque é que a política que quero acrescentar não está disponível no editor de política?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como posso criar vários ambientes numa única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Posso usar SABÃO com API Management?](#can-i-use-soap-with-api-management)
* [Posso configurar um servidor de autorização OAuth 2.0 com segurança AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Que método de encaminhamento utiliza a API Management em implantações para múltiplas localizações geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Posso utilizar um modelo de Gestor de Recursos Azure para criar uma instância de serviço de Gestão API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Posso usar um certificado TLS/SSL auto-assinado para uma parte traseira?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Por que tenho uma falha de autenticação quando tento clonar um repositório de GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [A API Management trabalha com a Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que exigimos uma sub-rede dedicada no estilo VNETs do gestor de recursos quando a API Management é implantada neles?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Qual é o tamanho mínimo da sub-rede necessária para a implantação da API Management num VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço da Gestão de API de uma subscrição para outra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existem restrições ou questões conhecidas com a importação da minha API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando uma funcionalidade está em pré-visualização?
Quando uma funcionalidade está em pré-visualização, significa que estamos ativamente à procura de feedback sobre como a funcionalidade está a funcionar para si. Uma funcionalidade de pré-visualização está funcionalmente completa, mas é possível que façamos uma mudança de rutura em resposta ao feedback do cliente. Recomendamos que não dependa de uma funcionalidade que esteja em pré-visualização no seu ambiente de produção.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a ligação entre o gateway de Gestão de API e os meus serviços de back-end?
Tem várias opções para garantir a ligação entre o gateway de Gestão API e os seus serviços back-end. Pode:

* Utilize a autenticação básica HTTP. Para mais informações, consulte [Import e publique a sua primeira API.](import-and-publish.md)
* Utilize a autenticação mútua TLS conforme descrito em [Como garantir serviços de back-end utilizando a autenticação de certificados de cliente na Azure API Management](api-management-howto-mutual-certificates.md).
* Utilize filtragem IP no seu serviço back-end. Em todos os níveis de Gestão da API, com exceção do nível de Consumo, o endereço IP do gateway permanece constante, com algumas ressalvas descritas [no artigo de documentação IP](api-management-howto-ip-addresses.md).
* Ligue a sua instância de Gestão API a uma Rede Virtual Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copio a minha instância de serviço de Gestão de API para uma nova instância?
Tem várias opções se quiser copiar uma instância de Gestão da API para um novo caso. Pode:

* Utilize a função de backup e restauro na Gestão da API. Para obter mais informações, consulte [Como implementar a recuperação de desastres utilizando a cópia de segurança do serviço e restaurar na Azure API Management.](api-management-howto-disaster-recovery-backup-restore.md)
* Crie a sua própria funcionalidade de backup e restauro utilizando a [API Management REST API.](/rest/api/apimanagement/) Utilize a API REST para salvar e restaurar as entidades da instância de serviço que pretende.
* Descarregue a configuração do serviço utilizando o Git e, em seguida, faça o upload para um novo caso. Para obter mais informações, consulte [Como guardar e configurar a configuração do seu serviço de Gestão API utilizando o Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerir o meu caso de Gestão de API programáticamente?
Sim, pode gerir a Gestão da API programáticamente utilizando:

* A [API Management REST API.](/rest/api/apimanagement/)
* A [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* Os cmdlets powershell [de implantação](/powershell/module/wds) e [gestão de serviços.](/powershell/azure/servicemanagement/overview)

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como posso adicionar um utilizador ao grupo de Administradores?
Eis como pode adicionar um utilizador ao grupo de Administradores:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá ao grupo de recursos que tem a instância de Gestão API que pretende atualizar.
3. Na Gestão da API, atribua o papel de Contribuinte do **Serviço de Gestão da API** ao utilizador.

Agora o colaborador recém-adicionado pode usar [cmdlets](/powershell/azure/)Azure PowerShell . Eis como se inscrever como administrador:

1. Utilize o `Connect-AzAccount` cmdlet para iniciar sinserção.
2. Desa parte para a subscrição que tem o serviço utilizando `Set-AzContext -SubscriptionID <subscriptionGUID>` .
3. Obtenha um único URL de inscrição utilizando `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` .
4. Utilize o URL para aceder ao portal de administração.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Porque é que a política que quero acrescentar não está disponível no editor de política?
Se a política que pretende adicionar aparecer escurecida ou sombreada no editor de política, certifique-se de que está no âmbito correto da política. Cada declaração política foi concebida para que possa utilizar em âmbitos específicos e secções políticas. Para rever as secções e âmbitos de política para uma política, consulte a secção de Utilização da política nas políticas de [Gestão da API](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como posso criar vários ambientes numa única API?
Para configurar vários ambientes, por exemplo, um ambiente de teste e um ambiente de produção, numa única API, você tem duas opções. Pode:

* Hospedar APIs diferentes no mesmo inquilino.
* Hospedar as mesmas APIs em inquilinos diferentes.

### <a name="can-i-use-soap-with-api-management"></a>Posso usar SABÃO com API Management?
[O](https://azure.microsoft.com/blog/soap-pass-through/) suporte de passagem SOAP já está disponível. Os administradores podem importar a WSDL do seu serviço SOAP, e a Azure API Management criará uma extremidade frontal SOAP. Documentação do portal do desenvolvedor, consola de teste, políticas e análise estão disponíveis para serviços SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Posso configurar um servidor de autorização OAuth 2.0 com segurança AD FS?
Para aprender a configurar um servidor de autorização OAuth 2.0 com segurança ative Directory Federation Services (AD FS), consulte [utilizar a ADFS em Gestão da API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de encaminhamento utiliza a API Management em implantações para múltiplas localizações geográficas?
A API Management utiliza o [método de encaminhamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance) em implementações para vários locais geográficos. O tráfego de entrada é encaminhado para o portal API mais próximo. Se uma região ficar offline, o tráfego de entrada é automaticamente encaminhado para o portão mais próximo. Saiba mais sobre os métodos de encaminhamento nos [métodos de encaminhamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Posso utilizar um modelo de Gestor de Recursos Azure para criar uma instância de serviço de Gestão API?
Sim. Consulte os modelos de arranque rápido [do Serviço de Gestão AZure API.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Posso usar um certificado TLS/SSL auto-assinado para uma parte traseira?
Sim. Isto pode ser feito através do PowerShell ou submetendo-se diretamente à API. Isto irá desativar a validação da cadeia de certificados e permitir-lhe-á utilizar certificados auto-assinados ou assinados por subscrição privada ao comunicar da API Management para os serviços de back-end.

#### <a name="powershell-method"></a>Método Powershell ####
Utilize os [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets PowerShell (para nova extremidade traseira) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (para a parte traseira existente) e desa um `-SkipCertificateChainValidation` parâmetro para `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método de atualização direta da API ####
1. Criar uma entidade [backend](/rest/api/apimanagement/) utilizando a API Management.     
2. Defina a propriedade **skipCertificateChainValidation** para **ser verdadeira**.     
3. Se já não pretender permitir certificados auto-assinados, elimine a entidade Backend ou defina a propriedade **skipCertificateChainValidation** em **falso**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que motivo recebo uma falha de autenticação ao tentar clonar um repositório Git?
Se utilizar o Git Credential Manager, ou se estiver a tentar clonar um repositório de Git utilizando o Visual Studio, poderá encontrar um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento da palavra-passe a 127 caracteres e trunca a palavra-passe gerada pela Microsoft. Estamos a tentar encurtar a senha. Por enquanto, por favor, use Git Bash para clonar o seu repositório git.

### <a name="does-api-management-work-with-azure-expressroute"></a>A API Management trabalha com a Azure ExpressRoute?
Sim. A API Management trabalha com a Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que exigimos uma sub-rede dedicada no estilo VNETs do gestor de recursos quando a API Management é implantada neles?
O requisito dedicado da sub-rede para a Gestão da API provém do facto de ser construído no modelo de implantação Classic (camada PAAS V1). Embora possamos implantar numa VNET (camada V2) do Gestor de Recursos, existem consequências para isso. O modelo de implantação Clássico em Azure não está muito associado ao modelo de Gestor de Recursos e por isso, se criar um recurso na camada V2, a camada V1 não sabe e os problemas podem acontecer, como a API Management tentando usar um IP que já está atribuído a um NIC (construído em V2).
Para saber mais sobre a diferença dos modelos Classic e Resource Manager em Azure, consulte a [diferença nos modelos de implementação.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Qual é o tamanho mínimo da sub-rede necessária para a implantação da API Management num VNET?
O tamanho mínimo da sub-rede necessária para implantar a API Management é [/29,](../virtual-network/virtual-networks-faq.md#configuration)que é o tamanho mínimo da sub-rede que a Azure suporta.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço da Gestão de API de uma subscrição para outra?
Sim. Para saber como, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existem restrições ou questões conhecidas com a importação da minha API?
[Questões e restrições conhecidas](api-management-api-import-restrictions.md) para formatos Open API(Swagger), WSDL e WADL.
