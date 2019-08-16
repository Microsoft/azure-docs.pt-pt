---
title: Converter links e URLs Aplicativo Azure AD proxy | Microsoft Docs
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533697"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecionar links codificados para aplicativos publicados com o Azure Proxy de Aplicativo do AD

O Azure Proxy de Aplicativo do AD torna seus aplicativos locais disponíveis para usuários que são remotos ou em seus próprios dispositivos. Alguns aplicativos, no entanto, foram desenvolvidos com links locais inseridos no HTML. Esses links não funcionam corretamente quando o aplicativo é usado remotamente. Quando você tem vários aplicativos locais apontam um para o outro, os usuários esperam que os links continuem funcionando quando não estão no escritório. 

A melhor maneira de garantir que os links funcionem da mesma forma dentro e fora de sua rede corporativa é configurar as URLs externas de seus aplicativos para serem as mesmas que suas URLs internas. Use [domínios personalizados](application-proxy-configure-custom-domain.md) para configurar suas URLs externas para ter seu nome de domínio corporativo em vez do domínio de proxy de aplicativo padrão.


Se você não puder usar domínios personalizados em seu locatário, há várias outras opções para fornecer essa funcionalidade. Todos eles também são compatíveis com domínios personalizados e outros, para que você possa configurar domínios personalizados e outras soluções, se necessário.

> [!NOTE]
> A conversão de link não tem suporte para URLs internas embutidas em código geradas por meio de JavaScript.

**Opção 1: Use o Managed browser ou o Microsoft** Edge – essa solução só será aplicável se você planeja recomendar ou exigir que os usuários acessem o aplicativo por meio do navegador Intune Managed browser ou Microsoft Edge. Ele tratará todas as URLs publicadas. 

**Opção 2: Usar a extensão** myapps – essa solução exige que os usuários instalem uma extensão de navegador do lado do cliente, mas ele manipulará todas as URLs publicadas e funcionará com os navegadores mais populares. 

**Opção 3: Use a configuração** de conversão de link – essa é uma configuração do lado do administrador que é invisível para os usuários. No entanto, ele só tratará URLs em HTML e CSS.   

