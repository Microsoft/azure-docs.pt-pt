---
title: Traduzir links e URLs Azure AD App Proxy [ Procuração] Microsoft Docs
description: Cobre o básico sobre conectores de procuração de aplicação ad azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533697"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecione links hardcoded para aplicações publicadas com procuração de aplicação da AD Azure

A Aplicação Azure AD Proxy disponibiliza as suas aplicações no local para utilizadores que sejam remotos ou nos seus próprios dispositivos. Algumas aplicações, no entanto, foram desenvolvidas com links locais incorporados no HTML. Estes links não funcionam corretamente quando a aplicação é utilizada remotamente. Quando tem várias aplicações no local apontadas umas para as outras, os seus utilizadores esperam que os links continuem a funcionar quando não estão no escritório. 

A melhor maneira de garantir que as ligações funcionam da mesma forma dentro e fora da sua rede corporativa é configurar os URLs externos das suas apps para serem os mesmos que os seus URLs internos. Utilize [domínios personalizados](application-proxy-configure-custom-domain.md) para configurar os seus URLs externos para ter o seu nome de domínio corporativo em vez do domínio de proxy de aplicação predefinido.


Se não pode usar domínios personalizados no seu inquilino, existem várias outras opções para fornecer esta funcionalidade. Todos estes também são compatíveis com domínios personalizados e uns com os outros, para que possa configurar domínios personalizados e outras soluções, se necessário.

> [!NOTE]
> A tradução de ligações não é suportada para URLs internos de código duro gerados através do Javascript.

**Opção 1: Utilize o Navegador Gerido ou o Microsoft Edge** – Esta solução só é aplicável se pretender recomendar ou exigir que os utilizadores acedam à aplicação através do Navegador Gerido Intune ou do Microsoft Edge Browser. Tratará de todos os URLs publicados. 

**Opção 2: Utilize a extensão MyApps** – Esta solução requer que os utilizadores instalem uma extensão de navegador do lado do cliente, mas irá lidar com todos os URLs publicados e funcionará com os navegadores mais populares. 

**Opção 3: Utilize a definição de tradução de ligações** – Esta é uma definição lateral de administração que é invisível para os utilizadores. No entanto, só irá lidar com URLs em HTML e CSS.   

Estas três funcionalidades mantêm os seus links funcionando independentemente de onde os seus utilizadores estejam. Quando tiver aplicações que apontam diretamente para pontos finais internos ou portas, pode mapear estes URLs internos para os URLs de Procuração de Aplicação externos publicados. 

 
> [!NOTE]
> A última opção é apenas para os inquilinos que, por qualquer razão, não podem usar domínios personalizados para ter os mesmos URLs internos e externos para as suas apps. Antes de ativar esta funcionalidade, veja se [os domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md) podem funcionar para si. 
> 
> Ou, se a aplicação que precisa configurar com tradução de links é SharePoint, consulte [Configurar mapeamentos de acesso alternativos para o SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) para outra abordagem aos links de mapeamento. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Opção 1: Intune Managed Browser e Microsoft Edge Integration 

Pode utilizar o Navegador Gerido intune ou o Microsoft Edge para proteger ainda mais a sua aplicação e conteúdo. Para utilizar esta solução, é necessário exigir/recomendar aos utilizadores o acesso à aplicação através do Navegador Gerido intune. Todos os URLs internos publicados com Proxy de Aplicação serão reconhecidos pelo Navegador Gerido e redirecionados para o URL externo correspondente. Isto garante que todos os URLs internos codificados funcionam, e se um utilizador for ao navegador e for diretamente para o URL interno, funciona mesmo que o utilizador seja remoto.  

