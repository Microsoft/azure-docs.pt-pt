---
title: Traduzir links e URLs Azure AD App Proxy | Microsoft Docs
description: Saiba como redirecionar links codificados para apps publicadas com o Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44266f7a7485b44ba60a27aadd7422e8d1c3acf4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259411"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecione links codificados para apps publicadas com Proxy de aplicações AD AZure

O Azure AD Application Proxy disponibiliza as suas aplicações no local para utilizadores que sejam remotos ou nos seus próprios dispositivos. Algumas aplicações, no entanto, foram desenvolvidas com ligações locais incorporadas no HTML. Estas ligações não funcionam corretamente quando a aplicação é utilizada remotamente. Quando tem várias aplicações no local que apontam umas para as outras, os seus utilizadores esperam que os links continuem a funcionar quando não estão no escritório. 

A melhor maneira de garantir que as ligações funcionam da mesma forma dentro e fora da sua rede corporativa é configurar os URLs externos das suas apps para serem os mesmos que os seus URLs internos. Utilize [domínios personalizados](application-proxy-configure-custom-domain.md) para configurar os seus URLs externos para ter o seu nome de domínio corporativo em vez do domínio proxy de aplicação padrão.


Se não puder utilizar domínios personalizados no seu inquilino, existem várias outras opções para fornecer esta funcionalidade. Todos estes são também compatíveis com domínios personalizados e uns aos outros, para que possa configurar domínios personalizados e outras soluções, se necessário.

> [!NOTE]
> A tradução de link não é suportada para URLs internos codificados gerados através do JavaScript.

**Opção 1: Utilizar o Microsoft Edge** – Esta solução só é aplicável se pretender recomendar ou exigir que os utilizadores acedam à aplicação através do navegador Microsoft Edge. Vai lidar com todos os URLs publicados. 

**Opção 2: Utilizar a Extensão do MyApps** – Esta solução requer que os utilizadores instalem uma extensão do navegador do lado do cliente, mas irá lidar com todos os URLs publicados e funciona com os navegadores mais populares. 

**Opção 3: Utilize a definição de tradução de ligação** – Esta é uma definição lateral de administração que é invisível para os utilizadores. No entanto, só manuseará URLs em HTML e CSS.   

Estas três funcionalidades mantêm as suas ligações a funcionar independentemente de onde os seus utilizadores estejam. Quando tiver aplicações que apontam diretamente para pontos finais internos ou portas, pode mapear estes URLs internos para os URLs de procuração de aplicações externas publicados. 

 
> [!NOTE]
> A última opção é apenas para os inquilinos que, por qualquer razão, não podem usar domínios personalizados para ter os mesmos URLs internos e externos para as suas apps. Antes de ativar esta funcionalidade, consulte se [os domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md) podem funcionar para si. 
> 
> Ou, se a aplicação que precisa de configurar com a tradução de links for o SharePoint, consulte [os mapeamentos de acesso alternativos Configure para o SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings) para outra abordagem às ligações de mapeamento. 

 
### <a name="option-1-microsoft-edge-integration"></a>Opção 1: Integração microsoft edge 

Pode utilizar o Microsoft Edge para proteger ainda mais a sua aplicação e conteúdo. Para utilizar esta solução, é necessário exigir/recomendar aos utilizadores o acesso à aplicação através do Microsoft Edge. Todos os URLs internos publicados com Application Proxy serão reconhecidos pela Edge e redirecionados para o URL externo correspondente. Isto garante que todos os URLs internos codificados funcionam, e se um utilizador for para o navegador e apresentar diretamente o URL interno, funciona mesmo que o utilizador seja remoto.  

Para saber mais, incluindo como configurar esta opção, consulte o [acesso web Manage usando o Edge para iOS e Android com documentação Microsoft Intune.](/mem/intune/apps/manage-microsoft-edge)  

### <a name="option-2-myapps-browser-extension"></a>Opção 2: Extensão do navegador MyApps 

Com a extensão do navegador MyApps, todos os URLs internos publicados com Application Proxy são reconhecidos pela extensão e redirecionados para o URL externo correspondente. Isto garante que todos os URLs internos codificados funcionam, e se um utilizador for à barra de endereços do navegador e apresentar diretamente o URL interno, funciona mesmo que o utilizador seja remoto.  

Para utilizar esta funcionalidade, o utilizador necessita de descarregar a extensão e iniciar sessão. Não há outra configuração necessária para administradores ou para os utilizadores. 

Para saber mais, incluindo como configurar esta opção, consulte a documentação da Extensão do [Navegador MyApps.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)

### <a name="option-3-link-translation-setting"></a>Opção 3: Definição de tradução de ligação 

Quando a tradução de links está ativada, o serviço Application Proxy procura através de HTML e CSS para ligações internas publicadas e traduz-as para que os seus utilizadores obtenham uma experiência ininterrupta. A utilização da extensão do navegador MyApps é preferível à Definição de Tradução de Ligação, uma vez que proporciona uma experiência mais performante aos utilizadores.

