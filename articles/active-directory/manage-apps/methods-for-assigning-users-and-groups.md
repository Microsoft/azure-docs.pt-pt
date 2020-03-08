---
title: Como atribuir utilizadores e grupos a uma aplicação | Documentos da Microsoft
description: Atribuir utilizadores à aplicação para conceder acesso
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
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376434"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Atribuir utilizadores e grupos a uma aplicação no Azure Active Directory
Este artigo mostra-lhe como atribuir utilizadores ou grupos a uma aplicação no Azure Active Directory (Azure AD). Os usuários primeiro devem ser atribuídos a uma aplicação antes de um administrador pode conceder-lhes acesso para fazer o seguinte:

-   Aceda a uma aplicação **navegando diretamente para o URL da aplicação** (também conhecida como sign-on iniciado por SP).

-   Aceda a uma aplicação utilizando o URL de Acesso ao **Utilizador** na página **Propriedades** de uma aplicação (também conhecida como sinal iniciado pelo IDP).

-   Consulte uma aplicação no seu Painel de Acesso à [Aplicação](https://myapps.microsoft.com/) ou aplicação móvel.

-   Consulte uma aplicação no seu Launcher de [Aplicações Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

A disponibilidade de atribuição baseada em grupo é determinada pelo seu contrato de licença. A atribuição baseada em grupo é apoiada apenas para grupos de segurança. Os membros do grupo aninhado e os grupos O365 não são atualmente apoiados.

## <a name="configure-the-application-to-require-assignment"></a>Configure o pedido para exigir a atribuição

Uma aplicação pode ser configurada para exigir a atribuição antes de poder ser acedida. Para exigir a atribuição:

1. Inicie sessão no portal Azure com uma conta de administrador, ou como proprietário da app ao abrigo de **aplicações da Enterprise.**
2. Clique no item **de todos os serviços** no menu principal.
3. Escolha o diretório que está a utilizar para a aplicação.
4. Clique no separador de **aplicações da Enterprise.**
5. Selecione a aplicação a partir da lista de aplicações associadas este diretório.
6. Clique no separador **Propriedades.**
7. Alterar a atribuição do **Utilizador necessária?**
8. Clique no botão **Guardar** na parte superior do ecrã.

## <a name="assign-users"></a>Atribuir utilizadores

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e **inscreva-se como Administrador Global ou como proprietário de aplicações não administradoras.**

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à mão esquerda do Diretório Ativo Azure.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à mão esquerda da aplicação.

8.  Clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

9.  clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** ou endereço de **e-mail** do utilizador que está interessado em atribuir na caixa de pesquisa de endereços de correio eletrónico **Pesquisar pelo nome ou pelo endereço de e-mail.**

11. Passe por cima do **utilizador** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do utilizador para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um utilizador**, digite outro nome **completo** ou endereço de **e-mail** na caixa de pesquisa de endereços de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este utilizador à lista **Selecionada.**

13. Quando terminar de selecionar os utilizadores, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique no botão **Atribuir** para atribuir a aplicação aos utilizadores selecionados.

Após um curto período de tempo, os utilizadores selecionados poderão lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução.

## <a name="assign-groups"></a>Atribuir grupos

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou como proprietário de aplicações não administradoras com licença Azure AD Premium atribuída.

2.  Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3.  Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4.  clique **em Aplicações Empresariais** a partir do menu de navegação à mão esquerda do Diretório Ativo Azure.

5.  clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

    * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Assim que a aplicação estiver carregada, clique em **Utilizadores e Grupos** a partir do menu de navegação à mão esquerda da aplicação.

8.  Clique no botão **Adicionar** em cima da lista de **Utilizadores e Grupos** para abrir o painel de atribuição de **adicionar.**

9.  clique nos Utilizadores e no seletor de **grupos** a partir do painel **adicionar atribuição.**

10. Digite o **nome completo** do grupo que está interessado em atribuir na caixa de pesquisa de endereços de pesquisa de Pesquisa por nome ou endereço de **e-mail.**

11. Paire sobre o **grupo** na lista para revelar uma caixa de **verificação**. Clique na caixa de verificação ao lado da foto ou logotipo do perfil do grupo para adicionar o seu utilizador à lista **Selecionada.**

12. **Opcional:** Se quiser **adicionar mais do que um grupo**, digite outro nome de grupo **completo** na caixa de pesquisa de endereços de pesquisa de pesquisa de nome ou endereço de **e-mail,** e clique na caixa de verificação para adicionar este grupo à lista **Selecionada.**

13. Quando terminar de selecionar grupos, clique no botão **Select** para adicioná-los à lista de utilizadores e grupos a atribuir à aplicação.

14. **Opcional:** clique no seletor **de funções seletor** no painel **de atribuição** de adicionar para selecionar uma função para atribuir aos grupos selecionados.

15. Clique no botão **Atribuir** para atribuir a aplicação aos grupos selecionados.

Após um curto período de tempo, os utilizadores dentro dos grupos selecionados poderão lançar estas aplicações utilizando os métodos descritos na secção de descrição da solução. Se estes são grupos dinâmicos, pode haver algum atraso de processamento adicional nestas atribuições que aparece para os utilizadores dentro estes grupos atribuídos.

## <a name="enable-self-service-application-access"></a>Ativar o acesso de aplicações self-service

Acesso de aplicações self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a esses aplicativos. Pode permitir que o grupo de empresas gerir as credenciais atribuídas a esses utilizadores para a direita da palavra-passe de início de sessão único em aplicações de seus painéis de acesso.

Para ativar o acesso de aplicações self-service a uma aplicação, siga os passos abaixo:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à mão esquerda do Diretório Ativo Azure.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende ativar a gestão personalizada de acesso a partir da lista.

7. Assim que a aplicação estiver carregada, clique em **Self-service** a partir do menu de navegação à mão esquerda da aplicação.

8. Para permitir o acesso à aplicação self-service para esta aplicação, rode os **utilizadores de Permitir em solicitar acesso a esta aplicação?**

9. Em seguida, para selecionar o grupo ao qual devem ser adicionados os utilizadores que solicitam o acesso a esta aplicação, clique no seletor ao lado da etiqueta **Para que grupo deve ser adicionado os utilizadores?**

10. **Opcional:** Se desejar uma aprovação de negócio antes de os utilizadores poderem aceder, detetete a **aprovação do Requires antes de conceder acesso a esta aplicação?**

11. **Opcional: Para aplicações** que utilizem apenas uma única placa de senha, se pretender permitir que esses aprovadores de negócios especifiquem as palavras-passe enviadaspara esta aplicação para utilizadores aprovados, detete te permite que os aprovadores de permitam definir as **palavras-passe do utilizador para esta aplicação?**

12. **Opcional:** Para especificar os aprovadores de negócios autorizados a aprovar o acesso a esta aplicação, clique no seletor ao lado do rótulo **Quem está autorizado a aprovar o acesso a esta aplicação?**

    >[!NOTE]
    >Grupos não são suportados.
    >
    >

13. **Opcional:** **Para aplicações que exponham funções,** caso deseje atribuir aos utilizadores aprovados por self-service uma função, clique no seletor ao lado do **To que deve ser atribuído aos utilizadores nesta aplicação?**

14. Clique no botão **Guardar** na parte superior do painel para terminar.

Assim que completar a configuração da aplicação self-service, os utilizadores podem navegar para o seu Painel de Acesso a [Aplicações](https://myapps.microsoft.com/) e clicar no botão **+Adicionar** para encontrar as aplicações às quais ativou o acesso ao self-service. Os aprovadores de negócios também vêem uma notificação no seu Painel de Acesso a [Aplicações.](https://myapps.microsoft.com/) Pode ativar um e-mail a notificar quando um utilizador pediu acesso a um aplicativo que requer a sua aprovação. 

Estas aprovações suportam apenas fluxos de trabalho de aprovação individuais, o que significa que se especificar vários aprovadores, qualquer único aprovador pode aprovar o acesso à aplicação.

## <a name="next-steps"></a>Passos seguintes
[Forneça um único sinal às suas apps com Procuração de Aplicação](application-proxy-configure-single-sign-on-with-kcd.md)
