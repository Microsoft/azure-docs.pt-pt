---
title: Utilizar e apresentam os requisitos - pesquisa de resposta de projeto
titlesuffix: Azure Cognitive Services
description: Utilize e apresentam os requisitos para o ponto final de pesquisa de resposta de projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 085cb20e4dad92ed55b5ba0914c677aa50f3ac97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345758"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Pesquisa de resposta de projeto usar e apresentam os requisitos

Requisitos de utilização e a exibição aplicam-se a qualquer implementação do conteúdo e informações associadas, por exemplo, relações, metadados e outros sinais, disponíveis por meio de chamadas para a pesquisa de conhecimento do Bing, pesquisa personalizada do Bing, pesquisa de entidades, pesquisa de imagens, Pesquisa de notícias, pesquisa de vídeos, pesquisa Visual, pesquisa da Web, verificação ortográfica e APIs de sugestão automática. Detalhes de implementação relacionados a esses requisitos podem ser encontrados na documentação para funcionalidades específicas e os resultados.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Verificação ortográfica do Bing e o Bing API de sugestão automática.

Não:

- copiar, armazenar ou colocar em cache todos os dados recebidos da verificação ortográfica do Bing ou APIs de sugestão automática do Bing
- Utilize dados recebidos da verificação de ortografia do Bing ou APIs de sugestão automática do Bing como parte de qualquer machine learning ou de uma atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços de novos ou existentes que ou de terceiros podem oferecer.

## <a name="2-definitions"></a>2. Definições

- "resposta" refere-se a uma categoria de resultados retornados numa resposta. Por exemplo, uma resposta da API de pesquisa Web do Bing pode incluir respostas nas categorias de resultados de página Web, imagem, vídeo e notícias;
- "resposta" significa que quaisquer e todas as respostas e os dados recebidos em resposta a uma única chamada para uma API de pesquisa; associados
- "resultado" refere-se a um item de informações numa resposta. Por exemplo, o conjunto de dados ligados com um artigo de notícias único é um resultado numa resposta de notícias.
- "As APIs de pesquisa" significa, coletivamente, a pesquisa personalizada do Bing, pesquisa de entidades, pesquisa de imagens, pesquisa de notícias, pesquisa de vídeos, pesquisa Visual e APIs de pesquisa da Web. 


## <a name="3-search-apis"></a>3. APIs de pesquisa

Os requisitos em 3 nesta secção aplicam-se para as APIs de pesquisa.

**A. Experiência de pesquisa da Internet.** Todos os dados retornados em respostas só podem ser utilizados em experiências de pesquisa da internet. Uma experiência de pesquisa da internet significa que o conteúdo exibido, conforme aplicável: 
- é relevante e responsivo a consulta direta do utilizador final ou outra indicação do interesse de pesquisa do usuário e a intenção (por exemplo, a consulta de pesquisa indicado por utilizador); 
- ajuda os usuários a localizar e navegue para as origens de dados (por exemplo, os URLs fornecidos são implementados como hiperlinks para que o conteúdo ou a atribuição é um link clicável visível apresentado com os dados); ou, se da API de pesquisa de entidades do Bing, visivelmente ligação para o URL de bing.com fornecido na resposta que permite que o usuário navegue para os resultados da pesquisa para a consulta relevante no bing.com;
- inclui vários resultados para o utilizador final selecionar a partir de (por exemplo, vários resultados da resposta de notícias são apresentados ou são devolvidos todos os resultados se menos de vários); 
- está limitado a uma quantidade adequada atender a finalidade de pesquisa (por exemplo, imagem miniaturas são miniatura em tamanho normal em proporção de exibição ao usuário); 
- inclui uma indicação visível ao utilizador final que o conteúdo está resultados de pesquisa da Internet (por exemplo, uma instrução que o conteúdo é "a partir da web"); e
- inclui qualquer outra combinação de medidas apropriadas para garantir a sua utilização dos dados recebidos a partir as APIs de pesquisa não viole qualquer leis aplicáveis ou direitos de terceiros (por exemplo, se contando com uma licença Creative Commons, conformidade com a licença aplicável termos). Consulte os consultores legais para determinar o que podem ser medidas adequadas.
É a única exceção para o requisito de experiência de pesquisa de internet para a deteção de URL, conforme descrito na seguinte do secção 3E (deteção de URL de apresentação não). 

