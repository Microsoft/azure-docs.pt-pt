---
title: Portal do desenvolvedor - Perguntas frequentes
titleSuffix: Azure API Management
description: Muitas vezes se questionam sobre o portal de desenvolvimento na API Management. O portal de desenvolvedores é um site personalizável onde os consumidores de API podem explorar as suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741833"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>Portal de desenvolvimento de gestão da API - perguntas frequentes

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>E se eu precisar de funcionalidades que não são suportadas no portal?

Pode abrir um pedido de recurso no [repositório GitHub](https://github.com/Azure/api-management-developer-portal) ou [implementar a funcionalidade que falta.](developer-portal-implement-widgets.md) Saiba mais sobre [a extensibilidade do](api-management-howto-developer-portal.md#managed-vs-self-hosted)portal do desenvolvedor.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Posso ter vários portais de desenvolvimento num serviço de Gestão API?

Pode ter um portal gerido e vários portais auto-hospedados. O conteúdo de todos os portais está armazenado no mesmo serviço de Gestão da API, pelo que serão idênticos. Se quiser diferenciar a aparência e funcionalidade dos portais, pode auto-hospedar-se com os seus próprios widgets personalizados que personalizam dinamicamente as páginas no tempo de execução, por exemplo com base no URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal suporta modelos e/ou é compatível com o Kit de Recursos de DevOps de Gestão de API?

N.º

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>O conteúdo do portal é guardado com a funcionalidade de backup/restauro na Gestão da API?

N.º

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Preciso de permitir a conectividade adicional do VNet para as dependências do portal geridas?

Na maioria dos casos, não.

Se o seu serviço de Gestão API estiver num VNet interno, o seu portal de desenvolvedores só está acessível a partir da rede. O nome de anfitrião do ponto final de gestão deve resolver-se para o VIP interno do serviço a partir da máquina que utiliza para aceder à interface administrativa do portal. Certifique-se de que o ponto final de gestão está registado no DNS. Em caso de má configuração, verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se o seu serviço de Gestão API estiver num VNet interno e estiver a aceder-lhe através do Application Gateway a partir da internet, certifique-se de que permite a conectividade com o portal do desenvolvedor e os pontos finais de gestão da API Management. Pode ser necessário desativar as regras de Firewall de Aplicações Web. Consulte [este artigo de documentação](api-management-howto-integrate-internal-vnet-appgateway.md) para mais detalhes.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Eu atribuí um domínio de Gestão de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, tem de voltar a [publicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um fornecedor de identidade e não consigo vê-lo no portal.

Depois de configurar um fornecedor de identidade (por exemplo, Azure AD, Azure AD B2C), precisa [de reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor. Certifique-se de que as páginas do portal do desenvolvedor incluem o widget de botões OAuth.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Eu criei uma delegação e o portal não a usa.

Depois de configurar a delegação, precisa [de reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>As minhas outras alterações de configuração da API Management não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, iniciar sing-in, termos do produto) [requerem a republicagem do portal](api-management-howto-developer-portal-customize.md#publish).

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Estou a receber um erro de CORS ao usar a consola interativa

A consola interativa faz um pedido de API do lado do cliente do navegador. Resolva o problema do CORS adicionando [uma política CORS](api-management-cross-domain-policies.md#CORS) à sua API(s).

Pode verificar o estado da política CORS na secção **de visão geral** do Portal do seu serviço de Gestão API no portal Azure. Uma caixa de aviso indica uma política ausente ou mal configurada.

> [!NOTE]
> 
> Apenas uma política do CORS é executada. Se especificou várias políticas DE CORS (por exemplo, no nível API e no nível de todas as APIs), a sua consola interativa pode não funcionar como esperado.

![Screenshot que mostra onde pode verificar o estado da sua política CORS.](media/developer-portal-faq/cors-azure-portal.png)

Aplique automaticamente a política CORS clicando no botão **Enable CORS.**

Também pode ativar o CORS manualmente.

1. Selecione **manualmente aplique-o na** ligação de nível global para ver o código de política gerado.
2. Navegue para **todas as APIs** na secção **APIs** do seu serviço de Gestão API no portal Azure.
3. Selecione o **</>** ícone na secção de processamento de **entrada.**
4. Insira a política na **<inbound>** secção do ficheiro XML. Certifique-se de que o **<origin>** valor corresponde ao domínio do seu portal de desenvolvedores.

> [!NOTE]
> 
> Se aplicar a política CORS no âmbito do Produto, em vez do âmbito API(s) e a sua API utilizar a autenticação da chave de subscrição através de um cabeçalho, a sua consola não funcionará.
>
> O navegador emite automaticamente um `OPTIONS` pedido HTTP, que não contém um cabeçalho com a chave de subscrição. Devido à chave de subscrição em falta, a API Management não pode associar a `OPTIONS` chamada a um Produto, por isso não pode aplicar a política do CORS.
>
> Como solução alternativa, pode passar a chave de subscrição num parâmetro de consulta.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Qual é a funcionalidade de procuração CORS e quando devo usá-la?

Selecione a opção **de procuração Use CORS** na configuração do widget de detalhes de operação API para encaminhar as chamadas API da consola interativa através do backend do portal no seu serviço de Gestão API. Nesta configuração, já não é necessário aplicar uma política DE CORS para as suas APIs, e a conectividade com o ponto final de gateway da máquina local não é necessária. Se as APIs forem expostas através de um gateway auto-hospedado ou o seu serviço estiver numa rede virtual, é necessária a conectividade do serviço de backend da API Management para o gateway. Se utilizar o portal auto-hospedado, especifique o ponto final de backend do portal utilizando a `backendUrl` opção nos ficheiros de configuração. Caso contrário, o portal auto-hospedado não estará ciente da localização do serviço de backend.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Que permissões preciso para editar o portal do desenvolvedor?

Se estiver a ver o `Oops. Something went wrong. Please try again later.` erro ao abrir o portal no modo administrativo, poderá não ter as permissões necessárias (Azure RBAC).

Os portais legados requeriam a permissão `Microsoft.ApiManagement/service/getssotoken/action` no âmbito do serviço para permitir o acesso do administrador do utilizador aos `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` portais. O novo portal requer a permissão `Microsoft.ApiManagement/service/users/token/action` no `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` âmbito.

Pode utilizar o seguinte script PowerShell para criar uma função com a permissão necessária. Lembre-se de mudar o `<subscription-id>` parâmetro. 

```powershell
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
 
Uma vez criada a função, pode ser concedida a qualquer utilizador a partir da secção **Controlo de Acesso (IAM)** no portal Azure. A atribuição desta função a um utilizador atribuirá a permissão no âmbito do serviço. O utilizador será capaz de gerar fichas SAS em nome de *qualquer* utilizador do serviço. No mínimo, esta função deve ser atribuída ao administrador do serviço. O seguinte comando PowerShell demonstra como atribuir a função a um utilizador `user1` no âmbito mais baixo para evitar a concessão de permissões desnecessárias ao utilizador: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois de as permissões terem sido concedidas a um utilizador, o utilizador deve assinar e iniciar novamente o súm na porta Azure para que as novas permissões produzam efeitos.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou a ver o `Unable to start the portal. See if settings are specified correctly (...)` erro.

Este erro é mostrado quando uma `GET` chamada para `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` falhar. A chamada é emitida a partir do navegador pela interface administrativa do portal.

Se o seu serviço de Gestão API estiver num VNet, consulte a [questão da conectividade VNet.](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies)

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável pelo navegador. Como mitigação, pode remover o domínio personalizado de controlo de gestão API Management irá recorrer ao ponto final padrão com um certificado fidedigno.

## <a name="whats-the-browser-support-for-the-portal"></a>Qual é o suporte do navegador para o portal?

| Browser                     | Suportado       |
|-----------------------------|-----------------|
| Apple Safari                | Sim<sup>1</sup> |
| Google Chrome               | Sim<sup>1</sup> |
| Microsoft Edge              | Sim<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Sim<sup>1</sup> |

 <small><sup>1</sup> Suportado nas duas versões de produção mais recentes.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>O desenvolvimento local do meu portal auto-hospedado já não está a funcionar.

Se a sua versão local do portal do desenvolvedor não conseguir guardar ou recuperar informações da conta de armazenamento ou da instância de Gestão da API, os tokens SAS podem ter expirado. Podes consertá-lo gerando novos símbolos. Para obter instruções, consulte o tutorial para [auto-hospedar o portal do desenvolvedor](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings).

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Como posso remover o conteúdo do portal do desenvolvedor previsto para o meu serviço de Gestão de API?

Forneça os parâmetros necessários no `scripts.v3/cleanup.bat` script no [repositório](https://github.com/Azure/api-management-developer-portal)do portal de desenvolvimento GitHub e execute o script

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Como posso permitir a autenticação de um único sign-on (SSO) para o portal de desenvolvedores auto-hospedado?

Entre outros métodos de autenticação, o portal do desenvolvedor suporta uma única sso (SSO). Para autenticar com este método, é necessário fazer uma chamada `/signin-sso` com o símbolo no parâmetro de consulta:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Gerar fichas de utilizador
Pode gerar *fichas específicas do utilizador* (incluindo fichas de administração) utilizando a operação Get Shared Access [Token](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) da [API Management REST API](/rest/api/apimanagement/apimanagementrest/api-management-rest).

> [!NOTE]
> O símbolo deve ser codificado por URL.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o novo portal de desenvolvedores:

- [Aceda e personalize o portal de desenvolvimento gerido](api-management-howto-developer-portal-customize.md)
- [Configurar a versão auto-hospedada do portal](developer-portal-self-host.md)
- [Implemente o seu próprio widget](developer-portal-implement-widgets.md)

Procurar outros recursos:

- [Repositório GitHub com o código fonte](https://github.com/Azure/api-management-developer-portal)

