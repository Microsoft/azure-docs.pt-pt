---
title: 'Azure AD Connect sync: Extensões de diretórios / Microsoft Docs'
description: Este tópico descreve a funcionalidade de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a0765f5279eb41324691c431c5973bb55a8b52d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89662490"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect Sync: Extensões de diretório
Pode utilizar extensões de diretório para estender o esquema no Azure Ative Directory (Azure AD) com os seus próprios atributos a partir do Ative Directory. Esta funcionalidade permite-lhe construir aplicações LOB consumindo atributos que continua a gerir no local. Estes atributos podem ser consumidos através [de extensões.](/graph/extensibility-overview
) Pode ver os atributos disponíveis utilizando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Também pode utilizar esta funcionalidade para criar grupos dinâmicos no Azure AD.

Atualmente, nenhuma carga de trabalho da Microsoft 365 consome estes atributos.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalize quais atributos para sincronizar com Azure AD

Configura quais os atributos adicionais que pretende sincronizar no caminho das definições personalizadas no assistente de instalação.

> [!NOTE]
> Nas versões Azure AD Connect antes de 1.2.65.0, a caixa de pesquisa de **Atributos Disponíveis** é sensível a casos.

![Assistente de extensão de Schema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

A instalação apresenta os seguintes atributos, que são candidatos válidos:

* Tipos de objetos de utilizador e grupo
* Atributos de valor único: String, Boolean, Inteiro, Binário
* Atributos multi-valorizados: String, Binary


>[!NOTE]
> Embora o Azure AD Connect suporte a sincronização de atributos de Diretório Ativo multi-valorizados ao Azure AD como extensões de diretório multi-valorizadas, não existe atualmente forma de recuperar/consumir os dados carregados em atributos de extensão de diretório multi-valor.

A lista de atributos é lida a partir da cache de esquema que é criada durante a instalação do Azure AD Connect. Se tiver estendido o esquema do Ative Directory com atributos adicionais, deve [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema) antes que estes novos atributos sejam visíveis.

Um objeto em Azure AD pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o motor de sincronização trunca-o.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Alterações de configuração em AD Azure feitas pelo assistente

Durante a instalação do Azure AD Connect, está registada uma aplicação onde estes atributos estão disponíveis. Pode ver esta aplicação no portal Azure. O seu nome é sempre **App de Extensão Schema do Inquilino.**

![App de extensão de Schema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Certifique-se de que seleciona **Todas as aplicações** para ver esta aplicação.

Os atributos são pré-fixados com **extensão \_ {ApplicationId} \_ **. O ApplicationId tem o mesmo valor para todos os atributos do seu inquilino AZure AD. Você precisará deste valor para todos os outros cenários neste tópico.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Atributos de visualização usando a API do Gráfico da Microsoft

Estes atributos estão agora disponíveis através da Microsoft Graph API, utilizando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Na API do Gráfico microsoft, é necessário pedir que os atributos sejam devolvidos. Selecione explicitamente os atributos como este: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Para obter mais informações, consulte [o Gráfico do Microsoft: Utilize parâmetros de consulta](/graph/query-parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Use os atributos em grupos dinâmicos

Um dos cenários mais úteis é utilizar estes atributos em grupos dinâmicos de segurança ou Microsoft 365.

1. Criar um novo grupo em Azure AD. Dê-lhe um bom nome e certifique-se de que o **tipo de Membro** é Utilizador **Dinâmico**.

   ![Screenshot com um novo grupo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecione para **Adicionar consulta dinâmica**. Se olhar para as propriedades, então não verá estes atributos estendidos. Precisa adicioná-los primeiro. Clique **em Obter propriedades de extensão personalizadas,** insira o ID da aplicação e clique em Atualizar **propriedades.**

   ![Screenshot onde foram adicionadas extensões de diretório](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Abra a propriedade e note que os atributos adicionados estão agora visíveis.

   ![Screenshot com novos atributos aparecendo na UI](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Complete a expressão de acordo com os seus requisitos. No nosso exemplo, a regra está definida para **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Vendas e marketing")**.

4. Após a criação do grupo, dê ao Azure AD algum tempo para povoar os membros e, em seguida, rever os membros.

   ![Screenshot com membros no grupo dinâmico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).