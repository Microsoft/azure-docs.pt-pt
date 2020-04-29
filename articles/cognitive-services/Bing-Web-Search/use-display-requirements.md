---
title: Requisitos de utilização e visualização para as APIs de pesquisa de Bing
titleSuffix: Azure Cognitive Services
description: Os requisitos para exibir os resultados da pesquisa a partir das APIs de pesquisa de Bing nas suas aplicações.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60499890"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Requisitos de utilização e apresentação da API de Pesquisa do Bing

Estes requisitos de utilização e exibição aplicam-se a qualquer implementação do conteúdo e informações associadas a partir das seguintes APIs de Pesquisa bing, incluindo relacionamentos, metadados e outros sinais.

- Pesquisa Personalizada do Bing
- Pesquisa de Entidades do Bing
- Pesquisa de Imagens do Bing
- Pesquisa do Bing Notícias
- Pesquisa de Vídeos do Bing
- Pesquisa Visual do Bing
- Pesquisa Web do Bing
- Verificação Ortográfica do Bing
- Sugestão Automática do Bing

## <a name="definitions"></a>Definições


|Termo  |Descrição  |
|---------|---------|
|Resposta     | Uma categoria de resultados voltou numa resposta. Por exemplo, uma resposta da API de Pesquisa Web bing pode incluir respostas nas categorias de resultados da página web, imagem, vídeo, visual e notícias. |
|Resposta     | Todas e quaisquer respostas e dados associados recebidos em resposta a uma única chamada para uma API de pesquisa. |
|Resultado    | Um item de informação numa resposta. Por exemplo, o conjunto de dados ligados a um único artigo noticioso é resultado de uma resposta noticiosa. |
|Pesquisar APIs    | coletivamente, a Pesquisa Personalizada bing, pesquisa de entidades, pesquisa de imagem, pesquisa de notícias, pesquisa de vídeo, pesquisa visual, pesquisa de negócios locais e APIs de pesquisa web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Bing Spell Check e Bing Autosuggest API restrições

Não:

- Copiar, armazenar ou cache quaisquer dados que receba do Bing Spell Check ou Bing Autosuggest APIs.
- Utilize os dados que receber de Bing Spell Check ou Bing Autosuggest APIs como parte de qualquer aprendizagem automática ou atividade algorítmica semelhante. Não utilize estes dados para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros possam oferecer.

## <a name="bing-search-apis"></a>APIs de Pesquisa do Bing

> [!NOTE]
> Os requisitos nesta secção aplicam-se apenas às APIs de pesquisa, que não incluem bing Spell Check ou Bing Autosuggest. 

### <a name="internet-search-experience-requirements"></a>Requisitos de experiência de pesquisa na Internet

Todos os dados devolvidos em respostas só podem ser utilizados em experiências de pesquisa na Internet. Uma experiência de pesquisa na Internet significa o conteúdo apresentado: 

- É relevante e sensível à consulta direta do utilizador final, ou outra indicação do seu interesse e intenção de pesquisa (por exemplo, uma consulta de pesquisa indicada pelo utilizador). 

- Ajuda os utilizadores a encontrar e navegar para as fontes de dados da resposta. Por exemplo, fornecer links clicáveis a partir de hiperligações na resposta.

- Inclui vários resultados para o utilizador escolher. 

- Estão numa colocação que permite aos utilizadores pesquisar.

- Inclui uma indicação visível de que o conteúdo é um resultado de pesquisa na Internet. Por exemplo, uma afirmação de que o conteúdo é "da web".

- Inclui quaisquer outras medidas apropriadas para garantir que os seus dados da API de Pesquisa de Bing não violem quaisquer leis ou direitos de terceiros aplicáveis. Consulte os seus assessores jurídicos para determinar quais as medidas adequadas.

A única exceção a estes requisitos de experiência de pesquisa na Internet é a descoberta de URL, como descrito mais tarde neste artigo. 

### <a name="restrictions"></a>Restrições

Não:

