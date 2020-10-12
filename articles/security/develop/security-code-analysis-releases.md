---
title: Lançamentos de Análise de Código de Segurança da Microsoft
description: Este artigo descreve as próximas versões para a extensão de Análise de Código de Segurança da Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85362045"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis lança e roteiro

A equipa de Análise de Códigos de Segurança da Microsoft em parceria com o Developer Support orgulha-se de anunciar melhorias recentes e futuras à nossa extensão MSCA.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Scanner credencial v2.0: Lançado em abril de 2020

### <a name="innovations--improvements"></a>Inovações & Melhorias

- **Motor central**

   - Atualização média de desempenho de 25% com tempos de execução quase lineares
   - Pesquisa e classificação baseadas em contexto/evidência para maior precisão
   - Melhorias nas deteções gerais de passwords e lógica de correspondência para espaços reservados óbvios (por exemplo, fakePassword)

- **Cobertura** - Suporte para mais de 25 tipos secretos, incluindo os seguintes principais pedidos:

   - Certificado de conta de tecido Passphrase
   - Chave Segredo/API do Cliente
   - Cabeçalho de autorização HTTP
   - Chave de acesso secreto ao cliente Amazon S3
   - Token de acesso ao cliente do Azure Ative Directory
   - Chave Azure Function Master/API
   - Chave de acesso ao BI de potência
   - Padrão de senha de modelo do gestor de recursos Azure

- **Saídas**

   - Suporte para formatos de ficheiro sarif 2.1 e CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0: Lançado em abril de 2020

### <a name="improvements"></a>Melhorias

- CARACTERÍSTICA: Atualização para o sarif final v2 (versão 2.1.16). Esta atualização permite que os resultados se caching ao passar --hashes na linha de comando, uma melhoria significativa do desempenho ao analisar recursivamente diretórios com várias cópias de alvos de digitalização.
- BUG FIX: Fix tipografia em BA2021. Saída DoNotMarkWritableSectionsAsExecutable output.
- DESEMPENHO: Elimine o carregamento de PDB para todos os modos não mistos para conjuntos geridos, incluindo binários da Biblioteca IL (antes do tempo compilado).
- FALSA CORREÇÃO NEGATIVA: Verifique se um PDB colocado ao lado de um binário corresponde ao binário em análise
- CARACTERÍSTICA: Fornecer --argumento local-symbol-directies para especificar locais de procura PDB adicionais (locais locais locais locais, não-símbolo-servidor)
- CORREÇÃO POSITIVA FALSA: Ignore a análise orientada por PDB para o exe de botas nativo .NET core (que não é um código controlável pelo utilizador).

## <a name="whats-next-in-q3-cy20"></a>O que vem a seguir no Q3 CY20?

- Ferramenta de Análise de Segurança java
- Ferramenta python de análise de segurança
- ES Lint para substituir TS Lint por TypeScript e JavaScript
- Ferramenta de análise de modelos de gestor de recursos

## <a name="tool-deprecation-notification"></a>Notificação de depreciação de ferramentas

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>A Microsoft Security Risk Detection (MSRD) é depreciada a 26 de junho de 2020.

O serviço de fuzzing MSRD preterido será substituído por uma plataforma de pelúia auto-hospedada de código aberto para o Azure. Esta plataforma está atualmente a ser desenvolvida e testada em parceria com muitas das principais equipas de produtos da Microsoft. Esta plataforma de fuzzing irá integrar desinfetantes e permitir testes de fuzz adaptativos e de aprendizagem incorporados em oleodutos CI/CD que crescem ao longo do tempo com projetos de software. O lançamento da Open Source desta plataforma está agendado para a segunda metade de 2020.

## <a name="next-steps"></a>Passos seguintes

Para obter instruções sobre como embarcar e instalar a Microsoft Security Code Analysis, consulte o nosso [guia de embarque e instalação](security-code-analysis-onboard.md).

Se tiver mais perguntas sobre a extensão e as ferramentas oferecidas, consulte a nossa [página de PERGUNTAS Frequentes.](security-code-analysis-faq.md)
