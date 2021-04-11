---
title: Modelo e fim de hora de vida da fala personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: O Custom Speech fornece modelos base para adaptação e permite criar modelos personalizados a partir dos seus dados. Este artigo descreve as cronologias dos modelos e dos pontos finais que utilizam estes modelos.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555494"
---
# <a name="model-and-endpoint-lifecycle"></a>Modelo e ciclo de vida Endpoint

O Custom Speech utiliza *modelos base* e *modelos personalizados.* Cada idioma tem um ou mais modelos base. Geralmente, quando um novo modelo de fala é lançado para o serviço regular de fala, também é importado para o serviço de Discurso Personalizado como um novo modelo base. São atualizados a cada 6 a 12 meses. Os modelos mais antigos costumam tornar-se menos úteis ao longo do tempo, porque o modelo mais recente geralmente tem maior precisão.

Em contraste, os modelos personalizados são criados adaptando um modelo base escolhido com dados do seu cenário de cliente particular. Pode continuar a utilizar um modelo personalizado específico durante muito tempo depois de ter um que satisfaça as suas necessidades. Mas recomendamos que atualize periodicamente o modelo base mais recente e o retreie ao longo do tempo com dados adicionais. 

Outros termos-chave relacionados com o ciclo de vida do modelo incluem:

* **Adaptação:** Levar um modelo base e personalizá-lo ao seu domínio/cenário utilizando dados de texto e/ou dados áudio.
* **Descodição**: Utilizando um modelo e realizando reconhecimento de voz (descodindo o áudio em texto).
* **Ponto final**: Uma implantação específica do utilizador de um modelo base ou de um modelo personalizado que só seja *acessível* a um determinado utilizador.

### <a name="expiration-timeline"></a>Prazo de expiração

À medida que novos modelos e novas funcionalidades se tornam disponíveis e os modelos mais antigos e menos precisos são retirados, consulte as seguintes linhas de tempo para a expiração do modelo e ponto final:

**Modelos base** 

* Adaptação: Disponível por um ano. Depois de o modelo ser importado, está disponível por um ano para criar modelos personalizados. Após um ano, novos modelos personalizados devem ser criados a partir de uma versão mais recente do modelo base.  
* Descodição: Disponível por dois anos após a importação. Assim, pode criar um ponto final e utilizar a transcrição do lote durante dois anos com este modelo. 
* Pontos finais: Disponível na mesma linha temporal que a descodagem.

**Modelos personalizados**

* Descoding: Disponível durante dois anos após a criação do modelo. Assim, pode utilizar o modelo personalizado durante dois anos (lote/em tempo real/teste) após a sua criação. Passados dois anos, *deves voltar a treinar o teu modelo,* porque o modelo base terá sido normalmente depreciado para adaptação.  
* Pontos finais: Disponível na mesma linha temporal que a descodagem.

Quando um modelo base ou um modelo personalizado expirarem, ele irá sempre voltar para a *versão mais recente* do modelo base . Portanto, a sua implementação nunca irá quebrar, mas pode tornar-se menos precisa para *os seus dados específicos* se os modelos personalizados atingirem a expiração. Pode ver a expiração de um modelo nos seguintes locais na área de Discurso Personalizado do Estúdio da Fala:

* Resumo da formação do modelo
* Detalhe de treinamento de modelo
* Resumo da implementação
* Detalhe de implantação

Aqui está um exemplo do resumo da formação do modelo:

![Resumo da formação de ](media/custom-speech/custom-speech-model-training-with-expiry.png) modelos E também da página de detalhes de treino do modelo:

![Detalhe de treinamento de modelo](media/custom-speech/custom-speech-model-details-with-expiry.png)

Também pode verificar as datas de validade através das [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) APIs de [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) fala personalizadas sob a `deprecationDates` propriedade na resposta JSON.

Aqui está um exemplo dos dados de expiração da chamada getModel API. Os "DEPRECATIONDATES" mostram: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Note que pode atualizar o modelo num ponto final de fala personalizado sem tempo de inatividade alterando o modelo utilizado pelo ponto final na secção de implementação do Estúdio de Discurso, ou através da API de discurso personalizado.

## <a name="next-steps"></a>Passos seguintes

* [Preparar e implementar um modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](./how-to-custom-speech-test-and-train.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)