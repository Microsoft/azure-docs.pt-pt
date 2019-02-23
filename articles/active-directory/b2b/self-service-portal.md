---
title: Portal de inscrição self-service para colaboração B2B - Azure Active Directory | Documentos da Microsoft
description: A colaboração do B2B Azure Active Directory suporta as relações entre empresas, permitindo a parceiros de negócios acederem, seletivamente, às suas aplicações empresariais
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3ad424e8cab444b2405715eaa468411166ebd9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674281"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de inscrição autónoma para a colaboração B2B com o Azure AD

Os clientes podem fazer muito mais com as funcionalidades incorporadas expostas através do [portal do Azure](https://portal.azure.com) e do [Painel de Acesso à Aplicação](https://myapps.microsoft.com) para utilizadores finais. No entanto, poderá ter de personalizar o fluxo de trabalho de implementação para utilizadores B2B de forma a satisfazer as necessidades da sua organização. Pode fazê-lo com [a API de convite](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Enquanto organização que convida, poderá não saber atempadamente quem são os colaboradores externos individuais que precisam de aceder aos seus recursos. Tem de ter uma forma de os utilizadores de empresas associadas se inscreverem num conjunto de políticas que o utilizador controla enquanto organização que convida. Este cenário é possível através das APIs. Existe um [projeto de exemplo no GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) que faz exatamente isso.

O projeto do GitHub mostra como as organizações podem utilizar as APIs para fornecer uma funcionalidade de inscrição autónoma baseada em políticas para os seus parceiros fidedignos, com regras que determinam as aplicações às quais podem aceder. Os utilizadores associados podem obter acesso aos recursos quando precisarem dos mesmos. Podem fazê-lo de forma segura, sem terem de pedir à organização que os convidou para os incluir manualmente. Pode facilmente implementar o projeto numa subscrição do Azure à sua escolha.

## <a name="as-is-code"></a>Código tal como está

Este código é disponibilizado como uma amostra para demonstrar a utilização da API de convite para B2B do Azure Active Directory. Deve ser personalizado pela sua equipa de programação ou por um parceiro e deve ser revisto antes de o implementar num cenário de produção.

## <a name="next-steps"></a>Passos Seguintes

* [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)
* [Licenciamento de colaboração B2B do Azure AD](licensing-guidance.md)
* [Perguntas mais frequentes (FAQ) sobre a colaboração B2B do Azure Active Directory](faq.md)
