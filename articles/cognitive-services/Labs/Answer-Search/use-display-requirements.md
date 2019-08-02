---
title: Usar e exibir requisitos-pesquisa de resposta do projeto
titlesuffix: Azure Cognitive Services
description: Use e exiba os requisitos para o ponto de extremidade de pesquisa de resposta do projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706710"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Requisitos de exibição e uso da pesquisa de resposta do projeto

Os requisitos de uso e exibição se aplicam a qualquer implementação do conteúdo e a informações associadas, por exemplo, relações, metadados e outros sinais, disponíveis por meio de chamadas para a pesquisa de conhecimento do Bing, Pesquisa Personalizada do Bing, Pesquisa de Entidade, Pesquisa de Imagem, As APIs Pesquisa de Notícias, Pesquisa de Vídeo, Pesquisa Visual, Pesquisa na Web, Verificação Ortográfica e sugestão automática. Os detalhes de implementação relacionados a esses requisitos podem ser encontrados na documentação para obter recursos e resultados específicos.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Verificação Ortográfica do Bing e API de Sugestão Automática do Bing.

Não:

- Copia, armazena ou armazena em cache todos os dados recebidos do Verificação Ortográfica do Bing, ou APIs de Sugestão Automática do Bing
- Use os dados recebidos das APIs Verificação Ortográfica do Bing ou Sugestão Automática do Bing como parte de um aprendizado de máquina ou atividade de algoritmo semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.

## <a name="2-definitions"></a>2. Definições

- "responder" refere-se a uma categoria de resultados retornados em uma resposta. Por exemplo, uma resposta do API de Pesquisa na Web do Bing pode incluir respostas nas categorias de resultados da página da Web, imagem, vídeo e notícias;
- "resposta" significa qualquer e todas as respostas e dados associados recebidos em resposta a uma única chamada para uma API de pesquisa;
- "resultado" refere-se a um item de informações em uma resposta. Por exemplo, o conjunto de dados conectados com um único artigo de notícias é resultado de uma resposta de notícias.
- As "APIs de pesquisa" significam, coletivamente, as APIs Pesquisa Personalizada do Bing, Pesquisa de Entidade, Pesquisa de Imagem, Pesquisa de Notícias, Pesquisa de Vídeo, pesquisa visual e Pesquisa na Web. 


## <a name="3-search-apis"></a>3. Pesquisar APIs

Os requisitos nesta seção 3 se aplicam às APIs de pesquisa.

**UM. Experiência de pesquisa na Internet.** Todos os dados retornados em respostas só podem ser usados em experiências de pesquisa na Internet. Uma experiência de pesquisa na Internet significa o conteúdo exibido, conforme aplicável: 
- é relevante e responsivo à consulta direta do usuário final ou outra indicação do interesse e da intenção de pesquisa do usuário (por exemplo, consulta de pesquisa indicada pelo usuário); 
- ajuda os usuários a localizar e navegar até as fontes de dados (por exemplo, as URLs fornecidas são implementadas como hiperlinks para que o conteúdo ou a atribuição seja um link clicável, exibido de forma evidente com os dados); ou, se for API de Pesquisa de Entidade do Bing, um link visivelmente para a URL bing.com fornecida na resposta que permite ao usuário navegar até os resultados da pesquisa para a consulta relevante em bing.com;
- inclui vários resultados para o usuário final selecionar (por exemplo, vários resultados da resposta de notícias são exibidos, ou todos os resultados se menos de vários forem retornados); 
- o é limitado a um valor apropriado para atender à finalidade da pesquisa (por exemplo, miniaturas de imagem são dimensionadas na forma de miniatura para a exibição do usuário); 
- inclui a indicação visível para o usuário final que o conteúdo é resultado da pesquisa na Internet (por exemplo, uma instrução que o conteúdo é "da Web"); e
- inclui qualquer outra combinação de medidas apropriada para garantir que o uso de dados recebidos das APIs de pesquisa não viole nenhuma legislação aplicável ou direitos de terceiros (por exemplo, se depender de uma licença Creative Commons, obedecer à licença aplicável) termos). Consulte seus consultores jurídicos para determinar quais medidas podem ser apropriadas.
A única exceção para o requisito da experiência de pesquisa na Internet é para a descoberta de URL, conforme descrito na seção 3E (descoberta de URL de não exibição) a seguir. 

