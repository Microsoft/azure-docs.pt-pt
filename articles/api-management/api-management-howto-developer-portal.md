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
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1eef7b6505c4800acbe8aa69cf6f17eecc503aed
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796012"
---
# <a name="azure-api-management-developer-portal-overview"></a>Visão geral do portal do desenvolvedor do gerenciamento de API do Azure

O portal do desenvolvedor é um site da Web, totalmente personalizado e gerado com a documentação de suas APIs. É aí que os consumidores de API podem descobrir suas APIs, saber como usá-las, solicitar acesso e experimentá-las.

Este artigo descreve as diferenças entre as versões autohospedadas e gerenciadas do portal do desenvolvedor no gerenciamento de API. Ele também explica sua arquitetura e fornece respostas para perguntas frequentes.

> [!WARNING]
> O novo portal do desenvolvedor está sendo lançado no momento nos serviços de gerenciamento de API.
> Se o serviço for criado recentemente ou for um serviço de camada de desenvolvedor, você já deverá ter a versão mais recente. Caso contrário, você poderá ter problemas (por exemplo, com a funcionalidade de publicação). A distribuição de recursos será concluída na segunda-feira de novembro de 11, 2019.
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

### <a name="has-the-old-portal-been-deprecated"></a>O portal antigo foi preterido?

Os portais antigos do desenvolvedor e do Publicador agora são recursos *herdados* . eles receberão apenas atualizações de segurança. Novos recursos serão implementados somente no novo portal do desenvolvedor.

A substituição dos portais herdados será anunciada separadamente. Se você tiver dúvidas, preocupações ou comentários, gere-os [em um problema dedicado do GitHub](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Como posso automatizar as implantações do portal?

Você pode acessar e gerenciar programaticamente o conteúdo do portal do desenvolvedor por meio da API REST, independentemente de estar usando uma versão gerenciada ou hospedada internamente.

A API está documentada na [seção wiki do repositório do GitHub][2]. Ele também pode ser usado para automatizar migrações de conteúdo do portal entre ambientes, por exemplo, de um ambiente de teste para o ambiente de produção. Você pode saber mais sobre esse processo [neste artigo de documentação](https://aka.ms/apimdocs/migrateportal) sobre o github.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>O portal dá suporte a modelos de Azure Resource Manager e/ou é compatível com o DevOps Resource Kit de gerenciamento de API?

Não.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>É necessário habilitar a conectividade VNET adicional para as dependências do portal gerenciado?

Não.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>Estou recebendo um erro de CORS ao usar o console interativo. O que devo fazer?

O console interativo faz uma solicitação de API do lado do cliente do navegador. Você pode resolver o problema de CORS adicionando [uma política de CORS](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) em suas API (s). Você pode especificar todos os parâmetros manualmente ou usar valores de `*` curinga. Por exemplo:

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o novo portal do desenvolvedor:

- [Acessar e personalizar o portal do desenvolvedor gerenciado](api-management-howto-developer-portal-customize.md)
- [Configurar a versão hospedada internamente do portal][2]

Procurar outros recursos:

- [Repositório GitHub com o código-fonte][1]
- [Roteiro público do projeto][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects