---
title: Quotas em Azure Static Web Apps Preview
description: Saiba mais sobre as quotas associadas à pré-visualização de aplicações web estáticas Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095174"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Quotas em Azure Static Web Apps Preview

Existem as seguintes quotas para a pré-visualização de aplicações web estáticas Azure.

> [!IMPORTANT]
> A azure Static Web Apps está em pré-visualização pública e não se destina a ser usada pela produção.

| Funcionalidade                     | Plano gratuito        |
|-----------------------------|------------------|
| Largura de banda incluída          | 100 GB por mês |
| Largura de banda overage           | Indisponível      |
| Aplicativos por subscrição Azure | 10               |
| Tamanho da aplicação                    | 250 MB           |
| Ambientes de pré-produção | 3                |
| Domínios personalizados              | 1                |
| Autorização (com funções personalizadas e regras de encaminhamento) | Máximo de 25 utilizadores finais que podem pertencer a funções personalizadas |
| Funções do Azure             | Disponível        |
| SLA                         | Nenhum             |

## <a name="github-storage"></a>Armazenamento GitHub

As ações e pacotes gitHub usam o Armazenamento GitHub, que tem o seu próprio conjunto de quotas. Quando cria um site de Aplicações Web Estáticas Azure, o GitHub armazena os artefactos para o seu site mesmo após a implementação.

Consulte os seguintes recursos para obter mais detalhes:

- [Gestão do espaço de armazenamento de ações](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Sobre faturação para ações do GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gerir o seu limite de gastos para as ações do GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Passos seguintes

- [Descrição geral](overview.md)