**B. Restrições.** Não:

- copiar, armazenar ou colocar em cache todos os dados de respostas (exceto a retenção até ao limite permitido pelos seguintes da secção de "Continuidade do serviço"); 
- Utilize dados recebidos a partir as APIs de pesquisa como parte de qualquer machine learning ou de uma atividade algorítmica semelhante para treinar, avaliar ou melhorar os serviços de novos ou existentes que ou de terceiros podem oferecer.
- modificar o conteúdo de resultados (em vez de ao reformatá-los de forma que não viole a qualquer outro requisito), a menos que o exigido por lei ou acordado pela Microsoft; 
- omitir a atribuição e URLs associadas com conteúdo de resultado;
- reordenação, incluindo a omissão, resultados exibidos numa resposta quando uma ordem ou a classificação é fornecida (para a API de pesquisa personalizada do Bing, esta regra não é aplicável a reordenação implementado através do portal de customsearch.ai), a menos que o exigido por lei ou acordado por Microsoft ;
- exibir outros tipos de conteúdo dentro de qualquer parte de uma resposta de uma forma que poderia levar um utilizador final para acreditar que o outro tipo de conteúdo é parte da resposta; 
- apresentar anúncios que não é fornecido pela Microsoft em qualquer página que apresenta qualquer parte de uma resposta; -apresentar publicidade com respostas (i) a partir de imagens do Bing, notícias ou APIs de pesquisa de vídeo; ou (ii) que são filtrados ou limitada principalmente (ou unicamente) aos resultados de imagem, notícias e/ou vídeo.

**C. Avisos e de imagem corporativa.** 

- Destaque incluir um hiperlink funcional para a declaração de privacidade do Microsoft, disponível em https://go.microsoft.com/fwlink/?LinkId=521839, junto a cada ponto na experiência do usuário (UX) que oferece a capacidade de inserir uma consulta de pesquisa de um utilizador. O hiperlink "Declaração de privacidade da Microsoft" da etiqueta.
- Destaque apresentar Bing identidade visual, consistente com as diretrizes disponíveis em https://go.microsoft.com/fwlink/?linkid=833278, junto a cada ponto na UX que oferece a capacidade de inserir uma consulta de pesquisa de um utilizador.  Essa imagem corporativa tem claramente indicar ao utilizador que a Microsoft está incluindo a experiência de pesquisa da internet.
- Pode atributo cada resposta (ou parte de uma resposta) é apresentado na Web do Bing, imagem, notícias e as APIs de vídeo para a Microsoft, conforme descrito em https://go.microsoft.com/fwlink/?linkid=833278, a menos que a Microsoft especifique o contrário escrever para a sua utilização. 
- Não atributo respostas (ou partes de respostas) apresentados a partir da API de pesquisa personalizada do Bing para a Microsoft, a menos que a Microsoft especifique o contrário escrever para a sua utilização particular.


**D. A transferência de respostas.** Se ativar um utilizador para transferir uma resposta de uma API de pesquisa para outro utilizador, tal como através de uma aplicação de mensagens ou lançamento de mídia social, situações seguintes: 
- Tem de respostas transferidas:
  - Consistem em conteúdo que é sem modificações do conteúdo das respostas apresentada ao utilizador a transferir (alterações de formatação são permitidas);
  - Não inclua todos os dados no formato de metadados
  - Para as respostas da Web do Bing, imagem, notícias e as APIs de vídeo, o idioma de apresentação que indica que a resposta foi obtido através de uma experiência de pesquisa da internet com tecnologia Bing (por exemplo, "Com a tecnologia por Bing," "Saiba mais sobre esta imagem no Bing", ou utilizando o logótipo do Bing);
  - Para as respostas da API de pesquisa personalizada do Bing, o idioma de apresentação que indica que a resposta foi obtido através de uma experiência de pesquisa da internet (por exemplo, "Saiba mais sobre este resultado de pesquisa");
  - Destaque apresentar a consulta completa utilizada para gerar a resposta; e
  - Inclua um link proeminente ou atribuição semelhante para a origem subjacente da resposta, diretamente ou por meio do mecanismo de pesquisa (bing.com, m.bing.com ou seu serviço de pesquisa personalizada, conforme aplicável).