**B. Quanto.** Não:

- Copia, armazena ou armazena em cache quaisquer dados de respostas (exceto a retenção até a extensão permitida pela seção "continuidade do serviço" a seguir); 
- Use os dados recebidos das APIs de pesquisa como parte de aprendizado de máquina ou atividade de algoritmo semelhante para treinar, avaliar ou melhorar os serviços novos ou existentes que você ou terceiros podem oferecer.
- modificar o conteúdo dos resultados (a não ser reformatá-los de forma que não viole nenhum outro requisito), a menos que seja exigido por lei ou acordado pela Microsoft; 
- omita a atribuição e as URLs associadas ao conteúdo do resultado;
- reordenar, incluindo por omissão, resultados exibidos em uma resposta quando um pedido ou uma classificação é fornecido (para o API de Pesquisa Personalizada do Bing, essa regra não se aplica a reordenação implementada por meio do portal do customsearch.ai), a menos que seja exigido por lei ou acordado pela Microsoft ;
- exibir outro conteúdo em qualquer parte de uma resposta de uma forma que possa levar um usuário final a acreditar que o outro conteúdo faz parte da resposta; 
- exibir anúncios que não são fornecidos pela Microsoft em qualquer página que exiba qualquer parte de uma resposta; -exibir qualquer anúncio com respostas (i) da imagem do Bing, notícias ou APIs de Pesquisa de Vídeo; ou (II) que são filtrados ou limitados principalmente (ou exclusivamente) a imagens, notícias e/ou resultados de vídeo.

**C. Avisos e identidade visual.** 

- Inclua de forma proeminente um hiperlink funcional para a política de privacidade da Microsoft https://go.microsoft.com/fwlink/?LinkId=521839 , disponível em, próximo a cada ponto da experiência do usuário (UX) que oferece a um usuário a capacidade de inserir uma consulta de pesquisa. Rotule o hiperlink "política de privacidade da Microsoft".
- Exiba de forma proeminente a identidade visual do Bing, consistente com as https://go.microsoft.com/fwlink/?linkid=833278 diretrizes disponíveis em, próximo a cada ponto da UX que oferece a um usuário a capacidade de inserir uma consulta de pesquisa.  Essa identidade visual deve indicar claramente ao usuário que a Microsoft está capacitando a experiência de pesquisa da Internet.
- Você pode atribuir cada resposta (ou parte de uma resposta) exibida da Web, da imagem, das notícias e das APIs de vídeo do Bing à Microsoft https://go.microsoft.com/fwlink/?linkid=833278 , conforme descrito em, a menos que a Microsoft especifique o contrário ao escrever para seu uso. 
- Não use as respostas de atributo (ou partes de respostas) exibidas da API de Pesquisa Personalizada do Bing para a Microsoft, a menos que a Microsoft especifique o contrário para ser escrita para seu uso específico.


**D. Transferindo respostas.** Se você permitir que um usuário transfira uma resposta de uma API de pesquisa para outro usuário, como por meio de um aplicativo de mensagens ou postagem de mídia social, o seguinte será aplicável: 
- As respostas transferidas devem:
  - Consistem em conteúdo que não é modificado do conteúdo das respostas exibidas para o usuário de transferência (alterações de formatação são permitidas);
  - Não incluir dados no formato de metadados;
  - Para respostas da Web do Bing, imagem, notícias e APIs de vídeo, linguagem de exibição indicando que a resposta foi obtida por meio de uma experiência de pesquisa na Internet fornecida pelo Bing (por exemplo, "da plataforma Bing", saiba mais sobre esta imagem no Bing, ou usando o logotipo do Bing);
  - Para obter respostas da API de Pesquisa Personalizada do Bing, exiba o idioma indicando que a resposta foi obtida por meio de uma experiência de pesquisa na Internet (por exemplo, "Saiba mais sobre este resultado da pesquisa");
  - Exibe de forma proeminente a consulta completa usada para gerar a resposta; e
  - Inclua um link proeminente ou atribuição semelhante à fonte subjacente da resposta, seja diretamente ou por meio do mecanismo de pesquisa (bing.com, m.bing.com ou seu serviço de pesquisa personalizado, conforme aplicável).
