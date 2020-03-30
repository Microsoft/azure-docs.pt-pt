---
title: Visão geral do portal de desenvolvimento da API Management Azure
titleSuffix: Azure API Management
description: Conheça o portal de desenvolvimento na API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/15/2020
ms.author: apimpm
ms.openlocfilehash: fefa5ff5d112b479110d484ee0ea4c358b5c88a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335899"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão geral do portal de desenvolvimento da Azure API Management

O portal de desenvolvimento é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores de API podem descobrir as suas APIs, aprender a usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre versões auto-hospedadas e geridas do portal de desenvolvimento em Gestão API. Também explica a sua arquitetura e fornece respostas a perguntas frequentes.

![Portal de desenvolvimento de gestão api](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a>Versões geridas e auto-hospedadas

Pode construir o seu portal de desenvolvimento de duas formas:

- **Versão gerida** - editando e personalizando o portal, que está integrado na `<your-api-management-instance-name>.developer.azure-api.net`sua instância de Gestão API e é acessível através do URL . Consulte [este artigo de documentação](api-management-howto-developer-portal-customize.md) para saber como aceder e personalizar o portal gerido.
- **Versão auto-hospedada** - implantando e auto-hospedando o seu portal fora de uma instância de Gestão API. Esta abordagem permite-lhe editar a base de códigos do portal e alargar a funcionalidade central fornecida - por exemplo, implementar widgets personalizados para integrações com sistemas de terceiros. Neste cenário, é o responsável pela atualização do portal para a versão mais recente. Para mais detalhes e instruções, consulte o [repositório GitHub com o código fonte do portal][1] e o tutorial sobre a [implementação de um widget][3]. O [tutorial para a versão gerida](api-management-howto-developer-portal-customize.md) percorre o painel administrativo do portal, que é comum para as versões geridas e auto-hospedadas.

## <a name="portal-architectural-concepts"></a>Conceitos arquitetónicos do portal

Os componentes do portal podem ser logicamente divididos em duas categorias: *código* e *conteúdo.*

*O código* é mantido [no repositório GitHub][1] e inclui:

- Widgets - que representam elementos visuais e combinam HTML, JavaScript, capacidade de styling, configurações e mapeamento de conteúdo. Exemplos são uma imagem, um parágrafo de texto, um formulário, uma lista de APIs etc.
- Definições de estilo - que especificam como os widgets podem ser modelados
- Motor - que gera páginas web estáticas a partir de conteúdo do portal e está escrito no JavaScript
- Editor visual - que permite personalizar no navegador e experiência de autoria

*O conteúdo* é dividido em duas subcategorias: conteúdo do *portal* e conteúdo de *Gestão API.*

*O conteúdo* do portal é específico do portal e inclui:

- Páginas - por exemplo, página de aterragem, tutoriais da API, posts de blog
- Meios de comunicação - imagens, animações e outros conteúdos baseados em ficheiros
- Layouts - modelos, que são combinados com um URL e definem como as páginas são exibidas
- Estilos - valores para definições de estilo, por exemplo, tipos de letra, cores, fronteiras
- Configurações - configuração, por exemplo favicon, metadados do site

*O conteúdo*do portal , com exceção dos meios de comunicação social, é expresso como documentos JSON.

O conteúdo da *API Management* inclui entidades como APIs, Operações, Produtos, Assinaturas.

O portal baseia-se numa bifurcação adaptada da [estrutura dos Paperbits.](https://paperbits.io/) A funcionalidade original dos Paperbits foi alargada para fornecer widgets específicos de Gestão API (por exemplo, uma lista de APIs, uma lista de Produtos) e um conector ao serviço de Gestão API para poupar e recuperar conteúdos.

## <a name="frequently-asked-questions"></a><a name="faq"></a>Perguntas frequentes

Nesta secção, respondemos a perguntas comuns sobre o portal de desenvolvimento, que são de natureza geral. Para questões específicas da versão auto-hospedada, consulte [a secção wiki do repositório GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/>Como posso migrar da versão de pré-visualização do portal?

Ao utilizar a versão de pré-visualização do portal de desenvolvimento, disponibilizou o conteúdo de pré-visualização no seu serviço de Gestão API. O conteúdo predefinido foi significativamente modificado na versão geralmente disponível para uma melhor experiência do utilizador. Também inclui novos widgets.

Se estiver a utilizar a versão gerida, redefinir o conteúdo do portal clicando em **redefinir o conteúdo** na secção de menu **sinuoso.** A confirmação desta operação removerá todo o conteúdo do portal e fornecerá o novo conteúdo predefinido. O motor do portal foi automaticamente atualizado no seu serviço de Gestão API.

![Redefinir conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se estiver a utilizar a versão auto-hospedada, utilize o `scripts/cleanup.bat` `scripts/generate.bat` repositório GitHub para remover o conteúdo existente e fornecer novos conteúdos. Certifique-se de atualizar previamente o código do seu portal para a versão mais recente do repositório GitHub.

Se não quiser redefinir o conteúdo do portal, poderá considerar utilizar widgets recém-disponíveis ao longo das suas páginas. Os widgets existentes foram automaticamente atualizados para as versões mais recentes.

Se o seu portal foi aprovisionado após o anúncio de disponibilidade geral, já deverá apresentar o novo conteúdo predefinido. Não é necessária nenhuma ação do seu lado.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-developer-portal"></a>Como posso migrar do antigo portal de desenvolvimento para o portal de desenvolvimento?

Os portais são incompatíveis e é necessário migrar o conteúdo manualmente.

### <a name="does-the-portal-have-all-the-features-of-the-old-portal"></a>O portal tem todas as características do antigo portal?

O portal de desenvolvimento já não suporta Aplicações e *Problemas.* *Issues*

A autenticação com o OAuth na consola de desenvolvimento interativo ainda não é suportada. Pode acompanhar o progresso através [da edição do GitHub.](https://github.com/Azure/api-management-developer-portal/issues/208)

### <a name="has-the-old-portal-been-deprecated"></a>O velho portal foi deprecado?

Os antigos portais de desenvolvimento e editor estiveram agora em funcionalidades *antigas* - estarão a receber apenas atualizações de segurança. Novas funcionalidades serão implementadas apenas no novo portal de desenvolvimento.

A depreciação dos portais do legado será anunciada separadamente. Se tiver dúvidas, preocupações ou comentários, levante-as numa edição dedicada do [GitHub.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A funcionalidade de que preciso não é suportada no portal

Você pode abrir um pedido de [funcionalidade](https://aka.ms/apimwish) ou [implementar a funcionalidade em falta por si mesmo][3]. Se implementar a funcionalidade por si mesmo, pode auto-hospedar o portal de desenvolvimento ou abrir um pedido de pull no GitHub para incluir as alterações na versão gerida.

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar as implementações do portal?

Pode aceder programaticamente e gerir o conteúdo do portal de desenvolvimento através da API REST, independentemente de estar a utilizar uma versão gerida ou auto-hospedada.

A API está documentada na [secção wiki do repositório GitHub][2]. Pode ser utilizado para automatizar migrações de conteúdos de portais entre ambientes - por exemplo, de um ambiente de teste ao ambiente de produção. Pode saber mais sobre este processo neste artigo de [documentação](https://aka.ms/apimdocs/migrateportal) no GitHub.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal suporta modelos de Gestor de Recursos Azure e/ou é compatível com o Kit de Recursos DevOps de Gestão aPi?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Preciso de permitir uma conectividade VNet adicional para as dependências geridas do portal?

Na maioria dos casos- não.

Se o seu serviço de Gestão API estiver num VNet interno, o portal de desenvolvimento só está acessível a partir da rede. O nome de anfitrião do ponto final da gestão deve ser resolvido com o VIP interno do serviço a partir da máquina que utiliza para aceder à interface administrativa do portal. Certifique-se de que o ponto final da gestão está registado no DNS. Em caso de configuração errada, verá `Unable to start the portal. See if settings are specified correctly in the configuration (...)`um erro: .

Se o seu serviço de Gestão API estiver num VNet interno e estiver a aceder-lhe através do Gateway de Aplicação a partir da Internet, certifique-se de permitir a conectividade ao portal de desenvolvimento e aos pontos finais de gestão da API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio personalizado de Gestão aPI e o portal publicado não funciona

Depois de atualizar o domínio, precisa de [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um fornecedor de identidade e não consigo vê-lo no portal.

Depois de configurar um fornecedor de identidade (por exemplo, AAD, AAD B2C), precisa de [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Eu estarei numa delegação e o portal não a usa.

Depois de configurar a delegação, precisa de [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>As minhas outras alterações de configuração de Gestão API não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, sign-in e termos do produto) exigem [a republicação do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a>Estou a ter um erro cors ao usar a consola interativa.

A consola interativa faz um pedido de API do lado do cliente do navegador. Resolva o problema do CORS adicionando [uma política CORS](api-management-cross-domain-policies.md#CORS) nas suas API(s).

Pode verificar o estado da política CORS na secção **de visão geral** do Portal do seu serviço de Gestão API no portal Azure. Uma caixa de aviso indica uma política ausente ou mal configurada.

![Portal de desenvolvimento de gestão api](media/api-management-howto-developer-portal/cors-azure-portal.png)

Aplique automaticamente a política CORS clicando no botão **Enable CORS.**

Também pode ativar o CORS manualmente.

1. Clique no **manualmente aplique-o no** link de nível global para ver o código de política gerado.
2. Navegue para **todas as APIs** na secção **APIs** do seu serviço de Gestão API no portal Azure.
3. Clique no **</>** ícone na secção de **processamento de Entrada.**
4. Insira a **<inbound>** política na secção do ficheiro XML. Certifique-se **<origin>** de que o valor corresponde ao domínio do seu portal de desenvolvimento.

> [!NOTE]
> 
> Se aplicar a política CORS no âmbito do Produto, em vez do âmbito API(s) e a sua API utilizar a autenticação da chave de subscrição através de um cabeçalho, a sua consola não funcionará.
>
> O navegador emite automaticamente um pedido OPÇÕES HTTP, que não contém um cabeçalho com a chave de subscrição. Devido à chave de subscrição em falta, a API Management não pode associar a chamada options com um Produto, por isso não pode aplicar a política CORS.
>
> Como uma suver, pode passar a chave de subscrição num parâmetro de consulta.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Que permissões preciso para editar o portal de desenvolvimento?

Se estiver a `Oops. Something went wrong. Please try again later.` ver o erro ao abrir o portal no modo administrativo, poderá não ter as permissões necessárias (RBAC).

Os portais legados `Microsoft.ApiManagement/service/getssotoken/action` exigiam a`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`permissão no âmbito do serviço para permitir o acesso do administrador utilizador aos portais. O novo portal requer `Microsoft.ApiManagement/service/users/token/action` a `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1`permissão no âmbito.

Pode utilizar o seguinte script PowerShell para criar um papel com a permissão necessária. Lembre-se `<subscription-id>` de mudar o parâmetro. 

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
 
Uma vez criada a função, pode ser concedida a qualquer utilizador da secção de Controlo de **Acesso (IAM)** no portal Azure. Atribuir esta função a um utilizador atribuirá a permissão no âmbito do serviço. O utilizador poderá gerar tokens SAS em nome de *qualquer* utilizador do serviço. No mínimo, este papel tem de ser atribuído ao administrador do serviço. O seguinte comando PowerShell demonstra como atribuir a `user1` função a um utilizador no âmbito mais baixo para evitar a concessão de permissões desnecessárias ao utilizador: 

```PowerShell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Depois de as permissões terem sido concedidas a um utilizador, o utilizador deve assinar e voltar a entrar no portal Azure para que as novas permissões entrem em vigor.

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou a ver `Unable to start the portal. See if settings are specified correctly (...)` o erro.

Este erro é mostrado `GET` quando `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` uma chamada falha. A chamada é emitida a partir do navegador pela interface administrativa do portal.

Se o seu serviço de Gestão API estiver num VNet - consulte a questão da conectividade VNet acima.

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável pelo navegador. Como mitigação, pode remover o domínio personalizado de ponto final de gestão - a API Management voltará ao ponto final padrão com um certificado de confiança.

### <a name="whats-the-browser-support-for-the-portal"></a>Qual é o suporte do navegador para o portal?

| Browser                     | Suportado       |
|-----------------------------|-----------------|
| Apple Safari                | Sim<sup>1</sup> |
| Google Chrome               | Sim<sup>1</sup> |
| Microsoft Edge              | Sim<sup>1</sup> |
| Microsoft Internet Explorer | Não              |
| Mozilla Firefox             | Sim<sup>1</sup> |

 <small><sup>1</sup> Suportado nas duas versões de produção mais recentes.</small>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o novo portal de desenvolvimento:

- [Aceda e personalize o portal de desenvolvimento gerido](api-management-howto-developer-portal-customize.md)
- [Configurar versão auto-hospedada do portal][2]
- [Implemente o seu próprio widget][3]

Navegue por outros recursos:

- [Repositório GitHub com o código fonte][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend