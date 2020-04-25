---
title: Lançamentos de Análise de Código de Segurança da Microsoft
description: Este artigo descreve as próximas versões para a extensão da Análise de Código de Segurança da Microsoft
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
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146126"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis lança e mapeia

A equipa de Análise de Códigos de Segurança da Microsoft, em parceria com o Developer Support, orgulha-se de anunciar melhorias recentes e futuras na nossa extensão MSCA. Por favor, consulte o Roadmap abaixo.

![Versões](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v2.0: Lançado em abril de 2020

### <a name="innovations--improvements"></a>Inovações & Melhorias

- **Motor Core**

   - Upgrade médio de desempenho de 25% com tempos de execução quase lineares
   - Pesquisa e classificação baseadas em contexto/evidência para maior precisão
   - Melhorias nas deteções gerais de passwords e lógica de correspondência para os espaços reservados óbvios (por exemplo, palavra-passe falsa)

- **Cobertura** - Suporte para mais de 25 tipos secretos, incluindo o seguinte top solicitado:

   - Palavra-passe de certificado de conta de tecido
   - Chave segredo/API do cliente
   - Cabeçalho de autorização HTTP
   - Chave de acesso secreto ao cliente da Amazon S3
   - Ficha de acesso ao cliente do Diretório Ativo Azure
   - Chave Master/API de função Azure
   - Chave de acesso bi de potência
   - Padrão de senha de modelo do Gestor de Recursos Azure

- **Saídas**

   - Suporte para formatos de ficheiros de saída de ficheiros SARIF 2.1 e CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0: Lançado em abril de 2020

### <a name="improvements"></a>Melhorias

- RECURSO: Atualização para o SARIF final v2 (versão 2.1.16). Isto permite que os resultados caching ao passar --hashes na linha de comando, uma melhoria significativa de desempenho ao analisar recursivamente diretórios com várias cópias de alvos de digitalização.
- BUG FIX: Fixe a tipografia em BA2021. DoNotMarkWritableSectionsAsExecutável saída.
- DESEMPENHO: Elimine o carregamento de PDB para todos os modos não mistos para conjuntos geridos, incluindo binários da Biblioteca IL (antes do tempo compilados).
- CORREÇÃO FALSA NEGATIVA: Verifique se um PDB colocado ao lado de um binário realmente corresponde ao binário em análise
- RECURSO: Forneça o argumento de --símbolo-directórios locais para especificar locais adicionais (locais, não-servidor de símbolos)
- CORREÇÃO FALSA POSITIVA: Ignore a análise impulsionada por PDB para o exe de botas nativas do núcleo gerado .NET (que não é código controlável pelo utilizador).

## <a name="whats-next-in-fy20"></a>O que vem a seguir em FY20?

- Ferramenta de análise de segurança java
- Ferramenta de análise de segurança python
- ES Lint para substituir TS Lint por TypeScript e JavaScript

## <a name="next-steps"></a>Passos seguintes

Para obter instruções sobre como embarcar e instalar a Análise do Código de Segurança da Microsoft, consulte o nosso guia de [embarque e instalação](security-code-analysis-onboard.md).

Se tiver mais dúvidas sobre a extensão e as ferramentas oferecidas, confira a nossa [página de PERGUNTAS FREQUENTEs](security-code-analysis-faq.md).
