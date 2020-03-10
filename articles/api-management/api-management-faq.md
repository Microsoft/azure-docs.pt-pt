---
title: FaQ de Gestão da API Azure Microsoft Docs
description: Aprenda as respostas a perguntas frequentes (FAQ), padrões e boas práticas na Gestão da API Azure.
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
ms.openlocfilehash: 21b46ba0012b71ed0e09dc09d041ceb020824843
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375399"
---
# <a name="azure-api-management-faqs"></a>FaQs de Gestão API Azure
Obtenha as respostas a perguntas, padrões e boas práticas comuns para a Gestão da API Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Contacte-nos
* [Como posso fazer uma pergunta à equipa de Gestão API do Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* [O que significa quando uma funcionalidade está na pré-visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como posso proteger a ligação entre o gateway de Gestão de API e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como copio a minha instância de serviço de Gestão de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerir a minha instância de Gestão API programáticamente?](#can-i-manage-my-api-management-instance-programmatically)
* [Como posso adicionar um utilizador ao grupo de Administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Porque é que a política que quero acrescentar está indisponível no editor de política?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como posso configurar vários ambientes numa única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Posso usar sabão com Gestão de API?](#can-i-use-soap-with-api-management)
* [Posso configurar um servidor de autorização OAuth 2.0 com segurança AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Que método de encaminhamento a API Management usa em implementações para vários locais geográficos?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Posso usar um modelo de Gestor de Recursos Azure para criar uma instância de serviço de Gestão API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Can I use a self-signed SSL certificate for a back end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)(Posso utilizar um certificado SSL autoassinado para um back-end?)
* [Por que tenho uma falha de autenticação quando tento clonar um repositório de GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [A API Management trabalha com a Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que precisamos de uma subnet dedicada em VNETs estilo Gestor de Recursos quando a API Management é implantada neles?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Qual é o tamanho mínimo da sub-rede necessário ao implantar a Gestão API num VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço da Gestão de API de uma subscrição para outra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existem restrições ou questões conhecidas com a importação da minha API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como posso fazer uma pergunta à equipa de Gestão API do Microsoft Azure?
Pode contactar-nos utilizando uma destas opções:

* Publique as suas perguntas no nosso [fórum DeGeSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)de Gestão de API .
* Enviar um e-mail para <mailto:apimgmt@microsoft.com>.
* Envie-nos um pedido de funcionalidade no fórum de [feedback do Azure.](https://feedback.azure.com/forums/248703-api-management)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando uma funcionalidade está na pré-visualização?
Quando uma funcionalidade está em pré-visualização, significa que estamos ativamente à procura de feedback sobre como a funcionalidade está a funcionar para si. Uma funcionalidade na pré-visualização está funcionalmente completa, mas é possível que façamos uma mudança de rutura em resposta ao feedback do cliente. Recomendamos que não dependa de uma funcionalidade que esteja em pré-visualização no seu ambiente de produção. Se tiver algum feedback sobre as funcionalidades de pré-visualização, por favor informe-nos através de uma das opções de contacto em Como posso fazer uma pergunta à equipa de [Gestão aPI do Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso assegurar a ligação entre o portal de gestão da API e os meus serviços de back-end?
Tem várias opções para garantir a ligação entre o gateway de Gestão API e os seus serviços de back-end. Pode:

* Utilize a autenticação básica HTTP. Para mais informações, consulte [Importar e publicar a sua primeira API](import-and-publish.md).
* Utilize a autenticação mútua SSL conforme descrito em Como garantir serviços back-end utilizando a autenticação de certificado de [cliente na Gestão da API Azure](api-management-howto-mutual-certificates.md).
* Utilize a lista de ifais IP no seu serviço de back-end. Em todos os níveis de Gestão da API, com exceção do nível de consumo, o endereço IP do gateway permanece constante, com algumas ressalvas descritas no [artigo de documentação IP.](api-management-howto-ip-addresses.md)
* Ligue a sua instância de Gestão API a uma Rede Virtual Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como copio a minha instância de serviço de Gestão API para uma nova instância?
Tem várias opções se quiser copiar uma instância de Gestão API para uma nova instância. Pode:

* Utilize a função de backup e restauro na Gestão API. Para mais informações, consulte [como implementar a recuperação de desastres utilizando backup de serviço e restauro na Gestão da API Azure.](api-management-howto-disaster-recovery-backup-restore.md)
* Crie a sua própria funcionalidade de backup e restauro utilizando a [API Management REST API](/rest/api/apimanagement/). Utilize a API REST para guardar e restaurar as entidades da instância de serviço que deseja.
* Descarregue a configuração do serviço usando git e, em seguida, carregue-a para uma nova instância. Para mais informações, consulte [Como guardar e configurar a configuração do seu serviço de Gestão API utilizando git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerir a minha instância de Gestão API programáticamente?
Sim, pode gerir a Gestão de API programáticamente utilizando:

* A [API Management REST API.](/rest/api/apimanagement/)
* A Biblioteca de Gestão de [Serviços Microsoft Azure ApiManagement SDK](https://aka.ms/apimsdk).
* A implementação de [serviços](https://docs.microsoft.com/powershell/module/wds) e gestão de [serviços](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell cmdlets.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como adiciono um utilizador ao grupo de Administradores?
Eis como pode adicionar um utilizador ao grupo de Administradores:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá ao grupo de recursos que tem a instância de Gestão API que pretende atualizar.
3. Na Gestão da API, atribuir ao utilizador a função de Colaborador do Serviço de Gestão da **API.**

Agora, o colaborador recém-adicionado pode usar [cmdlets](https://docs.microsoft.com/powershell/azure/overview)Azure PowerShell . Aqui está como se inscrever como administrador:

1. Utilize o `Connect-AzAccount` cmdlet para iniciar sessão.
2. Desloque o contexto para a subscrição que tem o serviço utilizando `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Obtenha um único URL de inscrição utilizando `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilize o URL para aceder ao portal de administração.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Porque é que a política que quero acrescentar está indisponível no editor de política?
Se a política que pretende adicionar aparecer escurecida ou sombreada no editor de política, certifique-se de que está no âmbito correto para a apólice. Cada declaração de política é concebida para que possa utilizar em âmbitos específicos e secções políticas. Para rever as secções e âmbitos de política para uma política, consulte a secção de utilização da política nas políticas de Gestão da [API.](/azure/api-management/api-management-policies)

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como posso configurar vários ambientes numa única API?
Para criar vários ambientes, por exemplo, um ambiente de teste e um ambiente de produção, numa única API, você tem duas opções. Pode:

* Acolde diferentes APIs no mesmo inquilino.
* Acolde as mesmas APIs em diferentes inquilinos.

### <a name="can-i-use-soap-with-api-management"></a>Posso usar sabão com Gestão de API?
O suporte [pass-through soap](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) já está disponível. Os administradores podem importar o WSDL do seu serviço SOAP, e a Azure API Management criará uma frente soap. Documentação do portal do desenvolvedor, consola de teste, políticas e análise estão disponíveis para serviços SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Posso configurar um servidor de autorização OAuth 2.0 com segurança AD FS?
Para aprender a configurar um servidor de autorização OAuth 2.0 com segurança ative Directory Federation Services (AD FS), consulte [ADFS em Gestão De API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de encaminhamento a API Management usa em implementações para vários locais geográficos?
A API Management utiliza o método de [encaminhamento de tráfego](../traffic-manager/traffic-manager-routing-methods.md#performance) de desempenho em implementações para vários locais geográficos. O tráfego de entrada é encaminhado para o portal da API mais próximo. Se uma região ficar offline, o tráfego de entrada é automaticamente encaminhado para o gateway mais próximo. Saiba mais sobre os métodos de encaminhamento nos métodos de [encaminhamento do Gestor](../traffic-manager/traffic-manager-routing-methods.md)de Tráfego.

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Posso usar um modelo de Gestor de Recursos Azure para criar uma instância de serviço de Gestão API?
Sim. Consulte os modelos de arranque rápido do Serviço de [Gestão API Azure.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Posso usar um certificado SSL auto-assinado para uma parte traseira?
Sim. Isto pode ser feito através do PowerShell ou submetendo-se diretamente à API. Isto irá desativar a validação da cadeia de certificados e permitir-lhe-á utilizar certificados auto-assinados ou assinados por privado saqueado quando comunicar da API Management para os serviços de backend.

#### <a name="powershell-method"></a>Método Powershell ####
Utilize o [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (para uma nova extremidade traseira) ou [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (para a extremidade traseira existente) cmdlets PowerShell e coloque o parâmetro de `-SkipCertificateChainValidation` para `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método de atualização direta da API ####
1. Crie uma entidade [Backend](/rest/api/apimanagement/) utilizando a API Management.     
2. Delineie a propriedade **skipCertificateChainValidação** **como verdadeira**.     
3. Se já não pretender autorizar certificados auto-assinados, elimine a entidade Backend ou detetetetete a propriedade **skipCertificateChainValidação** como **falsa**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que tenho uma falha de autenticação quando tento clonar um repositório Git?
Se utilizar o Git Credential Manager, ou se estiver a tentar clonar um repositório Git utilizando o Visual Studio, poderá encontrar um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento da palavra-passe a 127 caracteres e trunca a palavra-passe gerada pela Microsoft. Estamos a tentar encurtar a senha. Por agora, por favor, use Git Bash para clonar o seu repositório Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>A API Management trabalha com a Azure ExpressRoute?
Sim. A API Management trabalha com a Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que precisamos de uma subnet dedicada em VNETs estilo Gestor de Recursos quando a API Management é implantada neles?
O requisito dedicado da subnet para a API Management provém do facto de ser construído com base no modelo de implantação Clássico (camada PAAS V1). Embora possamos implantar-nos num VNET (camada V2) do Gestor de Recursos, há consequências para isso. O modelo de implantação Classic em Azure não está fortemente associado ao modelo De Gestor de Recursos e por isso, se criar um recurso em camada V2, a camada V1 não sabe disso e podem ocorrer problemas, como a API Management tentando usar um IP que já está atribuído a um NIC  (construído em V2).
Para saber mais sobre a diferença de modelos Classic e Resource Manager em Azure, consulte a diferença nos modelos de [implementação.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Qual é o tamanho mínimo da sub-rede necessário ao implantar a Gestão API num VNET?
O tamanho mínimo da subrede necessário para implementar a Gestão API é [de /29,](../virtual-network/virtual-networks-faq.md#configuration)que é o tamanho mínimo da sub-rede que o Azure suporta.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de Gestão API de uma subscrição para outra?
Sim. Para saber como, consulte [mover recursos para um novo grupo de recursos ou subscrição.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existem restrições ou questões conhecidas com a importação da minha API?
[Questões e restrições conhecidas](api-management-api-import-restrictions.md) para formatos Open API(Swagger), WSDL e WADL.
