---
title: FAQ da gestão de API do Azure | Documentos da Microsoft
description: Aprender as respostas às perguntas mais frequentes (FAQ), padrões e as práticas recomendadas na gestão de API do Azure.
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
ms.openlocfilehash: 99b54a5fe5c28eb66a61fad61d23b94f0955f126
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728573"
---
# <a name="azure-api-management-faqs"></a>Perguntas frequentes de gestão de API do Azure
Obtenha respostas para perguntas comuns, padrões e práticas recomendadas para a gestão de API do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Contacte-nos
* [Como posso fazer a equipe de gestão de API do Microsoft Azure uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
* [O que significa quando um recurso está em pré-visualização?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Como posso proteger a ligação entre o gateway de Gestão de API e os meus serviços de back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Como eu copio minha instância de serviço de gestão de API para uma nova instância?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Posso gerir a minha instância de gestão de API por meio de programação?](#can-i-manage-my-api-management-instance-programmatically)
* [Como posso adicionar um utilizador ao grupo de administradores?](#how-do-i-add-a-user-to-the-administrators-group)
* [Por que é a política que eu quero adicionar indisponível no editor de políticas?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Como posso configurar vários ambientes numa única API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Pode usar SOAP com a gestão de API?](#can-i-use-soap-with-api-management)
* [É a constante de endereço IP de gateway de gestão de API? Posso utilizá-la nas regras de firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* Pode configurar um servidor de autorização de OAuth 2.0 com segurança do AD FS?
* [Que método de encaminhamento de gestão de API o usa em implementações em várias localizações geográficas?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Pode utilizar um modelo Azure Resource Manager para criar uma instância de serviço de gestão de API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Pode utilizar um certificado SSL autoassinado para um back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Por que motivo recebo uma falha de autenticação ao tentar clonar um repositório GIT?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Gestão de API funciona com o Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Por que motivo é necessário quando a gestão de API é implementada numa sub-rede dedicada em VNETs do Resource Manager estilo?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [O que é o tamanho da sub-rede mínimo necessário ao implementar a gestão de API numa VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Posso mover um serviço de gestão de API de uma subscrição para outra?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existem restrições ou problemas conhecidos relacionados com a importação de minha API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Como posso fazer a equipe de gestão de API do Microsoft Azure uma pergunta?
Pode contactar-nos através de uma destas opções:

* Poste suas perguntas em nossos [fórum do MSDN de gestão de API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Enviar um e-mail para <mailto:apimgmt@microsoft.com>.
* Envie-num pedido de funcionalidade [fórum de comentários do Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>O que significa quando um recurso está em pré-visualização?
Quando um recurso está em pré-visualização, significa que podemos está ativamente procurando comentários sobre como a funcionalidade está a funcionar para. Uma funcionalidade em pré-visualização é funcionalmente completa, mas é possível que vamos fazer uma quebra de alterar em resposta aos comentários dos clientes. Recomendamos que não depende de um recurso que está em pré-visualização no seu ambiente de produção. Se tiver algum comentário sobre as funcionalidades de pré-visualização, faça contato por meio de uma das opções de contacto no [como posso fazer a equipe de gestão de API do Microsoft Azure uma pergunta?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Como posso proteger a ligação entre o gateway de gestão de API e meus serviços de back-end?
Tem várias opções para proteger a ligação entre o gateway de gestão de API e seus serviços de back-end. Pode:

* Utilize a autenticação básica HTTP. Para obter mais informações, consulte [importar e publicar a sua primeira API](import-and-publish.md).
* Utilizar a autenticação mútua de SSL, conforme descrito em [como proteger serviços de back-end com o cliente de autenticação de certificado na gestão de API do Azure](api-management-howto-mutual-certificates.md).
* Utilize listas de permissões de IP do seu serviço de back-end. Em todos os escalões da gestão de API, o endereço IP do gateway permaneça constante, com algumas [advertências](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Pode definir a sua lista de permissões para permitir que este endereço IP. Pode obter o endereço IP da sua instância de gestão de API no Dashboard do portal do Azure.
* Ligue-se a sua instância de gestão de API a uma rede Virtual do Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Como eu copio minha instância de serviço de gestão de API para uma nova instância?
Tem várias opções para copiar uma instância de gestão de API para uma nova instância. Pode:

* Utilize a cópia de segurança e restaurar a função na gestão de API. Para obter mais informações, consulte [como implementar a recuperação após desastre com o serviço backup e restaurar na gestão de API do Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Criar sua própria cópia de segurança e restaurar a funcionalidade utilizando o [API do REST de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilize a API REST para salvar e restaurar as entidades a partir da instância de serviço que pretende.
* Transferir a configuração do serviço utilizando o Git e, em seguida, carregue-o para uma nova instância. Para obter mais informações, consulte [para guardar e configurar a configuração do serviço de gestão de API com Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Posso gerir a minha instância de gestão de API por meio de programação?
Sim, pode gerir a gestão de API por meio de programação com:

* O [REST API de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* O [biblioteca de gestão de serviços de ApiManagement do Microsoft Azure SDK](https://aka.ms/apimsdk).
* O [implementação de serviços](https://docs.microsoft.com/powershell/module/wds) e [gestão de serviço](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) cmdlets do PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Como posso adicionar um utilizador ao grupo de administradores?
Eis como pode adicionar um utilizador ao grupo de administradores:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Vá para o grupo de recursos com a instância de gestão de API que pretende atualizar.
3. Na gestão de API, atribuir os **contribuinte de gestão de Api** função ao utilizador.

Agora, o colaborador adicionado recentemente pode utilizar o Azure PowerShell [cmdlets](https://docs.microsoft.com/powershell/azure/overview). Eis como iniciar sessão como administrador:

1. Utilize o `Connect-AzAccount` cmdlet para iniciar sessão.
2. Definir o contexto para a subscrição que tem o serviço utilizando `Set-AzContext -SubscriptionID <subscriptionGUID>`.
3. Obter um URL de início de sessão único com `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilize o URL para aceder ao portal de administração.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Por que é a política que eu quero adicionar indisponível no editor de políticas?
Se a política que pretende adicionar aparece desativado ou sombreados no editor de políticas, não se esqueça de que está no âmbito correto para a política. Cada declaração de política foi concebida para a utilização de âmbitos específicos e seções de política. Para rever as secções de política e os âmbitos para uma política, consulte a secção utilização da política [políticas de gestão de API](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Como posso configurar vários ambientes numa única API?
Para configurar a vários ambientes, por exemplo, um ambiente de teste e ambiente de produção, numa única API, tem duas opções. Pode:

* APIs de Host diferentes no mesmo inquilino.
* Aloje as mesmas APIs em inquilinos diferentes.

### <a name="can-i-use-soap-with-api-management"></a>Pode usar SOAP com a gestão de API?
[Pass-through SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) suporte está agora disponível. Os administradores podem importar WSDL do seu serviço SOAP e gestão de API do Azure irá criar um front-end SOAP. Documentação do portal de programador, a consola de teste, políticas e análise está disponível serviços SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>É a constante de endereço IP de gateway de gestão de API? Posso utilizá-la nas regras de firewall?
Em todos os escalões da gestão de API, o endereço IP público (VIP) do inquilino de gestão de API é estático para o tempo de vida do inquilino, com algumas exceções. As alterações do endereço IP nas seguintes circunstâncias:

* O serviço é eliminado e, em seguida, voltar a criar.
* A subscrição do serviço é [suspenso](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) ou [avisado](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (por exemplo, para não pagamento) e, em seguida, é reposto.
* Adicionar ou remover a rede Virtual do Azure (pode utilizar a rede Virtual apenas no desenvolvedor e o escalão Premium).

Para implementações em várias regiões, o endereço regional é alterado se a região é evacuada e, em seguida, restabelecida (pode usar implementação em várias regiões apenas no escalão Premium).

Inquilinos de escalão Premium que estão configurados para implementação em várias regiões são atribuídos um endereço IP público por região.

Pode obter o seu endereço IP (ou endereços, numa implementação em várias regiões) na página de inquilino no portal do Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Pode configurar um servidor de autorização de OAuth 2.0 com segurança do AD FS?
Para saber como configurar um servidor de autorização de OAuth 2.0 com segurança de serviços de Federação do Active Directory (AD FS), veja [usando o ADFS na gestão de API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Que método de encaminhamento de gestão de API o usa em implementações em várias localizações geográficas?
Gestão de API utiliza o [método de encaminhamento de tráfego de desempenho](../traffic-manager/traffic-manager-routing-methods.md#performance) nas implementações em várias localizações geográficas. O tráfego de entrada é encaminhado para o gateway de API mais próximo. Se uma região ficar offline, o tráfego de entrada é automaticamente encaminhado para o gateway mais próximo seguinte. Saiba mais sobre métodos de encaminhamento no [métodos de encaminhamento do Gestor de tráfego](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Pode utilizar um modelo Azure Resource Manager para criar uma instância de serviço de gestão de API?
Sim. Consulte a [serviço de gestão de API do Azure](https://aka.ms/apimtemplate) modelos de início rápido.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Pode utilizar um certificado SSL autoassinado para um back-end?
Sim. Isso pode ser feito através do PowerShell ou enviando diretamente para a API. Isto irá desativar a validação da cadeia de certificado e permite-lhe utilizar certificados autoassinados ou em privado assinado ao comunicar da gestão de API para os serviços de back-end.

#### <a name="powershell-method"></a>Método do PowerShell ####
Utilize o [ `New-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) (para o novo back-end) ou [ `Set-AzApiManagementBackend` ](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) (para o back-end existente) cmdlets do PowerShell e defina o `-SkipCertificateChainValidation` parâmetro `True`. 

```powershell
$context = New-AApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Método direto de atualização de API ####
1. Criar uma [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) entidade utilizando a gestão de API.       
2. Definir o **skipCertificateChainValidation** propriedade **verdadeiro**.     
3. Se já não pretender permitir que os certificados autoassinados, eliminar a entidade de back-end ou definir o **skipCertificateChainValidation** propriedade **falso**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Por que motivo recebo uma falha de autenticação ao tentar clonar um repositório de Git?
Se utilizar o Git Credential Manager, ou se estiver a tentar clonar um repositório de Git com o Visual Studio, poderá ocorrer um problema conhecido com a caixa de diálogo de credenciais do Windows. A caixa de diálogo limita o comprimento de palavra-passe a 127 carateres e ele trunca a palavra-passe gerada pelo Microsoft. Estamos a trabalhar no reduzindo a palavra-passe. Por agora, utilize o Git Bash para clonar o repositório de Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestão de API funciona com o Azure ExpressRoute?
Sim. Gestão de API funciona com o Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Por que motivo é necessário quando a gestão de API é implementada numa sub-rede dedicada em VNETs do Resource Manager estilo?
O requisito de sub-rede dedicada para a gestão de API vem do fato, o que ele se baseia no modelo de implementação clássica (camada de PAAS V1). Enquanto que pode implementar uma VNET do Resource Manager (camada V2), há consequências para que. O modelo de implementação clássico no Azure não está intimamente ligado com o modelo do Resource Manager e então, se criar um recurso na camada de V2, a camada de V1 não o conhece e problemas podem acontecer, por exemplo, a tentar utilizar um IP que já está atribuído a uma NIC de gestão de API  (baseado na V2).
Para saber mais sobre a diferença dos modelos clássico e Resource Manager no Azure consulte [diferença em modelos de implementação](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>O que é o tamanho da sub-rede mínimo necessário ao implementar a gestão de API numa VNET?
É o tamanho da sub-rede mínimos necessário para implementar a gestão de API [/29](../virtual-network/virtual-networks-faq.md#configuration), que é o tamanho da sub-rede mínimos suportados pelo Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Posso mover um serviço de gestão de API de uma subscrição para outra?
Sim. Para saber como, veja [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existem restrições ou problemas conhecidos relacionados com a importação de minha API?
[Conhecido restrições e problemas](api-management-api-import-restrictions.md) para API(Swagger) aberto, WSDL e WADL formatos.
