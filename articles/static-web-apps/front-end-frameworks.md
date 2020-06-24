---
title: Configurar quadros frontais com pré-visualização de aplicações web estáticas Azure
description: Configurações para quadros frontais populares necessários para aplicações web estáticas Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976715"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Configurar quadros front-end e bibliotecas com pré-visualização de aplicações web estáticas Azure

As Aplicações Web Estáticas Azure exigem que tenha os valores de configuração adequados no ficheiro de configuração de [construção](github-actions-workflow.md) para a sua estrutura frontal ou biblioteca.

## <a name="configuration"></a>Configuração

A tabela que se segue enumera as definições para uma série de quadros e bibliotecas<sup>1</sup>.

A intenção das colunas de mesa é explicada pelos seguintes itens:

- **Localização do artefacto da aplicação**: Lista o valor para `app_artifact_location` , que é a pasta para [versões construídas de ficheiros de aplicações](github-actions-workflow.md#build-and-deploy).

- **Comando de construção personalizado**: Quando a estrutura requer um comando diferente de `npm run build` `npm run azure:build` ou, pode definir um [comando de construção personalizado](github-actions-workflow.md#custom-build-commands).

| Arquitetura | Localização do artefacto da aplicação | Comando de construção personalizado |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Angular Universal](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n/a |
| [Backbone.js](https://backbonejs.org/) | `/` | n/a |
| [Ember](https://emberjs.com/) | `dist` | n/a |
| [Agitação](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Quadro 7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Brilho](https://glimmerjs.com/) | `dist` | n/a |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/a |
| [Hiperapp](https://hyperapp.dev/) | `/` | n/a |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/a |
| [jQuery](https://jquery.com/) | `/` | n/a |
| [KnockoutJs](https://knockoutjs.com/) | `dist` | n/a |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/a |
| [Marco](https://markojs.com/) | `public` | n/a |
| [Meteoro](https://www.meteor.com/) | `bundle` | n/a |
| [Mithril](https://mithril.js.org/) | `dist` | n/a |
| [Polímero](https://www.polymer-project.org/) | `build/default` | n/a |
| [Preact](https://preactjs.com/) | `build` | n/a |
| [Reagir](https://reactjs.org/) | `build` | n/a |
| [Stencil](https://stenciljs.com/) | `www` | n/a |
| [Svelte](https://svelte.dev/) | `public` | n/a |
| [Three.js](https://threejs.org/) | `/` | n/a |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/a |
| [Vue](http://vuejs.com/) | `dist` | n/a |

<sup>1</sup> A tabela acima não se destina a ser uma lista exaustiva de quadros e bibliotecas que funcionam com aplicações web estáticas Azure.

<sup>2</sup> Não aplicável

## <a name="next-steps"></a>Passos seguintes

- [Configuração de compilação e fluxo de trabalho](github-actions-workflow.md)