Esses três recursos mantêm seus links funcionando independentemente de onde estão os usuários. Quando você tem aplicativos que apontam diretamente para pontos de extremidade internos ou portas, você pode mapear essas URLs internas para as URLs de proxy de aplicativo externo publicadas. 

 
> [!NOTE]
> A última opção é apenas para locatários que, por qualquer motivo, não podem usar domínios personalizados para ter as mesmas URLs internas e externas para seus aplicativos. Antes de habilitar esse recurso, confira se os [domínios personalizados no Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md) podem funcionar para você. 
> 
> Ou, se o aplicativo que você precisa configurar com a conversão de links for o SharePoint, consulte [configurar mapeamentos alternativos de acesso para o sharepoint 2013](https://technet.microsoft.com/library/cc263208.aspx) para obter outra abordagem de mapeamento de links. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Opção 1: Integração do Intune Managed Browser e do Microsoft Edge 

Você pode usar o Intune Managed Browser ou o Microsoft Edge para proteger ainda mais seu aplicativo e conteúdo. Para usar essa solução, você precisa exigir/recomendar que os usuários acessem o aplicativo por meio do Intune Managed Browser. Todas as URLs internas publicadas com o proxy de aplicativo serão reconhecidas pelo Managed Browser e redirecionadas para a URL externa correspondente. Isso garante que todas as URLs internas embutidas em código funcionem, e se um usuário ir para o navegador e digitar diretamente a URL interna, ele funcionará mesmo se o usuário for remoto.  

Para saber mais, incluindo como configurar essa opção, consulte a documentação do [Managed browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) .  

### <a name="option-2-myapps-browser-extension"></a>Opção 2: Extensão de navegador do myapps 

Com a extensão do navegador myapps, todas as URLs internas publicadas com o proxy de aplicativo são reconhecidas pela extensão e redirecionadas para a URL externa correspondente. Isso garante que todas as URLs internas embutidas em código funcionem, e se um usuário ir para a barra de endereços do navegador e digitar diretamente a URL interna, ele funcionará mesmo que o usuário seja remoto.  

Para usar esse recurso, o usuário precisa baixar a extensão e estar conectado. Não há nenhuma outra configuração necessária para administradores ou usuários. 

Para saber mais, incluindo como configurar essa opção, confira a documentação da [extensão do navegador myapps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Opção 3: Configuração de conversão de link 

Quando a conversão de link está habilitada, o serviço de proxy de aplicativo pesquisa o HTML e o CSS em busca de links internos publicados e os traduz para que os usuários obtenham uma experiência ininterrupta. Usar a extensão de navegador myapps é preferencial para a configuração de conversão de link, pois oferece uma experiência mais eficaz aos usuários.

> [!NOTE]
> Se você estiver usando a opção 2 ou 3, apenas uma delas deverá ser habilitada por vez.

## <a name="how-link-translation-works"></a>Como funciona a tradução de link

Após a autenticação, quando o servidor proxy passa os dados do aplicativo para o usuário, o proxy de aplicativo examina o aplicativo em busca de links codificados e os substitui por suas respectivas URLs externas publicadas.

O proxy de aplicativo pressupõe que os aplicativos são codificados em UTF-8. Se esse não for o caso, especifique o tipo de codificação em um cabeçalho de resposta http `Content-Type:text/html;charset=utf-8`, como.

### <a name="which-links-are-affected"></a>Quais links são afetados?

O recurso de conversão de link só procura links que estejam em marcas de código no corpo de um aplicativo. O proxy de aplicativo tem um recurso separado para converter cookies ou URLs em cabeçalhos. 

Há dois tipos comuns de links internos em aplicativos locais:

- **Links internos relativos** que apontam para um recurso compartilhado em uma estrutura de arquivo `/claims/claims.html`local como. Esses links funcionam automaticamente em aplicativos que são publicados por meio do proxy de aplicativo e continuam a funcionar com ou sem conversão de link. 
- **Links internos embutidos em código** para outros aplicativos locais como `http://expenses` ou arquivos publicados como `http://expenses/logo.jpg`. O recurso de conversão de link funciona em links internos embutidos em código e os altera para apontar para as URLs externas que os usuários remotos precisam seguir.

A lista completa de marcas de código HTML que o proxy de aplicativo dá suporte à conversão de links para o incluem:
* a
* sonoro
* polybase
* Button
* div
* Incorporar
* isolada
* quadro
* principal
* html
* iframe
* img
* entrada
* ligação
* MenuItem
* volume
* object
* script
* source
* controles
* vídeo

Além disso, no CSS, o atributo URL também é traduzido.

### <a name="how-do-apps-link-to-each-other"></a>Como os aplicativos são vinculados entre si?

A conversão de link está habilitada para cada aplicativo, para que você tenha controle sobre a experiência do usuário no nível por aplicativo. Ative a conversão de link para um aplicativo quando desejar que os links *desse* aplicativo sejam convertidos, não os links *para* esse aplicativo. 

Por exemplo, suponha que você tenha três aplicativos publicados por meio do proxy de aplicativo que todos se vinculam entre si: Benefícios, despesas e viagens. Há um quarto aplicativo, comentários, que não é publicado por meio do proxy de aplicativo.

Quando você habilita a tradução de links para o aplicativo benefícios, os links para despesas e viagens são redirecionados para as URLs externas para esses aplicativos, mas o link para comentários não é redirecionado porque não há nenhuma URL externa. Os links de despesas e viagens de volta para os benefícios não funcionam, pois a conversão de links não foi habilitada para esses dois aplicativos.

![Links de benefícios para outros aplicativos quando a tradução de links está habilitada](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quais links não são traduzidos?

Para melhorar o desempenho e a segurança, alguns links não são traduzidos:

- Links que não estão dentro de marcas de código. 
- Links que não estão em HTML ou CSS. 
- Links no formato codificado por URL.
- Links internos abertos de outros programas. Os links enviados por email ou mensagem instantânea ou incluídos em outros documentos não serão traduzidos. Os usuários precisam saber para ir para a URL externa.

Se você precisar dar suporte a um desses dois cenários, use as mesmas URLs internas e externas em vez da conversão de link.  

## <a name="enable-link-translation"></a>Habilitar conversão de link

A introdução à conversão de links é tão fácil quanto clicar em um botão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Vá para **Azure Active Directory** > **aplicativos** > empresariais**todos os aplicativos** > selecione o aplicativo que você deseja gerenciar > **proxy de aplicativo**.
3. Transforme a **conversão de URLs no corpo do aplicativo** em **Sim**.

   ![Selecione Sim para converter URLs no corpo do aplicativo](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selecione **salvar** para aplicar suas alterações.

Agora, quando os usuários acessarem esse aplicativo, o proxy verificará automaticamente as URLs internas que foram publicadas por meio do proxy de aplicativo em seu locatário.

## <a name="send-feedback"></a>Enviar feedback

Queremos que sua ajuda faça com que esse recurso funcione para todos os seus aplicativos. Pesquisamos mais de 30 marcas em HTML e CSS. Se você tiver um exemplo de links gerados que não estão sendo traduzidos, envie um trecho de código para os [comentários do proxy de aplicativo](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passos seguintes
[Usar domínios personalizados com o Azure proxy de aplicativo do AD](application-proxy-configure-custom-domain.md) para ter a mesma URL interna e externa

[Configurar mapeamentos de acesso alternativos para o SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
