---
title: Visão geral da fala personalizada - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Custom Speech é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a nossa precisão fala-texto para as suas aplicações, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de ficheiros de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658473"
---
# <a name="what-is-custom-speech"></a>O que é a Voz Personalizada?

[Custom Speech](https://aka.ms/customspeech) é um conjunto de ferramentas baseadas em UI que lhe permitem avaliar e melhorar a precisão fala-texto da Microsoft para as suas aplicações e produtos. Tudo o que é preciso para começar são um punhado de ficheiros de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.

## <a name="whats-in-custom-speech"></a>O que há no Discurso Personalizado?

Antes de poder fazer qualquer coisa com o Discurso Personalizado, vai precisar de uma conta Azure e de uma subscrição de serviço de discurso. Assim que tiver uma conta, pode preparar os seus dados, treinar e testar os seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, em última análise, implementar e utilizar o modelo personalizado de voz a texto.

Este diagrama destaca as peças que compõem o [portal Discurso Personalizado.](https://aka.ms/customspeech) Use os links abaixo para saber mais sobre cada passo.

![Destaca os diferentes componentes que compõem o portal Discurso Personalizado.](./media/custom-speech/custom-speech-overview.png)

1. [Inscreva-se e crie um projeto](#set-up-your-azure-account) - Crie uma conta Azure e subscreva o serviço Discurso. Esta subscrição unificada dá-lhe acesso a discurso-a-texto, texto-a-discurso, tradução de fala e portal de [discurso personalizado](https://speech.microsoft.com/customspeech). Em seguida, utilizando a subscrição do serviço Speech, crie o seu primeiro projeto de Discurso Personalizado.

1. [Upload datas de teste](how-to-custom-speech-test-data.md) - Carreie os dados do teste (ficheiros áudio) para avaliar a oferta de voz para texto da Microsoft para as suas aplicações, ferramentas e produtos.

1. [Inspecione a qualidade](how-to-custom-speech-inspect-data.md) do reconhecimento - Utilize o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade de reconhecimento da fala dos seus dados de teste. Para medições quantitativas, consulte [os dados do Inspecione](how-to-custom-speech-inspect-data.md).

1. [Avaliar e melhorar a precisão](how-to-custom-speech-evaluate-data.md) - Avaliar e melhorar a precisão do modelo discurso-texto. O [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech) fornecerá uma Taxa de Erro de *Palavras,* que pode ser usada para determinar se é necessária formação adicional. Se estiver satisfeito com a precisão, pode utilizar diretamente os APIs do serviço de fala. Se quiser melhorar a precisão numa média relativa de 5% - 20%, use o **separador Training** no portal para carregar dados de formação adicionais, tais como transcrições com rótulo humano e texto relacionado.

1. [Treine e implemente um modelo](how-to-custom-speech-train-model.md) - Melhore a precisão do seu modelo de discurso-texto fornecendo transcrições escritas (10-1.000 horas) e texto relacionado (<200 MB) juntamente com os seus dados de teste áudio. Estes dados ajudam a formar o modelo de discurso-texto. Depois do treino, reteste e se estiver satisfeito com o resultado, pode colocar o seu modelo num ponto final personalizado.

## <a name="set-up-your-azure-account"></a>Crie a sua conta Azure

Uma subscrição de conta Azure e serviço de fala são necessárias antes de poder utilizar o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Por favor, certifique-se de criar subscrições padrão (S0), subscrições gratuitas (F0) não são suportadas.

Depois de criar uma conta Azure e uma subscrição de serviço de Discurso, terá de iniciar seduca no [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) e ligar a sua subscrição.

1. Iniciar s-in no [portal de discurso personalizado](https://aka.ms/custom-speech).
1. Selecione a subscrição em que precisa para trabalhar e criar um projeto de fala.
1. Se quiser modificar a sua subscrição, utilize o ícone **de engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no [portal Discurso Personalizado.](https://speech.microsoft.com/customspeech) Cada projeto é específico de um domínio e país/língua. Por exemplo, pode criar um projeto para call centers que usam inglês nos Estados Unidos.

Para criar o seu primeiro projeto, selecione o **discurso Discurso-a-Texto/Personalizado,** em seguida, clique em **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, deverá ver quatro separadores: **Dados,** **Testes,** **Formação** e **Implantação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal De Discurso Personalizado](https://aka.ms/custom-speech) foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, precisa de criar um novo projeto no novo portal para ligar a estas entidades antigas.

## <a name="model-lifecycle"></a>Ciclo de vida modelo

A fala personalizada utiliza **modelos base** e **modelos personalizados.** Cada idioma tem um ou mais **modelos base.** Geralmente, quando um novo modelo de fala é lançado para o serviço regular de fala, também é importado para o serviço de Discurso Personalizado como um novo **modelo base**. São normalmente atualizados a cada 3-6 meses, e os modelos mais antigos tornam-se menos úteis ao longo do tempo, uma vez que o modelo mais recente costuma ter maior precisão.

Em contraste, **os modelos personalizados** são criados adaptando um modelo base escolhido para um cenário de cliente particular. Pode continuar a utilizar um determinado modelo personalizado por um longo período de tempo depois de ter chegado a um que satisfaça as suas necessidades, mas recomendamos que atualize periodicamente o modelo base mais recente e retreine com dados adicionais.

Outros termos-chave relacionados com o ciclo de vida do modelo incluem:

* **Adaptação:** pegar num modelo base e personalizá-lo ao seu domínio/cenário utilizando dados de texto e/ou dados áudio
* **Descodição:** utilizando um modelo e realizando reconhecimento de voz (descodindo o áudio em texto)
* **Ponto final:** uma colocação específica do utilizador de um modelo base ou de um modelo personalizado que *só* esteja acessível por um determinado utilizador.

### <a name="expiration-timeline"></a>Prazo de expiração

À medida que novos modelos e novas funcionalidades se tornam disponíveis e os modelos mais antigos e menos precisos são retirados, consulte as seguintes linhas de tempo para a expiração do modelo e ponto final:

**Modelos base** 

* Adaptação: disponível para 1 ano. Uma vez importado o modelo, está disponível por 1 ano para criar modelos personalizados. Após 1 ano, novos modelos personalizados devem ser criados a partir de uma versão mais recente do modelo base.  
* Descodição: disponível por 2 anos após a importação. Isto significa que pode criar um ponto final e utilizar a transcrição do lote durante 2 anos com este modelo. 
* Pontos finais: disponível na mesma linha temporal que a descodagem

**Modelos personalizados**

* Descoding: disponível durante 2 anos após a criação do modelo. Isto significa que pode utilizar o modelo personalizado durante 2 anos (lote/em tempo real/teste) após a sua criação. Passados 2 anos, **deves voltar a treinar o teu modelo,** porque na maioria das vezes o modelo base terá sido depreciado para adaptação.  
* Pontos finais: disponível na mesma linha temporal que a descodagem

Quando um modelo base ou um modelo personalizado expirarem, ele irá sempre voltar para a **versão mais recente** do modelo base . Assim, a sua implementação nunca irá quebrar, mas pode tornar-se menos precisa para *os seus dados específicos* se os modelos personalizados atingirem a expiração. Pode ver a expiração de um modelo nos seguintes lugares no portal Discurso Personalizado:

* Resumo da formação do modelo
* Detalhe de treinamento de modelo
* Resumo da implementação
* Detalhe de implantação

Também pode verificar as datas de validade através das [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) APIs de [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) fala personalizadas sob a `deprecationDates` propriedade na resposta JSON.

Note que pode atualizar o modelo num ponto final de fala personalizado sem tempo de inatividade alterando o modelo utilizado pelo ponto final na secção de implementação do portal de fala personalizado, ou através da API de discurso personalizado.

## <a name="next-steps"></a>Próximos passos

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar e melhorar a precisão do modelo](how-to-custom-speech-evaluate-data.md)
* [Preparar e implementar um modelo](how-to-custom-speech-train-model.md)
