---
title: 'Como: Exportar aplicação de comandos personalizados como uma habilidade remota - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende a exportar a aplicação de Comandos Personalizados como uma habilidade
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 8c7cb1e9f39b1de7897da29467a607953b42bb24
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565743"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Aplicação de Comandos Personalizados de Exportação como uma habilidade remota

Neste artigo, você aprenderá a exportar uma aplicação de Comandos Personalizados como uma habilidade remota.

## <a name="prerequisites"></a>Pré-requisitos
> [!div class="checklist"]
> * [Compreensão da habilidade-quadro bot](https://aka.ms/speech/cc-skill-overview)
> * [Compreensão do Manifesto de Habilidades](https://aka.ms/speech/cc-skill-manifest)
> * [Como invocar uma habilidade de um Bot Framework Bot](https://aka.ms/speech/cc-skill-consumer)
> * Uma aplicação de comandos personalizados exisindo. Caso não tenha nenhuma aplicação de Comandos Personalizados, experimente com - [Quickstart: Criar um assistente de voz utilizando comandos personalizados](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Comandos personalizados como habilidades remotas
* Bot Framework Skills são reutilizáveis chatal skill-blocks que cobrem casos de uso de conversação que lhe permitem adicionar uma funcionalidade extensiva a um Bot em poucos minutos. Para ler mais sobre isto, vá ao [Bot Framework Skill.](https://microsoft.github.io/botframework-solutions/overview/skills/)
* Uma aplicação de Comandos Personalizados pode ser exportada como uma habilidade. Esta habilidade pode então ser invocada sobre o protocolo de habilidades remotas de um bot Desapertado.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Configure uma aplicação para ser exposta como uma habilidade remota

### <a name="application-level-settings"></a>Definições de nível de aplicação
1. No painel esquerdo, selecione **Definições**  >  **Habilidades remotas**.
1. Detive **as habilidades remotas ativadas** para o passar.

### <a name="authentication-to-skills"></a>Autenticação a competências
1. Se pretender ativar a autenticação, adicione ids de aplicação da Microsoft dos Bots De Estrutura Bots que pretende configurar para ligar para a aplicação de comandos personalizados.
      > [!div class="mx-imgBorder"]
      > ![Adicione um id MSA à habilidade](media/custom-commands/skill-add-msa-id.png)

1. Se tiver pelo menos uma entrada adicionada à lista, a autenticação será ativada na aplicação, e apenas os bots autorizados poderão ligar para a aplicação.
> [!TIP]
>  Para desativar a autenticação, elimine todos os IDs de aplicação da Microsoft da lista permitida. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Permitir/desativar comandos para serem expostos como competências

Tem a opção de escolher quais os comandos que pretende exportar sobre As Habilidades Remotas.

1. Para expor um comando sobre habilidades, selecione **Ative um novo comando** sob os **comandos Enable para habilidades**.
1. A partir do dropdown, selecione o comando que pretende adicionar.
1. Selecione **Guardar**.

### <a name="configure-triggering-utterances-for-commands"></a>Configurar declarações para comandos
Os Comandos Personalizados usam as frases de exemplo que são configuradas para os comandos de modo a gerar as habilidades que desencadeiam expressões. Estas **expressões de desencadeamento** serão usadas para gerar o manifesto de [**habilidade**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/)da secção de **despachante** .

Como autor, talvez queira controlar quais das suas frases de exemplo são **usadas** para gerar as proclamações desencadeantes para habilidades.
1. Por predefinição, todos os exemplos de Desencadeamento de um comando serão **incluídos** no ficheiro manifesto.
1. Se pretender eliminar explicitamente qualquer exemplo, **selecione editar** o ícone do comando a partir de **comandos Ativados para** a secção de habilidades.
    > [!div class="mx-imgBorder"]
    > ![Editar um comando habilitado para habilidade](media/custom-commands/skill-edit-enabled-command.png)

1. Em seguida, nas frases de exemplo que deseja omitir, **clique em**  >  **"Disable Example Sentence".**
    > [!div class="mx-imgBorder"]
    > ![Desativar exemplos](media/custom-commands/skill-disable-example-sentences.png)

1. Selecione **Guardar**.
1. Vai notar que não pode adicionar um novo exemplo nesta janela. Se houver necessidade de o fazer, dirija-se à saída para fora da secção de definições e selecione o comando relevante do acordeão de **Comandos.** Neste ponto, pode adicionar a nova entrada na secção **frases Exemplo.** Esta alteração será automaticamente refletida no valor das definições de habilidades remotas para o comando.

> [!IMPORTANT]
> Caso as suas frases de exemplo existentes tenham referências a tipos de dados **do Catálogo de >,** essas frases serão automaticamente omitidas da lista de habilidades que desencadeiam as expressões. 

## <a name="download-skill-manifest"></a>Descarregar manifesto de habilidades
1. Depois de **ter publicado** a sua aplicação, pode descarregar o ficheiro manifesto de habilidades.
1. Use o manifesto de habilidades para configurar o seu bot de consumo Bot Framework para ligar para a habilidade de Comandos Personalizados.
> [!IMPORTANT]
> Tem de **publicar** a sua aplicação Comandos Personalizados para descarregar o manifesto de habilidades. </br>
> Além disso, se tiver **esto** feito alguma alteração à aplicação, terá de publicar novamente o pedido para que as alterações mais recentes sejam refletidas no ficheiro manifesto.

> [!NOTE]
> Se enfrentar problemas com a publicação da aplicação e o erro direcionar-se para as competências que desencadeiam expressões, verifique novamente a configuração para **comandos ativados para obter competências**. Cada um dos comandos expostos deve ter pelo menos uma expressão de desencadeamento válida.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Atualizar um comando do cliente](./how-to-custom-commands-update-command-from-client.md)
