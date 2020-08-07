---
title: Migrar para a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre a Microsoft Authentication Library (MSAL) e a Azure AD Authentication Library (ADAL) e como migrar para o MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 32a1440bb641f0cbe8e3f3b8ada42e0841d9aa67
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922494"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrar aplicações para a Microsoft Authentication Library (MSAL)

Muitos desenvolvedores construíram e implementaram aplicações usando a Azure Ative Directory Authentication Library (ADAL). Recomendamos agora a utilização da Biblioteca de Autenticação da Microsoft (MSAL) para autenticação e autorização de entidades AZure AD.

Utilizando o MSAL em vez de ADAL:

- Pode autenticar um conjunto mais amplo de identidades:
  - Identidades da AD AZure
  - Contas microsoft
  - Contas sociais e locais utilizando Azure AD B2C
- Os seus utilizadores terão a melhor experiência de um único sinal.
- A sua aplicação pode permitir o consentimento incremental.
- Apoiar o Acesso Condicional é mais fácil.
- Beneficia-se da inovação. Como todos os esforços de desenvolvimento da Microsoft estão agora focados no MSAL, nenhuma nova funcionalidade será implementada no ADAL.

**A MSAL é agora a biblioteca de autenticação recomendada para utilização com a plataforma de identidade microsoft**.

## <a name="migration-guidance"></a>Orientação da migração

Os seguintes artigos podem ajudá-lo a migrar para o MSAL:

- [Migrar para MSAL.Android](migrate-android-adal-msal.md)
- [Migrar para MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrar para MSAL Java](migrate-adal-msal-java.md)
- [Migrar para MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrar para MSAL.NET](msal-net-migration.md)
- [Migrar para MSAL Python](migrate-python-adal-msal.md)
- [Migrar aplicações Xamarin com mediadores para o MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

__P: A ADAL está a ser depreciada?__  
R: Sim. A partir de 30 de junho de 2020, deixaremos de adicionar novas funcionalidades à ADAL. Continuaremos a adicionar correções críticas de segurança à ADAL até 30 de junho de 2022. Após este tempo, as suas aplicações que usam ADAL funcionarão, mas recomendamos o upgrade para o MSAL para tirar partido das funcionalidades mais recentes e manter-se seguro.

__P: Como sei qual das minhas aplicações está a usar a ADAL?__  
R: Se tiver o código-fonte para a aplicação, pode referenciar os guias de migração acima para ajudar a determinar que biblioteca a aplicação utiliza e como migrar para o MSAL. Se fez uma parceria com um ISV, sugerimos que contacte diretamente eles para entender a sua viagem de migração para a MSAL. Se não tiver acesso ao código fonte da sua aplicação, pode [abrir um pedido](developer-support-help-options.md#open-a-support-request) de apoio para obter uma lista das suas aplicações registadas e a biblioteca utiliza cada candidatura.

__P: As minhas aplicações ADAL existentes continuarão a funcionar?__  
R: As suas aplicações existentes continuarão a funcionar sem modificações. Se planeia mantê-los para além de 30 de junho de 2022, deve considerar atualizá-los para o MSAL para mantê-los seguros, mas migrar para o MSAL não é necessário para manter a funcionalidade existente.

__P: Por que devo investir na mudança para a MSAL?__  
R: O MSAL contém novas funcionalidades não no ADAL, incluindo consentimento incremental, súpido único e gestão de cache simbólica. Além disso, ao contrário da ADAL, a MSAL continuará a receber patches de segurança para além de 30 de junho de 2022. [Saiba mais](msal-overview.md).

__P: Vai lançar uma ferramenta que me ajuda a mover as minhas aplicações de ADAL para MSAL?__  
R: Não. As diferenças entre as bibliotecas exigiriam a dedicação de recursos ao desenvolvimento e manutenção da ferramenta que, de outra forma, seriam gastas a melhorar a MSAL. No entanto, fornecemos o conjunto anterior de guias de migração para ajudá-lo a fazer as alterações necessárias na sua aplicação.

__P: Como funciona a MSAL com a AD FS?__  
R: MSAL.NET apoia certos cenários para autenticar contra a AD FS 2019. Se a sua aplicação precisar de adquirir fichas diretamente da versão anterior do AD FS, deverá permanecer no ADAL. [Saiba mais](msal-net-adfs-support.md).

__P: Como consigo ajuda a migrar a minha candidatura?__  
R: Consulte a secção de orientação para a [migração](#migration-guidance) deste artigo. Se, depois de ler o guia para a plataforma da sua aplicação, tiver perguntas adicionais, pode publicar no Stack Overflow com a etiqueta `[adal-deprecation]` ou abrir um problema no repositório GitHub da biblioteca. Consulte a secção [de Línguas e Quadros](msal-overview.md#languages-and-frameworks) do artigo de visão geral do MSAL para obter links para o repo de cada biblioteca.

## <a name="next-steps"></a>Passos seguintes

- [Atualize as suas aplicações para utilizar a Microsoft Authentication Library e a Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Visão geral da plataforma de identidade da Microsoft](v2-overview.md)
- [Reveja as nossas amostras de código MSAL](sample-v2-code.md)