Para saber mais, incluindo como configurar esta opção, consulte a documentação do [Navegador Gerido.](https://docs.microsoft.com/intune/app-configuration-managed-browser)  

### <a name="option-2-myapps-browser-extension"></a>Opção 2: Extensão do navegador MyApps 

Com a extensão do navegador MyApps, todos os URLs internos publicados com Proxy de Aplicação são reconhecidos pela extensão e redirecionados para o URL externo correspondente. Isto garante que todos os URLs internos codificados funcionam, e se um utilizador for à barra de endereços do navegador e forre diretamente o URL interno, funciona mesmo que o utilizador esteja remoto.  

Para utilizar esta funcionalidade, o utilizador precisa de descarregar a extensão e ser iniciado. Não é necessária outra configuração para administradores ou utilizadores. 

Para saber mais, incluindo como configurar esta opção, consulte a documentação de extensão do [navegador MyApps.](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension)

### <a name="option-3-link-translation-setting"></a>Opção 3: Definição de tradução de ligações 

Quando a tradução de links está ativada, o serviço Proxy de Aplicação procura através de HTML e CSS para ligações internas publicadas e traduz-os para que os seus utilizadores obtem uma experiência ininterrupta. A utilização da extensão do navegador MyApps é preferível à Definição de Tradução de Link, uma vez que proporciona uma experiência mais performativa aos utilizadores.

> [!NOTE]
> Se estiver a utilizar a opção 2 ou 3, apenas uma destas deve ser ativada de cada vez.

## <a name="how-link-translation-works"></a>Como funciona a tradução de ligações

Após a autenticação, quando o servidor proxy passa os dados da aplicação para o utilizador, o Application Proxy digitaliza a aplicação para links codificados e substitui-os pelos respetivos URLs externos publicados.

Aplicação Proxy assume que as aplicações estão codificadas em UTF-8. Se não for esse o caso, especifique o `Content-Type:text/html;charset=utf-8`tipo de codificação num cabeçalho de resposta http, como .

### <a name="which-links-are-affected"></a>Que ligações são afetadas?

A função de tradução de links apenas procura links que estão em etiquetas de código no corpo de uma aplicação. Application Proxy tem uma característica separada para traduzir cookies ou URLs em cabeçalhos. 

Existem dois tipos comuns de ligações internas em aplicações no local:

- **Ligações internas relativas** que apontam para um `/claims/claims.html`recurso partilhado numa estrutura de ficheiros local como . Estes links funcionam automaticamente em aplicações que são publicadas através do Application Proxy, e continuam a trabalhar com ou sem tradução de links. 
- **Ligações internas codificadas** a outras `http://expenses` aplicações no `http://expenses/logo.jpg`local, como ou ficheiros publicados como . A função de tradução de links funciona em ligações internas codificadas e altera-as para apontar para os URLs externos que os utilizadores remotos precisam de passar.

A lista completa de etiquetas de código HTML para as quais o Application Proxy suporta a tradução de ligações incluem:
* a
* áudio
* base
* .
* div
* Incorporar
* forma
* quadro
* cabeça
* html
* iframe
* img
* entrada
* associar
* menuitem
* meta
* objeto
* .
* source
* controlar
* vídeo

Adicionalmente, dentro do CSS o atributo URL também é traduzido.

### <a name="how-do-apps-link-to-each-other"></a>Como é que as aplicações se ligam umas às outras?

A tradução de link está ativada para cada aplicação, de modo a que tenha controlo sobre a experiência do utilizador ao nível por app. Ligue a tradução de links para uma aplicação quando quiser que os links *dessa* aplicação sejam traduzidos, e não links *para* essa aplicação. 

Por exemplo, suponha que tem três aplicações publicadas através do Application Proxy que todas se ligam entre si: Benefícios, Despesas e Viagens. Há uma quarta aplicação, Feedback, que não é publicada através do Application Proxy.

Quando permite a tradução de links para a aplicação Benefits, as ligações para Despesas e Viagens são redirecionadas para os URLs externos para essas aplicações, mas o link para Feedback não é redirecionado porque não existe URL externo. As ligações das Despesas e viagens de volta aos Benefícios não funcionam, porque a tradução de links não foi ativada para essas duas aplicações.

![Links de Benefícios para outras apps quando a tradução de links está ativada](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Que ligações não são traduzidas?

Para melhorar o desempenho e a segurança, algumas ligações não são traduzidas:

- Ligações não dentro de etiquetas de código. 
- Ligações não em HTML ou CSS. 
- Links em formato codificado por URL.
- Ligações internas abertas de outros programas. Os links enviados por e-mail ou mensagem instantânea, ou incluídos em outros documentos, não serão traduzidos. Os utilizadores precisam de saber para ir ao URL externo.

Se precisar de suportar um destes dois cenários, utilize os mesmos URLs internos e externos em vez de tradução de ligação.  

## <a name="enable-link-translation"></a>Ativar a tradução de ligações

Começar com a tradução de link é tão fácil como clicar num botão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Vá a**aplicações** >  **Azure Ative Directory** > Enterprise**Todas as aplicações** > selecionar a aplicação que pretende gerir > procuração de **aplicações.**
3. Vire **urLs de tradução no corpo** de aplicação para **Sim**.

   ![Selecione Sim para traduzir URLs no corpo de aplicação](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selecione **Guardar** para aplicar as suas alterações.

Agora, quando os seus utilizadores acederem a esta aplicação, o representante irá automaticamente procurar URLs internos que tenham sido publicados através do Application Proxy no seu inquilino.

## <a name="send-feedback"></a>Enviar comentários

Queremos que esta funcionalidade funcione para todas as suas aplicações. Procuramos mais de 30 tags em HTML e CSS. Se tiver um exemplo de links gerados que não estão a ser traduzidos, envie um código para application [Proxy Feedback](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passos seguintes
[Utilize domínios personalizados com procuração de aplicação AD Azure](application-proxy-configure-custom-domain.md) para ter o mesmo URL interno e externo

[Configure mapeamentos de acesso alternativos para SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
