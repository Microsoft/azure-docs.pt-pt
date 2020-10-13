---
title: Visão geral do portal de desenvolvimento da Azure API Management
titleSuffix: Azure API Management
description: Conheça o portal de desenvolvimento na API Management. O portal de desenvolvedores é onde os consumidores podem encontrar as suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/28/2020
ms.author: apimpm
ms.openlocfilehash: 3642b95f5bd6d0207508ca85f1d22ce20b44eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715458"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão geral do portal de desenvolvimento da AZure API Management

O portal do desenvolvedor é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores da API podem descobrir as suas APIs, aprender a usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre versões auto-hospedadas e geridas do portal de desenvolvedores na API Management. Também explica a sua arquitetura e fornece respostas a perguntas frequentes.

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

> [!NOTE]
> <a name="migrate-from-legacy"></a> O novo portal de desenvolvedores é incompatível com o portal de desenvolvedores legados e a migração automatizada não é possível. É necessário recriar manualmente o conteúdo (páginas, texto, ficheiros de mídia) e personalizar a aparência do novo portal. Consulte [o tutorial do portal do desenvolvedor](api-management-howto-developer-portal-customize.md) para obter orientação.

## <a name="managed-and-self-hosted-versions"></a><a name="managed-vs-self-hosted"></a> Versões geridas e auto-hospedadas

Pode construir o seu portal de desenvolvedores de duas formas:

- **Versão gerida** - editando e personalizando o portal, que é incorporado na sua instância de Gestão de API e é acessível através do URL `<your-api-management-instance-name>.developer.azure-api.net` . Consulte [este artigo de documentação](api-management-howto-developer-portal-customize.md) para saber como aceder e personalizar o portal gerido.
- **Versão auto-hospedada** - implantando e auto-hospedando o seu portal fora de uma instância de Gestão da API. Esta abordagem permite-lhe editar a base de código do portal e alargar a funcionalidade principal fornecida - por exemplo, implementar widgets personalizados para integrações com sistemas de terceiros. Neste cenário, é o responsável pelo portal e é responsável pela atualização do portal para a versão mais recente. Para mais detalhes e instruções, consulte o [repositório GitHub com o código fonte do portal][1] e o tutorial sobre a [implementação de um widget][3]. O [tutorial para a versão gerida](api-management-howto-developer-portal-customize.md) percorre o painel administrativo do portal, que é comum para as versões geridas e auto-hospedadas.

## <a name="portal-architectural-concepts"></a>Conceitos arquitetónicos do portal

Os componentes do portal podem ser logicamente divididos em duas categorias: *código* e *conteúdo.*

*O código* é mantido [no repositório gitHub][1] e inclui:

- Widgets - que representam elementos visuais e combinam HTML, JavaScript, capacidade de estilo, configurações e mapeamento de conteúdo. Exemplos são uma imagem, um parágrafo de texto, um formulário, uma lista de APIs etc.
- Definições de estilo - que especificam como os widgets podem ser modelados
- Motor - que gera páginas estáticas a partir do conteúdo do portal e está escrito em JavaScript
- Editor visual - que permite personalização e experiência de autoria no navegador

*O conteúdo* divide-se em duas subcategorias: *conteúdo do portal* e conteúdo de *Gestão de API.*

*O conteúdo do portal* é específico do portal e inclui:

- Páginas - por exemplo, página de aterragem, tutoriais da API, posts de blog
- Meios de comunicação - imagens, animações e outros conteúdos baseados em ficheiros
- Layouts - modelos, que são combinados com um URL e definem como as páginas são exibidas
- Estilos - valores para definições de estilo, por exemplo, fontes, cores, fronteiras
- Definições - configuração, por exemplo favicon, metadados do site

*O conteúdo do*portal , com exceção dos meios de comunicação social, é expresso como documentos JSON.

*O conteúdo da API Management* inclui entidades como APIs, Operações, Produtos, Assinaturas.

