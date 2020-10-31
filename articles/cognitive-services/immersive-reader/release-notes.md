---
title: Notas de lançamento imersivas do reader SDK
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as novidades no Leitor Imersivo JavaScript SDK.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133598"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Notas de lançamento do leitor imersivo JavaScript SDK

## <a name="version-110"></a>Versão 1.1.0

Esta versão contém novas funcionalidades, correções de vulnerabilidade de segurança, correções de erros, atualizações para amostras de código e opções de configuração.

#### <a name="new-features"></a>Novas Funcionalidades

* Ativar as preferências dos utilizadores em diferentes navegadores e dispositivos
* Ativar opções de visualização predefinidoras
* Adicione a opção de definir o idioma de tradução, ativar a tradução de palavras e permitir a tradução de documentos ao lançar o Leitor Imersivo
* Adicione suporte para configurar Ler Em Voz Alta através de opções
* Adicionar capacidade de desativar a experiência de primeira execução
* Adicionar ImmersiveReaderView para UWP

#### <a name="improvements"></a>Melhorias

* Atualize a amostra de código Android HTML para trabalhar com os mais recentes SDK
* Atualizar resposta de lançamento para devolver o número de caracteres processados
* Atualizar amostras de código para usar v1.1.0
* Não permita que o lançamentoaync seja chamado quando já está a carregar
* Verifique se há conteúdo inválido ignorando mensagens onde os dados não são uma cadeia
* Embrulhe a chamada para a janela numa cláusula se verificar o suporte do navegador da Promise

#### <a name="fixes"></a>Correções

* Fixar dependabot removendo fios.lock de gitignore
* Corrigir vulnerabilidade de segurança através da atualização do pug para v3.0.0 na amostra de código quickstart-nodejs
* Corrigir múltiplas vulnerabilidades de segurança ao atualizar pacotes Jest e TypeScript
* Corrija uma vulnerabilidade de segurança atualizando Microsoft.IdentityModel.Clients.ActiveDirectory para v5.2.0

<br>

## <a name="version-100"></a>Versão 1.0.0

Esta versão contém alterações de rutura, novas funcionalidades, melhorias na amostra de código e correções de erros.

#### <a name="breaking-changes"></a>Alterações Interruptivas

* Requerem token AD Ad E subdomínio, e deprete os tokens usados em versões anteriores.
* Desative a Política de Cookies. A retenção das preferências do utilizador é desativada por padrão. O Leitor lança-se sempre com definições predefinidas, a não ser que a Política de Cookies esteja definida para ativar.

#### <a name="new-features"></a>Novas Funcionalidades

* Adicionar suporte para ativar ou desativar cookies
* Adicione amostra de código de arranque rápido Android Kotlin
* Adicione amostra de código de arranque rápido Android Java
* Adicione Node.js amostra de código de arranque rápido

#### <a name="improvements"></a>Melhorias

* Atualizar Node.js README.md avançados
* Alterar a amostra de código Python de avançado para início rápido
* Mover a amostra de código iOS Swift em js/samples
* Atualizar amostras de código para usar v1.0.0

#### <a name="fixes"></a>Correções

* Correção para Node.js amostra de código avançada
* Adicione ficheiros em falta para recursos avançados-csharp-múltiplos
* Remova-nos de hiperligações

<br>

## <a name="version-003"></a>Versão 0.0.3

Esta versão contém novas funcionalidades, melhorias em amostras de código, correções de vulnerabilidade de segurança e correções de bugs.

#### <a name="new-features"></a>Novas Funcionalidades

* Adicione a amostra de código iOS Swift
* Adicione C# amostra de código avançada demonstrando a utilização de múltiplos recursos 
* Adicione suporte para desativar a funcionalidade de toggle de ecrã completo
* Adicione suporte para ocultar o botão de saída da aplicação Immersive Reader
* Adicione uma função de retorno que pode ser usada pela aplicação do anfitrião ao sair do Leitor Imersivo
* Atualizar amostras de código para utilizar autenticação do Diretório Ativo Azure

#### <a name="improvements"></a>Melhorias

* Atualização C# amostra de código avançada para incluir documento Word
* Atualizar amostras de código para usar v0.0.3

#### <a name="fixes"></a>Correções

* Upgrade lodash para a versão 4.17.14 para corrigir vulnerabilidade de segurança
* Atualizar biblioteca C# MSAL para corrigir vulnerabilidade de segurança
* Atualizar misturas profundas para a versão 1.3.2 para corrigir vulnerabilidade de segurança
* Upgrade jest, webpack e webpack-cli que estavam usando versões vulneráveis de valor definido e mixin-deep para corrigir vulnerabilidade de segurança

<br>

## <a name="version-002"></a>Versão 0.0.2

Esta versão contém novas funcionalidades, melhorias em amostras de código, correções de vulnerabilidade de segurança e correções de bugs.

#### <a name="new-features"></a>Novas Funcionalidades

* Adicione a amostra de código avançada python
* Adicione amostra de código de arranque rápido java
* Adicionar amostra de código simples

#### <a name="improvements"></a>Melhorias

* Renomear recursoName para cogSvcsSubdomain
* Tirar segredos do código e usar variáveis ambientais
* Atualizar amostras de código para usar v0.0.2

#### <a name="fixes"></a>Correções

* Corrigir bugs de acessibilidade de botões imersivos
* Correção de scrolling quebrado
* Pacote de guiador de upgrade para a versão 4.1.2 para corrigir vulnerabilidade de segurança
* Corrige bugs em testes de unidade SDK
* Corrige erros de compatibilidade JavaScript Internet Explorer 11
* Atualiza urls SDK

<br>

## <a name="version-001"></a>Versão 0.0.1

O lançamento inicial do Leitor Imersivo JavaScript SDK.

* Adicionar leitor imersivo JavaScript SDK
* Adicionar suporte para especificar a língua de UI
* Adicione um tempo limite para determinar quando a função launchAsync deve falhar com um erro de tempo limite
* Adicione suporte para especificar o z-index do iframe imersivo Reader
* Adicione suporte para usar uma etiqueta webview em vez de um iframe, para compatibilidade com Apps do Chrome
* Adicionar testes de unidade SDK
* Adicione Node.js amostra de código avançada
* Adicione C# amostra de código avançado
* Adicione C# amostra de código de arranque rápido
* Adicione configuração de pacote, Yarn e outros ficheiros de construção
* Adicionar ficheiros de configuração de git
* Adicione ficheiros README.md a amostras de código e SDK
* Adicionar licença MIT
* Adicionar instruções de contribuinte
* Adicione ativos de ícone estático SVG

## <a name="next-steps"></a>Passos seguintes

Introdução à Leitura Avançada:

* Leia a biblioteca de [clientes imersivo Reader Referência](./reference.md)
* Explore a [biblioteca de clientes imersivos reader no GitHub](https://github.com/microsoft/immersive-reader-sdk)