- Não, pode automatizar a transferência de respostas. Uma transferência tem de ser iniciada por uma ação do utilizador evidencing claramente uma intenção de transferência de uma resposta.
- Apenas pode ativar um utilizador para transferir as respostas que foram apresentadas em resposta à consulta de transferência do utilizador.

**E. Continuidade do serviço.** Não copiar, armazenar ou colocar em cache todos os dados de respostas de API de pesquisa. No entanto, para ativar a continuidade de acesso de serviço e composição de dados, pode manter os resultados apenas nas seguintes condições:

**Dispositivo.** Pode ativar um utilizador final manter os resultados num dispositivo para o menor de (i) 24 horas desde o momento da consulta ou (ii) até que um utilizador final envia outra consulta para obter resultados atualizados, desde que manteve resultados podem ser utilizados apenas:

- para permitir que o utilizador final aceder aos resultados devolvidos anteriormente para que o utilizador final nesse dispositivo (por exemplo, em caso de interrupção do serviço); ou
- para armazenar os resultados devolvidos na sua consulta proativa personalizadas antes das necessidades do utilizador final com base em sinais desse utilizador final (por exemplo, em caso de interrupção do serviço antecipado).

**Servidor.** Pode manter resultados específicos para um único utilizador final com segurança num servidor pode controlar e exibe os resultados retidos apenas:

- para permitir que o utilizador final aceder a um relatório de histórico dos resultados anteriormente devolvido ao que o utilizador na sua solução, desde que os resultados podem não ser (i) retidos por mais de 21 dias a partir da hora de consulta inicial do utilizador final e (ii) exibida em resposta a um fim u consulta do utilizador novo ou repetidas; ou
- para armazenar os resultados devolvidos na sua consulta proativa personalizadas antes das necessidades do utilizador final com base em sinais desse utilizador final para o menor de (i) 24 horas desde o momento da consulta ou (ii) até que um utilizador final envia outra consulta para obter resultados atualizados.

Sempre que estiverem, resultados para um utilizador específico não podem ser commingled com resultados para outro utilizador, ou seja, os resultados de cada utilizador tem de ser mantidos e entregue em separado.

**Geral.** Para todos os apresentação de resultados retidos:

- incluir um aviso claro e visível do tempo que a consulta foi enviada,
- apresentar ao usuário um botão ou meio semelhante para efetuar uma nova consulta e obter resultados atualizados, 
- manter o Bing a imagem corporativa na apresentação de resultados, e
- Eliminar (e atualizar com uma nova consulta, se necessário) os resultados armazenados dentro os períodos de tempo especificados.

**F. Deteção de URL de apresentação não.** Só poderá utilizar as respostas da pesquisa numa experiência de pesquisa não internet para o único propósito de URLs de fontes de informações de capacidade de resposta a uma consulta do cliente ou utilizador a detetar. Pode copiar esses URLs num relatório ou resposta semelhante que fornecem (i) apenas para esse utilizador ou cliente, em resposta a essa consulta e (ii) que inclui significativo adicional valioso conteúdo relevante para a consulta. Os requisitos em secções 3A por meio de 3E destes requisitos de utilização e a exibição não são aplicáveis a esta utilização não apresentar, exceto: 

- Não deve armazenar em cache, copiar ou armazenar todos os dados ou conteúdo a partir de, ou derivada da, a resposta da pesquisa, que não seja a cópia de URL limitada descrita anteriormente;
- Certifique-se de que a utilização de dados (incluindo os URLs) recebida das APIs de pesquisa não viole qualquer leis aplicáveis ou direitos de terceiros; e
- Não, deve usar os dados (incluindo os URLs) recebidos das APIs de pesquisa como parte de qualquer índice de pesquisa ou machine learning ou atividade algorítmica semelhante para criar a treinar, avaliar ou melhorar os serviços que podem oferecer a ou de terceiros.

## <a name="next-steps"></a>Passos Seguintes
[Descrição geral de pesquisa de resposta](overview.md)
