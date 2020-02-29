---
title: 'Sincronização Azure AD Connect: Extensões de diretórios / Microsoft Docs'
description: Este tópico descreve a funcionalidade de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917918"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização Azure AD Connect: Extensões de diretório
Pode utilizar extensões de diretório para estender o esquema em Azure Ative Directory (Azure AD) com os seus próprios atributos a partir do Diretório Ativo no local. Esta funcionalidade permite-lhe construir aplicações LOB consumindo atributos que continua a gerir no local. Estes atributos podem ser consumidos através de [extensões](https://docs.microsoft.com/graph/extensibility-overview
). Pode ver os atributos disponíveis utilizando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Também pode utilizar esta funcionalidade para criar grupos dinâmicos em Azure AD.

Atualmente, nenhuma carga de trabalho do Office 365 consome estes atributos.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalize quais atributos para sincronizar com AD Azure

Configura quais os atributos adicionais que pretende sincronizar no caminho de definições personalizadas no assistente de instalação.

>[!NOTE]
>A caixa de Atributos Disponíveis é sensível a casos.

![Assistente de extensão schema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objetos de utilizador e grupo
* Atributos de valor único: String, Boolean, Integer, Binary
* Atributos multivalorizados: String, Binary


>[!NOTE]
> Embora o Azure AD Connect suporte a sincronização de atributos de Diretório Ativo multivalorizados ao Azure AD como extensões de diretório multi-valor, não existe atualmente forma de recuperar/consumir os dados carregados em atributos de extensão de diretório multivalorizados.

A lista de atributos é lida a partir da cache de esquema supor que é criada durante a instalação do Azure AD Connect. Se estendeu o esquema de Diretório Ativo com atributos adicionais, tem de [refrescar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema) antes de serem visíveis estes novos atributos.

Um objeto em Azure AD pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for mais longo, o motor sincronizado trunca-o.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Alterações de configuração em Anúncio Azure feitas pelo assistente

Durante a instalação do Azure AD Connect, está registada uma aplicação onde estes atributos estão disponíveis. Pode ver esta aplicação no portal Azure. O seu nome é sempre A App de **Extensão do Inquilino Schema.**

![App de extensão schema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Certifique-se de que seleciona **todas as aplicações** para ver esta aplicação.

Os atributos são pré-fixados com **\_de extensão {ApplicationId}\_** . ApplicationId tem o mesmo valor para todos os atributos do seu inquilino Azure AD. Você precisará deste valor para todos os outros cenários neste tópico.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Atributos de visualização usando o Microsoft Graph API

Estes atributos estão agora disponíveis através da Microsoft Graph API, utilizando o [Microsoft Graph Explorer.](https://developer.microsoft.com/graph/graph-explorer#)

>[!NOTE]
> Na API do Microsoft Graph, é necessário pedir que os atributos sejam devolvidos. Selecione explicitamente os atributos como este: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`.
>
> Para mais informações, consulte [o Microsoft Graph: Utilize parâmetros](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)de consulta .

## <a name="use-the-attributes-in-dynamic-groups"></a>Use os atributos em grupos dinâmicos

Um dos cenários mais úteis é usar estes atributos em segurança dinâmica ou grupos do Office 365.

1. Crie um novo grupo em Azure AD. Dê-lhe um bom nome e certifique-se de que o **tipo de Membro** é Utilizador **Dinâmico**.

   ![Screenshot com um novo grupo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecione para **adicionar consulta dinâmica**. Se olhar para as propriedades, não verá estes atributos estendidos. Precisa adicioná-los primeiro. Clique em **Obter propriedades de extensão personalizadas,** insira o ID da aplicação e clique em propriedades **refresh**.

   ![Screenshot onde foram adicionadas extensões de diretório](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Abra a propriedade e note que os atributos que adicionou são agora visíveis.

   ![Screenshot com novos atributos aparecendo na UI](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Complete a expressão de acordo com os seus requisitos. No nosso exemplo, a regra é definida para **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Vendas e marketing")** .

4. Depois de o grupo ter sido criado, dê ao Azure AD algum tempo para povoar os membros e depois rever os membros.

   ![Screenshot com membros no grupo dinâmico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