- Copiar, armazenar ou cache quaisquer dados de respostas (exceto a retenção na medida permitida pela [continuidade do serviço](#continuity-of-service). 

- Utilize os dados recebidos das APIs de pesquisa como parte de qualquer aprendizagem automática ou atividade algorítmica semelhante. Não utilize estes dados para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros possam oferecer.

- Modificar o conteúdo dos resultados (além de reformá-los de uma forma que não viole qualquer outro requisito), a menos que exigido por lei ou acordado pela Microsoft. 

- Omite informações de atribuição e URLs associados ao conteúdo do resultado.

- Reordenar, incluindo por omissão, os resultados apresentados numa resposta quando uma ordem ou classificação é fornecida, a menos que exigido por lei ou acordado pela Microsoft. 

    > [!NOTE]
    > Este requisito não se aplica ao reordenamento implementado através do portal para a API de Pesquisa Personalizada bing.

- Exiba outros conteúdos dentro de qualquer parte de uma resposta de uma forma que leve um utilizador a acreditar que o outro conteúdo faz parte da resposta. 

- Exiba publicidade que não seja fornecida pela Microsoft em nenhuma página que apresente qualquer parte de uma resposta. 

- Mostrar qualquer publicidade em páginas com respostas:
    - A partir da Imagem bing, pesquisa de notícias, pesquisa de vídeo ou APIs de pesquisa visual
    - São filtrados ou limitados principalmente (ou exclusivamente) a resultados de imagem, notícias e/ou vídeo ou pesquisa visual.

### <a name="notices-and-branding"></a>Avisos e marca 
Fazer:

- Em destaque incluem uma hiperligação funcional à Declaração de Privacidade da [Microsoft,](https://go.microsoft.com/fwlink/?LinkId=521839)perto de cada ponto da experiência do utilizador (UX) que oferece a um utilizador a capacidade de inserir uma consulta de pesquisa. Rotule a hiperligação **da Declaração**de Privacidade da Microsoft .

- Exiba de forma proeminente a marca Bing, consistente com as Diretrizes de Utilização da [Marca Bing,](https://go.microsoft.com/fwlink/?linkid=833278)perto de cada ponto do UX que oferece a um utilizador a capacidade de inserir uma consulta de pesquisa. Esta marca deve indicar claramente ao utilizador que a Microsoft está a alimentar a experiência de pesquisa na Internet.

- Pode atribuir cada resposta (ou parte de uma resposta) exibida a partir da Pesquisa web do Bing, Pesquisa de Imagens, Pesquisa de Notícias, Pesquisa de Vídeo e APIs de Pesquisa Visual à Microsoft, a menos que a Microsoft especifique o contrário por escrito para a sua utilização. Isto é descrito nas Diretrizes de [Utilização](https://go.microsoft.com/fwlink/?linkid=833278)da Marca Bing . 

Não:

- Atribui respostas (ou partes de respostas) exibidas a partir da API de Pesquisa Personalizada Bing para a Microsoft, a menos que a Microsoft especifique o contrário por escrito para a sua utilização específica.

### <a name="transferring-responses"></a>Transferência de respostas

Se permitir que um utilizador transfira uma resposta de uma API de pesquisa para outro utilizador, como por exemplo através de uma aplicação de mensagens ou de publicação nas redes sociais, aplicam-se as seguintes: 

- As respostas transferidas devem:
  - Consistir em conteúdo que não seja modificado a partir do conteúdo das respostas apresentadas ao utilizador transferidor. As alterações de formatação são admissíveis.
  - Não incluir quaisquer dados no formulário de metadados.
  - Para respostas da Bing Web, Image, News, Video e Visual APIs, a linguagem de exibição indicando que a resposta foi obtida através de uma experiência de pesquisa na Internet alimentada por Bing. Por exemplo, pode exibir linguagem como "Powered by Bing" ou "Saiba mais sobre esta imagem no Bing", ou pode usar o logótipo Bing.
  - Para respostas da API de pesquisa personalizada bing, a linguagem de exibição indicando que a resposta foi obtida através de uma experiência de pesquisa na Internet. Por exemplo, pode exibir linguagem como "Saiba mais sobre este resultado de pesquisa".
  - Exibia de forma proeminente a consulta completa usada para gerar a resposta.
  - Inclua um link proeminente ou atribuição semelhante à fonte subjacente da resposta, quer diretamente quer através do motor de busca (bing.com, m.bing.com ou o seu serviço de pesquisa personalizado, conforme aplicável).
- Não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do utilizador que evidencia claramente a intenção de transferir uma resposta.
- Só pode permitir que um utilizador transfira respostas que foram apresentadas em resposta à consulta do utilizador transferido.

### <a name="continuity-of-service"></a>Continuidade do serviço 

Não copie, armazene ou cache quaisquer dados das respostas da Search API. No entanto, para permitir a continuidade do acesso ao serviço e da renderização de dados, poderá conservar os resultados unicamente nas seguintes condições:

#### <a name="device"></a>Dispositivo

Pode permitir que um utilizador retenha resultados num dispositivo durante o menor de (i) 24 horas a partir da hora da consulta, ou (ii) até que um utilizador submeta outra consulta para resultados atualizados, desde que os resultados retidos possam ser utilizados apenas:

- Para permitir ao utilizador aceder aos resultados anteriormente devolvidos a esse utilizador naquele dispositivo (por exemplo, em caso de interrupção do serviço).
- Para armazenar os resultados devolvidos para a sua consulta proactiva personalizada em antecipação às necessidades do utilizador, com base nos sinais desse utilizador (por exemplo, em caso de interrupção prevista do serviço).

#### <a name="server"></a>Server

Pode reter resultados específicos de um único utilizador de forma segura num servidor que controla e exibir apenas os resultados retidos:

- Para permitir ao utilizador aceder a um relatório histórico de resultados anteriormente devolvidos a esse utilizador na sua solução. Os resultados podem não ser (i) retidos por mais de 21 dias a partir da hora da consulta inicial do utilizador final, e (ii) apresentados em resposta à nova ou repetida consulta de um utilizador.
- Para armazenar os resultados devolvidos para a sua consulta proativa personalizada em antecipação às necessidades do utilizador, com base nos sinais desse utilizador. Pode armazenar estes resultados durante o menor de (i) 24 horas a partir do momento da consulta, ou (ii) até que um utilizador submeta outra consulta para resultados atualizados.

Sempre que retido, os resultados para um utilizador específico não podem ser misturados com resultados para outro utilizador. Ou seja, os resultados de cada utilizador devem ser retidos e entregues separadamente.

### <a name="general"></a>Geral 

Para toda a apresentação dos resultados retidos:

- Inclua um aviso claro e visível da hora em que a consulta foi enviada.
- Apresente ao utilizador um botão ou meios semelhantes para re-consulta e obter resultados atualizados. 
- Mantenha a marca Bing na apresentação dos resultados.
- Eliminar (e refrescar com uma nova consulta, se necessário) os resultados armazenados dentro dos prazos especificados.

### <a name="non-display-url-discovery"></a>Descoberta de URL não-exibida 

Só pode utilizar respostas de pesquisa numa experiência de pesquisa não na Internet com o único propósito de descobrir URLs de fontes de informação que respondam a uma consulta do seu utilizador ou cliente. Pode copiar esses URLs num relatório ou resposta semelhante que forneça:

- Apenas a esse utilizador ou cliente, em resposta a essa consulta.
- Só se incluir conteúdo adicional significativo e valioso, relevante para a consulta.

As secções anteriores de utilização e os requisitos de visualização das APIs de pesquisa não se aplicam a esta utilização anti-visualização, exceto nas seguintes: 

- Não cache, copy ou guarde quaisquer dados ou conteúdos da resposta de pesquisa, que não seja a cópia de URL limitada descrita anteriormente.
- Garantir que a sua utilização de dados (incluindo os URLs) recebidos das APIs de Pesquisa não viola quaisquer leis ou direitos de terceiros aplicáveis.
- Não utilize os dados (incluindo os URLs) recebidos das APIs de pesquisa como parte de qualquer índice de pesquisa ou aprendizagem automática ou atividade algorítmica semelhante. Não utilize estes dados para criar comboio, avaliar ou melhorar os serviços que você ou terceiros possam oferecer.

## <a name="gdpr-compliance"></a>Conformidade com o RGPD  

No que diz respeito a quaisquer dados pessoais sujeitos ao Regulamento Geral de Proteção de Dados (RGPD) da União Europeia e que é tratado em relação a chamadas para as APIs de Pesquisa, Bing Spell Check API ou Bing Autosuggest API, você entende que você e a Microsoft são controladores de dados independentes sob o RGPD. É responsável independentemente pelo seu cumprimento ao RGPD.  

