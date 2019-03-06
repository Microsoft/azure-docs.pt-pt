---
title: Como atribuir utilizadores e grupos a uma aplicação | Documentos da Microsoft
description: Atribuir utilizadores à aplicação para conceder acesso
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
ms.date: 10/01/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38a8cac6508777abe676e243496c792f87131483
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432299"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Atribuir utilizadores e grupos a uma aplicação no Azure Active Directory
Este artigo mostra-lhe como atribuir utilizadores ou grupos a uma aplicação no Azure Active Directory (Azure AD). Os usuários primeiro devem ser atribuídos a uma aplicação antes de um administrador pode conceder-lhes acesso para fazer o seguinte:

-   Aceder a uma aplicação por **navegar diretamente para o URL da aplicação** (também conhecido como iniciado por SP início de sessão).

-   Aceder a uma aplicação utilizando o **URL de acesso de utilizador** num aplicativo **propriedades** página (também conhecido como IDP iniciada pelo início de sessão).

-   Ver uma aplicação que aparecem no seu [painel de acesso de aplicação](https://myapps.microsoft.com/) ou aplicações móveis.

-   Veja um aplicativo são apresentadas na respetiva [iniciador de aplicações do Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="prerequisites"></a>Pré-requisitos
Antes de poder atribuir utilizadores e grupos a uma aplicação, deve exigir a atribuição de utilizadores. Para exigir a atribuição de utilizadores:

1. Inicie sessão no portal do Azure com uma conta de administrador.
2. Clique nas **todos os serviços** item no menu principal.
3. Escolha o diretório que está a utilizar para a aplicação.
4. Clique nas **aplicações empresariais** separador.
5. Selecione a aplicação a partir da lista de aplicações associadas este diretório.
6. Clique nas **propriedades** separador.
7. Alteração da **atribuição utiliz. necessária?** alternância como Yes.
8. Clique nas **guardar** botão na parte superior do ecrã.

## <a name="assign-users"></a>Atribuir utilizadores

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerda principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8.  Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução.

## <a name="assign-groups"></a>Atribuir grupos

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerda principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8.  Clique nas **Add** botão na parte superior do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um grupo**, tipo em outro **nome do grupo completo** no **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Após um curto período de tempo, os utilizadores nos grupos que selecionou ser capaz de iniciar esses aplicativos usando os métodos descritos na secção de descrição de solução. Se estes são grupos dinâmicos, pode haver algum atraso de processamento adicional nestas atribuições que aparece para os utilizadores dentro estes grupos atribuídos.

## <a name="enable-self-service-application-access"></a>Ativar o acesso de aplicações self-service

Acesso de aplicações self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a esses aplicativos. Pode permitir que o grupo de empresas gerir as credenciais atribuídas a esses utilizadores para a direita da palavra-passe de início de sessão único em aplicações de seus painéis de acesso.

Para ativar o acesso de aplicações self-service a uma aplicação, siga os passos abaixo:

1.  Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerda principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6.  Selecione a aplicação que pretende ativar a gestão personalizada de acesso a partir da lista.

7.  Assim que o aplicativo é carregado, clique em **self-service** no menu de navegação do lado esquerdo da aplicação.

8.  Para ativar o acesso de aplicações self-service para esta aplicação, ative o **permitir que os utilizadores peçam acesso a esta aplicação?** alternar para **Sim.**

9.  Em seguida, para selecionar o grupo a que os utilizadores que pedem acesso a esta aplicação deve ser adicionado, clique o Seletor de junto à etiqueta **a que grupo devem os utilizadores atribuídos ser adicionados?** e selecione um grupo.

10. **Opcional:** Se quiser pedir uma aprovação de negócios antes dos utilizadores têm permissão para aceder, defina o **exigem a aprovação antes de conceder acesso a esta aplicação?** alternar para **Sim**.

11. **Opcional: Para aplicativos usando apenas, palavra-passe início de sessão único no** se pretender permitir que os aprovadores empresariais especificar as palavras-passe que são enviadas para esta aplicação para os utilizadores aprovados, defina o **permitir que os aprovadores definam as palavras-passe do utilizador para isso aplicação?**  alternar para **Sim**.

12. **Opcional:** Para especificar os aprovadores empresariais que têm permissão para aprovar o acesso a esta aplicação, clique o Seletor de junto à etiqueta **quem tem permissão para aprovar o acesso a esta aplicação?** para selecionar até 10 aprovadores de negócios individuais.

  >[!NOTE]
  >Grupos não são suportados.
  >
  >

13. **Opcional:** **Para aplicações que expõem funções**, se pretender atribuir utilizadores aprovados self-service a uma função, clique o Seletor de junto aos **a que função devem ser atribuídos os utilizadores desta aplicação?** para selecionar a função para o qual Estes utilizadores devem ser atribuídos.

14. Clique nas **guardar** botão na parte superior do painel para concluir.

Depois de concluir a configuração de aplicações self-service, os usuários podem navegar para seus [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique nas **+ adicionar** botão para encontrar as aplicações a que tiver ativado o Self-Service acesso. Aprovadores empresariais também veem uma notificação no seu [painel de acesso de aplicação](https://myapps.microsoft.com/). Pode ativar um e-mail a notificar quando um utilizador pediu acesso a um aplicativo que requer a sua aprovação. 

Estas aprovações suportam apenas, fluxos de trabalho de aprovação única que significa que, se especificar vários aprovadores, qualquer um único aprovador pode acesso aprovador à aplicação.

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
