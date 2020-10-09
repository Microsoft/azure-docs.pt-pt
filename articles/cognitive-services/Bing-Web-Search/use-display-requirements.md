---
title: Requisitos de utilização e exibição para as APIs de pesquisa de Bing
titleSuffix: Azure Cognitive Services
description: Os requisitos para a exibição dos resultados da pesquisa resultam das APIs de pesquisa de Bing nas suas aplicações.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "60499890"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Requisitos de utilização e apresentação da API de Pesquisa do Bing

Estes requisitos de utilização e exibição aplicam-se a qualquer implementação do conteúdo e informações associadas das seguintes APIs de Pesquisa de Bing, incluindo relações, metadados e outros sinais.

- Pesquisa Personalizada do Bing
- Pesquisa de Entidades do Bing
- Pesquisa de Imagens do Bing
- Pesquisa do Bing Notícias
- Pesquisa de Vídeos do Bing
- Pesquisa Visual do Bing
- Pesquisa na Web do Bing
- Verificação Ortográfica do Bing
- Sugestão Automática do Bing

## <a name="definitions"></a>Definições


|Termo  |Descrição  |
|---------|---------|
|Resposta     | Uma categoria de resultados devolvidos numa resposta. Por exemplo, uma resposta da API de Pesquisa Web Bing pode incluir respostas nas categorias de resultados da página web, imagem, vídeo, visual e notícias. |
|Resposta     | Todas e todas as respostas e dados associados recebidos em resposta a uma única chamada para uma API de Pesquisa. |
|Resultado    | Um item de informação numa resposta. Por exemplo, o conjunto de dados ligados a um único artigo de notícias é o resultado de uma resposta noticiosa. |
|APIs de pesquisa    | coletivamente, a Pesquisa Personalizada Bing, Pesquisa de Entidades, Pesquisa de Imagem, Pesquisa de Notícias, Pesquisa de Vídeo, Pesquisa Visual, Pesquisa Empresarial Local e APIs de Pesquisa Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Bing Spell Check e Bing Autosuggest API restrições

Não:

- Copie, guarde ou cache quaisquer dados que receba das APIs de Bing Spell Check ou Bing Autosuggest.
- Utilize os dados que recebe de Bing Spell Check ou Bing Autosuggest APIs como parte de qualquer aprendizagem automática ou atividade algorítmica semelhante. Não utilize estes dados para treinar, avaliar ou melhorar serviços novos ou existentes que você ou terceiros possam oferecer.

## <a name="bing-search-apis"></a>APIs de Pesquisa do Bing

> [!NOTE]
> Os requisitos nesta secção aplicam-se apenas às APIs de pesquisa, que não incluem Bing Spell Check ou Bing Autosuggest. 

### <a name="internet-search-experience-requirements"></a>Requisitos de experiência de pesquisa na Internet

Todos os dados devolvidos em respostas só podem ser utilizados em experiências de pesquisa na Internet. Uma experiência de pesquisa na Internet significa o conteúdo apresentado: 

- É relevante e responde à consulta direta do utilizador final, ou outra indicação do seu interesse e intenção de pesquisa (por exemplo, uma consulta de pesquisa indicada pelo utilizador). 

- Ajuda os utilizadores a encontrar e navegar para as fontes de dados da resposta. Por exemplo, fornecer links clicáveis de hiperligações na resposta.

- Inclui vários resultados para o utilizador selecionar. 

- Estão numa colocação que permite que os utilizadores procurem.

- Inclui uma indicação visível de que o conteúdo é um resultado de pesquisa na Internet. Por exemplo, uma afirmação de que o conteúdo é "da web".

- Inclui quaisquer outras medidas adequadas para garantir que os seus dados de Bing Search API não violem nenhuma lei aplicável ou direitos de terceiros. Consulte os seus assessores jurídicos para determinar que medidas podem ser adequadas.

A única exceção a estes requisitos de experiência de pesquisa na Internet é para a descoberta de URL, como descrito mais tarde neste artigo. 

### <a name="restrictions"></a>Restrições

Não:

- Copiar, armazenar ou cache quaisquer dados de respostas (exceto a retenção na medida permitida pela [continuidade do serviço](#continuity-of-service). 

- Utilize dados recebidos das APIs de pesquisa como parte de qualquer aprendizagem automática ou atividade algorítmica semelhante. Não utilize estes dados para treinar, avaliar ou melhorar serviços novos ou existentes que você ou terceiros possam oferecer.

- Modifique o conteúdo dos resultados (para além de os reformatar de uma forma que não viole qualquer outro requisito), a menos que seja exigido por lei ou acordado pela Microsoft. 

- Omitir informações de atribuição e URLs associados ao conteúdo de resultados.

- Reencomendar, incluindo por omissão, os resultados apresentados numa resposta quando uma encomenda ou ranking é fornecida, a menos que seja exigido por lei ou acordado pela Microsoft. 

    > [!NOTE]
    > Este requisito não se aplica ao reordenamento implementado através do portal para a API de Pesquisa Personalizada Bing.

- Mostrar outros conteúdos dentro de qualquer parte de uma resposta de uma forma que levaria um utilizador a acreditar que o outro conteúdo faz parte da resposta. 

- Exibir publicidade que não seja fornecida pela Microsoft em nenhuma página que apresente qualquer parte de uma resposta. 

- Exiba qualquer publicidade em páginas com respostas:
    - A partir da Imagem Bing, Pesquisa de Notícias, Pesquisa de Vídeo ou APIs de Pesquisa Visual
    - Que são filtrados ou limitados principalmente (ou exclusivamente) aos resultados de imagem, notícias e/ou vídeo ou pesquisa visual.

### <a name="notices-and-branding"></a>Avisos e marca 
Fazer:

- De forma proeminente, inclui-se uma hiperligação funcional para a Declaração de Privacidade da [Microsoft,](https://go.microsoft.com/fwlink/?LinkId=521839)perto de cada ponto da experiência do utilizador (UX) que oferece a um utilizador a capacidade de inserir uma consulta de pesquisa. Rotular a hiperligação **Microsoft Privacy Statement**.

- Exibir de forma proeminente a marca Bing, consistente com as [Diretrizes de Utilização da Marca Bing,](https://go.microsoft.com/fwlink/?linkid=833278)perto de cada ponto do UX que oferece a um utilizador a capacidade de inserir uma consulta de pesquisa. Esta marca deve indicar claramente ao utilizador que a Microsoft está a alimentar a experiência de pesquisa na Internet.

- Pode atribuir a Microsoft cada resposta (ou parte de uma resposta) exibida a partir da Pesquisa web de Bing, pesquisa de imagem, pesquisa de notícias, pesquisa de vídeo e APIs de pesquisa visual, a menos que a Microsoft especifique o contrário por escrito para a sua utilização. Isto é descrito nas [Diretrizes de Utilização da Marca Bing.](https://go.microsoft.com/fwlink/?linkid=833278) 

Não:

- Atribua respostas (ou porções de respostas) apresentadas da API de Pesquisa Personalizada Bing à Microsoft, a menos que a Microsoft especifique o contrário por escrito para a sua utilização particular.

### <a name="transferring-responses"></a>Transferência de respostas

Se permitir que um utilizador transfira uma resposta de uma API de pesquisa para outro utilizador, como por exemplo através de uma aplicação de mensagens ou de uma publicação nas redes sociais, aplicam-se: 

- As respostas transferidas devem:
  - Consiste em conteúdo que não é modificado a partir do conteúdo das respostas apresentadas ao utilizador transferidor. As alterações de formatação são permitidas.
  - Não inclua quaisquer dados em forma de metadados.
  - Para respostas das APIs Bing Web, Image, News, Video e Visual, o idioma de exibição indicando que a resposta foi obtida através de uma experiência de pesquisa na Internet alimentada por Bing. Por exemplo, pode exibir linguagem como "Powered by Bing" ou "Saiba mais sobre esta imagem em Bing", ou pode usar o logótipo Bing.
  - Para respostas da API de Pesquisa Personalizada de Bing, o idioma de exibição indicando que a resposta foi obtida através de uma experiência de pesquisa na Internet. Por exemplo, pode exibir linguagem como "Saiba mais sobre este resultado de pesquisa".
  - Exibir de forma proeminente a consulta completa utilizada para gerar a resposta.
  - Inclua um link proeminente ou uma atribuição semelhante à fonte subjacente da resposta, diretamente ou através do motor de busca (bing.com, m.bing.com ou do seu serviço de pesquisa personalizado, conforme aplicável).
- Não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do utilizador que evidencia claramente a intenção de transferir uma resposta.
- Só pode permitir que um utilizador transfira respostas que foram apresentadas em resposta à consulta do utilizador transferidor.

### <a name="continuity-of-service"></a>Continuidade do serviço 

Não copie, guarde ou cache quaisquer dados das respostas da API de pesquisa. No entanto, para permitir a continuidade do acesso ao serviço e da prestação de dados, poderá conservar os resultados unicamente nas seguintes condições:

#### <a name="device"></a>Dispositivo

Pode permitir que um utilizador retenha resultados num dispositivo durante menos de (i) 24 horas a partir do momento da consulta, ou (ii) até que um utilizador apresente outra consulta para resultados atualizados, desde que os resultados retidos possam ser utilizados apenas:

- Para permitir ao utilizador aceder aos resultados anteriormente devolvidos a esse utilizador naquele dispositivo (por exemplo, em caso de interrupção de serviço).
- Para armazenar os resultados devolvidos para a sua consulta proativa personalizada em antecipação das necessidades do utilizador, com base nos sinais desse utilizador (por exemplo, em caso de interrupção antecipada do serviço).

#### <a name="server"></a>Servidor

Pode reter resultados específicos de um único utilizador de forma segura num servidor que controla e exibir apenas os resultados retidos:

- Para permitir ao utilizador aceder a um relatório histórico de resultados anteriormente devolvidos a esse utilizador na sua solução. Os resultados podem não ser retidos por mais de 21 dias a partir do momento da consulta inicial do utilizador final, e (ii) apresentados em resposta à consulta nova ou repetida de um utilizador.
- Para armazenar os resultados devolvidos para a sua consulta proativa personalizada em antecipação das necessidades do utilizador, com base nos sinais desse utilizador. Pode armazenar estes resultados para o menor número de (i) 24 horas a partir do momento da consulta, ou (ii) até que um utilizador envie outra consulta para obter resultados atualizados.

Sempre que retidos, os resultados para um utilizador específico não podem ser misturados com resultados para outro utilizador. Ou seja, os resultados de cada utilizador devem ser conservados e entregues separadamente.

### <a name="general"></a>Geral 

Para toda a apresentação dos resultados retidos:

- Inclua um aviso claro e visível da hora em que a consulta foi enviada.
- Presentear o utilizador com um botão ou meios semelhantes para reencamis e obter resultados atualizados. 
- Mantenha a marca Bing na apresentação dos resultados.
- Eliminar (e refrescar com uma nova consulta, se necessário) os resultados armazenados dentro dos prazos especificados.

### <a name="non-display-url-discovery"></a>Descoberta de URL não-display 

Só pode utilizar respostas de pesquisa numa experiência de pesquisa não internet com o único propósito de descobrir URLs de fontes de informação que respondam a uma consulta do seu utilizador ou cliente. Pode copiar esses URLs num relatório ou resposta semelhante que forneça:

- Apenas a esse utilizador ou cliente, em resposta a essa consulta.
- Só se incluir conteúdo valioso adicional significativo, relevante para a consulta.

As secções anteriores de requisitos de utilização e visualização de APIs de pesquisa não se aplicam a esta utilização não exibida, exceto para as seguintes: 

- Não cache, copiar ou armazenar quaisquer dados ou conteúdos de, ou derivados, da resposta de pesquisa, para além da cópia limitada do URL descrita anteriormente.
- Garantir que a sua utilização de dados (incluindo os URLs) recebidos das APIs de pesquisa não viola nenhuma lei aplicável ou direitos de terceiros.
- Não utilize os dados (incluindo os URLs) recebidos das APIs de pesquisa como parte de qualquer índice de pesquisa ou machine learning ou atividade algorítmica semelhante. Não utilize estes dados para criar comboios, avaliar ou melhorar serviços que você ou terceiros possam oferecer.

## <a name="gdpr-compliance"></a>Conformidade com o RGPD  

No que diz respeito a quaisquer dados pessoais sujeitos ao Regulamento Geral de Proteção de Dados (RGPD) da União Europeia e que seja processado em conexão com chamadas para as APIs de pesquisa, Bing Spell Check API ou Bing Autosuggest API, entende que você e a Microsoft são controladores de dados independentes ao abrigo do RGPD. É independentemente responsável pela sua conformidade com o RGPD.  