> [!NOTE]
> Se estiver a utilizar a opção 2 ou 3, apenas uma delas deve ser ativada de cada vez.

## <a name="how-link-translation-works"></a>Como funciona a tradução de ligação

Após a autenticação, quando o servidor proxy passa os dados da aplicação para o utilizador, o Application Proxy verifica a aplicação de links codificados e substitui-os pelos respetivos URLs externos publicados.

O Application Proxy assume que as aplicações estão codificadas na UTF-8. Se não for esse o caso, especifique o tipo de codificação num cabeçalho de resposta HTTP, como `Content-Type:text/html;charset=utf-8` .

### <a name="which-links-are-affected"></a>Que ligações são afetadas?

A funcionalidade de tradução de links procura apenas links que estejam em etiquetas de código no corpo de uma aplicação. O Application Proxy tem uma característica separada para traduzir cookies ou URLs em cabeçalhos. 

Existem dois tipos comuns de ligações internas em aplicações no local:

- **Ligações internas relativas** que apontam para um recurso partilhado numa estrutura de ficheiros local como `/claims/claims.html` . Estes links funcionam automaticamente em aplicações que são publicadas através do Application Proxy, e continuam a trabalhar com ou sem tradução de links. 
- **Ligações internas codificadas com códigos rígidos** para outras aplicações no local, como `http://expenses` ou ficheiros publicados como `http://expenses/logo.jpg` . A funcionalidade de tradução de links funciona em ligações internas codificadas e altera-as para apontar para os URLs externos que os utilizadores remotos precisam de passar.

A lista completa de atributos em tags de código HTML que o Application Proxy suporta a tradução de ligação para incluir:
* a (href)
* áudio (src)
* base (href)
* botão (formação)
* div (dados-fundo, estilo, data-src)
* incorporado (src)
* forma (ação)
* quadro (src)
* cabeça (perfil)
* html (manifesto)
* iframe (longdesc, src)
* img (longdesc, src)
* entrada (formação, src, valor)
* ligação (href)
* menuitem (ícone)
* meta (conteúdo)
* objeto (arquivo, dados, base de código)
* script (src)
* fonte (src)
* pista (src)
* vídeo (src, cartaz)

Adicionalmente, dentro de CSS o atributo URL também é traduzido.

### <a name="how-do-apps-link-to-each-other"></a>Como é que as aplicações se ligam entre si?

A tradução de link está ativada para cada aplicação, de modo a ter controlo sobre a experiência do utilizador ao nível por aplicação. Ligue a tradução de links para uma aplicação quando quiser que os links *dessa* aplicação sejam traduzidos, e não links *para* essa app. 

Por exemplo, suponha que tem três aplicações publicadas através do Application Proxy que todos se ligam entre si: Benefícios, Despesas e Viagens. Há uma quarta aplicação, Feedback, que não é publicada através da Application Proxy.

Quando ativa a tradução de links para a aplicação Benefits, as ligações a Despesas e Viagens são redirecionadas para os URLs externos para essas aplicações, mas a ligação ao Feedback não é redirecionada porque não existe URL externo. As ligações entre despesas e viagens de volta a Benefícios não funcionam, porque a tradução de links não foi ativada para essas duas aplicações.

![Links de Benefícios para outras aplicações quando a tradução de links está ativada](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Que ligações não são traduzidas?

Para melhorar o desempenho e a segurança, algumas ligações não são traduzidas:

- Ligações não dentro de etiquetas de código. 
- Links não em HTML ou CSS. 
- Links em formato codificado por URL.
- Ligações internas abertas a partir de outros programas. Os links enviados por e-mail ou mensagem instantânea, ou incluídos em outros documentos, não serão traduzidos. Os utilizadores precisam de saber para irem para o URL externo.

Se precisar de suportar um destes dois cenários, utilize os mesmos URLs internos e externos em vez de tradução de ligações.  

## <a name="enable-link-translation"></a>Ativar a tradução de ligações

Começar com a tradução de link é tão fácil como clicar num botão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Vá a aplicações **Azure Ative Directory**  >  **Enterprise**  >  **Todas as aplicações** > selecione a aplicação que pretende gerir > **Procuração de Aplicação**.
3. Rode **os URLs de tradução no corpo de aplicação** para **Sim**.

   ![Selecione Sim para traduzir URLs no corpo de aplicação](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selecione **Guardar** para aplicar as suas alterações.

Agora, quando os seus utilizadores acederem a esta aplicação, o proxy irá automaticamente procurar URLs internos que tenham sido publicados através do Application Proxy no seu inquilino.

## <a name="send-feedback"></a>Enviar comentários

Queremos que a sua ajuda faça com que esta funcionalidade funcione para todas as suas aplicações. Procuramos mais de 30 tags em HTML e CSS. Se tiver um exemplo de links gerados que não estão a ser traduzidos, envie um código para [o Feedback do Procuração de Aplicações](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passos seguintes
[Utilize domínios personalizados com Proxy de aplicação AD Azure](application-proxy-configure-custom-domain.md) para ter o mesmo URL interno e externo

[Configurar mapeamentos de acesso alternativo para SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings)
