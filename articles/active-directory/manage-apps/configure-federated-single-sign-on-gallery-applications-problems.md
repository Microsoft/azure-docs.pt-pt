---
title: Problemas de configuração de SSO federado para aplicações da Azure AD Gallery
description: Resolver alguns dos problemas comuns que pode encontrar ao configurar um único letreiro federado usando o SAML para aplicações listadas na Galeria de Aplicações da AD Azure
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c2497a781b0d46b3b2f1e281a3d7b327b60952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274643"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema configurando um único sign-on federado para uma aplicação da Galeria AD Azure

Se encontrar algum problema ao configurar uma aplicação. Verifique se seguiu todos os passos do tutorial para a aplicação. Na configuração da aplicação, tem documentação inline sobre como configurar a aplicação. Além disso, pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo.

## <a name="cant-add-another-instance-of-the-application"></a>Não posso adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, você precisa ser capaz de:

-   Configure um identificador único para a segunda instância. Não conseguirá configurar o mesmo identificador usado em primeira instância.

-   Configure um certificado diferente do utilizado em primeira instância.

Se a aplicação não apoiar nenhuma das anteriores. Então, não será capaz de configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não pode adicionar o Identificador ou o URL de Resposta

Se não conseguir configurar o Identificador ou o URL de resposta, confirme que os valores de URL do Identificador e resposta correspondem aos padrões pré-configurados para a aplicação.

Para conhecer os padrões pré-configurados para a aplicação:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.** Vai para o passo 7. Se já estiver na lâmina de configuração da aplicação em Azure AD.

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que pretende configurar um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Selecione **sign-on baseado em SAML** a partir do **modo** drop-down.

9. Vá à caixa de texto URL **do Identificador** ou **resposta,** na **secção Domínio e URLs.**

10. Existem três formas de conhecer os padrões suportados para a aplicação:

    * Na caixa de texto, vê os padrões suportados como espaço reservado *Exemplo:* <https://contoso.com>.

    * se o padrão não for suportado, vê-se um ponto de exclamação vermelho quando se tenta introduzir o valor na caixa de texto. Se pairar sobre o rato sobre a marca de exclamação vermelha, verá os padrões suportados.

    * No tutorial para a aplicação, também pode obter informações sobre os padrões suportados. Sob a secção de inscrição individual da **Configuração Azure AD.** Vá ao passo para configurar os valores sob a secção **Domínio e URLs.**

Se os valores não coincidirem com os padrões pré-configurados em Azure AD. Pode:

-   Trabalhe com o fornecedor de aplicações para obter valores que correspondam ao padrão pré-configurado em Azure AD

-   Ou, pode contactar a equipa <aadapprequest@microsoft.com> da Azure AD no tutorial para solicitar a atualização dos padrões suportados para a aplicação

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde posso definir o formato EntityID (Identificador de Utilizador)

Não será possível selecionar o formato EntityID (Identificador de Utilizador) que o Azure AD envia para a aplicação na resposta após a autenticação do utilizador.

A Azure AD selecione o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) de assinatura única ao abrigo da secção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível encontrar os metadados da AD Azure para completar a configuração com a aplicação

Para descarregar os metadados de aplicação ou certificado da Azure AD, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Digite em **"Azure Ative Directory"** na caixa de pesquisa de filtros e selecione o item **azure Ative Directory.**

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e detetete a opção **Mostrar** para Todas **as Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Vá à secção **de Certificado de Assinatura SAML** e, em seguida, clique no valor da coluna **Descarregue.** Dependendo do que a aplicação requer configurar um único sinal, você vê a opção de descarregar o Metadata XML ou o Certificado.

A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sei como personalizar as reclamações da SAML enviadas para uma aplicação

Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Gestão de Aplicações com Diretório Ativo Azure](what-is-application-management.md)