O portal baseia-se num garfo adaptado da [estrutura Paperbits.](https://paperbits.io/) A funcionalidade original de Paperbits foi alargada para fornecer widgets específicos da API Management (por exemplo, uma lista de APIs, uma lista de Produtos) e um conector para o serviço de Gestão API para guardar e recuperar conteúdos.

## <a name="frequently-asked-questions"></a><a name="faq"></a> Perguntas frequentes

Nesta secção, respondemos a questões comuns sobre o portal de desenvolvimento, que são de natureza geral. Para questões específicas da versão auto-hospedada, consulte [a secção wiki do repositório GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Como posso migrar da versão de pré-visualização do portal?

Quando lançou pela primeira vez a versão de pré-visualização do portal do programador, adifou a versão de pré-visualização do seu conteúdo padrão no seu serviço de Gestão API. O conteúdo predefinido foi significativamente modificado na versão geralmente disponível. Por exemplo, a versão de pré-visualização do conteúdo predefinido não inclui botões OAuth nas páginas de login, utiliza diferentes widgets para exibir APIs, e baseia-se em capacidades limitadas para estruturar páginas do portal do desenvolvedor. Apesar de existirem diferenças no conteúdo, o motor do portal (incluindo widgets subjacentes) é automaticamente atualizado sempre que publica o seu portal de desenvolvimento.

Se personalizou fortemente o seu portal com base na versão de pré-visualização dos conteúdos, poderá continuar a usá-lo como está e colocar novos widgets manualmente nas páginas do portal. Caso contrário, recomendamos a substituição do conteúdo do seu portal pelo novo conteúdo predefinido.

Para redefinir o conteúdo num portal gerido, clique em Redefinir o **conteúdo** na secção menu **'Operações'.** Esta operação removerá todo o conteúdo do portal e irá providenciar novos conteúdos predefinidos. Perderá todas as personalizações e alterações do portal do desenvolvedor. **Não podes desfazer esta ação.**

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se estiver a utilizar a versão auto-hospedada, corra `scripts.v2/cleanup.bat` e `scripts.v2/generate.bat` scripts do repositório GitHub para remover os conteúdos existentes e forrê-lo de novos conteúdos. Certifique-se de atualizar o código do seu portal para a versão mais recente do repositório GitHub.

Caso tenha acedido pela primeira vez ao portal após o anúncio de disponibilidade geral em novembro de 2019, deverá já contar com o novo conteúdo predefinido e não é necessária qualquer outra ação.

### <a name="does-the-portal-have-all-the-features-of-the-legacy-portal"></a>O portal tem todas as características do portal do legado?

O portal de desenvolvedores já não suporta *aplicações*, *Problemas*e integração direta com o Facebook, Microsoft, Twitter e Google como fornecedores de identidade (pode utilizar o Azure AD B2C em vez disso).

### <a name="has-the-legacy-portal-been-deprecated"></a>O portal do legado foi deprectado?

Os antigos portais de desenvolvedores e editores são agora funcionalidades *antigas* - apenas receberão atualizações de segurança. As novas funcionalidades serão implementadas apenas no novo portal de desenvolvimento.

A depreciação dos portais legados será anunciada separadamente. Se tiver dúvidas, preocupações ou comentários, levante-os [numa questão dedicada do GitHub.](https://github.com/Azure/api-management-developer-portal/issues/121)

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A funcionalidade que preciso não é suportada no portal

Pode abrir um [pedido de funcionalidade](https://aka.ms/apimwish) ou implementar a funcionalidade que [falta.][3] Se implementar a funcionalidade por si mesmo, pode apresentar-se no portal do desenvolvedor ou abrir um pedido de pull no GitHub para incluir as alterações na versão gerida.

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar as implementações do portal?

Pode aceder programaticamente e gerir o conteúdo do portal do desenvolvedor através da API REST, independentemente de utilizar uma versão gerida ou auto-hospedada.

A API está documentada na [secção wiki do repositório do GitHub.][2] Pode ser utilizado para automatizar migrações de conteúdos portais entre ambientes - por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre este processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) no GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Como é que passo do gerido para a versão auto-hospedada?

Consulte o artigo detalhado na [secção Wiki do repositório do portal de desenvolvimento no GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Posso ter vários portais de desenvolvimento num serviço de Gestão API?

Pode ter um portal gerido e vários portais auto-hospedados. O conteúdo de todos os portais está armazenado no mesmo serviço de Gestão da API, pelo que serão idênticos. Se quiser diferenciar a aparência e funcionalidade dos portais, pode auto-hospedar-se com os seus próprios widgets personalizados que personalizam dinamicamente as páginas no tempo de execução, por exemplo com base no URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal suporta modelos e/ou é compatível com o Kit de Recursos de DevOps de Gestão de API?

N.º

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>O conteúdo do portal é guardado com a funcionalidade de backup/restauro na Gestão da API?

N.º

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Preciso de permitir a conectividade adicional do VNet para as dependências do portal geridas?

Na maioria dos casos, não.

Se o seu serviço de Gestão API estiver num VNet interno, o seu portal de desenvolvedores só está acessível a partir da rede. O nome de anfitrião do ponto final de gestão deve resolver-se para o VIP interno do serviço a partir da máquina que utiliza para aceder à interface administrativa do portal. Certifique-se de que o ponto final de gestão está registado no DNS. Em caso de má configuração, verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se o seu serviço de Gestão API estiver num VNet interno e estiver a aceder-lhe através do Gateway de Aplicação a partir da Internet, certifique-se de que permite a conectividade com o portal do desenvolvedor e os pontos finais de gestão da API Management.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio de Gestão de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, tem de voltar a [publicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um fornecedor de identidade e não consigo vê-lo no portal.

Depois de configurar um fornecedor de identidade (por exemplo, AAD, AAD B2C), é necessário [reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Criei uma delegação e o portal não a usa.

Depois de configurar a delegação, precisa [de reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>As minhas outras alterações de configuração da API Management não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, sign-in e termos de produto) [requerem a republicagem do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Estou a receber um erro de CORS ao usar a consola interativa

A consola interativa faz um pedido de API do lado do cliente do navegador. Resolva o problema do CORS adicionando [uma política CORS](api-management-cross-domain-policies.md#CORS) à sua API(s).

Pode verificar o estado da política CORS na secção **de visão geral** do Portal do seu serviço de Gestão API no portal Azure. Uma caixa de aviso indica uma política ausente ou mal configurada.

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cors-azure-portal.png)

Aplique automaticamente a política CORS clicando no botão **Enable CORS.**

Também pode ativar o CORS manualmente.

1. Clique no **manualmente aplique-o no** link de nível global para ver o código de política gerado.
2. Navegue para **todas as APIs** na secção **APIs** do seu serviço de Gestão API no portal Azure.
3. Clique no **</>** ícone na secção de processamento de **Entrada.**
4. Insira a política na **<inbound>** secção do ficheiro XML. Certifique-se de que o **<origin>** valor corresponde ao domínio do seu portal de desenvolvedores.

> [!NOTE]
> 
> Se aplicar a política CORS no âmbito do Produto, em vez do âmbito API(s) e a sua API utilizar a autenticação da chave de subscrição através de um cabeçalho, a sua consola não funcionará.
>
> O navegador emite automaticamente um pedido HTTP OPTIONS, que não contém um cabeçalho com a chave de subscrição. Devido à chave de subscrição em falta, a API Management não pode associar a chamada OPTIONS a um Produto, pelo que não pode aplicar a política do CORS.
>
> Como solução alternativa, pode passar a chave de subscrição num parâmetro de consulta.

### <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Que permissões preciso para editar o portal do desenvolvedor?

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

### <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Estou a ver o `Unable to start the portal. See if settings are specified correctly (...)` erro.

Este erro é mostrado quando uma `GET` chamada para `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` falhar. A chamada é emitida a partir do navegador pela interface administrativa do portal.

Se o seu serviço de Gestão API estiver num VNet - consulte a questão da conectividade VNet acima.

A falha de chamada também pode ser causada por um certificado TLS/SSL, que é atribuído a um domínio personalizado e não é confiável pelo navegador. Como mitigação, pode remover o domínio personalizado de ponta de gestão - a API Management voltará ao ponto final padrão com um certificado fidedigno.

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

Saiba mais sobre o novo portal de desenvolvedores:

- [Aceda e personalize o portal de desenvolvimento gerido](api-management-howto-developer-portal-customize.md)
- [Configurar a versão auto-hospedada do portal][2]
- [Implemente o seu próprio widget][3]

Procurar outros recursos:

- [Repositório GitHub com o código fonte][1]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://aka.ms/apimdevportal/extend
