---
title: Resolver problemas de início de sessão único baseado em SAML no Azure Active Directory
description: Problemas de resolução de problemas com uma aplicação AD AZure que está configurada para um único sign-on baseado em SAML.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257531"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Resolver problemas de início de sessão único baseado em SAML no Azure Active Directory
Se encontrar um problema ao configurar uma aplicação. Verifique se seguiu todos os passos do tutorial para a aplicação. Na configuração da aplicação, tem documentação em linha sobre como configurar a aplicação. Além disso, pode aceder à [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Ative Directory](../saas-apps/tutorial-list.md) para uma orientação detalhada passo a passo.

## <a name="cant-add-another-instance-of-the-application"></a>Não posso adicionar outro exemplo da aplicação
Para adicionar uma segunda instância de uma aplicação, você precisa ser capaz de:
-   Configurar um identificador único para a segunda instância. Não conseguirá configurar o mesmo identificador usado em primeira instância.
-   Configurar um certificado diferente do utilizado em primeira instância.

Se a aplicação não apoiar nenhum dos acima. Então, não poderá configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não é possível adicionar o Identificador ou o URL de Resposta
Se não conseguir configurar o Identificador ou o URL de resposta, confirme que os valores de URL de identificação e resposta correspondem aos padrões pré-configurados para a aplicação.

Para conhecer os padrões pré-configurados para a aplicação:
1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.** Vai para o passo 7. Se já estiver na lâmina de configuração da aplicação no Azure AD.
2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.
3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.
5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.
   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**
6. Selecione a aplicação desejada para configurar um único sinal de inscrição.
7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.
8. Selecione **O Sinal de SDOL baseado no** **modo** de entrega.
9. Aceda à caixa de texto de URL **de identificação** ou **resposta,** na **secção Domínio e URLs.**
10. Existem três formas de conhecer os padrões suportados para a aplicação:
    * Na caixa de texto, vê os padrãos suportados como um espaço reservado *Exemplo:* <https://contoso.com> .
    * se o padrão não for suportado, vê-se um ponto de exclamação vermelho quando tenta introduzir o valor na caixa de texto. Se pairares sobre o ponto de exclamação vermelho, vês os padrões suportados.
    * No tutorial para a aplicação, também pode obter informações sobre os padrões suportados. Sob a secção de inscrição única da **Azure AD.** Vá ao passo para configurar os valores na secção **Domínio e URLs.**

Se os valores não corresponderem aos padrões pré-configurados no Azure AD. Pode:
-   Trabalhe com o fornecedor de aplicações para obter valores que correspondam ao padrão pré-configurado no AD AZure
-   Ou, você pode contactar a equipa AZure AD em <aadapprequest@microsoft.com> ou deixar um comentário no tutorial para solicitar a atualização dos padrões suportados para a aplicação

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde é que eu defini o formato EntityID (User Identifier)
Não poderá selecionar o formato EntityID (User Identifier) que o Azure AD envia para a aplicação na resposta após a autenticação do utilizador.

A Azure AD seleciona o formato para o atributo NameID (User Identifier) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o artigo [Protocolo de Sign-On SAML](../develop/single-sign-on-saml-protocol.md#authnrequest) de solteiro na secção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível encontrar os metadados AD Azure para completar a configuração com a aplicação
Para descarregar os metadados ou certificados da aplicação a partir da Azure AD, siga estes passos:
1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministração.**
2. Abra a **Extensão do Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.
3. Digite **"Azure Ative Directory"** na caixa de pesquisa do filtro e selecione o item **Azure Ative Directory.**
4. clique em **Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative.
5. clique em **Todas as Aplicações** para ver uma lista de todas as suas aplicações.
   * Se não vir a aplicação deseja aparecer aqui, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações** e desate a opção **'Mostrar'** a **todas as aplicações.**
6. Selecione a aplicação que configurar um único sinal de inscrição.
7. Uma vez que a aplicação é carregada, clique **no único sinal de inscrição** do menu de navegação à esquerda da aplicação.
8. Aceda à secção **de Certificado de Assinatura SAML** e, em seguida, clique em Baixar **o** valor da coluna. Dependendo do que a aplicação requer configurar um único sinal, vê a opção de descarregar o Metadadata XML ou o Certificado.

A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Personalizar afirmações do SAML enviadas para uma aplicação
Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Azure Ative Diretório](../develop/active-directory-claims-mapping.md) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
* [Quickstart Series em Gestão de Aplicações](view-applications-portal.md)