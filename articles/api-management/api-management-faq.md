---
title: Perguntas frequentes sobre o gerenciamento de API do Azure | Microsoft Docs
description: Conheça as respostas para perguntas frequentes (FAQ), padrões e práticas recomendadas no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 55a340f2ee2dceb31a8457f6f2201160e573e8a2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012370"
---
# <a name="azure-api-management-faqs"></a>Perguntas frequentes sobre gerenciamento de API do Azure
Obtenha as respostas para perguntas, padrões e práticas recomendadas comuns para o gerenciamento de API do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Contacte-nos
* [Como posso fazer uma pergunta à equipe de gerenciamento de API de Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* [O que significa quando um recurso está em visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como posso proteger a ligação entre o gateway de Gestão de API e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como copio a minha instância de serviço de Gestão de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerenciar minha instância de gerenciamento de API de forma programática?](#can-i-manage-my-api-management-instance-programmatically)
* [Como posso adicionar um utilizador ao grupo de Administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Por que a política que desejo adicionar não está disponível no editor de políticas?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como fazer configurar vários ambientes em uma única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Posso usar o SOAP com o gerenciamento de API?](#can-i-use-soap-with-api-management)
* [Posso configurar um servidor de autorização OAuth 2,0 com segurança AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Qual método de roteamento o gerenciamento de API usa em implantações em várias localizações geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Posso usar um modelo de Azure Resource Manager para criar uma instância de serviço de gerenciamento de API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Can I use a self-signed SSL certificate for a back end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)(Posso utilizar um certificado SSL autoassinado para um back-end?)
* [Por que recebo uma falha de autenticação quando tento clonar um repositório GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [O gerenciamento de API funciona com o Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que exigimos uma sub-rede dedicada no estilo do Resource Manager VNETs quando o gerenciamento de API é implantado neles?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Qual é o tamanho mínimo de sub-rede necessário ao implantar o gerenciamento de API em uma VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço da Gestão de API de uma subscrição para outra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Há restrições ou problemas conhecidos com a importação da minha API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como posso fazer uma pergunta à equipe de gerenciamento de API de Microsoft Azure?
Você pode entrar em contato conosco usando uma destas opções:

* Poste suas perguntas em nosso [Fórum do MSDN de gerenciamento de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Enviar um e-mail para <mailto:apimgmt@microsoft.com>.
* Envie-nos uma solicitação de recurso no [Fórum de comentários do Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando um recurso está em visualização?
Quando um recurso está em visualização, isso significa que estamos buscando ativamente comentários sobre como o recurso está funcionando para você. Um recurso na visualização é funcionalmente concluído, mas é possível que possamos fazer uma alteração significativa em resposta aos comentários dos clientes. É recomendável que você não dependa de um recurso que está em versão prévia em seu ambiente de produção. Se você tiver algum comentário sobre os recursos de visualização, informe-nos por meio de uma das opções de contato em [como posso fazer uma pergunta à equipe de gerenciamento de API de Microsoft Azure?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a conexão entre o gateway de gerenciamento de API e meus serviços de back-end?
Você tem várias opções para proteger a conexão entre o gateway de gerenciamento de API e seus serviços de back-end. Pode:

* Use a autenticação básica HTTP. Para obter mais informações, consulte [importar e publicar sua primeira API](import-and-publish.md).
* Use a autenticação mútua SSL, conforme descrito em [como proteger serviços de back-end usando a autenticação de certificado de cliente no gerenciamento de API do Azure](api-management-howto-mutual-certificates.md).
* Use a lista de permissões de IP em seu serviço de back-end. Em todas as camadas do gerenciamento de API com exceção da camada de consumo, o endereço IP do gateway permanece constante, com algumas limitações descritas no [artigo de documentação de IP](api-management-howto-ip-addresses.md).
* Conecte sua instância de gerenciamento de API a uma rede virtual do Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como fazer copiar minha instância do serviço de gerenciamento de API para uma nova instância?
Você terá várias opções se desejar copiar uma instância de gerenciamento de API para uma nova instância. Pode:

* Use a função de backup e restauração no gerenciamento de API. Para obter mais informações, consulte [como implementar a recuperação de desastre usando o backup e a restauração do serviço no gerenciamento de API do Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Crie seu próprio recurso de backup e restauração usando a [API REST de gerenciamento de API](/rest/api/apimanagement/). Use a API REST para salvar e restaurar as entidades da instância de serviço desejada.
* Baixe a configuração de serviço usando git e, em seguida, carregue-a em uma nova instância. Para obter mais informações, consulte [como salvar e configurar a configuração do serviço de gerenciamento de API usando o Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerenciar minha instância de gerenciamento de API de forma programática?
Sim, você pode gerenciar o gerenciamento de API programaticamente usando:

* A [API REST de gerenciamento de API](/rest/api/apimanagement/).
* O [SDK do Microsoft Azure ApiManagement Service Management library](https://aka.ms/apimsdk).
* Os cmdlets do PowerShell de [implantação de serviço](https://docs.microsoft.com/powershell/module/wds) e [Gerenciamento de serviços](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) .

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como fazer adicionar um usuário ao grupo Administradores?
Veja como você pode adicionar um usuário ao grupo Administradores:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos que tem a instância de gerenciamento de API que você deseja atualizar.
3. No gerenciamento de API, atribua a função **colaborador do serviço de gerenciamento de API** ao usuário.

Agora o colaborador recém-adicionado pode usar Azure PowerShell [cmdlets](https://docs.microsoft.com/powershell/azure/overview). Veja como entrar como administrador:

1. Use o `Connect-AzAccount` cmdlet para entrar.
2. Defina o contexto para a assinatura que tem o serviço usando `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Obtenha uma URL de logon único usando `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`o.
4. Use a URL para acessar o portal de administração.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Por que a política que desejo adicionar não está disponível no editor de políticas?
Se a política que você deseja adicionar aparecer esmaecida ou sombreada no editor de política, certifique-se de que você está no escopo correto para a política. Cada declaração de política é projetada para você usar em escopos específicos e seções de política. Para examinar as seções e os escopos de política de uma política, consulte a seção uso da política em [políticas de gerenciamento de API](/azure/api-management/api-management-policies).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como fazer configurar vários ambientes em uma única API?
Para configurar vários ambientes, por exemplo, um ambiente de teste e um ambiente de produção, em uma única API, você tem duas opções. Pode:

* Hospede diferentes APIs no mesmo locatário.
* Hospede as mesmas APIs em locatários diferentes.

### <a name="can-i-use-soap-with-api-management"></a>Posso usar o SOAP com o gerenciamento de API?
O suporte [de passagem SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) agora está disponível. Os administradores podem importar o WSDL de seu serviço SOAP e o gerenciamento de API do Azure criará um front-end SOAP. Documentação do portal do desenvolvedor, console de teste, políticas e análises estão disponíveis para serviços SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Posso configurar um servidor de autorização OAuth 2,0 com segurança AD FS?
Para saber como configurar um servidor de autorização OAuth 2,0 com segurança Serviços de Federação do Active Directory (AD FS) (AD FS), consulte [usando o ADFS no gerenciamento de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Qual método de roteamento o gerenciamento de API usa em implantações em várias localizações geográficas?
O gerenciamento de API usa o [método de roteamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance) em implantações em várias localizações geográficas. O tráfego de entrada é roteado para o gateway de API mais próximo. Se uma região ficar offline, o tráfego de entrada será roteado automaticamente para o gateway mais próximo. Saiba mais sobre os métodos de roteamento nos [métodos de roteamento do Gerenciador de tráfego](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Posso usar um modelo de Azure Resource Manager para criar uma instância de serviço de gerenciamento de API?
Sim. Consulte os modelos de início rápido do [serviço de gerenciamento de API do Azure](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Posso usar um certificado SSL autoassinado para um back-end?
Sim. Isso pode ser feito por meio do PowerShell ou enviando diretamente para a API. Isso desabilitará a validação da cadeia de certificados e permitirá que você use certificados autoassinados ou assinados de forma privada ao se comunicar com o gerenciamento de API para os serviços de back-end.

#### <a name="powershell-method"></a>Método do PowerShell ####
Use os [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets do PowerShell (para novo [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) back-end) ou (para back-end existente) `-SkipCertificateChainValidation` e defina `True`o parâmetro como. 

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método de atualização de API direta ####
1. Crie uma entidade de [back-end](/rest/api/apimanagement/) usando o gerenciamento de API.     
2. Defina a propriedade **skipCertificateChainValidation** como **true**.     
3. Se você não quiser mais permitir certificados autoassinados, exclua a entidade de back-end ou defina a propriedade **skipCertificateChainValidation** como **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que recebo uma falha de autenticação quando tento clonar um repositório git?
Se você usar o Gerenciador de credenciais do git ou se estiver tentando clonar um repositório git usando o Visual Studio, poderá encontrar um problema conhecido com a caixa de diálogo credenciais do Windows. A caixa de diálogo limita o tamanho da senha a 127 caracteres e trunca a senha gerada pela Microsoft. Estamos trabalhando para reduzir a senha. Por enquanto, use o Git bash para clonar seu repositório git.

### <a name="does-api-management-work-with-azure-expressroute"></a>O gerenciamento de API funciona com o Azure ExpressRoute?
Sim. O gerenciamento de API funciona com o Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que exigimos uma sub-rede dedicada no estilo do Resource Manager VNETs quando o gerenciamento de API é implantado neles?
O requisito de sub-rede dedicado para o gerenciamento de API é proveniente do fato de que ele se baseia no modelo de implantação clássico (camada PAAS v1). Embora possamos implantar em uma VNET do Resource Manager (camada v2), há conseqüências para isso. O modelo de implantação clássico no Azure não está intimamente acoplado ao modelo do Resource Manager e, portanto, se você criar um recurso na camada v2, a camada v1 não saberá disso e poderão ocorrer problemas, como o gerenciamento de API, tentando usar um IP que já está alocado a uma NIC  (criado em v2).
Para saber mais sobre a diferença entre os modelos clássico e do Resource Manager no Azure, consulte [diferença nos modelos de implantação](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Qual é o tamanho mínimo de sub-rede necessário ao implantar o gerenciamento de API em uma VNET?
O tamanho mínimo de sub-rede necessário para implantar o gerenciamento de API é [/29](../virtual-network/virtual-networks-faq.md#configuration), que é o tamanho mínimo de sub-rede ao qual o Azure dá suporte.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de gerenciamento de API de uma assinatura para outra?
Sim. Para saber como, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Há restrições ou problemas conhecidos com a importação da minha API?
[Problemas conhecidos e restrições](api-management-api-import-restrictions.md) para os formatos Swagger (Open API), WSDL e WADL.
