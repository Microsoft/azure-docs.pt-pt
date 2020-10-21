---
title: Visão geral do portal de desenvolvimento na Azure API Management
titleSuffix: Azure API Management
description: Conheça o portal de desenvolvedores em API Management - um site personalizável, onde os consumidores de API podem explorar as suas APIs.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/15/2020
ms.author: apimpm
ms.openlocfilehash: 30a6a73768db7b073258487435ddbe6c0daccf16
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92317830"
---
# <a name="overview-of-the-developer-portal"></a>Visão geral do portal do desenvolvedor

O portal do desenvolvedor é um website gerado automaticamente e totalmente personalizável com a documentação das suas APIs. É onde os consumidores da API podem descobrir as suas APIs, aprender a usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre versões auto-hospedadas e geridas do portal de desenvolvedores na API Management. Também fornece respostas a perguntas frequentes.

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migração do portal do legado

> [!IMPORTANT]
> O portal de desenvolvedores legados está agora deprecado e receberá apenas atualizações de segurança. Pode continuar a usá-lo, como de costume, até à sua reforma em outubro de 2023, altura em que será removido de todos os serviços de Gestão da API.

A migração para o novo portal de desenvolvimento é descrita no [artigo de documentação dedicado.](developer-portal-deprecated-migration.md)

## <a name="customization-and-styling"></a>Personalização e styling

O portal de desenvolvedores pode ser personalizado e modelado através do editor visual incorporado, arrastado e drop. Consulte [este tutorial](api-management-howto-developer-portal-customize.md) para mais detalhes.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Extensibilidade

O seu serviço de Gestão API inclui um portal de desenvolvedores gerido, sempre atualizado e **atualizado.** Pode aceder-lhe a partir da interface do portal Azure.

Se precisar estender com lógica personalizada, que não é suportada fora da caixa, pode modificar a sua base de código. A base de código do portal está [disponível num repositório GitHub.][1] Por exemplo, pode implementar um novo widget, que se integra com um sistema de suporte de terceiros. Quando implementar uma nova funcionalidade, pode escolher uma das seguintes opções:

- **Auto-hospedar** o portal resultante fora do seu serviço de Gestão API. Quando se auto-hospeda o portal, torna-se o seu criador e é responsável pelas suas atualizações. A assistência do Azure Support limita-se apenas à configuração básica de portais auto-hospedados, conforme documentado na [secção Wiki do repositório][2].
- Abra um pedido de puxar para que a equipa de Gestão da API funda nova funcionalidade com a base de código do portal **gerido.**

Para obter detalhes e instruções de extensibilidade, consulte o [repositório GitHub][1] e [os tutoriais sobre a implementação de um widget][3]. O [tutorial para personalizar o portal gerido](api-management-howto-developer-portal-customize.md) acompanha-o através do painel administrativo do portal, que é comum para versões **geridas** e **auto-hospedadas.**

## <a name="frequently-asked-questions"></a><a name="faq"></a> Perguntas frequentes

Nesta secção, respondemos a questões comuns sobre o portal de desenvolvimento, que são de natureza geral. Para questões específicas da versão auto-hospedada, consulte [a secção wiki do repositório GitHub](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"></a> Como posso migrar da versão de pré-visualização do portal?

Quando lançou pela primeira vez a versão de pré-visualização do portal do programador, adifou a versão de pré-visualização do seu conteúdo padrão no seu serviço de Gestão API. O conteúdo predefinido foi significativamente modificado na versão geralmente disponível. Por exemplo, a versão de pré-visualização do conteúdo predefinido não inclui botões OAuth nas páginas de login, utiliza diferentes widgets para exibir APIs, e baseia-se em capacidades limitadas para estruturar páginas do portal do desenvolvedor. Apesar de existirem diferenças no conteúdo, o motor do portal (incluindo widgets subjacentes) é automaticamente atualizado sempre que publica o seu portal de desenvolvimento.

Se personalizou fortemente o seu portal com base na versão de pré-visualização dos conteúdos, poderá continuar a usá-lo como está e colocar novos widgets manualmente nas páginas do portal. Caso contrário, recomendamos a substituição do conteúdo do seu portal pelo novo conteúdo predefinido.

Para redefinir o conteúdo num portal gerido, **selecione Repor** o conteúdo na secção menu **Operações.** Esta operação removerá todo o conteúdo do portal e irá providenciar novos conteúdos predefinidos. Perderá todas as personalizações e alterações do portal do desenvolvedor. **Não podes desfazer esta ação.**

![Redefinir o conteúdo do portal](media/api-management-howto-developer-portal/reset-content.png)

Se estiver a utilizar a versão auto-hospedada, corra `scripts.v2/cleanup.bat` e `scripts.v2/generate.bat` scripts do repositório GitHub para remover os conteúdos existentes e forrê-lo de novos conteúdos. Certifique-se de atualizar o código do seu portal para a versão mais recente do repositório GitHub.

Caso tenha acedido pela primeira vez ao portal após o anúncio de disponibilidade geral em novembro de 2019, deverá já contar com o novo conteúdo predefinido e não é necessária qualquer outra ação.

### <a name="functionality-i-need-isnt-supported-in-the-portal"></a>A funcionalidade que preciso não é suportada no portal

Pode abrir um pedido de recurso no [repositório GitHub][1] ou [implementar a funcionalidade que falta.][3] Consulte a secção **Extensibilidade** acima para mais detalhes.

### <a name="how-can-i-automate-portal-deployments"></a><a id="automate"></a> Como posso automatizar as implementações do portal?

Pode aceder programaticamente e gerir o conteúdo do portal do desenvolvedor através da API REST, independentemente de utilizar uma versão gerida ou auto-hospedada.

A API está documentada na [secção wiki do repositório do GitHub.][2] Pode ser utilizado para automatizar migrações de conteúdos portais entre ambientes - por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre este processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) no GitHub.

