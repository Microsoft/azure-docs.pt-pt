---
title: Traduzir links e URLs do Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Abrange as noções básicas sobre os conectores de Proxy de aplicações do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c010a7bcd2d811b31d9c2d05e81cce5dc85c2ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118600"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Redirecionar os links codificados para as aplicações publicadas com o Proxy de aplicações do Azure AD

Proxy de aplicações do Azure AD torna as aplicações no local disponível para os utilizadores que são remotos ou em seus próprios dispositivos. No entanto, algumas aplicações, foram desenvolvidas com ligações locais incorporadas no HTML. Estas ligações não funcionam corretamente quando a aplicação seja utilizada remota. Quando tiver várias aplicações no local, aponte para si, os usuários esperam os links para continuar a trabalhar quando não estiverem no escritório. 

É a melhor forma para se certificar de que as ligações funcionam da mesma dentro e fora da rede empresarial configurar os URLs externos das suas aplicações para ser o mesmo que suas URLs internos. Uso [domínios personalizados](application-proxy-configure-custom-domain.md) para configurar os URLs externos para que o seu nome de domínio empresariais em vez do domínio de proxy de aplicação do padrão.


Se não conseguir utilizar domínios personalizados no seu inquilino, existem várias outras opções para fornecer essa funcionalidade. Todos esses também são compatíveis com domínios personalizados e entre si, pelo que pode configurar domínios personalizados e outras soluções, se necessário. 

**Opção 1: Utilizar o Managed Browser** – esta solução só é aplicável se planeja recomendável ou exigir que os utilizadores acedem à aplicação através do Browser gerido do Intune. Ele tratará todas as URLs publicadas. 

**Opção 2: Utilizar a extensão My Apps** – esta solução requer que os utilizadores instalem uma extensão de browser do lado do cliente, mas vai processar URLs publicados tudo e funciona com navegadores mais populares. 

**Opção 3: Utilizar a definição de conversão de link** – esta é uma definição de lado de admin é invisível para os utilizadores. No entanto, só irá processar URLs em HTML e CSS. URLs internos hard-coded geradas por meio de Javascript (por exemplo) não funcionará.  

Desses três recursos mantêm as suas ligações, independentemente de onde os seus utilizadores estiverem a funcionar. Quando tiver aplicações que apontem diretamente para pontos finais internos ou as portas, pode mapear estes URLs internos para os URLs de Proxy de aplicação externa publicados. 

 
> [!NOTE]
> A última opção é apenas para inquilinos que, por qualquer motivo, não é possível utilizar domínios personalizados para que os mesmos URLs internos e externos para seus aplicativos. Antes de ativar esta funcionalidade, veja se [domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md) pode ser útil para. 
> 
> Ou, se a aplicação terá de configurar com ligação a tradução é o SharePoint, consulte [configurar mapeamentos de acesso alternativo para SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) para outra abordagem para ligações de mapeamento. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Opção 1: Integração de Browser gerido do Intune 

Pode utilizar o Intune Managed Browser para proteger ainda mais a sua aplicação e o conteúdo. Para utilizar esta solução, precisa exigir/Recomendamos que os utilizadores acedam a aplicação através do Intune Managed Browser. Todos os URL internos publicados com o Proxy de aplicações serão reconhecidos pelo Managed Browser e redirecionados para o URL externo correspondente. Isto garante que todos os hard-coded internos URLs de trabalho e, se um usuário vai para o navegador e tipos diretamente o URL interno, funciona mesmo que o utilizador é remoto.  

Para obter mais informações, incluindo como configurar esta opção, consulte a [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) documentação.  

### <a name="option-2-myapps-browser-extension"></a>Opção 2: Extensão do Browser de My Apps 

Com a extensão de Browser MyApps, todos os URL internos publicados com o Proxy de aplicações são reconhecidos pela extensão e redirecionados para o URL externo correspondente. Isto garante que todos os hard-coded internos URLs de trabalho e, se um usuário vai para a barra de endereço do browser e tipos diretamente o URL interno, funciona mesmo que o utilizador é remoto.  

Para utilizar esta funcionalidade, o utilizador tem de transferir a extensão e de ter sessão iniciada. Não existe nenhuma outra configuração necessária para os administradores ou pelos utilizadores. 

 

### <a name="option-3-link-translation-setting"></a>Opção 3: Definição de tradução de ligação 

