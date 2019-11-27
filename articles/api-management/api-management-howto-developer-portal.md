---
title: Visão geral do portal do desenvolvedor de gerenciamento de API do Azure – gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre o portal do desenvolvedor no gerenciamento de API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: 2b69fdd7abefca360433fc9fb090569cba23febe
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454402"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão geral do portal do desenvolvedor do gerenciamento de API do Azure

O portal do desenvolvedor é um site da Web, totalmente personalizado e gerado com a documentação de suas APIs. É aí que os consumidores de API podem descobrir suas APIs, saber como usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre as versões autohospedadas e gerenciadas do portal do desenvolvedor no gerenciamento de API. Ele também explica sua arquitetura e fornece respostas para perguntas frequentes.

> [!WARNING]
>
> [Saiba como migrar da versão de visualização para a versão disponível](#preview-to-ga) do portal do desenvolvedor.

![Portal do desenvolvedor de gerenciamento de API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Versões gerenciadas e auto-hospedadas

Você pode criar seu portal do desenvolvedor de duas maneiras:

- **Versão gerenciada** – editando e personalizando o portal, que é incorporado à sua instância de gerenciamento de API e pode ser acessado por meio da URL `<your-api-management-instance-name>.developer.azure-api.net`. Consulte [Este artigo de documentação](api-management-howto-developer-portal-customize.md) para saber como acessar e personalizar o portal gerenciado.
- **Versão hospedada internamente** – Implantando e hospedando internamente seu portal fora de uma instância de gerenciamento de API. Essa abordagem permite que você edite a base de código do portal e estenda a funcionalidade básica fornecida. Você também precisa atualizar o portal para a versão mais recente por conta própria. Para obter detalhes e instruções, consulte o [repositório do GitHub com o código-fonte do portal][1]. O [tutorial para a versão gerenciada](api-management-howto-developer-portal-customize.md) percorre o painel administrativo do portal, que também está em destaque na versão hospedada internamente.

## <a name="portal-architectural-concepts"></a>Conceitos de arquitetura do portal

Os componentes do portal podem ser divididos logicamente em duas categorias: *código* e *conteúdo*.

O *código* é mantido no [repositório do GitHub][1] e inclui:

- Widgets – que representam elementos visuais e combinam HTML, JavaScript, capacidade de estilo, configurações e mapeamento de conteúdo. Os exemplos são uma imagem, um parágrafo de texto, um formulário, uma lista de APIs etc.
- Definições de estilo – que especificam como os widgets podem ser estilizados
- Mecanismo – que gera páginas da Web estáticas do conteúdo do portal e é escrito em JavaScript
- Editor visual – que permite a experiência de criação e personalização no navegador

O *conteúdo* é dividido em duas subcategorias: conteúdo do *portal* e conteúdo de *Gerenciamento de API*.

O *conteúdo do portal* é específico para o portal e inclui:

- Páginas-por exemplo, página de aterrissagem, tutoriais de API, Postagens de blog
- Mídia-imagens, animações e outros conteúdos com base em arquivo
- Layouts-modelos, que são correspondidos em uma URL e definem como as páginas são exibidas
- Estilos – valores para definições de estilo, por exemplo, fontes, cores, bordas
- Configurações-configuração, por exemplo, favicon, metadados do site

O *conteúdo do portal*, exceto para mídia, é expresso como documentos JSON.

O *conteúdo de gerenciamento de API* inclui entidades como APIs, operações, produtos, assinaturas.

O portal é baseado em uma bifurcação adaptada da [estrutura Paperbits](https://paperbits.io/). A funcionalidade original do Paperbits foi estendida para fornecer widgets específicos de gerenciamento de API (por exemplo, uma lista de APIs, uma lista de produtos) e um conector para o serviço de gerenciamento de API para salvar e recuperar conteúdo.

## <a name="faq"></a>Perguntas frequentes

Nesta seção, respondemos a perguntas comuns sobre o novo portal do desenvolvedor, que são de natureza geral. Para perguntas específicas para a versão hospedada internamente, consulte [a seção wiki do repositório do GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> como posso migrar da versão de visualização do portal?

Usando a versão de visualização do portal do desenvolvedor, você provisionou o conteúdo de visualização em seu serviço de gerenciamento de API. O conteúdo padrão foi modificado significativamente na versão disponível para melhor experiência do usuário. Ele também inclui novos widgets.

Se você estiver usando a versão gerenciada, redefina o conteúdo do portal clicando em **Redefinir Conteúdo** na seção do menu **operações** . A confirmação dessa operação removerá todo o conteúdo do portal e provisionar o novo conteúdo padrão. O mecanismo do portal foi atualizado automaticamente no serviço de gerenciamento de API.

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se você estiver usando a versão hospedada internamente, use o `scripts/cleanup.bat` e `scripts/generate.bat` do repositório GitHub para remover o conteúdo existente e provisionar novo conteúdo. Certifique-se de atualizar o código do portal para a versão mais recente do repositório do GitHub com antecedência.

Se não quiser redefinir o conteúdo do portal, você pode considerar o uso de widgets disponíveis recentemente em todas as páginas. Os widgets existentes foram atualizados automaticamente para as versões mais recentes.

Se o portal foi provisionado após o comunicado de disponibilidade geral, ele já deve apresentar o novo conteúdo padrão. Nenhuma ação é necessária do seu lado.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Como posso migrar do portal do desenvolvedor antigo para o novo portal do desenvolvedor?

Os portais são incompatíveis e você precisa migrar o conteúdo manualmente.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>O novo portal tem todos os recursos do portal antigo?

O novo portal do desenvolvedor não dá suporte a *aplicativos* e *problemas*. Se você tiver usado *problemas* no portal antigo e precisar deles em um novo, poste um comentário em [um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/122).

Ainda não há suporte para a autenticação com OAuth no console interativo do desenvolvedor. Você pode acompanhar o progresso por meio [do problema do GitHub](https://github.com/Azure/api-management-developer-portal/issues/208).

### <a name="has-the-old-portal-been-deprecated"></a>O portal antigo foi preterido?

Os portais antigos do desenvolvedor e do Publicador agora são recursos *herdados* . eles receberão apenas atualizações de segurança. As novas funcionalidades só serão implementadas no novo portal do programador.

A substituição dos portais herdados será anunciada separadamente. Se você tiver dúvidas, preocupações ou comentários, gere-os [em um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar as implantações do portal?

Você pode acessar e gerenciar programaticamente o conteúdo do portal do desenvolvedor por meio da API REST, independentemente de estar usando uma versão gerenciada ou hospedada internamente.

A API está documentada na [seção wiki do repositório do GitHub][2]. Ele também pode ser usado para automatizar migrações de conteúdo do portal entre ambientes, por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre esse processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) sobre o github.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal dá suporte a modelos de Azure Resource Manager e/ou é compatível com o DevOps Resource Kit de gerenciamento de API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-new-managed-portal-dependencies"></a>Preciso habilitar conectividade VNet adicional para as novas dependências do portal gerenciado?

Na maioria dos casos, não.

Se o serviço de gerenciamento de API estiver em uma VNet interna, o portal do desenvolvedor só poderá ser acessado de dentro da rede. O nome do host do ponto de extremidade de gerenciamento deve ser resolvido para o VIP interno do serviço do computador que você usa para acessar a interface administrativa do Portal. Verifique se o ponto de extremidade de gerenciamento está registrado no DNS. No caso de uma configuração incorreta, você verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio de gerenciamento de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um provedor de identidade e não consigo vê-lo no portal

Depois de configurar um provedor de identidade (por exemplo, AAD, AAD B2C), você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Configurei a delegação e o portal não a utiliza

Depois de configurar a delegação, você precisará [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Minhas outras alterações de configuração de gerenciamento de API não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, entrada e termos do produto) requer [a republicação do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a>Estou recebendo um erro de CORS ao usar o console interativo

O console interativo faz uma solicitação de API do lado do cliente do navegador. Você pode resolver o problema de CORS adicionando [uma política de CORS](api-management-cross-domain-policies.md#CORS) em suas API (s). Você pode especificar todos os parâmetros manualmente ou usar valores de `*` curinga. Por exemplo:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> Se você aplicar a política CORS no escopo do produto, em vez do escopo de API (s), e sua API usar a autenticação de chave de assinatura por meio de um cabeçalho, o console não funcionará.
>
> O navegador emite automaticamente uma solicitação HTTP OPTIONS, que não contém um cabeçalho com a chave de assinatura. Devido à chave de assinatura ausente, o gerenciamento de API não pode associar a chamada de opções a um produto, portanto, ele não pode aplicar a política CORS.
>
> Como alternativa, você pode passar a chave de assinatura em um parâmetro de consulta.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quais permissões preciso para editar o portal do desenvolvedor?

Se você estiver vendo o erro de `Oops. Something went wrong. Please try again later.` ao abrir o portal no modo administrativo, talvez você não esteja perdendo as permissões necessárias (RBAC).

Os portais herdados exigiam a permissão `Microsoft.ApiManagement/service/getssotoken/action` no escopo do serviço (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) para permitir que o administrador do usuário acesse os portais. O novo portal requer a permissão `Microsoft.ApiManagement/service/users/token/action` no `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`do escopo.

Você pode usar o seguinte script do PowerShell para criar uma função com a permissão necessária. Lembre-se de alterar o parâmetro `<subscription-id>`. 

```PowerShell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Depois que a função é criada, ela pode ser concedida a qualquer usuário da seção de **controle de acesso (iam)** no portal do Azure. A atribuição dessa função a um usuário atribuirá a permissão no escopo do serviço. O usuário poderá gerar tokens SAS em nome de *qualquer* usuário no serviço. No mínimo, essa função precisa ser atribuída ao administrador do serviço. O comando do PowerShell a seguir demonstra como atribuir a função a um usuário `user1` no escopo mais baixo para evitar conceder permissões desnecessárias ao usuário: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois que as permissões tiverem sido concedidas a um usuário, o usuário deverá sair e entrar novamente no portal do Azure para que as novas permissões entrem em vigor.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou vendo o erro de `Unable to start the portal. See if settings are specified correctly (...)`

Esse erro é mostrado quando uma chamada de `GET` para `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` falha. A chamada é emitida pelo navegador pela interface administrativa do Portal.

Se o serviço de gerenciamento de API estiver em uma VNet, consulte a pergunta de conectividade de VNet acima.

A falha de chamada também pode ser causada por um certificado SSL, que é atribuído a um domínio personalizado e não é confiável para o navegador. Como uma mitigação, você pode remover o ponto de extremidade de gerenciamento domínio personalizado-o gerenciamento de API voltará ao ponto de extremidade padrão com um certificado confiável.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o novo portal do desenvolvedor:

- [Acessar e personalizar o portal do desenvolvedor gerenciado](api-management-howto-developer-portal-customize.md)
- [Configurar a versão hospedada internamente do portal][2]

Procurar outros recursos:

- [Repositório GitHub com o código-fonte][1]
- [Roteiro público do projeto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects