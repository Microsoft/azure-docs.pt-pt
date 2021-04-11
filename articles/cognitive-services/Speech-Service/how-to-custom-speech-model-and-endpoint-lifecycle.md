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
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385030"
---
# <a name="model-and-endpoint-lifecycle"></a>Modelo e ciclo de vida de ponto final

O nosso discurso padrão (não personalizado) baseia-se em modelos de IA a que chamamos modelos base. Na maioria dos casos, treinamos um modelo base diferente para cada língua falada que apoiamos.  Atualizamos o serviço de fala com novos modelos base a cada poucos meses para melhorar a precisão e a qualidade.  
Com o Custom Speech, os modelos personalizados são criados adaptando um modelo base escolhido com dados do seu cenário de cliente particular. Uma vez criado um modelo personalizado, este modelo não será atualizado ou alterado, mesmo que o modelo base correspondente a partir do qual foi adaptado seja atualizado no serviço de fala padrão.  
Esta política permite-lhe continuar a utilizar um modelo personalizado específico durante muito tempo depois de ter um modelo personalizado que satisfaça as suas necessidades.  Mas recomendamos que recrie periodicamente o seu modelo personalizado para que possa adaptar-se a partir do mais recente modelo base para tirar partido da melhor precisão e qualidade.

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

Aqui está um exemplo dos dados de expiração da chamada getModel API. Os **DEPRECATIONDATES** mostram quando o modelo expira: 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
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

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>O que acontece quando os modelos expiram e como atualizá-los
O que acontece quando um modelo expira e como atualizar o modelo depende de como está a ser utilizado.
### <a name="batch-transcription"></a>Transcrição em lote
Se um modelo expirar que for utilizado com pedidos [de transcrição de transcrição](batch-transcription.md) de lote falhará com um erro de 4xx. Para evitar esta atualização, o `model` parâmetro no JSON enviado no corpo de pedido de **Transcrição criar** para apontar para um modelo base mais recente ou um modelo personalizado mais recente. Também pode remover a `model` entrada do JSON para utilizar sempre o modelo base mais recente.
### <a name="custom-speech-endpoint"></a>Ponto final de discurso personalizado
Se um modelo expirar que é utilizado por um [ponto final de fala personalizado,](how-to-custom-speech-train-model.md)então o serviço voltará automaticamente a utilizar o modelo base mais recente para o idioma que está a utilizar. , está a utilizar, pode selecionar **a Implementação** no menu **Discurso Personalizado** no topo da página e, em seguida, clicar no nome do ponto final para ver os seus dados. No topo da página de detalhes, verá um botão **'Atualizar Modelo'** que lhe permite atualizar perfeitamente o modelo utilizado por este ponto final sem tempo de inatividade. Também pode fazer esta alteração programáticamente utilizando a API [**de repouso do modelo de atualização.**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel)

## <a name="next-steps"></a>Passos seguintes

* [Preparar e implementar um modelo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Prepare e teste os seus dados](./how-to-custom-speech-test-and-train.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)