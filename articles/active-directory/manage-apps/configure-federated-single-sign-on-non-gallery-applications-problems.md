---
title: Problema configurar inscrição única federada para aplicação não-galeria  Microsoft Docs
description: Resolva alguns dos problemas comuns que pode encontrar ao configurar um único sign-on federado para a sua aplicação SAML personalizada que não está listada na Galeria de Aplicações da AD Azure
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
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711889"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problema configurar inscrição única federada para aplicação não-galeria

Se encontrar algum problema ao configurar uma aplicação. Verifique se seguiu todos os passos do artigo Configurando um único sinal para [aplicações que não estão na galeria de aplicações do Azure Ative Directory.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Não posso adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, você precisa ser capaz de:

-   Configure um identificador único para a segunda instância. Não é possível configurar o mesmo identificador utilizado em primeira instância.

-   Configure um certificado diferente do utilizado em primeira instância.

Se a aplicação não suportar nenhum dos anteriores, não pode configurar uma segunda instância.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde posso definir o formato EntityID (Identificador de Utilizador)

Não é possível selecionar o formato EntityID (Identificador de Utilizador) que o Azure AD envia para a aplicação na resposta após a autenticação do utilizador.

A Azure AD seleciona o formato para o atributo NameID (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações visite o [protocolo SAML](../develop/single-sign-on-saml-protocol.md#authnrequest) de assinatura única ao abrigo da secção NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Onde obtenho os metadados de aplicação ou certificado da Azure AD

Para descarregar os metadados de aplicação ou certificado da Azure AD, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como **Administrador Global** ou **Coadministrador.**

2. Abra a extensão do **Diretório Ativo Azure** clicando em **todos os serviços** no topo do menu principal de navegação à esquerda.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. clique **em Aplicações Empresariais** a partir do menu de navegação à esquerda do Azure Ative Diretório.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que configurau um único sinal.

7. Uma vez que a aplicação seja carregada, clique no único sinal de **inscrição** do menu de navegação à esquerda da aplicação.

8. Vá à secção **de Certificado de Assinatura SAML** e, em seguida, clique no valor da coluna **Descarregue.** Dependendo do que a aplicação requer configurar um único sinal, você vê a opção de descarregar o Metadata XML ou o Certificado.

A Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser recuperados como um ficheiro XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sei como personalizar as reclamações da SAML enviadas para uma aplicação

Para saber como personalizar as reclamações de atributos SAML enviadas para a sua aplicação, consulte [o mapeamento de Reclamações no Diretório Ativo Azure](../develop/active-directory-claims-mapping.md) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Managing Applications with Azure Active Directory](what-is-application-management.md) (Gerir Aplicações com o Azure Active Directory)