Quando a conversão de link está ativada, o serviço de Proxy de aplicações pesquisa por meio de HTML e CSS publicados links internos e converte-os para que os utilizadores obtêm uma experiência sem interrupções. 



## <a name="how-link-translation-works"></a>Como ligar funciona de tradução

Após a autenticação, quando o servidor proxy passa os dados de aplicação para o utilizador, o Proxy de aplicações analisa o aplicativo para links codificados e substitui-los com seus respectivos publicado URLs externos.

Proxy de aplicações parte do princípio de que os aplicativos são codificados em UTF-8. Se não for esse o caso, especifique o tipo de codificação num cabeçalho de resposta http, como `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Quais as ligações são afetadas?

A funcionalidade de conversão de link procura apenas ligações que estão em etiquetas de código no corpo de uma aplicação. Proxy de aplicações tem um recurso separado para traduzir cookies ou URLs nos cabeçalhos. 

Existem dois tipos comuns de ligações internas em aplicações no local:

- **Ligações internas relativas** que aponte para um recurso compartilhado numa estrutura de arquivos local como `/claims/claims.html`. Esses links funcionam automaticamente nas aplicações que são publicadas através do Proxy de aplicações e continuam a trabalhar com ou sem a conversão de link. 
- **Links de internos codificado** para outras aplicações no local, como `http://expenses` ou publicado arquivos como `http://expenses/logo.jpg`. A funcionalidade de conversão de link funciona nos links internos de embutidos em código e é alterado para que apontem para os URLs externos que os usuários remotos precisam passar pelo.

### <a name="how-do-apps-link-to-each-other"></a>Como os aplicativos ligar entre si?

Conversão de link está ativada para cada aplicativo, para que tem controle sobre a experiência do usuário no nível por aplicação. Ativar a conversão de link para uma aplicação quando pretender que os links *partir* essa aplicação para ser convertido, não liga *para* essa aplicação. 

Por exemplo, suponha que tem três aplicações publicadas através do Proxy de aplicações que todos ligar entre si: Vantagens, as despesas e viagens. Existe uma quarta aplicação, comentários, que não não publicado através do Proxy de aplicações.

Ao ativar a conversão de link para a aplicação de benefícios, as ligações para as despesas e viagens são redirecionadas para os URLs externos para essas aplicações, mas a ligação para comentários não for redirecionada porque não existe nenhuma URL externo. Links de despesas e viagem para a benefícios não funcionam, porque não foi ativada a conversão de link para essas duas aplicações.

![Ligações a partir de benefícios para outras aplicações quando a conversão de link está ativada](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Quais as ligações que não são traduzidas?

Para melhorar o desempenho e a segurança, não são traduzidos alguns links:

- Ligações não dentro de etiquetas de código. 
- Ligações não no HTML ou CSS. 
- Links no formato com codificação URL.
- Ligações internas abertas a partir de outros programas. Links enviados por e-mail ou por mensagem instantânea ou incluídos em outros documentos, não ser traduzido. Os usuários precisarão saber para ir para o URL externo.

Se tiver de oferecer suporte a um desses dois cenários, utilize os mesmos URLs internos e externos em vez de conversão de link.  

## <a name="enable-link-translation"></a>Ativar a conversão de link

Começar a utilizar com a conversão de link é tão fácil quanto clicar num botão:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Aceda a **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** > selecione a aplicação que pretende gerir >  **Proxy de aplicações**.
3. Ative **traduzir URLs no corpo do aplicativo** ao **Sim**.

   ![Selecione Sim para traduzir URLs no corpo de aplicação](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Selecione **guardar** para aplicar as alterações.

Agora, quando os utilizadores acedem a esta aplicação, o proxy irá analisar automaticamente para URLs internos que foram publicadas através do Proxy de aplicações no seu inquilino.

## <a name="send-feedback"></a>Enviar comentários

Queremos que a sua ajuda para tornar esta funcionalidade funciona para todas as suas aplicações. Vamos procurar mais de 30 etiquetas em HTML e CSS. Se tiver um exemplo de ligações geradas que não estão a ser traduzidas, envie um trecho de código para [comentários de Proxy de aplicações](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Passos Seguintes
[Utilizar domínios personalizados com o Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md) para ter o mesmo URL interno e externo

[Configurar mapeamentos de acesso alternativo para SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
