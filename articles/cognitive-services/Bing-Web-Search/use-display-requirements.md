---
title: Utilizar e apresentam os requisitos para as APIs de pesquisa do Bing
titleSuffix: Azure Cognitive Services
description: Os requisitos para apresentar os resultados das APIs de pesquisa do Bing da pesquisa nas suas aplicações.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738730"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Requisitos de utilização e apresentação da API de Pesquisa do Bing

Estes requisitos de utilização e a exibição de aplicar a qualquer implementação do conteúdo e informações associadas nas APIs de pesquisa do Bing seguintes, incluindo as relações, metadados e outros sinais.

- Pesquisa Personalizada do Bing
- Pesquisa de Entidades do Bing
- Pesquisa de Imagens do Bing
- Pesquisa de Notícias do Bing
- Pesquisa de Vídeos do Bing
- Pesquisa Visual do Bing
- Pesquisa Web do Bing
- Verificação Ortográfica do Bing
- Sugestão Automática do Bing

## <a name="definitions"></a>Definições


|Termo  |Descrição  |
|---------|---------|
|Resposta     | Uma categoria de resultados retornados numa resposta. Por exemplo, uma resposta da API de pesquisa Web do Bing pode incluir respostas nas categorias de resultados de página Web, imagem, vídeo, visual e notícias. |
|Resposta     | Quaisquer e todas as respostas e os dados associados recebidos em resposta a uma única chamada para uma API de pesquisa. |
|Resultado    | Um item de informações numa resposta. Por exemplo, o conjunto de dados ligados com um artigo de notícias único é um resultado numa resposta de notícias. |
|Pesquisar APIs    | coletivamente, a pesquisa personalizada do Bing, pesquisa de entidades, pesquisa de imagens, pesquisa de notícias, pesquisa de vídeos, pesquisa Visual, Local de negócios de pesquisa e as APIs de pesquisa Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Restrições de verificação ortográfica do Bing e API de sugestão automática do Bing

Não:

- Copiar, armazenar ou colocar em cache quaisquer dados recebidos da verificação de ortografia do Bing ou APIs de sugestão automática do Bing.
- Utilize dados recebidos de verificação ortográfica do Bing ou APIs de sugestão automática do Bing como parte de qualquer machine learning ou de uma atividade de algoritmo semelhante. Não utilize estes dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que pode oferecer a ou de terceiros.

## <a name="bing-search-apis"></a>APIs de Pesquisa do Bing

> [!NOTE]
> Os requisitos nesta secção aplicam-se para apenas as APIs de pesquisa, que não inclui verificação ortográfica do Bing ou sugestão automática do Bing. 

### <a name="internet-search-experience-requirements"></a>Requisitos de experiência de pesquisa de Internet

Todos os dados retornados em respostas só podem ser utilizados em experiências de pesquisa da internet. Uma experiência de pesquisa da internet significa que o conteúdo exibido: 

- É relevante e capacidade de resposta a consulta direta do utilizador final ou outra indicação do seu interesse de pesquisa e a intenção (por exemplo, uma consulta de pesquisa indicado por utilizador). 

- Ajuda os usuários a localizar e navegue para origens de dados da resposta. Por exemplo, fornecendo links clicáveis de hiperlinks na resposta.

- Inclui vários resultados para o utilizador selecionar a partir de. 

- Estão num posicionamento que permite aos utilizadores procurar.

- Inclui uma indicação visível que o conteúdo é um resultado de pesquisa da internet. Por exemplo, uma instrução que o conteúdo é "a partir da web".

- Inclui quaisquer outras medidas apropriadas para garantir que os dados da API de pesquisa do Bing não viole qualquer leis aplicáveis ou direitos de terceiros. Consulte os consultores legais para determinar o que podem ser medidas adequadas.

A única exceção a esses requisitos de experiência de pesquisa de internet é para a deteção de URL, conforme descrito neste artigo. 

### <a name="restrictions"></a>Restrições

Não:

- Copiar, armazenar ou quaisquer dados a partir de respostas em cache (exceto a retenção até ao limite permitido pela [continuidade do serviço](#continuity-of-service). 

- Utilize dados recebidos a partir as APIs de pesquisa como parte de qualquer machine learning ou de uma atividade de algoritmo semelhante. Não utilize estes dados para treinar, avaliar ou melhorar os serviços de novos ou existentes que pode oferecer a ou de terceiros.

- Modificar o conteúdo de resultados (exceto ao reformatá-los de forma que não viole a qualquer outro requisito), a menos que o exigido por lei ou acordado pela Microsoft. 

- Omitir informações de atribuição e URLs associadas com conteúdo de resultado.

- Reordene, incluindo a omissão, os resultados exibidos numa resposta quando uma ordem ou a classificação é fornecida, a menos que o exigido por lei ou acordado pela Microsoft. 

    > [!NOTE]
    > Este requisito não é aplicável a reordenação implementado através do portal para a API de pesquisa personalizada do Bing.

- Exiba outros tipos de conteúdo dentro de qualquer parte de uma resposta de uma forma que poderia levar um utilizador para acreditar que o outro tipo de conteúdo é parte da resposta. 

- Apresentar anúncios que não é fornecido pela Microsoft em qualquer página que apresenta qualquer parte de uma resposta. 

- Apresenta publicidade nas páginas com respostas:
    - Do imagens do Bing, pesquisa de notícias, pesquisa de vídeos ou APIs de pesquisa Visual
    - Que são filtrados ou limitada principalmente (ou unicamente) para imagens, notícias e/ou os resultados da pesquisa de vídeo ou visual.

### <a name="notices-and-branding"></a>Avisos e de imagem corporativa 
Fazer:

- Destaque incluir um hiperlink funcional para o [declaração de privacidade do Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), junto a cada ponto na experiência do usuário (UX) que oferece a capacidade de inserir uma consulta de pesquisa de um utilizador. Etiquetar o hiperlink **declaração de privacidade do Microsoft**.

- Destaque apresentar Bing identidade visual, consistente com o [diretrizes de utilização de marcas registadas do Bing](https://go.microsoft.com/fwlink/?linkid=833278), junto a cada ponto na UX que oferece a capacidade de inserir uma consulta de pesquisa de um utilizador. Essa imagem corporativa tem claramente estado para o usuário que a Microsoft está incluindo a experiência de pesquisa da internet.

- Pode atributo cada resposta (ou parte de uma resposta) apresentados a partir de pesquisa Web Bing, pesquisa de imagens, pesquisa de notícias, pesquisa de vídeos e APIs de pesquisa Visual à Microsoft, a menos que a Microsoft especifique o contrário escrever para a sua utilização. Isso é descrito em [diretrizes de utilização de marcas registadas do Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Não:

- Respostas de atributo (ou partes de respostas) apresentados a partir do API de pesquisa personalizada do Bing para a Microsoft, a menos que a Microsoft especifique o contrário escrever para a sua utilização particular.

### <a name="transferring-responses"></a>Transferência de respostas

Se ativar um utilizador para transferir uma resposta de uma API de pesquisa para outro utilizador, tal como através de uma aplicação de mensagens ou lançamento de mídia social, situações seguintes: 

- Tem de respostas transferidas:
  - Consistem em conteúdo que é sem modificações do conteúdo das respostas apresentada ao utilizador a transferir. Alterações de formatação são permitidas.
  - Não inclua quaisquer dados em formato de metadados.
  - Para as respostas da Web do Bing, imagem, notícias, vídeo e Visual APIs, o idioma de apresentação que indica que a resposta foi obtido através de uma experiência de pesquisa da internet com tecnologia Bing. Por exemplo, pode exibir o idioma, como "Ligado ao Bing" ou "Saiba mais" mais informações sobre esta imagem no Bing, ou pode utilizar o logótipo do Bing.
  - Para as respostas da API de pesquisa personalizada do Bing, o idioma de apresentação que indica que a resposta foi obtido através de uma experiência de pesquisa da internet. Por exemplo, pode exibir o idioma, como "Saiba mais sobre este resultado de pesquisa."
  - Destaque-apresente a consulta completa utilizada para gerar a resposta.
  - Inclua um link proeminente ou atribuição semelhante para a origem subjacente da resposta, diretamente ou por meio do mecanismo de pesquisa (bing.com, m.bing.com ou seu serviço de pesquisa personalizada, conforme aplicável).
- Não, pode automatizar a transferência de respostas. Uma transferência tem de ser iniciada por uma ação do utilizador evidencing claramente uma intenção de transferência de uma resposta.
- Apenas pode ativar um utilizador para transferir as respostas que foram apresentadas em resposta à consulta de transferência do utilizador.

### <a name="continuity-of-service"></a>Continuidade do serviço 

Não copiar, armazenar ou colocar em cache todos os dados de respostas de API de pesquisa. No entanto, para ativar a continuidade de acesso de serviço e composição de dados, pode manter os resultados apenas nas seguintes condições:

#### <a name="device"></a>Dispositivo

Pode permitir que um utilizador manter os resultados num dispositivo para o menor de (i) 24 horas desde o momento da consulta, ou (ii) até que um usuário envia outra consulta para obter resultados atualizados, desde que manteve resultados podem ser utilizados apenas:

- Para permitir que o usuário acessar os resultados devolvidos anteriormente para esse utilizador nesse dispositivo (por exemplo, em caso de interrupção do serviço).
- Para armazenar os resultados devolvidos na sua consulta proativa personalizadas antes das necessidades do usuário, com base em sinais desse usuário (por exemplo, em caso de interrupção do serviço antecipado).

#### <a name="server"></a>Servidor

Pode manter os resultados específicos para um único utilizador em segurança num servidor que controla e exibir os resultados retidos apenas:

- Para permitir que o utilizador aceder a um relatório de histórico de resultados devolvido anteriormente ao que o utilizador na sua solução. Os resultados podem não ser (i) mantidos durante mais de 21 dias a partir da hora de consulta de inicial do usuário final e (ii) exibidos em resposta a consulta de um utilizador novo ou repetidas.
- Para armazenar os resultados devolvidos na sua consulta proativa personalizadas antes das necessidades do usuário, com base em sinais desse utilizador. Pode armazenar estes resultados para o menor de (i) 24 horas desde o momento da consulta, ou (ii) até que um usuário envia outra consulta para obter resultados atualizados.

Sempre que estiverem, resultados de um utilizador específico não podem ser commingled com resultados para outro utilizador. Ou seja, os resultados de cada utilizador devem ser retidos e entregue em separado.

### <a name="general"></a>Geral 

Para todos os apresentação de resultados retidos:

- Inclua um aviso claro e visível do tempo que a consulta foi enviada.
- Atualizar o presente, significa que o utilizador com um botão ou semelhante para efetuar uma nova consulta e obter resultados. 
- Manter o Bing a imagem corporativa na apresentação dos resultados.
- Eliminar (e atualizar com uma nova consulta, se necessário) os resultados armazenados dentro os períodos de tempo especificados.

### <a name="non-display-url-discovery"></a>Deteção de URL de apresentação não 

Só poderá utilizar as respostas da pesquisa numa experiência de pesquisa não internet para o único propósito de URLs de fontes de informações de capacidade de resposta a uma consulta do cliente ou utilizador a detetar. Pode copiar esses URLs num relatório ou resposta semelhante que é fornecer:

- Apenas para esse utilizador ou o cliente, em resposta a essa consulta.
- Apenas se ele inclui significativo valiosos conteúdos adicionais, relevantes para a consulta.

Utilizam as secções anteriores das APIs de pesquisa e requisitos de apresentação não são aplicáveis a esta utilização não apresentar, exceto para o seguinte: 

- Não colocar em cache, copiar ou armazenar quaisquer dados ou conteúdo a partir de, ou derivada, a resposta da pesquisa, que não seja a URL limitado cópia descrito anteriormente.
- Certifique-se de que a utilização de dados (incluindo os URLs) recebida das APIs de pesquisa não viole qualquer leis aplicáveis ou direitos de terceiros.
- Não utilize os dados (incluindo os URLs) recebidos das APIs de pesquisa como parte de qualquer aprendizado de máquina ou índice de pesquisa ou de uma atividade de algoritmo semelhante. Não utilize estes dados para criar a treinar, avaliar ou melhorar os serviços que ou de terceiros pode oferecer.

## <a name="gdpr-compliance"></a>Conformidade do GDPR  

Em relação a quaisquer dados pessoais sujeitos a União Europeia geral regulamento de proteção de dados (GDPR) e que é processado juntamente com chamadas a APIs de pesquisa, a API de verificação ortográfica do Bing ou a API de sugestão automática do Bing, compreende que a Microsoft e são controladores de dados independente no GDPR. É responsável independentemente de sua conformidade com o GDPR.  