### <a name="how-do-i-move-from-the-managed-to-the-self-hosted-version"></a>Como é que passo do gerido para a versão auto-hospedada?

Consulte o artigo detalhado na [secção Wiki do repositório do portal de desenvolvimento no GitHub][2].

### <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Posso ter vários portais de desenvolvimento num serviço de Gestão API?

Pode ter um portal gerido e vários portais auto-hospedados. O conteúdo de todos os portais está armazenado no mesmo serviço de Gestão da API, pelo que serão idênticos. Se quiser diferenciar a aparência e funcionalidade dos portais, pode auto-hospedar-se com os seus próprios widgets personalizados que personalizam dinamicamente as páginas no tempo de execução, por exemplo com base no URL.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal suporta modelos e/ou é compatível com o Kit de Recursos de DevOps de Gestão de API?

Não.

### <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>O conteúdo do portal é guardado com a funcionalidade de backup/restauro na Gestão da API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Preciso de permitir a conectividade adicional do VNet para as dependências do portal geridas?

Na maioria dos casos, não.

Se o seu serviço de Gestão API estiver num VNet interno, o seu portal de desenvolvedores só está acessível a partir da rede. O nome de anfitrião do ponto final de gestão deve resolver-se para o VIP interno do serviço a partir da máquina que utiliza para aceder à interface administrativa do portal. Certifique-se de que o ponto final de gestão está registado no DNS. Em caso de má configuração, verá um erro: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se o seu serviço de Gestão API estiver num VNet interno e estiver a aceder-lhe através do Gateway de Aplicação a partir da Internet, certifique-se de que permite a conectividade com o portal do desenvolvedor e os pontos finais de gestão da API Management. Pode ser necessário desativar as regras de Firewall de Aplicações Web. Consulte [este artigo de documentação](api-management-howto-integrate-internal-vnet-appgateway.md) para mais detalhes.

### <a name="i-have-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Atribuí um domínio de Gestão de API personalizado e o portal publicado não funciona

Depois de atualizar o domínio, tem de voltar a [publicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="i-have-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Adicionei um fornecedor de identidade e não consigo vê-lo no portal.

Depois de configurar um fornecedor de identidade (por exemplo, Azure AD, Azure AD B2C), precisa [de reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor. Certifique-se de que as páginas do portal do desenvolvedor incluem o widget de botões OAuth.

### <a name="i-have-set-up-delegation-and-the-portal-doesnt-use-it"></a>Criei uma delegação e o portal não a usa.

Depois de configurar a delegação, precisa [de reeditar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações entrem em vigor.

### <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>As minhas outras alterações de configuração da API Management não foram propagadas no portal do desenvolvedor

A maioria das alterações de configuração (por exemplo, VNet, iniciar sing-in, termos do produto) [requerem a republicagem do portal](api-management-howto-developer-portal-customize.md#publish).

### <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Estou a receber um erro de CORS ao usar a consola interativa

A consola interativa faz um pedido de API do lado do cliente do navegador. Resolva o problema do CORS adicionando [uma política CORS](api-management-cross-domain-policies.md#CORS) à sua API(s).

Pode verificar o estado da política CORS na secção **de visão geral** do Portal do seu serviço de Gestão API no portal Azure. Uma caixa de aviso indica uma política ausente ou mal configurada.

![Portal de desenvolvimento de gestão da API](media/api-management-howto-developer-portal/cors-azure-portal.png)

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
> O navegador emite automaticamente um pedido HTTP OPTIONS, que não contém um cabeçalho com a chave de subscrição. Devido à chave de subscrição em falta, a API Management não pode associar a chamada OPTIONS a um Produto, pelo que não pode aplicar a política do CORS.
>
> Como solução alternativa, pode passar a chave de subscrição num parâmetro de consulta.

> [!NOTE]
> 
> Apenas uma política do CORS é executada. Se especificou várias políticas DE CORS (por exemplo, no nível API e no nível de todas as APIs), a sua consola interativa pode não funcionar como esperado.

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