- Você não pode automatizar a transferência de respostas. Uma transferência deve ser iniciada por uma ação do usuário claramente evidenciando uma intenção de transferir uma resposta.
- Você só pode habilitar um usuário a transferir respostas que foram exibidas em resposta à consulta do usuário de transferência.

**E. Continuidade do serviço.** Não copie, armazene ou faça cache de quaisquer dados das respostas da API de pesquisa. No entanto, para habilitar a continuidade de acesso a serviços e renderização de dados, você pode reter os resultados exclusivamente nas seguintes condições:

**Vice.** Você pode permitir que um usuário final retenha os resultados em um dispositivo para o menor (i) 24 horas a partir do momento da consulta ou (II) até que um usuário final envie outra consulta para obter resultados atualizados, desde que os resultados retidos possam ser usados apenas:

- para permitir que o usuário final acesse os resultados retornados anteriormente para esse usuário final no dispositivo (por exemplo, no caso de interrupção de serviço); or
- para armazenar os resultados retornados para sua consulta proativa personalizada na antecipação das necessidades do usuário final com base nos sinais do usuário final (por exemplo, no caso de interrupção do serviço antecipado).

**Servidor.** Você pode reter os resultados específicos para um único usuário final com segurança em um servidor que controla e exibir apenas os resultados retidos:

- para permitir que o usuário final acesse um relatório histórico de resultados retornados anteriormente para esse usuário em sua solução, desde que os resultados não possam ser (i) retidos por mais de 21 dias a partir da hora da consulta inicial do usuário final e (II) exibidos em resposta a um u final consulta nova ou repetida; or
- para armazenar os resultados retornados para sua consulta proativa personalizada na antecipação das necessidades de um usuário final com base nos sinais do usuário final para o menor (i) 24 horas a partir do momento da consulta ou (II) até que um usuário final envie outra consulta para obter resultados atualizados.

Sempre que retidos, os resultados de um usuário específico não podem ser commisturados com os resultados para outro usuário, ou seja, os resultados de cada usuário devem ser mantidos e entregues separadamente.

**Genéricos.** Para todas as apresentações de resultados retidos:

- incluir um aviso claro e visível da hora em que a consulta foi enviada,
- apresente ao usuário um botão ou meios semelhantes para consultar novamente e obter resultados atualizados, 
- manter a identidade visual do Bing na apresentação dos resultados e
- excluir (e atualizar com uma nova consulta, se necessário) os resultados armazenados dentro dos períodos de tempo especificados.

**FIXO. Descoberta de URL de não exibição.** Você só pode usar respostas de pesquisa em uma experiência de pesquisa que não seja da Internet para a única finalidade de descobrir URLs de fontes de informações responsivas a uma consulta de seu usuário ou cliente. Você pode copiar essas URLs em um relatório ou resposta semelhante que você fornece (i) somente para esse usuário ou cliente, em resposta a essa consulta e (II) que inclui um conteúdo valioso adicional significativo para a consulta. Os requisitos nas seções 3A a 3E desses requisitos de uso e exibição não se aplicam a esse uso de não exibição, exceto: 

- Você não deve armazenar em cache, copiar ou armazená-los, ou derivados, da resposta de pesquisa, além da cópia de URL limitada descrita anteriormente;
- Certifique-se de que o uso de dados (incluindo as URLs) recebidos das APIs de pesquisa não viole nenhuma legislação aplicável ou direitos de terceiros; e
- Você não deve usar os dados (incluindo as URLs) recebidos das APIs de pesquisa como parte de qualquer índice de pesquisa ou aprendizado de máquina ou atividade de algoritmo semelhante para criar treinamento, avaliação ou melhoria de serviços que você ou terceiros podem oferecer.

## <a name="next-steps"></a>Passos Seguintes
[Visão geral da pesquisa de resposta](overview.md)
