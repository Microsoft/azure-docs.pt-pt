---
title: Criação de Conteúdo Sonoro - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Audio Content Creation é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7e43c472c939049e5dfd4ec4df909a3178ef8e2d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553265"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Melhorar a síntese com a ferramenta de Criação de Conteúdos Áudio

[Audio Content Creation](https://aka.ms/audiocontentcreation) é uma ferramenta fácil de usar e poderosa que permite construir conteúdo sonoro altamente natural para uma variedade de cenários, como audiolivros, transmissões de notícias, narrações de vídeo e chat bots. Com a Audio Content Creation, pode afinar vozes de texto para discurso e desenhar experiências de áudio personalizadas de forma eficiente e de baixo custo.

A ferramenta baseia-se na [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Permite-lhe ajustar atributos de saída de texto para voz em tempo real ou síntese de lote, tais como caracteres de voz, estilos de voz, velocidade de fala, pronúncia e prosódica.

Você pode ter fácil acesso a mais de 150 vozes pré-construídas em cerca de 50 línguas diferentes, incluindo as vozes de TTS neurais de última geração, e a sua voz personalizada se você construiu uma. 

Veja o [tutorial de vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para Audio Content Creation.

## <a name="how-to-get-started"></a>Como começar?

A Criação de Conteúdos Áudio é uma ferramenta gratuita, mas pagará pelo serviço Azure Speech que consome. Para trabalhar com a ferramenta, precisa fazer login com uma conta Azure e criar um recurso de fala. Para cada conta Azure, tem quotas de liberdade de expressão mensais que incluem 500.000 caracteres para vozes Neural TTS (por mês), 5 milhões de caracteres para vozes padrão e personalizadas (por mês) e 1 serviço de hospedagem de ponta de voz personalizado (por mês). O valor mensal atribuído é geralmente suficiente para uma pequena equipa de conteúdo de cerca de 3-5 pessoas. Aqui estão os passos para como criar uma conta Azure e obter um recurso de fala. 

### <a name="step-1---create-an-azure-account"></a>Passo 1 - Criar uma conta Azure

Para trabalhar com a Audio Content Creation, precisa de ter uma [conta Microsoft](https://account.microsoft.com/account) e uma [conta Azure.](https://azure.microsoft.com/free/ai/) Siga estas instruções para [configurar a conta](./overview.md#try-the-speech-service-for-free). 

[O portal Azure](https://portal.azure.com/) é o local centralizado para gerir a sua conta Azure. Pode criar o recurso de fala, gerir o acesso ao produto e monitorizar tudo, desde aplicações web simples até implementações complexas na nuvem. 

### <a name="step-2---create-a-speech-resource"></a>Passo 2 - Criar um recurso de fala

Depois de se inscrever na conta Azure, precisa de criar um recurso De discurso na sua conta Azure para aceder aos serviços de Discurso. Consulte as instruções de [como criar um recurso de voz](./overview.md#create-the-azure-resource). 

Leva alguns momentos para implementar o seu novo recurso speech. Uma vez concluída a implementação, pode iniciar a jornada de Criação de Conteúdos Áudio. 

 >[!NOTE]
   > Se pretender utilizar vozes neurais, certifique-se de que cria o seu recurso [numa região que suporta vozes neurais](regions.md#neural-and-standard-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Passo 3 - Inicie sessão na Criação de Conteúdos Áudio com a sua conta Azure e recurso de fala

1. Depois de obter a conta Azure e o recurso Speech, pode iniciar sessão na [Criação](https://aka.ms/audiocontentcreation) de Conteúdos áudio clicando **em Iniciar**.
2. A página **de recursos da Fala** será mostrada a si. Selecione o recurso Speech em que pretende trabalhar. Clique **em Ir ao Estúdio** para iniciar a sua criação de áudio. Também pode criar um novo recurso de Discurso aqui clicando em **Criar novo**. Quando iniciar sessão na ferramenta De criação de conteúdos áudio para a próxima vez, ligaremos-no diretamente aos ficheiros de trabalho áudio sob o recurso de fala atual. 
3. Pode modificar o seu recurso Speech a qualquer momento com a opção **Definições,** localizada na navegação superior.

## <a name="how-to-use-the-tool"></a>Como usar a ferramenta?

Este diagrama mostra os passos que é preciso para afinar as saídas de texto para discurso. Use os links abaixo para saber mais sobre cada passo.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Um diagrama dos passos que é preciso para afinar as saídas de texto para discurso":::


1. Escolha o recurso de fala em que pretende trabalhar.
2. [Crie um ficheiro de afinação de áudio](#create-an-audio-tuning-file) utilizando scripts simples ou SSML. Digite ou faça o upload do seu conteúdo para a Criação de Conteúdos Áudio.
3. Escolha a voz e o idioma para o seu conteúdo de script. A Criação de Conteúdo sonoro inclui todas as [vozes texto-a-voz](language-support.md#text-to-speech)da Microsoft. Pode usar a voz normal, neural ou personalizada.
   >[!NOTE]
   > O acesso fechado está disponível para Vozes Neurais Personalizadas, que permitem criar vozes de alta definição semelhantes à fala de som natural. Para mais detalhes, consulte [o processo De Gating.](./text-to-speech.md)

4. Clique no ícone **de reprodução** (um triângulo) para visualizar a saída de síntese padrão. Em seguida, melhore a saída ajustando a pronúncia, quebra, tom, taxa, entoação, estilo de voz, e muito mais. Para obter uma lista completa de opções, consulte [a linguagem de marcação de síntese de discursos](speech-synthesis-markup.md). Aqui está um [vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para mostrar como afinar a saída da fala com a Audio Content Creation. 
5. Guarde e [exporte o seu áudio afinado.](#export-tuned-audio) Quando guardar a faixa de afinação no sistema, pode continuar a trabalhar e iterar na saída. Quando estiver satisfeito com a saída, pode criar uma tarefa de criação de áudio com a funcionalidade de exportação. Pode observar o estado da tarefa de exportação e descarregar a saída para uso com as suas apps e produtos.

## <a name="create-an-audio-tuning-file"></a>Criar um ficheiro de afinação de áudio

Existem duas formas de colocar o seu conteúdo na ferramenta de Criação de Conteúdos Áudio.

**Opção 1:**

1. Clique no ícone **de novo ficheiro** no canto superior direito para criar um novo ficheiro de afinação de áudio.
2. Digite ou cole o seu conteúdo na janela de edição. Os caracteres de cada ficheiro são até 20.000. Se o seu script for superior a 20.000 caracteres, pode utilizar a Opção 2 para dividir automaticamente o seu conteúdo em vários ficheiros. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique **em Upload** para importar um ou mais ficheiros de texto. Tanto o texto simples como o SSML são suportados. Se o seu ficheiro de script for superior a 20.000 caracteres, por favor divida o ficheiro por parágrafos, por caráter ou por expressões regulares. 
3. Ao fazer o upload dos seus ficheiros de texto, certifique-se de que o ficheiro satisfaz estes requisitos.

   | Propriedade | Valor / Notas |
   |----------|---------------|
   | Formato do ficheiro | Texto simples (.txt)<br/> Texto SSML (.txt)<br/> Os ficheiros zip não são suportados |
   | Formato codificante | UTF-8 |
   | Nome de ficheiro | Cada ficheiro deve ter um nome único. Duplicados não são apoiados. |
   | Comprimento do texto | A limitação do carácter do ficheiro de texto é de 20.000. Se os seus ficheiros excederem a limitação, divida os ficheiros com as instruções na ferramenta. |
   | Restrições SSML | Cada ficheiro SSML só pode conter uma única peça de SSML. |

**Exemplo de texto simples**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Exemplo de texto SSML**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportação de áudio afinado

Depois de ter revisto a sua saída de áudio e de estar satisfeito com a sua afinação e ajuste, pode exportar o áudio.

1. Clique **em Exportação** para criar uma tarefa de criação de áudio. **Recomenda-se a exportação para a Audio Library,** uma vez que suporta a longa saída de áudio e a experiência de saída áudio completa. Também pode baixar o áudio para o seu disco local diretamente, mas apenas os primeiros 10 minutos estão disponíveis.
2. Escolha o formato de saída para o seu áudio afinado. Uma lista de formatos suportados e taxas de amostra está disponível abaixo.
3. Pode ver o estado da tarefa no separador **tarefa Exportação.** Se a tarefa falhar, consulte a página de informação detalhada para um relatório completo.
4. Quando a tarefa estiver concluída, o seu áudio está disponível para download no separador **Audio Library.**
5. Clique em **Transferir**. Agora está pronto para usar o seu áudio afinado personalizado nas suas apps ou produtos.

**Formatos áudio suportados**

| Formato | Taxa de amostra de 16 kHz | Taxa de amostra de 24 kHz |
|--------|--------------------|--------------------|
| ondulação | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | áudio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Como adicionar/remover utilizadores da Criação de Conteúdos áudio?

Se mais de um utilizador quiser utilizar a Criação de Conteúdos Áudio, pode conceder ao utilizador acesso à subscrição do Azure e ao recurso de fala. Se adicionar um utilizador a uma subscrição do Azure, o utilizador pode aceder a todos os recursos sob a subscrição do Azure. Mas se apenas adicionar um utilizador a um recurso de fala, o utilizador apenas terá acesso ao recurso de fala, e não poderá aceder a outros recursos ao abrigo desta subscrição do Azure. Um utilizador com acesso ao recurso de fala pode utilizar a Criação de Conteúdos Áudio.

### <a name="add-users-to-a-speech-resource"></a>Adicione os utilizadores a um recurso de fala

Siga estes passos para adicionar um utilizador a um recurso de fala para que possa utilizar a Criação de Conteúdo Sonoro.

1. Procure **serviços cognitivos** no [portal Azure,](https://portal.azure.com/)selecione o recurso de voz a que pretende adicionar os utilizadores.
2. Clique em **Controlo de acesso (IAM)** . Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Separador de atribuição de funções":::
1. Clique em **Adicionar** > **Adicionar atribuição de funções** para abrir o painel Adicionar atribuição de funções. Na lista de drop-down role, selecione a função **utilizador do serviço cognitivo.** Se quiser dar ao utilizador a propriedade deste recurso de fala, pode selecionar a função **Proprietário.**
1. Na lista, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa Select para pesquisar nomes de exibição e endereços de e-mail. Se o utilizador não estiver neste diretório, pode inserir a [conta Microsoft](https://account.microsoft.com/account) do utilizador (que é fidedigna pelo diretório ativo da Azure).
1. Clique em **Guardar** para atribuir a função. Após alguns momentos, o utilizador é atribuído ao utilizador do Serviço Cognitivo no âmbito do recurso de voz.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Adicionar diálogo de função":::

1. Os utilizadores que adicionar receberão um e-mail de convite. Depois de clicarem **em Aceitar convite** Aceite para se juntar ao  >  **Azure,** então eles podem usar a [Criação de Conteúdo Sonoro](https://aka.ms/audiocontentcreation).

Os utilizadores que estejam no mesmo recurso de fala verão o trabalho uns dos outros no estúdio audio content Creation. Se quiser que cada utilizador tenha um local de trabalho único e privado na Criação de Conteúdos Áudio, por favor [crie um novo recurso](#step-2---create-a-speech-resource) de fala para cada utilizador e dê a cada utilizador o acesso único ao recurso de voz. 

### <a name="remove-users-from-a-speech-resource"></a>Remover os utilizadores de um recurso de fala
1. Procure **serviços cognitivos** no portal Azure, selecione o recurso de voz de onde pretende remover os utilizadores.
2. Clique em **Controlo de acesso (IAM)**. Clique no **separador atribuições de funções** para ver todas as atribuições de funções para este recurso de fala.
3. Selecione os utilizadores que pretende remover, clique em **Remover**  >  **Ok**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Remover botão":::

### <a name="enable-users-to-grant-access"></a>Permitir que os utilizadores concedam acesso
Se pretender que um dos utilizadores dê acesso a outros utilizadores, tem de dar ao utilizador o papel de proprietário para o recurso de fala e definir o utilizador como leitor de diretórios Azure. 
1. Adicione o utilizador como proprietário do recurso de fala. Veja [como adicionar os utilizadores a um recurso de fala](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Fun Owner field":::
1. Selecione o menu colapsado na parte superior esquerda. Clique **em Azure Ative Directory** e, em seguida, clique nos **Utilizadores**.
1. Pesse na conta microsoft do utilizador e aceda à página de detalhes do utilizador. Clique **em funções atribuídas**.
1. Clique **em Adicionar atribuições**  ->  **Leitores de Diretórios**.

## <a name="see-also"></a>Ver também

* [API de áudio longo](./long-audio-api.md)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
