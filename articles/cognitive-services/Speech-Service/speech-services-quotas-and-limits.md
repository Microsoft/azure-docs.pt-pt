---
title: Limites e Quotas dos Serviços de Voz
titleSuffix: Azure Cognitive Services
description: Referência rápida, descrição detalhada e boas práticas sobre quotas e limites de serviços de fala cognitiva Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: alexeyo
ms.openlocfilehash: 78e40250710c133cbed53c05137971247663b0df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564427"
---
# <a name="speech-services-quotas-and-limits"></a>Limites e Quotas dos Serviços de Voz

Este artigo contém uma referência rápida e a **descrição detalhada** das quotas e limites dos serviços de fala cognitiva Azure para todos os [níveis de preços.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) Contém também algumas boas práticas para evitar o estrangulamento do pedido. 

## <a name="quotas-and-limits-quick-reference"></a>Quotas e Limites referência rápida
Saltar para [quotas e limites de texto para discurso](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Limites e Quotas da Conversão de Voz em Texto por recurso de Voz
Nas tabelas abaixo Os parâmetros sem linha "ajustável" **não** são ajustáveis para todos os níveis de preço.

#### <a name="online-transcription"></a>Transcrição online
Para a utilização com API REST [de Speech SDK](speech-sdk.md) e/ou [Discurso-a-texto PARA áudio curto](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Quota | Grátis (F0)<sup>1</sup> | Padrão (S0) |
|--|--|--|
| **Limite de pedido simultâneo - Modelo base** | 1 | 100 (valor predefinido) |
| Ajustável | Nº<sup>2</sup> | Sim<sup>2</sup> |
| **Limite de pedido simultâneo - modelo personalizado** | 1 | 20 (valor predefinido) |
| Ajustável | Nº<sup>2</sup> | Sim<sup>2</sup> |

#### <a name="batch-transcription"></a>Transcrição do lote
| Quota | Grátis (F0)<sup>1</sup> | Padrão (S0) |
|--|--|--|
| Limite de API de repouso | Transcrição do lote não está disponível para F0 | 300 pedidos por minuto |
| Tamanho do ficheiro de entrada de áudio max | N/D | 1 GB |
| Tamanho máximo da bolha de entrada (pode conter mais de um ficheiro, por exemplo, num arquivo zip; certifique-se de que o limite de tamanho do ficheiro acima) | N/D | 2,5 GB |
| Tamanho do recipiente de bolhas max | N/D | 5 GB |
| Número máximo de bolhas por recipiente | N/D | 10000 |
| Número máximo de ficheiros por pedido de transcrição (quando utilizar urls de conteúdo múltiplo como entrada) | N/D | 1000  |
| Número máximo de empregos simultaneamente em execução | N/D | 2000  |

#### <a name="model-customization"></a>Personalização do modelo
| Quota | Grátis (F0)<sup>1</sup> | Padrão (S0) |
|--|--|--|
| Limite de API de repouso | 300 pedidos por minuto | 300 pedidos por minuto |
| Número máximo de conjuntos de dados de fala | 2 | 500 |
| Tamanho máximo do ficheiro do conjunto de dados acústico para importação de dados | 2 GB | 2 GB |
| Tamanho máximo do ficheiro do conjunto de dados linguísticos para importação de dados | 200 MB | 1,5 GB |
| Tamanho máximo do ficheiro de conjunto de dados de pronúncia para importação de dados | 1 KB | 1 MB |
| Tamanho do texto máximo ao usar `text` o parâmetro no pedido de [API do modelo Criar](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) | 200 KB | 500 KB |

<sup>1</sup> O nível de preços **gratuito (F0)** veja também as licenças mensais na [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> Consulte [explicações adicionais,](#detailed-description-quota-adjustment-and-best-practices) [boas práticas](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [instruções de ajuste](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Quotas de texto para discurso e limites por recurso de fala
Na tabela abaixo os parâmetros sem linha "ajustável" **não** são ajustáveis para todos os níveis de preço.

| Quota | Grátis (F0)<sup>3</sup> | Padrão (S0) |
|--|--|--|
| **Número máximo de transações por segundo (TPS) para vozes standard e neurais** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limite de pedido simultâneo para voz personalizada** |  |  |
| Valor predefinido | 10 | 10 |
| Ajustável | Nº<sup>5</sup> | Sim<sup>5</sup> |
| **Quotas específicas em HTTP** |  |
| Comprimento áudio max produzido por pedido | 10 min | 10 min |
| Número máximo de `<voice>` etiquetas distintas em SSML | 50 | 50 |
| **Quotas específicas websocket** |  |  |
|Comprimento de áudio max produzido por turno | 10 min | 10 min |
|Tamanho da mensagem Max SSML por turno |64 KB |64 KB |
| **Limite de API de repouso** | 20 pedidos por minuto | 300 pedidos por minuto |


<sup>3</sup> Para o nível de preços **gratuito (F0)** ver também subsídios mensais na [página de preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> Consulte [explicações adicionais](#detailed-description-quota-adjustment-and-best-practices) e [boas práticas.](#general-best-practices-to-mitigate-throttling-during-autoscaling)<br/>
<sup>5</sup> Consulte [explicações adicionais,](#detailed-description-quota-adjustment-and-best-practices) [boas práticas](#general-best-practices-to-mitigate-throttling-during-autoscaling)e [instruções de ajuste](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Descrição detalhada, ajustamento das quotas e boas práticas
Antes de solicitar um aumento de quota (se aplicável) certifique-se de que é necessário. O serviço de fala está a utilizar tecnologias de autoscalização para trazer os recursos computacionais necessários no modo "on-demand" e, ao mesmo tempo, para manter os custos do cliente baixos, não mantendo uma quantidade excessiva de capacidade de hardware. Sempre que a sua aplicação recebe um Código de Resposta 429 ("Demasiados pedidos") enquanto a sua carga de trabalho está dentro dos limites definidos (ver [Quotas e Limites referência rápida),](#quotas-and-limits-quick-reference)a explicação mais provável é que o Serviço esteja a aumentar a sua procura e ainda não tenha atingido a escala exigida, pelo que não dispõe imediatamente de recursos suficientes para servir o pedido. Este estado é geralmente transitório e não deve durar muito.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Melhores práticas gerais para mitigar o estrangulamento durante o autoscaling
Para minimizar as questões relacionadas com o estrangulamento (Código de Resposta 429), recomendamos a utilização das seguintes técnicas:
- Implemente a lógica de relemgar na sua aplicação
- Evite alterações acentuadas na carga de trabalho. Aumentar a carga de trabalho gradualmente <br/>
*Exemplo.* A sua aplicação está a usar Texto-a-Fala e a sua carga de trabalho atual é de 5 TPS (transações por segundo). No segundo seguinte, aumenta-se a carga para 20 TPS (ou seja, quatro vezes mais). O Serviço começa imediatamente a aumentar para cumprir a nova carga, mas provavelmente não será capaz de fazê-lo dentro de um segundo, pelo que alguns dos pedidos receberão o Código de Resposta 429.   
- Testar diferentes padrões de aumento de carga
  - Ver [exemplo de discurso a texto](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Crie recursos de fala adicionais nas mesmas regiões ou diferentes regiões e distribua a carga de trabalho entre elas utilizando a técnica "Round Robin". Isto é especialmente importante para o parâmetro **TPS de texto-a-voz (transações por segundo),** que é definido como 200 por recurso de fala e não pode ser ajustado  

As secções seguintes descrevem casos específicos de ajustamento das quotas.<br/>
Salta [para o Texto-a-Discurso. Aumento do limite](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice) de pedido simultâneo de transcrição para voz personalizada

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Discurso-a-texto: aumento do limite de pedido simultâneo de transcrição online
Por predefinição, o número de pedidos simultâneos é limitado a 20 por recurso de fala (modelo base) ou por ponto final personalizado (modelo personalizado). Para o nível de preços standard este montante pode ser aumentado. Antes de submeter o pedido, certifique-se de que conhece o material [desta secção](#detailed-description-quota-adjustment-and-best-practices) e está ciente [destas boas práticas.](#general-best-practices-to-mitigate-throttling-during-autoscaling)

Aumentar o limite de Pedido Simultâneo **não** afeta diretamente os seus custos. Os Serviços de Fala utilizam o modelo "Pague apenas pelo que usa". O limite define a altura da escala do Serviço antes de iniciar o aceleração dos seus pedidos.

Os limites de pedido simultâneos para modelos **base** e **personalizados** devem ser ajustados **separadamente**.

O valor existente do parâmetro limite de pedido simultâneo **não** é visível através do portal Azure, Command-Line ferramentas ou pedidos de API. Para verificar o valor existente, crie um Pedido de Suporte Azure.

>[!NOTE]
>[Os recipientes](speech-container-howto.md) de fala não requerem aumentos do limite de pedido simultâneo, uma vez que os contentores são limitados apenas pelas CPUs do hardware em que estão hospedados. No entanto, os contentores da fala têm as suas próprias limitações de capacidade que devem ser tidas em conta. Veja a pergunta *"Poderia ajudar no planeamento de capacidades e na estimativa de custos dos recipientes de fala-a-texto pré-pré-texto?"* a partir dos recipientes de [fala FAQ](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>Tenha as informações necessárias prontas:
- Para **o modelo Base:**
  - ID de recursos de fala
  - Region
- Para **modelo personalizado:** 
  - Region
  - ID de ponto final personalizado

- **Como obter informações (modelo base)**:  
  - Ir para o [portal Azure](https://portal.azure.com/)
  - Selecione o Recurso de Voz para o qual gostaria de aumentar o limite de pedido de concurrency
  - Selecione *Propriedades* (Grupo *de Gestão de Recursos)* 
  - Copiar e guardar os valores dos seguintes campos:
    - **ID de recursos**
    - **Localização** (região do seu ponto final)

- **Como obter informações (Modelo Personalizado)**:
  - Ir ao portal [do Estúdio da Fala](https://speech.microsoft.com/)
  - Inscreva-se se necessário
  - Ir para o discurso personalizado
  - Selecione o seu projeto
  - Ir para a *Implantação*
  - Selecione o ponto final necessário
  - Copiar e guardar os valores dos seguintes campos:
    - **Região de Serviços** (região do seu ponto final)
    - **ID de ponto final**
  
#### <a name="create-and-submit-support-request"></a>Criar e submeter pedido de apoio
Inicie o aumento do limite de Pedido Simultâneo para o seu recurso ou, se necessário, verifique o limite de hoje, apresentando o Pedido de Apoio:

- Certifique-se de que tem as [informações necessárias](#have-the-required-information-ready)
- Ir para o [portal Azure](https://portal.azure.com/)
- Selecione o Recurso de Voz para o qual gostaria de aumentar (ou verificar) o limite de Pedido de Concurrency
- Selecione *novo pedido de suporte* *(Grupo de suporte + resolução de problemas)* 
- Uma nova janela aparecerá com informações auto-povoadas sobre a sua Subscrição Azure e O Recurso Azure
- Insira *o Resumo* (como "Aumentar O Limite de Pedido de Concurrency STT")
- No *tipo de problema* selecione "Questões de quota ou subscrição"
- No *subtipo de problemas* apareceu selecione:
  - "Aumento de quota ou pedidos simultâneos" - para um pedido de aumento
  - "Validação de quota ou utilização" para verificar o limite existente
- Clique *em Seguida: Soluções*
- Avance ainda com a criação do pedido
- Quando em *Detalhes* o separador entrar no campo *Descrição:*
  - uma nota, que o pedido é sobre **quota de discurso a texto**
  - **Modelo base** ou **personalizado**
  - Informação sobre recursos Azure que [recolheu antes](#have-the-required-information-ready) 
  - Concluir a introdução das informações necessárias e clicar em *Criar* botão no *separador 'Revisão+'*
  - Note o número de pedido de suporte nas notificações do portal Azure. Em breve será contactado para posterior processamento

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Discurso-a-texto: exemplo de um padrão de carga de trabalho de melhor prática
Este exemplo apresenta a abordagem que recomendamos seguir para atenuar possíveis pedidos de estrangulamento devido à [autoscalagem em curso](#detailed-description-quota-adjustment-and-best-practices). Não é uma "receita exata", mas apenas um modelo que convidamos a seguir e ajustar conforme necessário.

Suponhamos que um recurso de Discurso tem o limite de Pedido Simultâneo definido para 300. Inicie a carga de trabalho a partir de 20 ligações simultâneas e aumente a carga em 20 ligações simultâneas a cada 1,5-2 minutos. Controlar as respostas do Serviço e implementar a lógica que recua (reduz a carga) se receber demasiados Códigos de Resposta 429. Em seguida, reda o padrão de 1-2-4-4 minutos. (Isto é, tentar o aumento da carga em 1 min, se ainda não funcionar, em seguida, em 2 min, e assim por diante)

Geralmente, é altamente recomendado testar a carga de trabalho e os padrões de carga de trabalho antes de ir para a produção.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Texto-a-discurso: aumento do limite de pedido simultâneo de transcrição para Voz Personalizada
Por predefinição, o número de pedidos simultâneos para um ponto final de Voz Personalizado é limitado a 10. Para o nível de preços standard este montante pode ser aumentado. Antes de submeter o pedido, certifique-se de que conhece o material [desta secção](#detailed-description-quota-adjustment-and-best-practices) e está ciente [destas boas práticas.](#general-best-practices-to-mitigate-throttling-during-autoscaling)

Aumentar o limite de Pedido Simultâneo **não** afeta diretamente os seus custos. Os Serviços de Fala utilizam o modelo "Pague apenas pelo que usa". O limite define a altura da escala do Serviço antes de iniciar o aceleração dos seus pedidos.

O valor existente do parâmetro limite de pedido simultâneo **não** é visível através do portal Azure, Command-Line ferramentas ou pedidos de API. Para verificar o valor existente, crie um Pedido de Suporte Azure.

>[!NOTE]
>[Os recipientes](speech-container-howto.md) de fala não requerem aumentos do limite de pedido simultâneo, uma vez que os contentores são limitados apenas pelas CPUs do hardware em que estão hospedados.

#### <a name="prepare-the-required-information"></a>Preparar as informações necessárias:
Para criar um pedido de aumento, terá de fornecer a sua Região de Implantação e o ID de ponto final personalizado. Para obtê-lo, execute as seguintes ações: 

- Ir ao portal [do Estúdio da Fala](https://speech.microsoft.com/)
- Inscreva-se se necessário
- Ir para voz *personalizada*
- Selecione o seu projeto
- Ir para a *Implantação*
- Selecione o ponto final necessário
- Copiar e guardar os valores dos seguintes campos:
    - **Região de Serviços** (região do seu ponto final)
    - **ID de ponto final**
  
#### <a name="create-and-submit-support-request"></a>Criar e submeter pedido de apoio
Inicie o aumento do limite de Pedido Simultâneo para o seu recurso ou, se necessário, verifique o limite de hoje, apresentando o Pedido de Apoio:

- Certifique-se de que tem as [informações necessárias](#prepare-the-required-information)
- Ir para o [portal Azure](https://portal.azure.com/)
- Selecione o Recurso de Voz para o qual gostaria de aumentar (ou verificar) o limite de Pedido de Concurrency
- Selecione *novo pedido de suporte* *(Grupo de suporte + resolução de problemas)* 
- Uma nova janela aparecerá com informações auto-povoadas sobre a sua Subscrição Azure e O Recurso Azure
- Insira *o resumo* (como "Aumentar o limite de pedido de concurrency de ponto final personalizado TTS")
- No *tipo de problema* selecione "Questões de quota ou subscrição"
- No *subtipo de problemas* apareceu selecione:
  - "Aumento de quota ou pedidos simultâneos" - para um pedido de aumento
  - "Validação de quota ou utilização" para verificar o limite existente
- Clique *em Seguida: Soluções*
- Avance ainda com a criação do pedido
- Quando em *Detalhes* o separador entrar no campo *Descrição:*
  - uma nota, que o pedido é sobre **quota texto-a-discurso**
  - Informação sobre recursos Azure que [recolheu antes](#prepare-the-required-information) 
  - Concluir a introdução das informações necessárias e clicar em *Criar* botão no *separador 'Revisão+'*
  - Note o número de pedido de suporte nas notificações do portal Azure. Em breve será contactado para posterior processamento