---
title: 'Quickstart: Criar um assistente de voz utilizando comandos personalizados'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, cria-se e testa uma aplicação básica de Comandos Personalizados utilizando o Speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: ea32358aa86a6093cd72adf959d5fe731cf1e125
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786881"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Criar um assistente de voz com os Comandos Personalizados

Neste arranque rápido, cria-se e testa uma aplicação básica de Comandos Personalizados utilizando o Speech Studio. Também poderá aceder a esta aplicação a partir de uma aplicação para clientes Windows.

## <a name="region-availability"></a>Disponibilidade da Região
Neste momento, os Comandos Personalizados suportam subscrições de discurso criadas nestas regiões:
* E.U.A. Oeste
* E.U.A. Oeste 2
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Centro-Oeste
* Europa do Norte
* Europa Ocidental
* Ásia Leste
* Sudeste Asiático
* Índia Central

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Crie um recurso de Discurso Azul numa região que suporte comandos personalizados.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Consulte a secção **disponibilidade da região** acima para obter a lista de regiões apoiadas.
> * Descarregue a amostra [smart room Lite](https://aka.ms/speech/cc-quickstart) json file.
> * Descarregue a versão mais recente do [Cliente Assistente de Voz](https://aka.ms/speech/va-samples-wvac)do Windows .

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vá ao Estúdio de Fala para Comandos Personalizados

1. Num navegador web, vá ao [Speech Studio.](https://speech.microsoft.com/)
1. Introduza as suas credenciais para iniciar sessão no portal.

   A vista padrão é a sua lista de subscrições do Discurso.
   > [!NOTE]
   > Se não vir a página de subscrição selecionada, pode navegar por aí escolhendo "Recursos de fala" a partir do menu de definições na barra superior.

1. Selecione a subscrição do Discurso e, em seguida, selecione **Ir para o Estúdio**.
1. Selecione **Comandos Personalizados**.

   A vista predefinida é uma lista das aplicações de Comandos Personalizados que tem sob a subscrição selecionada.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importe uma aplicação existente como um novo projeto de Comandos Personalizados

1. Selecione **novo projeto** para criar um projeto.

1. Na caixa **Nome,** insira o nome do projeto como `Smart-Room-Lite` (ou outra coisa à sua escolha).
1. Na lista **de línguas,** selecione **Inglês (Estados Unidos)**.
1. **Selecione Procurar ficheiros** e na janela de navegação, selecione o **SmartRoomLite.jsno** ficheiro.

    > [!div class="mx-imgBorder"]
    > ![Criar um projeto](media/custom-commands/import-project.png)

1.  Na lista **de recursos de autoria LUIS,** selecione um recurso de autoria. Se não houver recursos de autoria válidos, crie um selecionando **Criar novo recurso de autoria LUIS.**

    > [!div class="mx-imgBorder"]
    > ![Criar um grupo de recursos](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Na caixa **Nome de Recursos,** insira o nome do recurso.
    1. Na lista **do Grupo de Recursos,** selecione um grupo de recursos.
    1. Na lista **de localização,** selecione uma localização.
    1. Na lista **de Preços,** selecione um nível.
    
    
    > [!NOTE]
    > Pode criar grupos de recursos introduzindo o nome de grupo de recursos pretendido no campo "Grupo de Recursos". O grupo de recursos será criado quando **a Criação** for selecionada.


1. Em seguida, **selecione Criar** para criar o seu projeto.
1. Depois de o projeto ser criado, selecione o seu projeto.
Deverá agora consultar a sua nova aplicação de Comandos Personalizados.

## <a name="try-out-some-voice-commands"></a>Experimente alguns comandos de voz
1. Selecione **Train** no topo do painel direito.
1. Uma vez concluído o treino, selecione **Teste** e experimente as seguintes expressões:
    - Ligue a televisão
    - Definir a temperatura para 80 graus
    - Desliga isso
    - A televisão
    - Desemote um alarme para as 17:00

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrar a aplicação de Comandos Personalizados num assistente
Antes de poder aceder a esta aplicação a partir de fora do Speech Studio, tem de publicar a aplicação. Para a publicação de uma aplicação, terá de configurar o recurso LUIS de previsão.  

### <a name="update-prediction-luis-resource"></a>Atualizar previsão de recurso LUIS


1. Selecione **Definições** no painel esquerdo e selecione  **os recursos LUIS** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **Criar novo recurso**.
1. Selecione **Guardar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir Recursos LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Como o recurso de autoria suporta apenas 1.000 pedidos de previsão por mês, você terá de definir um recurso de previsão LUIS antes de publicar a sua aplicação de Comandos Personalizados.

### <a name="publish-the-application"></a>Publicar a aplicação

Selecione  **Publicar** em cima do painel direito. Uma vez concluída a publicação, aparecerá uma nova janela. Note o valor da chave **de ID** de aplicação e **de recursos** de fala a partir do mesmo. Você precisará destes dois valores para poder aceder à aplicação a partir de fora do Speech Studio.

Em alternativa, também pode obter estes valores selecionando **definições**  >  **secções gerais.**

### <a name="access-application-from-client"></a>Pedido de acesso do cliente

No âmbito deste artigo, utilizaremos o cliente Assistente de Voz do Windows que descarregou como parte dos requisitos prévios. Deszipe a pasta.
1. Lançamento **VoiceAssistantClient.exe.**
1. Crie um novo perfil de publicação e introduza valor para **o Perfil de Ligação.** Na secção **Definições Gerais,** introduza valores **A Chave de Subscrição** (isto é o mesmo que o valor chave de **recurso do Discurso** que guardou ao publicar a aplicação), **região-chave de subscrição** e **ID de aplicação de comandos personalizados**.
    > [!div class="mx-imgBorder"]
    > ![Screenshot que realça a secção Definições Gerais para criar um perfil WVAC.](media/custom-commands/create-profile.png)
1. **Selecione Guardar e Aplicar Perfil**.
1. Agora experimente as seguintes entradas através de discurso/texto
    > [!div class="mx-imgBorder"]
    > ![WVAC Criar perfil](media/custom-commands/conversation.png)


> [!TIP]
> Pode clicar em entradas no **Registo de Atividades** para inspecionar as respostas brutas enviadas a partir do serviço Comandos Personalizados.

## <a name="next-steps"></a>Próximos passos

Neste artigo, usou uma aplicação existente. Em seguida, nas [secções de como desenhar,](./how-to-develop-custom-commands-application.md)desenvolver, depurar, testar e integrar uma aplicação de Comandos Personalizados do zero.