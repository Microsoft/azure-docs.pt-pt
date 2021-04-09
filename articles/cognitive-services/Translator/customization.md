---
title: Personalização de Tradução - Tradutor
titleSuffix: Azure Cognitive Services
description: Utilize o Microsoft Tradutor Hub para construir o seu próprio sistema de tradução automática utilizando a sua terminologia e estilo preferidos.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 0930024a791fe8b76c90e8ab4249a070b22a1c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98898041"
---
# <a name="customize-your-text-translations"></a>Personalize as suas traduções de texto

O Tradutor Personalizado é uma característica do serviço Tradutor, que permite aos utilizadores personalizar a tradução avançada da máquina neural da Microsoft Tradutor ao traduzir texto usando o Tradutor (apenas versão 3).

A funcionalidade também pode ser usada para personalizar a tradução da fala quando usada com [o Discurso dos Serviços Cognitivos.](../speech-service/index.yml)

## <a name="custom-translator"></a>Custom Translator

Com o Custom Tradutor, pode construir sistemas de tradução neural que compreendam a terminologia utilizada no seu próprio negócio e indústria. O sistema de tradução personalizado irá então integrar-se em aplicações, fluxos de trabalho e websites existentes.

### <a name="how-does-it-work"></a>Como funciona?

Utilize os seus documentos previamente traduzidos (folhetos, páginas web, documentação, etc.) para construir um sistema de tradução que reflita a terminologia e o estilo específicos do seu domínio, melhor do que um sistema de tradução padrão. Os utilizadores podem carregar documentos TMX, XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados paralelos ao nível do documento, mas que ainda não estão alinhados ao nível da frase. Se os utilizadores tiverem acesso a versões do mesmo conteúdo em vários idiomas, mas em documentos separados o Custom Tradutor poderá corresponder automaticamente as frases entre documentos.  O sistema também pode usar dados monolingues em ambos os idiomas para complementar os dados de formação paralela para melhorar as traduções.

O sistema personalizado está então disponível através de uma chamada regular para Tradutor utilizando o parâmetro da categoria.

Dado o tipo e quantidade adequados de dados de formação, não é incomum esperar ganhos entre 5 e 10, ou ainda mais pontos BLEU sobre a qualidade da tradução utilizando o Tradutor Personalizado.

Mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis podem ser encontrados no Guia do [Utilizador do Tradutor Personalizado.](./custom-translator/overview.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie um sistema de linguagem personalizado utilizando o Tradutor Personalizado](./custom-translator/overview.md)
